# On-Device AI Tester - Flutter Cross-Platform App

## Executive Summary

A cross-platform mobile application for **Android and iOS** that enables users to download, manage, and run on-device AI models locally. The app provides a unified interface for testing various model types (LLMs, vision, audio, embeddings) without requiring cloud connectivity.

**Key Differentiator:** Works on both platforms with open-source models from Hugging Face.

---

## Project Overview

| Aspect | Details |
|--------|---------|
| **Platform** | Android + iOS (Flutter) |
| **Models** | Open-source (Llama, Gemma, Phi, Whisper, etc.) |
| **Engine** | llama.cpp, TensorFlow Lite, ONNX Runtime |
| **Model Source** | Hugging Face Hub |
| **Distribution** | Google Play Store + Apple App Store |
| **Target Users** | Developers, researchers, AI enthusiasts |

---

## 1. Technology Stack

### Core Framework: Flutter

**Why Flutter over React Native:**
- Superior on-device AI performance with AOT compilation
- Direct native code compilation (no JavaScript bridge overhead)
- First-party support for Google ML Kit and TensorFlow Lite
- Single codebase for iOS, Android (and future desktop/web)
- `flutter_llama` plugin provides ready-made llama.cpp integration
- GitHub: 170k stars vs React Native's 121k (2025 data)
- Better suited for graphics-heavy AI visualizations

### Tech Stack Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                         FLUTTER APP                              │
├─────────────────────────────────────────────────────────────────┤
│  UI Framework      │ Flutter 3.16+ / Dart 3.2+                  │
│  State Management  │ Riverpod                                   │
│  Navigation        │ go_router                                  │
│  Local Database    │ Drift (SQLite)                             │
│  Networking        │ Dio                                        │
├─────────────────────────────────────────────────────────────────┤
│  ML/AI ENGINES                                                   │
│  • llama.cpp (flutter_llama) - LLM inference                    │
│  • TensorFlow Lite (tflite_flutter) - Vision/Audio              │
│  • ONNX Runtime - Cross-platform models                         │
│  • Whisper.cpp - Speech-to-text                                 │
├─────────────────────────────────────────────────────────────────┤
│  NATIVE LAYER                                                    │
│  Android: Kotlin + JNI/NDK                                      │
│  iOS: Swift + C++ bridges                                       │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. On-Device ML Frameworks

### Framework Matrix

| Component | Android | iOS | Format |
|-----------|---------|-----|--------|
| **LLM Inference** | llama.cpp (NDK) | llama.cpp (C++) | GGUF |
| **Traditional ML** | TensorFlow Lite | TensorFlow Lite | TFLite |
| **Vision** | ML Kit / TFLite | Core ML / TFLite | Various |
| **Audio/Speech** | Whisper.cpp | Whisper.cpp | GGUF |
| **Embeddings** | llama.cpp | llama.cpp | GGUF |

### llama.cpp Integration (Primary LLM Engine)

```
flutter_llama package
├── Supports GGUF model format
├── Works on Android, iOS, macOS
├── Quantization: Q4_K_M, Q5_0, Q8_0
├── Performance: 8-12 tok/s on flagship devices
└── Streaming token generation
```

### Performance Expectations

| Device Class | Model | Speed | RAM Usage |
|--------------|-------|-------|-----------|
| Flagship (SD 8 Gen 3) | Llama 3.2 3B Q4 | 10-15 tok/s | 2.5 GB |
| Flagship (SD 8 Gen 3) | Phi-3 Mini Q4 | 12-18 tok/s | 2.0 GB |
| Mid-range (SD 7 Gen 1) | Gemma 2B Q4 | 6-10 tok/s | 1.5 GB |
| iPhone 15 Pro | Llama 3.2 3B Q4 | 12-16 tok/s | 2.5 GB |

---

## 3. Supported Models

### Model Categories

| Category | Example Models | Format | Size Range |
|----------|---------------|--------|------------|
| **Small LLMs** | Phi-3-mini, Gemma-2B, TinyLlama, Qwen2-0.5B | GGUF | 0.5-2 GB |
| **Medium LLMs** | Llama-3.2-3B, Gemma-7B, Mistral-7B | GGUF | 2-6 GB |
| **Vision LLMs** | SmolVLM2, LLaVA-Phi, moondream | GGUF | 1-4 GB |
| **Image Models** | MobileNet-v3, EfficientNet, YOLO | TFLite | 10-50 MB |
| **Speech** | Whisper-tiny, Whisper-small, Whisper-base | GGUF | 50-500 MB |
| **Embeddings** | all-MiniLM, BGE-small, nomic-embed | GGUF | 50-200 MB |

### Model Sources

1. **Hugging Face Hub** (Primary)
   - Direct GGUF model search and download
   - Thousands of quantized models
   - Community-maintained quantizations

2. **Built-in Catalog**
   - Curated mobile-optimized models
   - Pre-verified compatibility
   - One-tap download

3. **Custom Import**
   - Local file picker
   - URL-based download
   - Drag-and-drop (desktop)

---

## 4. App Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        Flutter UI Layer                          │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐            │
│  │  Model   │ │  Chat/   │ │  Vision  │ │  Audio   │            │
│  │  Store   │ │  Text    │ │  Test    │ │  Test    │            │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘            │
├─────────────────────────────────────────────────────────────────┤
│                     State Management (Riverpod)                  │
├─────────────────────────────────────────────────────────────────┤
│                     Service Layer                                │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────────────┐     │
│  │ ModelManager │ │DownloadMgr  │ │   InferenceService   │     │
│  └──────────────┘ └──────────────┘ └──────────────────────┘     │
├─────────────────────────────────────────────────────────────────┤
│                     Platform Channels                            │
│  ┌──────────────────────┐  ┌──────────────────────────────┐     │
│  │    MethodChannel     │  │     EventChannel (streaming)  │     │
│  └──────────────────────┘  └──────────────────────────────┘     │
├─────────────────────────────────────────────────────────────────┤
│                     Native Layer                                 │
│  ┌─────────────────────────┐  ┌─────────────────────────────┐   │
│  │     Android (Kotlin)    │  │        iOS (Swift)          │   │
│  │  • llama.cpp (JNI/NDK)  │  │  • llama.cpp (C++ bridge)   │   │
│  │  • TensorFlow Lite      │  │  • TensorFlow Lite          │   │
│  │  • ONNX Runtime         │  │  • Core ML (optional)       │   │
│  └─────────────────────────┘  └─────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
```

### Directory Structure

```
on_device_ai_tester/
├── lib/
│   ├── main.dart
│   ├── app/
│   │   ├── app.dart
│   │   ├── routes.dart
│   │   └── theme.dart
│   ├── features/
│   │   ├── model_store/
│   │   │   ├── screens/
│   │   │   │   ├── model_browser_screen.dart
│   │   │   │   ├── model_detail_screen.dart
│   │   │   │   └── downloads_screen.dart
│   │   │   ├── widgets/
│   │   │   ├── providers/
│   │   │   └── models/
│   │   ├── chat/
│   │   │   ├── screens/
│   │   │   │   ├── chat_screen.dart
│   │   │   │   └── conversation_list_screen.dart
│   │   │   ├── widgets/
│   │   │   │   ├── message_bubble.dart
│   │   │   │   ├── typing_indicator.dart
│   │   │   │   └── model_selector.dart
│   │   │   └── providers/
│   │   ├── vision/
│   │   │   ├── screens/
│   │   │   │   ├── image_classification_screen.dart
│   │   │   │   ├── object_detection_screen.dart
│   │   │   │   └── vision_llm_screen.dart
│   │   │   └── widgets/
│   │   ├── audio/
│   │   │   ├── screens/
│   │   │   │   ├── speech_to_text_screen.dart
│   │   │   │   └── audio_analysis_screen.dart
│   │   │   └── widgets/
│   │   ├── embeddings/
│   │   │   ├── screens/
│   │   │   │   └── embedding_test_screen.dart
│   │   │   └── widgets/
│   │   ├── benchmarks/
│   │   │   └── screens/
│   │   │       └── benchmark_screen.dart
│   │   └── settings/
│   │       ├── screens/
│   │       └── providers/
│   ├── core/
│   │   ├── services/
│   │   │   ├── model_manager.dart
│   │   │   ├── download_manager.dart
│   │   │   ├── inference_service.dart
│   │   │   ├── huggingface_api.dart
│   │   │   └── storage_service.dart
│   │   ├── models/
│   │   │   ├── ai_model.dart
│   │   │   ├── download_task.dart
│   │   │   ├── inference_config.dart
│   │   │   └── model_metadata.dart
│   │   ├── utils/
│   │   └── constants/
│   └── shared/
│       ├── widgets/
│       └── extensions/
├── android/
│   └── app/src/main/
│       ├── kotlin/.../
│       │   └── LlamaPlugin.kt
│       └── cpp/
│           └── llama_bridge.cpp
├── ios/
│   └── Runner/
│       ├── LlamaPlugin.swift
│       └── llama_bridge.mm
├── assets/
│   ├── model_catalog.json
│   └── icons/
├── test/
└── pubspec.yaml
```

---

## 5. Core Features

### 5.1 Model Store & Management

```
┌─────────────────────────────────────────────────┐
│              MODEL STORE                        │
├─────────────────────────────────────────────────┤
│  🔍 Search Hugging Face...                      │
├─────────────────────────────────────────────────┤
│  📦 FEATURED MODELS                             │
│  ┌─────────────────────────────────────────┐   │
│  │ 🦙 Llama 3.2 1B          [Download 1.2GB]│   │
│  │ ⚡ Fast • English • Chat                 │   │
│  └─────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────┐   │
│  │ 💎 Gemma 2B               [Download 2.0GB]│   │
│  │ ⚡ Google • Multilingual                 │   │
│  └─────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────┐   │
│  │ 🔬 Phi-3 Mini             [Download 2.3GB]│   │
│  │ ⚡ Microsoft • Reasoning                 │   │
│  └─────────────────────────────────────────┘   │
├─────────────────────────────────────────────────┤
│  📥 MY MODELS (3)                    [Manage]  │
│  • Llama-3.2-1B-Q4 ✓ Ready                     │
│  • Whisper-tiny ✓ Ready                        │
│  • MobileNet-v3 ✓ Ready                        │
├─────────────────────────────────────────────────┤
│  ⬇️ ACTIVE DOWNLOADS                            │
│  Gemma-2B-Q4... ████████░░ 78% • 1.2 GB/1.5 GB │
└─────────────────────────────────────────────────┘
```

**Features:**
- Browse curated model catalog
- Search Hugging Face for GGUF models
- Filter by: type, size, language, capabilities
- Download with progress tracking & resume support
- Background downloads
- Storage management (delete, move to SD card on Android)
- Model metadata display (size, quantization, benchmarks)
- Model verification (SHA256 checksums)

### 5.2 LLM Chat Interface

```
┌─────────────────────────────────────────────────┐
│  🦙 Llama 3.2 1B                    [⚙️ Config] │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 🤖 Hello! I'm running locally on your   │   │
│  │    device. How can I help you today?    │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 👤 Explain quantum computing simply     │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 🤖 Quantum computing uses quantum bits  │   │
│  │ (qubits) that can exist in multiple     │   │
│  │ states simultaneously, unlike classical │   │
│  │ bits which are either 0 or 1...         │   │
│  │                                          │   │
│  │ ⚡ 12.3 tok/s • 156 tokens • 12.7s      │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
├─────────────────────────────────────────────────┤
│  [📷] Type a message...              [Send ➤]  │
└─────────────────────────────────────────────────┘
```

**Features:**
- Real-time token streaming
- Performance metrics (tokens/sec, latency, total tokens)
- Conversation history (persisted locally)
- System prompt customization
- Generation parameters:
  - Temperature (0.0 - 2.0)
  - Top-P (nucleus sampling)
  - Top-K
  - Max tokens
  - Repeat penalty
- Image input for vision LLMs
- Export/share conversations
- Multiple conversation threads

### 5.3 Vision Testing

```
┌─────────────────────────────────────────────────┐
│  📷 VISION TESTING                              │
├─────────────────────────────────────────────────┤
│  [Classification] [Detection] [Vision LLM]      │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │                                          │   │
│  │         [Camera Preview / Image]         │   │
│  │                                          │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  📊 RESULTS                                     │
│  ┌─────────────────────────────────────────┐   │
│  │ 🐕 Golden Retriever          95.2%      │   │
│  │ 🐕 Labrador Retriever         3.1%      │   │
│  │ 🐕 Dog                        1.2%      │   │
│  │                                          │   │
│  │ ⏱️ Inference: 45ms • Model: MobileNet   │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
├─────────────────────────────────────────────────┤
│  [📷 Camera] [🖼️ Gallery] [🔄 Run Again]        │
└─────────────────────────────────────────────────┘
```

**Features:**
- Real-time camera classification
- Gallery image selection
- Image classification (MobileNet, EfficientNet)
- Object detection with bounding boxes (YOLO, SSD)
- Vision-Language models (describe images with LLM)
- Batch processing for multiple images
- Export results

### 5.4 Audio/Speech Testing

**Features:**
- Speech-to-text with Whisper models
- Real-time transcription from microphone
- Audio file upload and processing
- Language detection
- Timestamp generation
- Performance metrics (real-time factor)
- Export transcripts

### 5.5 Embeddings Testing

**Features:**
- Text embedding generation
- Semantic similarity calculation
- Batch embedding processing
- Cosine similarity visualization
- Export embeddings (JSON/CSV)
- Simple vector search demo

### 5.6 Benchmarking

```
┌─────────────────────────────────────────────────┐
│  📊 BENCHMARKS                                  │
├─────────────────────────────────────────────────┤
│  DEVICE INFO                                    │
│  • Model: Pixel 8 Pro                          │
│  • SoC: Tensor G3                              │
│  • RAM: 12 GB (6.2 GB available)               │
│  • Storage: 45 GB free                         │
├─────────────────────────────────────────────────┤
│  BENCHMARK RESULTS                              │
│  ┌─────────────────────────────────────────┐   │
│  │ Llama 3.2 1B (Q4_K_M)                   │   │
│  │ • Prompt processing: 156 tok/s          │   │
│  │ • Token generation: 12.3 tok/s          │   │
│  │ • Memory usage: 1.8 GB                  │   │
│  │ • Model load time: 2.3s                 │   │
│  │ • First token: 450ms                    │   │
│  └─────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────┐   │
│  │ Whisper Tiny                            │   │
│  │ • Real-time factor: 0.15x               │   │
│  │ • Memory usage: 120 MB                  │   │
│  └─────────────────────────────────────────┘   │
├─────────────────────────────────────────────────┤
│  [Run Full Benchmark] [Export Results] [Share] │
└─────────────────────────────────────────────────┘
```

---

## 6. Technical Implementation

### 6.1 Model Download System

```dart
class DownloadManager {
  final Dio _dio;
  final Box<DownloadTask> _taskBox;

  Future<void> downloadModel(ModelInfo model) async {
    final task = DownloadTask(
      id: uuid.v4(),
      modelId: model.id,
      url: model.downloadUrl,
      totalSize: model.sizeBytes,
      downloadedSize: 0,
      status: DownloadStatus.pending,
    );

    await _taskBox.put(task.id, task);

    await _dio.download(
      model.downloadUrl,
      model.localPath,
      onReceiveProgress: (received, total) {
        _updateProgress(task.id, received, total);
      },
      options: Options(
        headers: {
          'Range': 'bytes=${task.downloadedSize}-', // Resume support
        },
      ),
    );
  }

  Future<void> resumeDownload(String taskId) async {
    final task = _taskBox.get(taskId);
    if (task == null) return;

    // Resume from last downloaded byte
    await _dio.download(
      task.url,
      task.localPath,
      onReceiveProgress: (received, total) {
        _updateProgress(taskId, task.downloadedSize + received, total);
      },
      options: Options(
        headers: {'Range': 'bytes=${task.downloadedSize}-'},
      ),
    );
  }
}
```

### 6.2 Inference Service

```dart
abstract class InferenceService {
  Future<void> loadModel(String modelPath, ModelConfig config);
  Future<void> unloadModel();
  Stream<String> generateText(String prompt, GenerationConfig config);
  Future<List<double>> generateEmbedding(String text);
  Future<ClassificationResult> classifyImage(Uint8List imageData);
}

class LlamaInferenceService implements InferenceService {
  static const _channel = MethodChannel('com.app/llama');
  static const _eventChannel = EventChannel('com.app/llama/stream');

  @override
  Stream<String> generateText(String prompt, GenerationConfig config) {
    _channel.invokeMethod('startGeneration', {
      'prompt': prompt,
      'temperature': config.temperature,
      'maxTokens': config.maxTokens,
      'topP': config.topP,
      'topK': config.topK,
      'repeatPenalty': config.repeatPenalty,
    });

    return _eventChannel
        .receiveBroadcastStream()
        .map((token) => token as String);
  }
}
```

### 6.3 Hugging Face API

```dart
class HuggingFaceApi {
  static const _baseUrl = 'https://huggingface.co/api';

  Future<List<ModelInfo>> searchGGUFModels(String query) async {
    final response = await _dio.get(
      '$_baseUrl/models',
      queryParameters: {
        'search': query,
        'filter': 'gguf',
        'sort': 'downloads',
        'direction': -1,
        'limit': 50,
      },
    );

    return (response.data as List)
        .map((json) => ModelInfo.fromHuggingFace(json))
        .toList();
  }

  Future<List<ModelFile>> getModelFiles(String repoId) async {
    final response = await _dio.get('$_baseUrl/models/$repoId');
    final siblings = response.data['siblings'] as List;

    return siblings
        .where((f) => (f['rfilename'] as String).endsWith('.gguf'))
        .map((f) => ModelFile.fromJson(f))
        .toList();
  }

  String getDownloadUrl(String repoId, String filename) {
    return 'https://huggingface.co/$repoId/resolve/main/$filename';
  }
}
```

### 6.4 Native Bridge - Android (Kotlin)

```kotlin
class LlamaPlugin : FlutterPlugin, MethodCallHandler, EventChannel.StreamHandler {
    private var llamaContext: Long = 0
    private var eventSink: EventChannel.EventSink? = null
    private val mainHandler = Handler(Looper.getMainLooper())

    override fun onMethodCall(call: MethodCall, result: Result) {
        when (call.method) {
            "loadModel" -> {
                val modelPath = call.argument<String>("path")!!
                val nCtx = call.argument<Int>("contextSize") ?: 2048
                val nThreads = call.argument<Int>("threads") ?: 4

                thread {
                    llamaContext = LlamaCpp.loadModel(modelPath, nCtx, nThreads)
                    mainHandler.post {
                        result.success(llamaContext != 0L)
                    }
                }
            }
            "startGeneration" -> {
                val prompt = call.argument<String>("prompt")!!
                val config = GenerationConfig.fromMap(call.arguments as Map<*, *>)

                thread {
                    LlamaCpp.generate(llamaContext, prompt, config) { token ->
                        mainHandler.post { eventSink?.success(token) }
                    }
                    mainHandler.post { eventSink?.endOfStream() }
                }
                result.success(true)
            }
            "unloadModel" -> {
                LlamaCpp.unloadModel(llamaContext)
                llamaContext = 0
                result.success(true)
            }
        }
    }
}
```

### 6.5 Native Bridge - iOS (Swift)

```swift
class LlamaPlugin: NSObject, FlutterPlugin, FlutterStreamHandler {
    private var llamaContext: OpaquePointer?
    private var eventSink: FlutterEventSink?

    func handle(_ call: FlutterMethodCall, result: @escaping FlutterResult) {
        switch call.method {
        case "loadModel":
            guard let args = call.arguments as? [String: Any],
                  let modelPath = args["path"] as? String else {
                result(FlutterError(code: "INVALID_ARGS", message: nil, details: nil))
                return
            }

            let contextSize = args["contextSize"] as? Int32 ?? 2048
            let threads = args["threads"] as? Int32 ?? 4

            DispatchQueue.global(qos: .userInitiated).async {
                self.llamaContext = llama_load_model(modelPath, contextSize, threads)
                DispatchQueue.main.async {
                    result(self.llamaContext != nil)
                }
            }

        case "startGeneration":
            guard let args = call.arguments as? [String: Any],
                  let prompt = args["prompt"] as? String else {
                result(FlutterError(code: "INVALID_ARGS", message: nil, details: nil))
                return
            }

            DispatchQueue.global(qos: .userInitiated).async { [weak self] in
                llama_generate(self?.llamaContext, prompt) { token in
                    DispatchQueue.main.async {
                        self?.eventSink?(token)
                    }
                }
                DispatchQueue.main.async {
                    self?.eventSink?(FlutterEndOfEventStream)
                }
            }
            result(true)

        default:
            result(FlutterMethodNotImplemented)
        }
    }
}
```

---

## 7. Data Storage

### SQLite Schema (Drift)

```sql
-- Models table
CREATE TABLE models (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,  -- llm, vision, audio, embedding
    format TEXT NOT NULL,  -- gguf, tflite, onnx
    size_bytes INTEGER NOT NULL,
    local_path TEXT,
    huggingface_repo TEXT,
    quantization TEXT,
    metadata TEXT,  -- JSON blob
    downloaded_at INTEGER,
    last_used_at INTEGER
);

-- Conversations table
CREATE TABLE conversations (
    id TEXT PRIMARY KEY,
    model_id TEXT NOT NULL,
    title TEXT,
    system_prompt TEXT,
    created_at INTEGER NOT NULL,
    updated_at INTEGER NOT NULL,
    FOREIGN KEY (model_id) REFERENCES models(id)
);

-- Messages table
CREATE TABLE messages (
    id TEXT PRIMARY KEY,
    conversation_id TEXT NOT NULL,
    role TEXT NOT NULL,  -- user, assistant, system
    content TEXT NOT NULL,
    tokens INTEGER,
    generation_time_ms INTEGER,
    created_at INTEGER NOT NULL,
    FOREIGN KEY (conversation_id) REFERENCES conversations(id)
);

-- Benchmarks table
CREATE TABLE benchmarks (
    id TEXT PRIMARY KEY,
    model_id TEXT NOT NULL,
    device_info TEXT NOT NULL,  -- JSON
    prompt_eval_tps REAL,
    generation_tps REAL,
    memory_mb REAL,
    load_time_ms INTEGER,
    first_token_ms INTEGER,
    created_at INTEGER NOT NULL,
    FOREIGN KEY (model_id) REFERENCES models(id)
);

-- Downloads table
CREATE TABLE downloads (
    id TEXT PRIMARY KEY,
    model_id TEXT NOT NULL,
    url TEXT NOT NULL,
    local_path TEXT NOT NULL,
    total_bytes INTEGER NOT NULL,
    downloaded_bytes INTEGER NOT NULL DEFAULT 0,
    status TEXT NOT NULL,  -- pending, downloading, paused, completed, failed
    error_message TEXT,
    started_at INTEGER,
    completed_at INTEGER
);
```

---

## 8. Device Requirements

### Minimum Requirements

| Platform | Requirement |
|----------|-------------|
| **Android** | Android 7.0 (API 24)+ |
| **iOS** | iOS 14.0+ |
| **RAM** | 4 GB minimum |
| **Storage** | 2 GB app + model storage |
| **CPU** | ARM64 required |

### Recommended

| Component | Recommendation |
|-----------|----------------|
| **RAM** | 8-12 GB |
| **Android SoC** | Snapdragon 8 Gen 1+ / Tensor G2+ |
| **iOS Device** | iPhone 12+ / iPad Pro M1+ |
| **Storage** | 20+ GB free for models |

---

## 9. Dependencies

```yaml
name: on_device_ai_tester
description: Download and run on-device AI models

environment:
  sdk: '>=3.2.0 <4.0.0'
  flutter: '>=3.16.0'

dependencies:
  flutter:
    sdk: flutter

  # State Management
  flutter_riverpod: ^2.4.9
  riverpod_annotation: ^2.3.3

  # Navigation
  go_router: ^13.0.0

  # Networking
  dio: ^5.4.0
  connectivity_plus: ^5.0.2

  # Local Storage
  drift: ^2.14.1
  sqlite3_flutter_libs: ^0.5.18
  shared_preferences: ^2.2.2
  hive_flutter: ^1.1.0

  # ML/AI
  flutter_llama: ^0.1.0
  tflite_flutter: ^0.10.4
  google_mlkit_commons: ^0.6.1
  google_mlkit_image_labeling: ^0.10.0
  google_mlkit_object_detection: ^0.11.0

  # Media
  camera: ^0.10.5+7
  image_picker: ^1.0.7
  audio_waveforms: ^1.0.5
  record: ^5.0.4

  # UI
  flutter_markdown: ^0.6.18+3
  shimmer: ^3.0.0
  flutter_animate: ^4.3.0

  # Utils
  path_provider: ^2.1.2
  permission_handler: ^11.1.0
  url_launcher: ^6.2.2
  share_plus: ^7.2.1
  uuid: ^4.2.2
  crypto: ^3.0.3  # For SHA256 verification

dev_dependencies:
  flutter_test:
    sdk: flutter
  flutter_lints: ^3.0.1
  build_runner: ^2.4.8
  riverpod_generator: ^2.3.9
  drift_dev: ^2.14.1
  freezed: ^2.4.6
  json_serializable: ^6.7.1
```

---

## 10. Implementation Phases

### Phase 1: Foundation
- [ ] Project setup with Flutter
- [ ] Basic app architecture and navigation
- [ ] Theme and design system
- [ ] Model data models and local database
- [ ] Hugging Face API integration
- [ ] Download manager with resume support
- [ ] Basic model list UI

### Phase 2: LLM Integration
- [ ] llama.cpp native integration (Android NDK)
- [ ] llama.cpp native integration (iOS)
- [ ] Flutter platform channels for streaming
- [ ] Chat UI with real-time token display
- [ ] Generation settings UI
- [ ] Conversation persistence
- [ ] System prompt support

### Phase 3: Vision & Audio
- [ ] TensorFlow Lite integration
- [ ] Image classification screen
- [ ] Object detection with bounding boxes
- [ ] Camera real-time inference
- [ ] Whisper.cpp integration
- [ ] Speech-to-text screen
- [ ] Audio file processing

### Phase 4: Polish & Advanced
- [ ] Vision LLM support (image + text)
- [ ] Embeddings testing screen
- [ ] Comprehensive benchmarking
- [ ] Settings and customization
- [ ] Performance optimization
- [ ] Error handling and edge cases
- [ ] App store assets and descriptions

---

## 11. Challenges & Mitigations

| Challenge | Mitigation |
|-----------|------------|
| **Large model sizes** | Aggressive quantization (Q4_K_M), chunked/resumable downloads |
| **Memory constraints** | Context size limits, automatic model unloading, memory monitoring |
| **Battery drain** | Efficient inference loops, user-configurable limits |
| **Platform differences** | Abstraction layers, comprehensive testing |
| **Model compatibility** | Curated catalog, format validation, clear error messages |
| **App Store size** | Models downloaded separately (not bundled) |

---

## 12. Security & Privacy

- All inference runs 100% on-device
- No data sent to external servers (except Hugging Face for downloads)
- No cloud API keys required
- Optional Hugging Face token for private models only
- Secure storage for credentials (Keychain/Keystore)
- Model file integrity verification (SHA256)
- No analytics or tracking

---

## References

### Frameworks & Libraries
- [flutter_llama Package](https://pub.dev/packages/flutter_llama)
- [tflite_flutter Package](https://pub.dev/packages/tflite_flutter)
- [llama.cpp GitHub](https://github.com/ggml-org/llama.cpp)
- [MLC LLM](https://llm.mlc.ai/)

### Model Resources
- [Hugging Face GGUF Models](https://huggingface.co/models?library=gguf)
- [GGUF Format Documentation](https://huggingface.co/docs/hub/en/gguf)
- [Awesome Mobile LLM](https://github.com/stevelaskaridis/awesome-mobile-llm)

### Guides
- [LLM Inference on Edge](https://huggingface.co/blog/llm-inference-on-edge)
- [Running LLMs on Android](https://towardsdatascience.com/a-weekend-ai-project-running-llama-and-gemma-ai-models-on-the-android-phone-47a261d257a7/)
- [Mobile AI Frameworks Comparison](https://booleaninc.com/blog/mobile-ai-frameworks-onnx-coreml-tensorflow-lite/)
