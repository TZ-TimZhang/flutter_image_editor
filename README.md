# image_editor

![BUILD](https://github.com/fluttercandies/flutter_image_editor/workflows/PR/badge.svg)

The version of readme pub and github may be inconsistent, please refer to [github](https://github.com/fluttercandies/flutter_image_editor).

Use native(objc,kotlin) code to handle image data, it is easy to process pictures, and can be used for saving/uploading/preview images.

- [image_editor](#image_editor)
  - [Screenshot](#screenshot)
  - [Platform of support](#platform-of-support)
  - [Support](#support)
  - [ImageEditor Usage](#imageeditor-usage)
    - [ImageEditor method params](#imageeditor-method-params)
    - [ImageEditorOption](#imageeditoroption)
    - [Option](#option)
      - [Flip](#flip)
      - [Clip](#clip)
      - [Rotate](#rotate)
      - [Color Martix](#color-martix)
      - [Scale](#scale)
      - [AddText](#addtext)
      - [MixImage](#miximage)
        - [BlendMode](#blendmode)
    - [OutputFormat](#outputformat)
  - [ImageMerge](#imagemerge)
  - [Common issue](#common-issue)
    - [iOS](#ios)
      - [Privacy of camera](#privacy-of-camera)
  - [LICENSE](#license)
    - [Third party](#third-party)

## Screenshot

![img](https://github.com/kikt-blog/image/raw/master/github/flutter_image_editor_ss.gif)

## Platform of support

Android, iOS.

## Support

- [x] flip
- [x] crop
- [x] rotate
- [x] scale
- [x] matrix
- [x] add text
- [x] mix image
- [x] merge multi image
- [ ] draw somethings
  - [ ] draw point
  - [ ] draw line
  - [ ] draw rect
  - [ ] draw circle
  - [ ] draw path
  - [ ] draw Bezier
- [ ] Gaussian blur

## ImageEditor Usage

[![pub package](https://img.shields.io/pub/v/image_editor.svg)](https://pub.dev/packages/image_editor) [![GitHub](https://img.shields.io/github/license/fluttercandies/flutter_image_editor.svg)](https://github.com/fluttercandies/flutter_image_editor) [![GitHub stars](https://img.shields.io/github/stars/fluttercandies/flutter_image_editor.svg?style=social&label=Stars)](https://github.com/fluttercandies/flutter_image_editor)

```yaml
dependencies:
  image_editor: ^0.6.0
```

Import

```dart
import 'package:image_editor/image_editor.dart';
```

Method list:

```dart
ImageEditor.editImage();
ImageEditor.editFileImage();
ImageEditor.editFileImageAndGetFile();
ImageEditor.editImageAndGetFile();
```

[Example used alone](https://github.com/CaiJingLong/flutter_image_editor/blob/master/example/lib/advanced_page.dart)

[Example](https://github.com/CaiJingLong/flutter_image_editor/blob/master/example/lib/advanced_page.dart) of [extended_image](https://github.com/fluttercandies/extended_image)

### ImageEditor method params

| Name              | Description                            |
| ----------------- | -------------------------------------- |
| image             | dart.typed_data.Uint8List              |
| file              | dart.io.File                           |
| imageEditorOption | flutter_image_editor.ImageEditorOption |

### ImageEditorOption

```dart
final editorOption = ImageEditorOption();
editorOption.addOption(FlipOption());
editorOption.addOption(ClipOption());
editorOption.addOption(RotateOption());
editorOption.addOption(); // and other option.

editorOption.outputFormat = OutputFormat.png(88);
```

### Option

#### Flip

```dart
FlipOption(horizontal:true, vertical:false);
```

#### Clip

```dart
ClipOption(x:0, y:0, width:1920, height:1920);
```

#### Rotate

```dart
RotateOption(degree: 180);
```

#### Color Martix

```dart
ColorOption();
```

it's use 5x4 matrix : https://developer.android.google.cn/reference/android/graphics/ColorMatrix.html Although it is an Android document, the color matrix is also applicable to iOS.

a, b, c, d, e,  
f, g, h, i, j,  
k, l, m, n, o,  
p, q, r, s, t

---

In iOS, it's use 4x4 matrix. The last of line will be ignored.

a, b, c, d,  
f, g, h, i,  
k, l, m, n,  
p, q, r, s

#### Scale

```dart
ScaleOption(width,height);
```

After specifying the width and height, it is not clipped, but stretched to the specified width and height (Does not maintain the aspect ratio of the image).

#### AddText

All of unit is **pixel**.

```dart
final textOption = AddTextOption();
textOption.addText(
  EditorText(
    offset: Offset(0, 0),
    text: this._controller.text,
    fontSizePx: size,
    color: Colors.red,
  ),
);
```

#### MixImage

```dart
void mix(BlendMode blendMode) async {
  final src = await loadFromAsset(R.ASSETS_SRC_PNG);
  final dst = await loadFromAsset(R.ASSETS_DST_PNG);
  final optionGroup = ImageEditorOption();
  optionGroup.outputFormat = OutputFormat.png();
  optionGroup.addOption(
    MixImageOption(
      x: 300,
      y: 300,
      width: 150,
      height: 150,
      target: MemoryImageSource(src),
      blendMode: blendMode,
    ),
  );
  final result =
      await ImageEditor.editImage(image: dst, imageEditorOption: optionGroup);
  this.image = MemoryImage(result);
  setState(() {});
}
```

##### BlendMode

Support next BlendMode, other will be ignored.

| iOS                         | android(PorterDuff.Mode) | flutter(BlendMode) |
| --------------------------- | ------------------------ | ------------------ |
| kCGBlendModeClear           | CLEAR                    | clear              |
|                             | SRC                      | src                |
|                             | DST                      | dst                |
| kCGBlendModeNormal          | SRC_OVER                 | srcOver            |
| kCGBlendModeDestinationOver | DST_OVER                 | dstOver            |
| kCGBlendModeSourceIn        | SRC_IN                   | srcIn              |
| kCGBlendModeDestinationIn   | DST_IN                   | dstIn              |
| kCGBlendModeSourceOut       | SRC_OUT                  | srcOut             |
| kCGBlendModeDestinationOver | DST_OUT                  | dstOut             |
| kCGBlendModeSourceAtop      | SRC_ATOP                 | srcATop            |
| kCGBlendModeDestinationAtop | DST_ATOP                 | dstATop            |
| kCGBlendModeXOR             | XOR                      | xor                |
| kCGBlendModeDarken          | DARKEN                   | darken             |
| kCGBlendModeLighten         | LIGHTEN                  | lighten            |
| kCGBlendModeMultiply        | MULTIPLY                 | multiply           |
| kCGBlendModeScreen          | SCREEN                   | screen             |
| kCGBlendModeOverlay         | OVERLAY                  | overlay            |

### OutputFormat

```dart
var outputFormat = OutputFormat.png();
var outputFormat = OutputFormat.jpeg(95);

```

## ImageMerge

```dart
    final slideLength = 180.0;
    final option = ImageMergeOption(
      canvasSize: Size(slideLength * count, slideLength * count),
      format: OutputFormat.png(),
    );

    final memory = await loadFromAsset(R.ASSETS_ICON_PNG);
    for (var i = 0; i < count; i++) {
      option.addImage(
        MergeImageConfig(
          image: MemoryImageSource(memory),
          position: ImagePosition(
            Offset(slideLength * i, slideLength * i),
            Size.square(slideLength),
          ),
        ),
      );
    }
    for (var i = 0; i < count; i++) {
      option.addImage(
        MergeImageConfig(
          image: MemoryImageSource(memory),
          position: ImagePosition(
            Offset(
                slideLength * count - slideLength * (i + 1), slideLength * i),
            Size.square(slideLength),
          ),
        ),
      );
    }

    final result = await ImageMerger.mergeToMemory(option: option);
    provider = MemoryImage(result);
    setState(() {});
```

## Common issue

### iOS

#### Privacy of camera

Because, I include [GPUImage](https://github.com/BradLarson/GPUImage.git) to handle image data, and the library have Camera api, so you must add next Usage String in info.plist. It was introduced in version 0.3.x, if you don't need the new features added after 0.3, you can keep using the old version.

[Why need add it by apple](https://developer.apple.com/library/archive/qa/qa1937/_index.html)  
[How to add it by apple](https://help.apple.com/xcode/mac/8.0/#/dev3f399a2a6)

## LICENSE

MIT Style.

### Third party

Under BSD3 style:
[GPUImage](https://github.com/BradLarson/GPUImage.git)

Under Apache 2.0 style:
Some martix code come from android sdk.
