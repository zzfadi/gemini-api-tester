# Gemini API Tester

> **Google Generative AI Testing Console** — Test Gemini & Veo APIs

A simple, web-based testing console for Google's Gemini and Veo AI APIs. Built for developers to quickly experiment with multimodal AI generation and validate API keys.

🌐 **Live Demo**: [meter.zuabi.dev](https://meter.zuabi.dev)

---

## ✨ Features

### 🖼️ **Image Generation**
- Test Gemini 2.5 Flash Image and Gemini 3 Pro Image models
- Configurable aspect ratios
- Real-time cost calculation

### 🎬 **Video Generation**
- Veo 3.1 Fast and Standard models
- Customizable duration and resolution
- Progress tracking for long-running operations

### 📝 **Text Generation**
- Multiple Gemini models (2.5 Flash, Flash-Lite, 3 Pro)
- Thinking mode support
- System instructions and temperature control
- Token usage and cost breakdown

### 🔧 **Diagnostics**
- API key validation
- System health checks
- Error reference table

---

## 🚀 Quick Start

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

---

## 🌐 Deployment

This project is configured for GitHub Pages.

1. Push to GitHub
2. Enable GitHub Pages in repository settings (Source: `main` branch)
3. (Optional) Configure custom domain

---

## 🔐 Security

- **Never commit API keys**
- API keys are stored only in browser memory
- All requests made directly from client to Google APIs

---

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details
