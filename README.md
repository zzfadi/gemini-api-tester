# Deep Time Laboratory

> **Geological AI Console** — Test Gemini & Veo APIs for prehistoric Earth visualization

A sophisticated web-based testing console for Google's Gemini and Veo AI APIs, themed around deep geological time and prehistoric Earth. Built for developers and researchers to experiment with multimodal AI generation.

🌐 **Live Demo**: [meter.zuabi.dev](https://meter.zuabi.dev)

---

## ✨ Features

### 🖼️ **Image Generation**
- Test Gemini 2.5 Flash Image and Gemini 3 Pro Image models
- Configurable aspect ratios (16:9, 9:16, 1:1, 4:3)
- Real-time cost calculation
- Live preview with download capability

### 🎬 **Video Generation**
- Veo 3.1 Fast and Standard models
- Customizable duration (4-8 seconds)
- Multiple aspect ratios and resolutions (720p/1080p)
- Long-running operation polling with progress tracking
- Automatic video download and playback

### 📝 **Text Generation**
- Multiple Gemini models (2.5 Flash, Flash-Lite, 3 Pro)
- Thinking mode support with configurable token budget
- System instructions and temperature control
- Token usage and cost breakdown
- JSON response formatting

### 🔧 **Diagnostics**
- API key validation
- System health checks
- Common error reference table
- Rate limit documentation
- Network troubleshooting

---

## 🚀 Quick Start

### Local Development

1. **Clone the repository**
   ```bash
   git clone https://github.com/yourusername/api-tester.git
   cd api-tester
   ```

2. **Open in browser**
   Simply open `index.html` in your web browser. No build process required!

3. **Add your API key**
   - Get your API key from [Google AI Studio](https://aistudio.google.com/app/apikey)
   - Enter it in the authentication section
   - Click "Validate" to test connectivity

---

## 🌐 GitHub Pages Deployment

This project is configured for GitHub Pages with a custom domain.

### Initial Setup

1. **Push to GitHub**
   ```bash
   git remote add origin https://github.com/yourusername/api-tester.git
   git branch -M main
   git push -u origin main
   ```

2. **Enable GitHub Pages**
   - Go to repository Settings → Pages
   - Source: Deploy from branch `main`
   - Folder: `/ (root)`
   - Click Save

3. **Custom Domain (Optional)**
   - In GitHub Pages settings, add custom domain: `meter.zuabi.dev`
   - The `CNAME` file is already included in this repo
   - Update your DNS provider with:
     ```
     CNAME record: meter → yourusername.github.io
     ```

4. **Enforce HTTPS**
   - Wait for DNS propagation (can take 24-48 hours)
   - Enable "Enforce HTTPS" in GitHub Pages settings

### DNS Configuration

For `meter.zuabi.dev` subdomain:
```
Type:  CNAME
Name:  meter
Value: yourusername.github.io
TTL:   3600 (or default)
```

---

## 🎨 Design Philosophy

### Theme: Deep Geological Time
- **Color Palette**: Amber CRT display aesthetic inspired by vintage computing
- **Typography**: IBM Plex Mono (code) + Instrument Serif (headings)
- **Visual Effects**: Scanlines, CRT glow, noise overlay
- **Naming**: Geological and mineralogical terminology throughout

### Architecture
- **Zero Dependencies**: Pure HTML, CSS, and vanilla JavaScript
- **Progressive Enhancement**: Works in all modern browsers
- **Mobile Responsive**: Adapts to different screen sizes
- **API-First**: Direct REST API integration without SDKs

---

## 📖 Usage

### Image Generation
1. Select model (Flash or Pro)
2. Choose aspect ratio
3. Write detailed generation prompt
4. Click "Execute Generation"
5. View result and download

### Video Generation
1. Select Veo model variant
2. Configure duration, aspect ratio, and resolution
3. Write cinematic prompt with camera movements
4. Execute and wait for long-running operation (2-5 minutes)
5. Video auto-downloads when complete

### Text Generation
1. Choose Gemini model
2. Set temperature and token limits
3. Configure thinking budget (if supported)
4. Provide system instructions and user prompt
5. Review generated text and token costs

---

## 💰 Pricing Reference

### Image Models
- **Gemini 2.5 Flash Image**: $0.039 per image
- **Gemini 3 Pro Image**: $0.134 per image

### Video Models
- **Veo 3.1 Fast**: $0.15 per second
- **Veo 3.1 Standard**: $0.40 per second

### Text Models
- **Gemini 2.5 Flash**: $0.30/1M in, $2.50/1M out
- **Gemini 2.5 Flash-Lite**: $0.10/1M in, $0.40/1M out
- **Gemini 3 Pro**: $2.00/1M in, $12.00/1M out

---

## 🔐 Security Notes

- **Never commit API keys** to version control
- API keys are stored only in browser memory (not localStorage)
- All requests made directly from client to Google APIs
- No backend server required

---

## 🛠️ Technical Stack

- **HTML5**: Semantic structure
- **CSS3**: Custom properties, Grid, Flexbox
- **Vanilla JavaScript**: ES6+ features
- **Google Gemini API**: Direct REST integration
- **Google Veo API**: Long-running operations

---

## 📋 API Documentation

- [Gemini API Reference](https://ai.google.dev/gemini-api/docs)
- [Veo Video Generation](https://ai.google.dev/api/generate-video)
- [Image Generation Guide](https://ai.google.dev/gemini-api/docs/imagen)
- [Troubleshooting](https://ai.google.dev/gemini-api/docs/troubleshooting)

---

## 🤝 Contributing

Contributions welcome! Areas for improvement:
- Additional model support
- Enhanced error handling
- Batch processing features
- Export/save functionality
- Advanced configuration options

---

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details

---

## 🙏 Acknowledgments

- Google AI Studio for API access
- Inspired by geological sciences and deep time
- Built for the AI research community

---

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/yourusername/api-tester/issues)
- **Discussions**: [Google AI Forum](https://discuss.ai.google.dev)
- **Documentation**: [Gemini API Docs](https://ai.google.dev)

---

**Made with 🌋 for exploring AI through the lens of geological time**
