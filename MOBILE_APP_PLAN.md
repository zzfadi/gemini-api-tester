# On-Device AI Model Tester - Mobile App Planning Document

## Executive Summary

A cross-platform mobile application for Android and iOS that enables users to download, manage, and run on-device AI models locally. The app provides a unified interface for testing various model types (LLMs, vision, audio, embeddings) without requiring cloud connectivity.

---

## 1. Technology Stack Decision

### Recommended: Flutter + Native Bridges

**Why Flutter:**
- Superior on-device AI performance with AOT compilation
- Direct native code compilation (no JavaScript bridge overhead)
- First-party support for Google ML Kit and TensorFlow Lite
- Single codebase for iOS, Android (and future desktop/web)
- `flutter_llama` plugin provides ready-made llama.cpp integration
- GitHub: 170k stars vs React Native's 121k (2025 data)
- Better suited for graphics-heavy AI visualizations

**Alternative Considered:** React Native
- Larger JavaScript developer pool
- Better for web-connected AI services
- Would require native bridges for on-device ML (performance overhead)

---

## 2. On-Device ML Frameworks Integration

### Primary Framework Stack

| Component | Android | iOS | Cross-Platform |
|-----------|---------|-----|----------------|
| **LLM Inference** | llama.cpp (NDK) | llama.cpp | MLC LLM |
| **Traditional ML** | TensorFlow Lite | Core ML | ONNX Runtime |
| **Vision** | ML Kit | Vision Framework | TFLite |
| **Audio** | TFLite Audio | Core ML Audio | Whisper.cpp |

### Framework Integration Details

#### 1. llama.cpp Integration (Primary LLM Engine)
```
flutter_llama package
├── Supports GGUF model format
├── Works on Android, iOS, macOS
├── Quantization: Q4_K_M, Q5_0, Q8_0
└── Performance: 8-10 tok/s on Snapdragon 8 Gen 2
```

#### 2. MLC LLM (Alternative/Advanced)
```
MLC LLM Engine
├── Universal deployment engine
├── OpenAI-compatible API
├── Compiles model + runtime together
└── Pre-built packages for iOS App Store
```

#### 3. TensorFlow Lite / LiteRT
```
tflite_flutter package
├── Image classification
├── Object detection
├── Text embeddings
└── Custom model support
```

---

## 3. Supported Model Types & Sources

### Model Categories

| Category | Example Models | Format | Typical Size |
|----------|---------------|--------|--------------|
| **Small LLMs** | Phi-3-mini, Gemma-2B, TinyLlama | GGUF | 1-4 GB |
| **Medium LLMs** | Llama-3.2-3B, Gemma-7B | GGUF | 4-8 GB |
| **Vision LLMs** | SmolVLM2, LLaVA | GGUF | 2-6 GB |
| **Image Models** | MobileNet, EfficientNet | TFLite/CoreML | 10-50 MB |
| **Audio/Speech** | Whisper-tiny/small | GGUF/TFLite | 50-500 MB |
| **Embeddings** | all-MiniLM, BGE-small | GGUF/TFLite | 50-200 MB |

### Model Sources

1. **Hugging Face Hub** (Primary)
   - Direct GGUF model search and download
   - API: `huggingface_hub` library
   - Thousands of quantized models available

2. **Built-in Model Catalog**
   - Curated list of tested, mobile-optimized models
   - Pre-configured download URLs
   - Verified compatibility

3. **Custom Model Import**
   - Local file picker (GGUF, TFLite, ONNX)
   - URL-based download
   - Model conversion utilities

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
│                     State Management (Riverpod/Bloc)             │
├─────────────────────────────────────────────────────────────────┤
│                     Service Layer                                │
│  ┌──────────────┐ ┌──────────────┐ ┌──────────────────────┐     │
│  │ ModelManager │ │DownloadManager│ │   InferenceService   │     │
│  └──────────────┘ └──────────────┘ └──────────────────────┘     │
├─────────────────────────────────────────────────────────────────┤
│                     Platform Channels                            │
│  ┌──────────────────────┐  ┌──────────────────────────────┐     │
│  │    MethodChannel     │  │     EventChannel (streaming)  │     │
│  └──────────────────────┘  └──────────────────────────────┘     │
├─────────────────────────────────────────────────────────────────┤
│                     Native Layer                                 │
│  ┌─────────────────────────┐  ┌─────────────────────────────┐   │
│  │       Android (Kotlin)   │  │         iOS (Swift)          │   │
│  │  ┌─────────────────────┐ │  │  ┌─────────────────────────┐ │   │
│  │  │ llama.cpp (JNI/NDK) │ │  │  │  llama.cpp (C++ bridge) │ │   │
│  │  │ TensorFlow Lite     │ │  │  │  Core ML                │ │   │
│  │  │ ONNX Runtime        │ │  │  │  ONNX Runtime           │ │   │
│  │  └─────────────────────┘ │  │  └─────────────────────────┘ │   │
│  └─────────────────────────┘  └─────────────────────────────────┘   │
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
│   └── app/
│       └── src/main/
│           ├── kotlin/...
│           │   └── LlamaPlugin.kt
│           └── cpp/
│               └── llama_bridge.cpp
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
│  ⬇️ DOWNLOADS                                   │
│  Gemma-2B-Q4... ████████░░ 78%                 │
└─────────────────────────────────────────────────┘
```

**Features:**
- Browse curated model catalog
- Search Hugging Face for GGUF models
- Filter by: type, size, language, capabilities
- Download with progress tracking & resume
- Storage management (delete, move to SD card)
- Model metadata display (size, quantization, benchmarks)

### 5.2 LLM Chat Interface

```
┌─────────────────────────────────────────────────┐
│  🦙 Llama 3.2 1B                    [⚙️ Settings]│
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 🤖 Hello! I'm running locally on your   │   │
│  │    device. How can I help you today?    │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 👤 What can you help me with?           │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │ 🤖 I can help with:                     │   │
│  │ • Writing and editing text              │   │
│  │ • Answering questions                   │   │
│  │ • Summarizing content                   │   │
│  │ • Creative brainstorming                │   │
│  │ • And much more!                        │   │
│  │                                          │   │
│  │ ⚡ 12.3 tok/s • 256 tokens              │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
├─────────────────────────────────────────────────┤
│  [📷] Type a message...              [Send ➤]  │
└─────────────────────────────────────────────────┘
```

**Features:**
- Real-time token streaming
- Performance metrics (tokens/sec, latency)
- Conversation history (local SQLite)
- System prompt customization
- Generation parameters (temperature, top_p, etc.)
- Image input for vision LLMs
- Export/share conversations

### 5.3 Vision Testing

```
┌─────────────────────────────────────────────────┐
│  📷 VISION TESTING                              │
├─────────────────────────────────────────────────┤
│  [Image Classification] [Object Detection]      │
│  [Vision LLM]                                   │
├─────────────────────────────────────────────────┤
│                                                  │
│  ┌─────────────────────────────────────────┐   │
│  │                                          │   │
│  │         [Selected Image Here]            │   │
│  │                                          │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
│  📊 RESULTS                                     │
│  ┌─────────────────────────────────────────┐   │
│  │ 🐕 Dog (Golden Retriever)    95.2%      │   │
│  │ 🐕 Labrador                   3.1%      │   │
│  │ 🐕 Pet                        1.2%      │   │
│  │ ⏱️ Inference: 45ms                       │   │
│  └─────────────────────────────────────────┘   │
│                                                  │
├─────────────────────────────────────────────────┤
│  [📷 Camera] [🖼️ Gallery] [🔄 Run Again]        │
└─────────────────────────────────────────────────┘
```

**Features:**
- Camera capture & gallery selection
- Image classification (MobileNet, EfficientNet)
- Object detection with bounding boxes
- Vision-Language model support (describe images)
- Batch processing
- Performance benchmarking

### 5.4 Audio/Speech Testing

**Features:**
- Speech-to-text (Whisper models)
- Real-time transcription
- Audio file processing
- Language detection
- Performance metrics

### 5.5 Embeddings Testing

**Features:**
- Text embedding generation
- Semantic similarity calculation
- Batch embedding processing
- Vector visualization
- Export embeddings (JSON/CSV)

### 5.6 Benchmarking & Diagnostics

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
│  │ • Prompt eval: 156 tok/s                │   │
│  │ • Generation: 12.3 tok/s                │   │
│  │ • Memory: 1.8 GB                        │   │
│  │ • Load time: 2.3s                       │   │
│  └─────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────┐   │
│  │ Whisper Tiny                            │   │
│  │ • Real-time factor: 0.15x               │   │
│  │ • Memory: 120 MB                        │   │
│  └─────────────────────────────────────────┘   │
├─────────────────────────────────────────────────┤
│  [Run Full Benchmark] [Export Results]          │
└─────────────────────────────────────────────────┘
```

---

## 6. Technical Implementation Details

### 6.1 Model Download System

```dart
// download_manager.dart
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

    final response = await _dio.download(
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
}
```

### 6.2 Inference Service

```dart
// inference_service.dart
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
    });

    return _eventChannel.receiveBroadcastStream().map((token) => token as String);
  }
}
```

### 6.3 Hugging Face Integration

```dart
// huggingface_api.dart
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
        .where((f) => f['rfilename'].endsWith('.gguf'))
        .map((f) => ModelFile.fromJson(f))
        .toList();
  }
}
```

### 6.4 Native Bridge (Android - Kotlin)

```kotlin
// LlamaPlugin.kt
class LlamaPlugin : FlutterPlugin, MethodCallHandler, EventChannel.StreamHandler {
    private lateinit var llamaContext: Long
    private var eventSink: EventChannel.EventSink? = null

    override fun onMethodCall(call: MethodCall, result: Result) {
        when (call.method) {
            "loadModel" -> {
                val modelPath = call.argument<String>("path")!!
                val nCtx = call.argument<Int>("contextSize") ?: 2048

                llamaContext = LlamaCpp.loadModel(modelPath, nCtx)
                result.success(llamaContext != 0L)
            }
            "startGeneration" -> {
                val prompt = call.argument<String>("prompt")!!
                val config = GenerationConfig.fromMap(call.arguments as Map<*, *>)

                thread {
                    LlamaCpp.generate(llamaContext, prompt, config) { token ->
                        mainHandler.post {
                            eventSink?.success(token)
                        }
                    }
                    mainHandler.post {
                        eventSink?.endOfStream()
                    }
                }
                result.success(true)
            }
        }
    }
}
```

### 6.5 Native Bridge (iOS - Swift)

```swift
// LlamaPlugin.swift
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
            llamaContext = llama_load_model(modelPath, contextSize)
            result(llamaContext != nil)

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

### Local Database Schema (SQLite/Drift)

```sql
-- Models table
CREATE TABLE models (
    id TEXT PRIMARY KEY,
    name TEXT NOT NULL,
    type TEXT NOT NULL,  -- llm, vision, audio, embedding
    format TEXT NOT NULL,  -- gguf, tflite, coreml, onnx
    size_bytes INTEGER NOT NULL,
    local_path TEXT,
    huggingface_repo TEXT,
    quantization TEXT,
    metadata TEXT,  -- JSON
    downloaded_at INTEGER,
    last_used_at INTEGER
);

-- Conversations table
CREATE TABLE conversations (
    id TEXT PRIMARY KEY,
    model_id TEXT NOT NULL,
    title TEXT,
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
    created_at INTEGER NOT NULL,
    FOREIGN KEY (model_id) REFERENCES models(id)
);
```

---

## 8. Device Requirements

### Minimum Requirements

| Platform | Requirement |
|----------|-------------|
| **Android** | Android 7.0 (API 24)+ |
| **iOS** | iOS 14.0+ |
| **RAM** | 4 GB minimum, 8 GB recommended |
| **Storage** | 2 GB app + model storage |
| **CPU** | ARM64 (arm64-v8a / arm64) |

### Recommended for Best Performance

| Component | Recommendation |
|-----------|----------------|
| **RAM** | 8-12 GB |
| **SoC** | Snapdragon 8 Gen 1+ / Apple A15+ |
| **NPU** | Dedicated neural processing unit |
| **Storage** | 20+ GB free for models |

---

## 9. Dependencies (pubspec.yaml)

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
  flutter_llama: ^0.1.0  # llama.cpp binding
  tflite_flutter: ^0.10.4
  google_mlkit_commons: ^0.6.1
  google_mlkit_image_labeling: ^0.10.0
  google_mlkit_object_detection: ^0.11.0

  # Media
  camera: ^0.10.5+7
  image_picker: ^1.0.7
  audio_waveforms: ^1.0.5
  record: ^5.0.4

  # UI Components
  flutter_markdown: ^0.6.18+3
  shimmer: ^3.0.0
  flutter_animate: ^4.3.0

  # Utils
  path_provider: ^2.1.2
  permission_handler: ^11.1.0
  url_launcher: ^6.2.2
  share_plus: ^7.2.1
  uuid: ^4.2.2

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

### Phase 1: Foundation (Weeks 1-3)
- [ ] Project setup with Flutter
- [ ] Basic app architecture and navigation
- [ ] Model data models and local database
- [ ] Hugging Face API integration
- [ ] Download manager with resume support
- [ ] Basic model list UI

### Phase 2: LLM Integration (Weeks 4-6)
- [ ] llama.cpp native integration (Android)
- [ ] llama.cpp native integration (iOS)
- [ ] Flutter platform channels
- [ ] Chat UI with streaming
- [ ] Basic generation settings
- [ ] Conversation persistence

### Phase 3: Vision & Audio (Weeks 7-9)
- [ ] TensorFlow Lite integration
- [ ] Core ML integration (iOS)
- [ ] Image classification screen
- [ ] Object detection screen
- [ ] Whisper.cpp integration
- [ ] Speech-to-text screen

### Phase 4: Polish & Advanced (Weeks 10-12)
- [ ] Vision LLM support
- [ ] Embeddings testing
- [ ] Benchmarking system
- [ ] Settings and customization
- [ ] Performance optimization
- [ ] App store preparation

---

## 11. Key Challenges & Mitigations

| Challenge | Mitigation |
|-----------|------------|
| **Large model sizes** | Aggressive quantization (Q4), chunked downloads, SD card storage |
| **Memory constraints** | Context size limits, model unloading, memory monitoring |
| **Battery drain** | Background processing limits, efficient inference loops |
| **Platform differences** | Abstraction layers, platform-specific optimizations |
| **Model compatibility** | Curated model catalog, format validation, fallbacks |
| **App Store size limits** | Models downloaded separately, not bundled |

---

## 12. Security Considerations

- All inference runs locally (no data leaves device)
- No cloud API keys required for core functionality
- Optional Hugging Face token for private models
- Secure storage for any saved credentials
- Model file integrity verification (SHA256)

---

## 13. Future Enhancements

- **Model fine-tuning** on device (LoRA)
- **Federated learning** support
- **Multi-model workflows** (chaining models)
- **Desktop/Web versions** (Flutter multi-platform)
- **Model sharing** between users
- **Offline model catalog** caching
- **NPU acceleration** (Qualcomm AI Engine, Apple Neural Engine)

---

## References & Sources

### On-Device ML Frameworks
- [Mobile AI Frameworks: ONNX to CoreML & TensorFlow Lite](https://booleaninc.com/blog/mobile-ai-frameworks-onnx-coreml-tensorflow-lite/)
- [Edge AI: TensorFlow Lite vs ONNX Runtime](https://dzone.com/articles/edge-ai-tensorflow-lite-vs-onnx-runtime-vs-pytorch)
- [Running Large Transformers on Mobile](https://huggingface.co/blog/tugrulkaya/running-large-transformer-models-on-mobile)

### Cross-Platform Development
- [Flutter vs React Native 2025 AI Development](https://www.sidetool.co/post/flutter-vs-react-native-2025-ai-development-showdown/)
- [Building AI-Powered Apps with Flutter & React Native](https://200oksolutions.com/blog/building-cross-platform-ai-powered-apps-with-flutter-react-native/)

### Mobile LLM Resources
- [Awesome Mobile LLM (GitHub)](https://github.com/stevelaskaridis/awesome-mobile-llm)
- [LLMs in Mobile Apps: Phi-3, Gemma, Open Source](https://booleaninc.com/blog/llms-in-mobile-apps-phi-3-gemma-open-source/)
- [Running LLaMA and Gemma on Android](https://towardsdatascience.com/a-weekend-ai-project-running-llama-and-gemma-ai-models-on-the-android-phone-47a261d257a7/)

### llama.cpp & GGUF
- [llama.cpp GitHub](https://github.com/ggml-org/llama.cpp)
- [LLM Inference on Edge with React Native](https://huggingface.co/blog/llm-inference-on-edge)
- [flutter_llama Package](https://pub.dev/packages/flutter_llama)
- [GGUF on Hugging Face](https://huggingface.co/docs/hub/en/gguf)

### MLC LLM
- [MLC LLM Official Documentation](https://llm.mlc.ai/)
- [MLC LLM GitHub](https://github.com/mlc-ai/mlc-llm)
- [Android SDK Documentation](https://llm.mlc.ai/docs/deploy/android.html)
- [iOS Swift SDK Documentation](https://llm.mlc.ai/docs/deploy/ios.html)
