# Images Directory

This directory contains images and assets for the Talking Rock Labs blog.

## Required Images

### Logo
- **Filename**: `talking-rock-labs-logo.png`
- **Recommended size**: 400x200px or similar aspect ratio
- **Format**: PNG with transparent background
- **Location**: Place your logo file in this directory

The logo will be displayed in the masthead (header) of the blog. If no logo is present, a placeholder will be shown instead.

### Favicon
- **Filename**: `favicon.png`
- **Recommended size**: 32x32px or 64x64px
- **Format**: PNG
- **Location**: Place your favicon file in this directory

### Apple Touch Icon
- **Filename**: `apple-touch-icon.png`
- **Recommended size**: 180x180px
- **Format**: PNG
- **Location**: Place your apple touch icon file in this directory

## Post Images

You can add images for blog posts in this directory or create subdirectories to organize them.

Example directory structure:
```
public/images/
├── talking-rock-labs-logo.png
├── favicon.png
├── apple-touch-icon.png
└── posts/
    ├── 2024-01-example-post/
    │   ├── screenshot1.png
    │   └── diagram.jpg
    └── ...
```

## Usage in Posts

Reference images in your posts using:

```markdown
![Alt text]({{ site.baseurl }}/public/images/posts/your-image.png)
```
