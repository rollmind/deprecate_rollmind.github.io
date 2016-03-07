---
author: singcodes
comments: true
date: 2016-07-09 14:40:22+00:00
layout: post
link: https://singcodes.wordpress.com/2016/07/09/cgpath-from-character-with-ctfont/
slug: cgpath-from-character-with-ctfont
title: CGPath from Character With CTFont
wordpress_id: 1503
categories:
- swift

---
```
func pathFromCharacter(character char: Character) -> CGPath {
    let font = CTFontCreateUIFontForLanguage(.system, 30, nil)!
    let charStr = AttributedString(string: "\(char)", 
    				attributes: 
    				[kCTFontAttributeName as String : font])
    let line = CTLineCreateWithAttributedString(charStr)
    let array = CTLineGetGlyphRuns(line) as [AnyObject]
    let paths = UIBezierPath()
    for ao in array {
        let run = ao as! CTRun
        let attributes = CTRunGetAttributes(run) as NSDictionary
        let runFont = attributes.object(forKey: kCTFontAttributeName) as! CTFont
        
        let glyphCount = CTRunGetGlyphCount(run)
        for index in 0..<glyphCount {
            var glyph = CGGlyph()
            var point = CGPoint()
            CTRunGetGlyphs(run, CFRangeMake(index, 1), &glyph)
            CTRunGetPositions(run, CFRangeMake(index, 1), &point)
            
            var transform = CGAffineTransform(translationX: point.x, y: point.y)
            transform = transform.concat(CGAffineTransform(scaleX: 1, y: -1))
            let path = CTFontCreatePathForGlyph(runFont, glyph, &transform)!
            paths.append(UIBezierPath(cgPath: path))
        }
    }
    paths.close()
    return paths.cgPath
}
```