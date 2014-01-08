### Dynamic type

  iOS7对系统字体在显示上做了一些优化，让不同大小的字体在屏幕上都能清晰的显示。通常用户设置了自己偏好的字体，他们希望在所有程序中都看到文本显示是根据他们的设定进行调整。为了实现这个，开发者需要在自己的应用中给文本控件设置当前用户设置字体，而不是指定死字体及大小。可以通过`UIFont`中新增的`preferredFontForTextStyle:`方法来获取用户偏好的字体。
  iOS7中给出了6中字体样式供选择：
  
    UIFontTextStyleHeadline
    UIFontTextStyleBody
    UIFontTextStyleSubheadline
    UIFontTextStyleFootnote
    UIFontTextStyleCaption1
    UIFontTextStyleCaption2

  在系统字体修改时，系统会给运行中的程序发送`UIContentSizeCategoryDidChangeNotification`通知，我们只需要监听这个通知，并重新设置一下字体即可。

    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(preferredContentSizeChanged:) name:UIContentSizeCategoryDidChangeNotification object:nil];
    - (void)preferredContentSizeChanged:(NSNotification *)notification{
        self.textView.font = [UIFont preferredFontForTextStyle:UIFontTextStyleBody];
    }

### Letterpress effects
  
  凸版印刷替效果是给文字加上奇妙阴影和高光，让文字看起有凹凸感，像是被压在屏幕上。当然这种看起来很高端大气上档次的效果实现起来确实相当的简单，只需要给`AttributedString`加一个`NSTextEffectAttributeName`属性，并指定该属性值为`NSTextEffectLetterpressStyle`就可以了。
  
    NSDictionary *attributes = @{ 
    NSForegroundColorAttributeName: [UIColor redColor],
    NSFontAttributeName: [UIFont preferredFontForTextStyle:UIFontTextStyleHeadline],
    NSTextEffectAttributeName: NSTextEffectLetterpressStyle
    };
    self.titleLabel.attributedText = [[NSAttributedString alloc] initWithString:@"Title" attributes:attributes];
    
### Exclusion paths

  Explosion pats基本原理是将需要被文本留出来的形状的路径告诉文本控件的`NSTextContainer`对象，`NSTextContainer`在文字排版时就会避开该路径。

    UIBezierPath *floatingPath = [self pathOfImage];
    self.textView.textContainer.exclusionPaths = @[floatingPath];
    


@end
