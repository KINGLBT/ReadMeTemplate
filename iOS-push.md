### push原理

### App开发基础设置

  * 1) 在iPhone Provisioning Portal中建立好APP ID和Device.
  * 2) 在Keychain Access.app中生成证书请求CertificateSigningRequest.certSigningRequest(菜单 > Keychain Access > Certificate Assistant > Request a Certificate From a Certificate Authority...).
  * 3) 在iPhone Provisioning Portal > Certificates中请求一个证书(点击Request Certificate,上传CertificateSigningRequest.certSigningRequest).
  * 4) 请求完成后,将证书文件(developer_identity.cer)下载,双击导入到Key Chain中.
  * 5) 在iPhone Provisioning Portal > Provisioning 中,新建一个Profile, 选择指定的APP ID和 Devices后生成.
  * 6) 将刚刚生成的Profile下载为*_profile.mobileprovision, 双击该文件, 将profile加载到iPhone中.

### push设置及证书的生成

  * 1) 在iPhone Provisioning Portal > App IDs,选择需要Push服务的App ID, 进入Configure.
  * 2) 确认 Enable for Apple Push Notification service ,配置 Development Push SSL Certificate, 上传第2步生成的证书请求.
  * 3) 下载生成的aps_developer_identity.cer, 完成Push服务配置.
  * 4) 双击aps_developer_identity.cer,保存到Key Chain.
  * 5) 在Keychain Access.app里选定这个新证书(Apple Development Push Services*),导出到桌面,保存为Certificates.p12.
  * 6) 运行如下命令:

        openssl pkcs12 -clcerts -nokeys -out cert.pem -in Certificates.p12
        openssl pkcs12 -nocerts -out key.pem -in Certificates.p12
        openssl rsa -in key.pem -out key.unencrypted.pem
        cat cert.pem key.unencrypted.pem > ck.pem

### 测试php

  * 测试服务器: gateway.sandbox.push.apple.com:2195
  * 测试服务器: gateway.push.apple.com:2195

        <?php
        $deviceToken = "设备令牌";
        $body = array("aps" => array("alert" => 'message', "badge" => 1, "sound" => 'default'));
        $ctx = stream_context_create();
        stream_context_set_option($ctx, "ssl", "local_cert", "ck.pem");
        $fp = stream_socket_client("ssl://gateway.sandbox.push.apple.com:2195", $err, $errstr, 60, STREAM_CLIENT_CONNECT, $ctx);
        if (!$fp) {
          print "Failed to connect $err $errstrn";
          return;
        }
        print "Connection OK\n";
        $payload = json_encode($body);
        $msg = chr(0) . pack("n",32) . pack("H*", $deviceToken) . pack("n",strlen($payload)) . $payload;
        print "sending message :" . $payload . "\n";
        fwrite($fp, $msg);
        fclose($fp);
        ?>
  
@end

