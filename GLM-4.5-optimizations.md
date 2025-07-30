<!DOCTYPE html><html lang="en"><head>
    <meta charset="UTF-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Optimizing GLM-4.5-Air: Advanced Model Compression Techniques</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com"/>
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin=""/>
    <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:ital,wght@0,400;0,500;0,600;0,700;1,400;1,500&amp;family=Inter:wght@300;400;500;600;700&amp;display=swap" rel="stylesheet"/>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css"/>
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    fontFamily: {
                        'serif': ['Playfair Display', 'serif'],
                        'sans': ['Inter', 'sans-serif']
                    },
                    colors: {
                        'navy': {
                            50: '#f0f4f8',
                            100: '#d9e2ec',
                            200: '#bcccdc',
                            300: '#9fb3c8',
                            400: '#829ab1',
                            500: '#627d98',
                            600: '#486581',
                            700: '#334e68',
                            800: '#243b53',
                            900: '#102a43',
                            950: '#0a1929'
                        },
                        'gold': {
                            50: '#fffbeb',
                            100: '#fef3c7',
                            200: '#fde68a',
                            300: '#fcd34d',
                            400: '#fbbf24',
                            500: '#f59e0b',
                            600: '#d97706',
                            700: '#b45309',
                            800: '#92400e',
                            900: '#78350f'
                        }
                    }
                }
            }
        }
    </script>
    <style>
        .hero-gradient {
            background: linear-gradient(135deg, rgba(16, 42, 67, 0.95) 0%, rgba(36, 59, 83, 0.9) 100%);
        }
        .toc-fixed {
            position: fixed;
            top: 0;
            left: 0;
            width: 280px;
            height: 100vh;
            background: rgba(255, 255, 255, 0.98);
            backdrop-filter: blur(10px);
            border-right: 1px solid #e5e7eb;
            z-index: 50;
            overflow-y: auto;
            padding: 2rem 1.5rem;
        }
        .main-content {
            margin-left: 280px;
            min-height: 100vh;
        }
        .toc-link {
            transition: all 0.3s ease;
            border-left: 2px solid transparent;
            padding-left: 1rem;
        }
        .toc-link:hover, .toc-link.active {
            border-left-color: #d97706;
            background-color: rgba(217, 119, 6, 0.05);
        }
        .section-spacing {
            scroll-margin-top: 2rem;
        }
        @media (max-width: 1024px) {
            .toc-fixed {
                transform: translateX(-100%);
                transition: transform 0.3s ease;
            }
            .toc-fixed.mobile-open {
                transform: translateX(0);
            }
            .main-content {
                margin-left: 0;
            }
        }
        @media (min-width: 1025px) {
            .toc-fixed {
                transform: translateX(0) !important;
            }
        }
        .code-block {
            background: #1e293b;
            border-radius: 8px;
            overflow-x: auto;
        }
        .highlight-box {
            background: linear-gradient(135deg, rgba(217, 119, 6, 0.05) 0%, rgba(217, 119, 6, 0.02) 100%);
            border-left: 4px solid #d97706;
        }
        .chart-container {
            position: relative;
            height: 400px;
            margin: 2rem 0;
        }
        .overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0,0,0,0.5);
            z-index: 40;
            display: none;
        }
        @media (max-width: 1024px) {
            .overlay.mobile-open {
                display: block;
            }
        }
    </style>
  </head>

  <body class="bg-gray-50 font-sans text-gray-900 leading-relaxed overflow-x-hidden">
    <!-- Fixed Table of Contents -->
    <nav class="toc-fixed" id="toc">
      <div class="mb-8">
        <h3 class="font-serif font-semibold text-navy-900 text-lg mb-4">Contents</h3>
        <ul class="space-y-2 text-sm">
          <li>
            <a href="#introduction" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Introduction to Model Compression</a>
          </li>
          <li>
            <a href="#llm-compressor" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Understanding LLM-Compressor</a>
          </li>
          <li>
            <a href="#glm-architecture" class="toc-link block py-2 text-gray-700 hover:text-navy-900">GLM-4.5-Air Architecture</a>
          </li>
          <li>
            <a href="#compression-recipe" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Constructing Compression Recipes</a>
          </li>
          <li>
            <a href="#implementation" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Implementation Examples</a>
          </li>
          <li>
            <a href="#quantization-scenarios" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Mixed-Precision Scenarios</a>
          </li>
          <li>
            <a href="#performance-analysis" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Performance Analysis</a>
          </li>
          <li>
            <a href="#best-practices" class="toc-link block py-2 text-gray-700 hover:text-navy-900">Best Practices &amp; Considerations</a>
          </li>
        </ul>
      </div>

      <div class="border-t pt-4">
        <h4 class="font-medium text-navy-900 mb-2">Key Metrics</h4>
        <div class="space-y-2 text-xs text-gray-600">
          <div class="flex justify-between">
            <span>Base Model Size:</span>
            <span class="font-medium">218.25 GB</span>
          </div>
          <div class="flex justify-between">
            <span>Target Size:</span>
            <span class="font-medium">27.28-109.13 GB</span>
          </div>
          <div class="flex justify-between">
            <span>Sparsity:</span>
            <span class="font-medium">2:4 (50%)</span>
          </div>
        </div>
      </div>
    </nav>

    <!-- Mobile TOC Toggle -->
    <button class="lg:hidden fixed top-4 left-4 z-50 bg-white p-2 rounded-lg shadow-lg" onclick="toggleMobileTOC()">
      <i class="fas fa-bars text-navy-900"></i>
    </button>

    <!-- Overlay for closing menu -->
    <div class="overlay" id="toc-overlay" onclick="closeMobileTOC()"></div>

    <!-- Main Content -->
    <main class="main-content">
      <!-- Hero Section -->
      <section class="relative min-h-[70vh] flex items-center">
        <div class="absolute inset-0">
          <img src="https://kimi-web-img.moonshot.cn/img/miro.medium.com/7f9c9e1949f376f218384ad219e2ab48b593cb88.jpeg" alt="Abstract neural network visualization" class="w-full h-full object-cover" size="wallpaper" aspect="wide" query="abstract neural network" referrerpolicy="no-referrer" data-modified="1" data-score="0.00"/>
          <div class="absolute inset-0 hero-gradient"></div>
        </div>

        <div class="relative z-10 max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="grid grid-cols-1 lg:grid-cols-12 gap-4 lg:gap-8 items-center">
            <!-- Main Title Block -->
            <div class="lg:col-span-8">
              <div class="bg-white/95 backdrop-blur-sm p-6 lg:p-8 rounded-lg shadow-xl">
                <h1 class="font-serif text-3xl sm:text-4xl lg:text-5xl font-semibold text-navy-900 mb-4 leading-tight">
                  Optimizing <em class="text-gold-600 italic">GLM-4.5-Air</em>
                </h1>
                <p class="text-lg sm:text-xl lg:text-2xl text-navy-700 mb-6 font-light">
                  Advanced Model Compression using LLM-Compressor
                </p>
                <div class="border-l-4 border-gold-500 pl-4 mb-6">
                  <p class="text-gray-700 leading-relaxed">
                    A comprehensive guide to applying 2:4 sparsity and mixed-precision quantization
                    to Mixture-of-Experts models for optimal performance and efficiency.
                  </p>
                </div>
              </div>
            </div>

            <!-- Key Highlights Grid -->
            <div class="lg:col-span-4 mt-6 lg:mt-0">
              <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                <div class="bg-white/90 backdrop-blur-sm p-4 sm:p-6 rounded-lg text-center">
                  <div class="text-2xl sm:text-3xl font-bold text-gold-600 mb-2">95%</div>
                  <div class="text-xs sm:text-sm text-gray-600">Size Reduction Potential</div>
                </div>
                <div class="bg-white/90 backdrop-blur-sm p-4 sm:p-6 rounded-lg text-center">
                  <div class="text-2xl sm:text-3xl font-bold text-gold-600 mb-2">2:4</div>
                  <div class="text-xs sm:text-sm text-gray-600">Structured Sparsity</div>
                </div>
                <div class="bg-white/90 backdrop-blur-sm p-4 sm:p-6 rounded-lg text-center">
                  <div class="text-2xl sm:text-3xl font-bold text-gold-600 mb-2">INT4</div>
                  <div class="text-xs sm:text-sm text-gray-600">Quantization Precision</div>
                </div>
                <div class="bg-white/90 backdrop-blur-sm p-4 sm:p-6 rounded-lg text-center">
                  <div class="text-2xl sm:text-3xl font-bold text-gold-600 mb-2">106B</div>
                  <div class="text-xs sm:text-sm text-gray-600">Total Parameters</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Introduction Section -->
      <section id="introduction" class="section-spacing py-16 bg-white">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="grid grid-cols-1 lg:grid-cols-3 gap-12">
            <div class="lg:col-span-2">
              <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-6">Introduction to Model Compression</h2>
              <p class="text-lg text-gray-700 mb-6 leading-relaxed">
                Large Language Models have revolutionized AI capabilities, but their massive size presents significant deployment challenges.
                The GLM-4.5-Air model, with 106 billion total parameters, requires sophisticated compression techniques to become practically deployable.
              </p>

              <div class="highlight-box p-6 rounded-lg mb-8">
                <h3 class="font-semibold text-navy-900 mb-3">Compression Objectives</h3>
                <ul class="space-y-2 text-gray-700">
                  <li class="flex items-start">
                    <i class="fas fa-check-circle text-gold-600 mt-1 mr-3"></i>
                    <span><strong>Model Size Reduction:</strong> From 218.25GB to under 55GB</span>
                  </li>
                  <li class="flex items-start">
                    <i class="fas fa-check-circle text-gold-600 mt-1 mr-3"></i>
                    <span><strong>Inference Speedup:</strong> Leveraging hardware-optimized sparsity patterns</span>
                  </li>
                  <li class="flex items-start">
                    <i class="fas fa-check-circle text-gold-600 mt-1 mr-3"></i>
                    <span><strong>Memory Efficiency:</strong> Reduced GPU memory requirements</span>
                  </li>
                  <li class="flex items-start">
                    <i class="fas fa-check-circle text-gold-600 mt-1 mr-3"></i>
                    <span><strong>Accuracy Preservation:</strong> Maintaining model performance</span>
                  </li>
                </ul>
              </div>
            </div>

            <div class="space-y-6">
              <div class="bg-navy-50 p-6 rounded-lg">
                <h4 class="font-semibold text-navy-900 mb-3">Compression Techniques</h4>
                <div class="space-y-4">
                  <div>
                    <h5 class="font-medium text-navy-800 mb-2">2:4 Structured Sparsity</h5>
                    <p class="text-sm text-gray-600">For every 4 weights, 2 are set to zero, enabling hardware acceleration on modern GPUs.</p>
                  </div>
                  <div>
                    <h5 class="font-medium text-navy-800 mb-2">Mixed-Precision Quantization</h5>
                    <p class="text-sm text-gray-600">Combining INT4, INT8, and FP16 precisions for optimal performance.</p>
                  </div>
                  <div>
                    <h5 class="font-medium text-navy-800 mb-2">Expert-Targeted Compression</h5>
                    <p class="text-sm text-gray-600">Applying different strategies to expert vs. non-expert layers.</p>
                  </div>
                </div>
              </div>

              <div class="bg-gold-50 p-6 rounded-lg">
                <h4 class="font-semibold text-navy-900 mb-3">Key Benefits</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li>• 4x reduction in model size</li>
                  <li>• 2x theoretical speedup with 2:4 sparsity</li>
                  <li>• Maintained accuracy for critical components</li>
                  <li>• Hardware-optimized deployment</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- LLM-Compressor Section -->
      <section id="llm-compressor" class="section-spacing py-16 bg-gray-50">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8 text-center">Understanding LLM-Compressor</h2>

          <div class="grid grid-cols-1 lg:grid-cols-2 gap-12 mb-12">
            <div>
              <h3 class="font-serif text-2xl font-medium text-navy-900 mb-4">Core Features</h3>
              <p class="text-gray-700 mb-6">
                LLM-Compressor is a specialized Python library designed for optimizing Large Language Models for deployment,
                with seamless integration with vLLM and Hugging Face ecosystems.
              </p>

              <div class="space-y-4">
                <div class="flex items-start">
                  <div class="bg-gold-100 p-2 rounded-full mr-4 mt-1">
                    <i class="fas fa-compress text-gold-600"></i>
                  </div>
                  <div>
                    <h4 class="font-medium text-navy-900">Comprehensive Compression Algorithms</h4>
                    <p class="text-sm text-gray-600">Supports AWQ, GPTQ, SmoothQuant, and RTN quantization methods</p>
                  </div>
                </div>

                <div class="flex items-start">
                  <div class="bg-gold-100 p-2 rounded-full mr-4 mt-1">
                    <i class="fas fa-project-diagram text-gold-600"></i>
                  </div>
                  <div>
                    <h4 class="font-medium text-navy-900">Advanced Sparsity Support</h4>
                    <p class="text-sm text-gray-600">SparseGPT for 2:4 structured sparsity and unstructured pruning</p>
                  </div>
                </div>

                <div class="flex items-start">
                  <div class="bg-gold-100 p-2 rounded-full mr-4 mt-1">
                    <i class="fas fa-network-wired text-gold-600"></i>
                  </div>
                  <div>
                    <h4 class="font-medium text-navy-900">MoE Optimization</h4>
                    <p class="text-sm text-gray-600">Enhanced support for Mixture-of-Experts model architectures</p>
                  </div>
                </div>
              </div>
            </div>

            <div>
              <img src="https://kimi-web-img.moonshot.cn/img/png.pngtree.com/4b5e0daeb819e0f64b7511840d345239a9ad1350.jpg" alt="Abstract neural network background" class="w-full rounded-lg shadow-lg" size="medium" aspect="wide" query="neural network compression" referrerpolicy="no-referrer" data-modified="1" data-score="0.00"/>
            </div>
          </div>

          <div class="bg-white p-8 rounded-lg shadow-sm">
            <h3 class="font-serif text-xl font-medium text-navy-900 mb-4">Supported Algorithms</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
              <div class="text-center p-4 border border-gray-200 rounded-lg">
                <div class="text-2xl font-bold text-navy-900 mb-2">AWQ</div>
                <p class="text-sm text-gray-600">Activation-aware Weight Quantization</p>
              </div>
              <div class="text-center p-4 border border-gray-200 rounded-lg">
                <div class="text-2xl font-bold text-navy-900 mb-2">GPTQ</div>
                <p class="text-sm text-gray-600">Generative PTQ with gradient-based optimization</p>
              </div>
              <div class="text-center p-4 border border-gray-200 rounded-lg">
                <div class="text-2xl font-bold text-navy-900 mb-2">SmoothQuant</div>
                <p class="text-sm text-gray-600">Weight-and-activation quantization</p>
              </div>
              <div class="text-center p-4 border border-gray-200 rounded-lg">
                <div class="text-2xl font-bold text-navy-900 mb-2">RTN</div>
                <p class="text-sm text-gray-600">Round-to-Nearest baseline quantization</p>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- GLM Architecture Section -->
      <section id="glm-architecture" class="section-spacing py-16 bg-white">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8">GLM-4.5-Air Architecture</h2>

          <div class="grid grid-cols-1 lg:grid-cols-3 gap-8 mb-12">
            <div class="lg:col-span-2">
              <div class="bg-navy-50 p-6 rounded-lg mb-6">
                <h3 class="font-serif text-xl font-medium text-navy-900 mb-4">Mixture-of-Experts Structure</h3>
                <p class="text-gray-700 mb-4">
                  GLM-4.5-Air employs a sophisticated MoE architecture with 106 billion total parameters,
                  of which only 12 billion are active during inference - a key feature that enables selective compression strategies.
                </p>

                <div class="highlight-box p-4 rounded-lg">
                  <h4 class="font-semibold text-navy-900 mb-2">Expert Layer Naming Convention</h4>
                  <code class="text-sm bg-gray-100 px-2 py-1 rounded">model.layers.*.mlp.experts.*.(down_proj|gate_proj|up_proj)</code>
                  <p class="text-sm text-gray-600 mt-2">
                    This consistent pattern enables precise targeting of expert layers for compression while excluding critical components.
                  </p>
                </div>
              </div>

              <div class="space-y-4">
                <h4 class="font-semibold text-navy-900">Key Components</h4>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                  <div class="border-l-4 border-gold-500 pl-4">
                    <h5 class="font-medium text-navy-800">Router/Gate Network</h5>
                    <p class="text-sm text-gray-600">Responsible for expert selection - kept in FP16 for accuracy</p>
                  </div>
                  <div class="border-l-4 border-gold-500 pl-4">
                    <h5 class="font-medium text-navy-800">Expert Layers</h5>
                    <p class="text-sm text-gray-600">Specialized MLP blocks - primary targets for compression</p>
                  </div>
                  <div class="border-l-4 border-gold-500 pl-4">
                    <h5 class="font-medium text-navy-800">Language Model Head</h5>
                    <p class="text-sm text-gray-600">Final output layer - maintained in high precision</p>
                  </div>
                  <div class="border-l-4 border-gold-500 pl-4">
                    <h5 class="font-medium text-navy-800">Shared Parameters</h5>
                    <p class="text-sm text-gray-600">Attention layers and embeddings - moderate compression</p>
                  </div>
                </div>
              </div>
            </div>

            <div>
              <div class="bg-white border border-gray-200 rounded-lg p-6">
                <h4 class="font-semibold text-navy-900 mb-4">Model Specifications</h4>
                <div class="space-y-3 text-sm">
                  <div class="flex justify-between">
                    <span class="text-gray-600">Total Parameters:</span>
                    <span class="font-medium">106B</span>
                  </div>
                  <div class="flex justify-between">
                    <span class="text-gray-600">Active Parameters:</span>
                    <span class="font-medium">12B</span>
                  </div>
                  <div class="flex justify-between">
                    <span class="text-gray-600">Base FP16 Size:</span>
                    <span class="font-medium">218.25 GB</span>
                  </div>
                  <div class="flex justify-between">
                    <span class="text-gray-600">Expert Fraction:</span>
                    <span class="font-medium">~30%</span>
                  </div>
                  <div class="flex justify-between">
                    <span class="text-gray-600">Context Length:</span>
                    <span class="font-medium">128k tokens</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Compression Recipe Section -->
      <section id="compression-recipe" class="section-spacing py-16 bg-gray-50">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8">Constructing Compression Recipes</h2>

          <div class="mb-12">
            <h3 class="font-serif text-2xl font-medium text-navy-900 mb-6">Core Components</h3>
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
              <div class="bg-white p-6 rounded-lg shadow-sm">
                <h4 class="font-semibold text-navy-900 mb-4 flex items-center">
                  <i class="fas fa-cut text-gold-600 mr-3"></i>
                  SparseGPTModifier
                </h4>
                <p class="text-gray-700 mb-4">Applies structured 2:4 sparsity to targeted layers using the SparseGPT algorithm.</p>

                <div class="code-block p-4 text-sm text-gray-300 rounded">
                  <pre>SparseGPTModifier(
    targets=[&#34;re:.*\.mlp\.experts\..*&#34;],
    sparsity=0.50,
    mask_structure=&#34;2:4&#34;,
    post_prune_method=&#34;reweight&#34;,
    ignore=[&#34;lm_head&#34;, &#34;router&#34;, &#34;gate_proj&#34;]
)</pre>
                </div>
              </div>

              <div class="bg-white p-6 rounded-lg shadow-sm">
                <h4 class="font-semibold text-navy-900 mb-4 flex items-center">
                  <i class="fas fa-compress-arrows-alt text-gold-600 mr-3"></i>
                  AWQModifier
                </h4>
                <p class="text-gray-700 mb-4">Configures activation-aware weight quantization with mixed precision support.</p>

                <div class="code-block p-4 text-sm text-gray-300 rounded">
                  <pre>AWQModifier(
    config_groups={
        &#34;group_0&#34;: {
            &#34;targets&#34;: [&#34;re:.*\.mlp\.experts\..*&#34;],
            &#34;weights&#34;: {&#34;num_bits&#34;: 4, ...}
        }
    },
    calib_data=calib_loader
)</pre>
                </div>
              </div>
            </div>
          </div>

          <div class="bg-white p-8 rounded-lg shadow-sm">
            <h3 class="font-serif text-xl font-medium text-navy-900 mb-6">Configuration Parameters</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              <div>
                <h4 class="font-medium text-navy-900 mb-3">Targeting Strategy</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li><strong>Regex Patterns:</strong> Precise layer selection</li>
                  <li><strong>Component Types:</strong> Expert vs. shared layers</li>
                  <li><strong>Ignore Lists:</strong> Protect sensitive components</li>
                </ul>
              </div>
              <div>
                <h4 class="font-medium text-navy-900 mb-3">Quantization Settings</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li><strong>Bit-width:</strong> INT4, INT8, FP8 options</li>
                  <li><strong>Symmetry:</strong> Symmetric vs. asymmetric</li>
                  <li><strong>Group Size:</strong> 128, 64, or per-tensor</li>
                </ul>
              </div>
              <div>
                <h4 class="font-medium text-navy-900 mb-3">Sparsity Configuration</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li><strong>Ratio:</strong> 50% for 2:4 pattern</li>
                  <li><strong>Structure:</strong> 2:4 semi-structured</li>
                  <li><strong>Reweighting:</strong> Post-prune adjustment</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Implementation Examples -->
      <section id="implementation" class="section-spacing py-16 bg-white">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8">Implementation Examples</h2>

          <div class="space-y-8">
            <!-- Example 1: Basic Sparsity + Quantization -->
            <div class="bg-gray-50 p-8 rounded-lg">
              <h3 class="font-serif text-xl font-medium text-navy-900 mb-4">Example 1: INT8 Weight &amp; Activation Quantization with 2:4 Sparsity</h3>
              <p class="text-gray-700 mb-6">
                This configuration applies 2:4 structured sparsity to all Linear layers (50% sparsity) and 25% sparsity to expert weights specifically,
                followed by 8-bit weight and activation quantization.
              </p>

              <div class="code-block p-6 rounded-lg mb-4">
                <pre class="text-sm text-gray-300 leading-relaxed overflow-x-auto"><code>from llmcompressor import oneshot
from llmcompressor.modifiers.pruning import SparseGPTModifier
from llmcompressor.modifiers.quantization import AWQModifier

recipe = [
    # 2:4 structured sparsity on all Linear weights
    SparseGPTModifier(
        targets=[&#34;Linear&#34;],
        sparsity=0.50,
        mask_structure=&#34;2:4&#34;,
        post_prune_method=&#34;reweight&#34;,
        ignore=[&#34;lm_head&#34;, &#34;router&#34;, &#34;gate_proj&#34;],
    ),
    
    # 2:4 structured sparsity on expert weights only (25%)
    SparseGPTModifier(
        targets=[&#34;expert&#34;],
        sparsity=0.25,
        mask_structure=&#34;2:4&#34;,
        post_prune_method=&#34;reweight&#34;,
        ignore=[&#34;lm_head&#34;, &#34;router&#34;, &#34;gate_proj&#34;],
    ),
    
    # 8-bit weight and activation quantization
    AWQModifier(
        config_groups={
            &#34;group_0&#34;: {
                &#34;targets&#34;: [&#34;Linear&#34;],
                &#34;weights&#34;: {
                    &#34;num_bits&#34;: 8,
                    &#34;type&#34;: &#34;int&#34;,
                    &#34;symmetric&#34;: True,
                    &#34;group_size&#34;: 128,
                },
                &#34;activations&#34;: {
                    &#34;num_bits&#34;: 8,
                    &#34;type&#34;: &#34;int&#34;,
                },
            }
        },
        calib_data=calib_loader,
        ignore=[&#34;lm_head&#34;, &#34;router&#34;, &#34;gate_proj&#34;],
    ),
]

oneshot(model=model, recipe=recipe)</code></pre>
              </div>

              <div class="bg-white p-4 rounded-lg border-l-4 border-gold-500">
                <p class="text-sm text-gray-700">
                  <strong>Expected Outcome:</strong> This configuration targets a model size of approximately 70.93GB with 65% effective density,
                  maintaining high precision for the router and language modeling head while aggressively compressing expert layers.
                </p>
              </div>
            </div>

            <!-- Example 2: Expert-Only Compression -->
            <div class="bg-gray-50 p-8 rounded-lg">
              <h3 class="font-serif text-xl font-medium text-navy-900 mb-4">Example 2: Expert-Only Weight/Activation Quantization with Sparsity</h3>
              <p class="text-gray-700 mb-6">
                This recipe specifically targets expert layers for both 2:4 sparsity and 4-bit weight quantization with 8-bit activations,
                while keeping all other components in their original precision.
              </p>

              <div class="code-block p-6 rounded-lg mb-4">
                <pre class="text-sm text-gray-300 leading-relaxed overflow-x-auto"><code>recipe = [
    # 2:4 structured sparsity on expert weights only
    SparseGPTModifier(
        targets=[&#34;re:.*\.mlp\.experts\..*&#34;],  # Regex for expert layers
        sparsity=0.50,
        mask_structure=&#34;2:4&#34;,
        post_prune_method=&#34;reweight&#34;,
        ignore=[&#34;lm_head&#34;, &#34;router&#34;, &#34;gate_proj&#34;],
    ),
    
    # 4-bit weight quantization with activation quantization on expert weights only
    AWQModifier(
        config_groups={
            &#34;group_0&#34;: {
                &#34;targets&#34;: [&#34;re:.*\.mlp\.experts\..*&#34;],
                &#34;weights&#34;: {
                    &#34;num_bits&#34;: 4,
                    &#34;type&#34;: &#34;int&#34;,
                    &#34;symmetric&#34;: True,
                    &#34;group_size&#34;: 128,
                },
                &#34;activations&#34;: {
                    &#34;num_bits&#34;: 8,
                    &#34;type&#34;: &#34;int&#34;,
                },
            }
        },
        calib_data=calib_loader,
        ignore=[&#34;lm_head&#34;, &#34;router&#34;, &#34;gate_proj&#34;],
    ),
]</code></pre>
              </div>

              <div class="bg-white p-4 rounded-lg border-l-4 border-gold-500">
                <p class="text-sm text-gray-700">
                  <strong>Targeted Approach:</strong> By focusing compression exclusively on expert layers (approximately 30% of total parameters),
                  this method achieves significant size reduction while minimizing impact on model accuracy.
                </p>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Mixed-Precision Scenarios -->
      <section id="quantization-scenarios" class="section-spacing py-16 bg-navy-50">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8">Mixed-Precision Scenarios</h2>

          <p class="text-lg text-gray-700 mb-8">
            The following table presents various quantization and sparsity combinations for GLM-4.5-Air,
            focusing on mixed-precision approaches where the router and lm_head remain in FP16 while
            applying different compression strategies to model and expert weights.
          </p>

          <div class="bg-white rounded-lg shadow-sm overflow-hidden">
            <div class="overflow-x-auto">
              <table class="w-full">
                <thead class="bg-navy-900 text-white">
                  <tr>
                    <th class="px-6 py-4 text-left text-sm font-medium">Router/lm_head</th>
                    <th class="px-6 py-4 text-left text-sm font-medium">Model Weights</th>
                    <th class="px-6 py-4 text-left text-sm font-medium">Expert Weights</th>
                    <th class="px-6 py-4 text-left text-sm font-medium">Model Sparsity</th>
                    <th class="px-6 py-4 text-left text-sm font-medium">Expert Sparsity</th>
                    <th class="px-6 py-4 text-left text-sm font-medium">Size (GB)</th>
                    <th class="px-6 py-4 text-left text-sm font-medium">Notes</th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-200">
                  <tr class="hover:bg-gray-50">
                    <td class="px-6 py-4 text-sm font-medium text-navy-900">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT4-W4A16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT4-W4A16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm font-medium text-gold-600">54.56</td>
                    <td class="px-6 py-4 text-sm text-gray-600">All quantized except router/lm_head</td>
                  </tr>
                  <tr class="hover:bg-gray-50">
                    <td class="px-6 py-4 text-sm font-medium text-navy-900">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT8-W8A8</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT8-W8A8</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm font-medium text-gold-600">73.12</td>
                    <td class="px-6 py-4 text-sm text-gray-600">All quantized except router/lm_head</td>
                  </tr>
                  <tr class="hover:bg-gray-50">
                    <td class="px-6 py-4 text-sm font-medium text-navy-900">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT4-W4A16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm text-gray-700">None</td>
                    <td class="px-6 py-4 text-sm font-medium text-gold-600">92.76</td>
                    <td class="px-6 py-4 text-sm text-gray-600">Only experts in FP16</td>
                  </tr>
                  <tr class="hover:bg-gray-50">
                    <td class="px-6 py-4 text-sm font-medium text-navy-900">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT8-W8A8</td>
                    <td class="px-6 py-4 text-sm text-gray-700">INT4-W4A16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 25%</td>
                    <td class="px-6 py-4 text-sm font-medium text-gold-600">84.57</td>
                    <td class="px-6 py-4 text-sm text-gray-600">Mixed expert quantization</td>
                  </tr>
                  <tr class="hover:bg-gray-50 bg-gold-50">
                    <td class="px-6 py-4 text-sm font-medium text-navy-900">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">FP16</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm text-gray-700">2:4 50%</td>
                    <td class="px-6 py-4 text-sm font-medium text-gold-600">109.13</td>
                    <td class="px-6 py-4 text-sm text-gray-600">All FP16 except router/lm_head</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div class="mt-8 bg-white p-6 rounded-lg border-l-4 border-gold-500">
            <h3 class="font-semibold text-navy-900 mb-3">Key Insights</h3>
            <ul class="space-y-2 text-gray-700">
              <li>• <strong>Most Aggressive Compression:</strong> INT4 with 2:4 sparsity on both model and experts achieves 75% size reduction</li>
              <li>• <strong>Balanced Approach:</strong> INT8 quantization with 2:4 sparsity offers good trade-off between compression and accuracy</li>
              <li>• <strong>Expert-Specific Strategy:</strong> Keeping experts in FP16 while compressing other components provides moderate reduction</li>
              <li>• <strong>Sparsity Impact:</strong> 2:4 sparsity alone on FP16 weights achieves 50% size reduction</li>
            </ul>
          </div>
        </div>
      </section>

      <!-- Performance Analysis -->
      <section id="performance-analysis" class="section-spacing py-16 bg-white">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8">Performance Analysis</h2>

          <div class="grid grid-cols-1 lg:grid-cols-2 gap-12">
            <div>
              <h3 class="font-serif text-xl font-medium text-navy-900 mb-4">Compression Impact</h3>
              <div class="highlight-box p-6 rounded-lg mb-6">
                <h4 class="font-semibold text-navy-900 mb-3">Theoretical Speedup Factors</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li><strong>2:4 Sparsity:</strong> Up to 2x speedup on supported hardware (Ampere+ GPUs)</li>
                  <li><strong>INT8 Quantization:</strong> 2x memory bandwidth improvement</li>
                  <li><strong>INT4 Quantization:</strong> 4x memory bandwidth improvement</li>
                  <li><strong>Combined Approaches:</strong> Multiplicative benefits when hardware supports both</li>
                </ul>
              </div>

              <div class="bg-gray-50 p-6 rounded-lg">
                <h4 class="font-semibold text-navy-900 mb-3">Memory Footprint Analysis</h4>
                <div class="space-y-3 text-sm">
                  <div class="flex justify-between items-center">
                    <span class="text-gray-600">Original FP16 Model:</span>
                    <span class="font-medium">218.25 GB</span>
                  </div>
                  <div class="flex justify-between items-center">
                    <span class="text-gray-600">INT8 + 2:4 Sparsity:</span>
                    <span class="font-medium text-gold-600">54.56 GB</span>
                  </div>
                  <div class="flex justify-between items-center">
                    <span class="text-gray-600">INT4 + 2:4 Sparsity:</span>
                    <span class="font-medium text-gold-600">27.28 GB</span>
                  </div>
                  <div class="flex justify-between items-center">
                    <span class="text-gray-600">Memory Reduction:</span>
                    <span class="font-medium text-green-600">75-88%</span>
                  </div>
                </div>
              </div>
            </div>

            <div>
              <img src="https://kimi-web-img.moonshot.cn/img/eu-images.contentstack.com/8384b71b7c45f1ecaf95f63b2fe72f89be5b105e.jpg" alt="GPU hardware optimized for AI computations" class="w-full rounded-lg shadow-lg mb-6" size="medium" aspect="wide" style="photo" query="GPU AI computing" referrerpolicy="no-referrer" data-modified="1" data-score="0.00"/>

              <div class="bg-navy-50 p-6 rounded-lg">
                <h4 class="font-semibold text-navy-900 mb-3">Hardware Considerations</h4>
                <p class="text-sm text-gray-700 mb-3">
                  Modern GPU architectures provide specialized support for compressed models:
                </p>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li>• <strong>NVIDIA Ampere+:</strong> Native 2:4 sparsity acceleration</li>
                  <li>• <strong>Tensor Cores:</strong> Optimized for INT8/INT4 computations</li>
                  <li>• <strong>Memory Bandwidth:</strong> Reduced data movement with compression</li>
                  <li>• <strong>vLLM Integration:</strong> Automatic kernel selection for compressed models</li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Best Practices -->
      <section id="best-practices" class="section-spacing py-16 bg-gray-50">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <h2 class="font-serif text-3xl font-semibold text-navy-900 mb-8">Best Practices &amp; Considerations</h2>

          <div class="grid grid-cols-1 lg:grid-cols-2 gap-12">
            <div>
              <h3 class="font-serif text-xl font-medium text-navy-900 mb-6">Implementation Guidelines</h3>

              <div class="space-y-6">
                <div class="bg-white p-6 rounded-lg border-l-4 border-gold-500">
                  <h4 class="font-semibold text-navy-900 mb-3 flex items-center">
                    <i class="fas fa-layer-group text-gold-600 mr-3"></i>
                    Order of Operations
                  </h4>
                  <p class="text-gray-700 text-sm mb-3">
                    Always apply sparsity before quantization. This allows the model to adapt to the pruned structure
                    before quantization parameters are calculated.
                  </p>
                  <div class="bg-gray-50 p-3 rounded text-xs font-mono">
                    Sparsity → [Fine-tuning] → Quantization → Deployment
                  </div>
                </div>

                <div class="bg-white p-6 rounded-lg border-l-4 border-gold-500">
                  <h4 class="font-semibold text-navy-900 mb-3 flex items-center">
                    <i class="fas fa-shield-alt text-gold-600 mr-3"></i>
                    Protecting Critical Components
                  </h4>
                  <p class="text-gray-700 text-sm mb-3">
                    Always exclude sensitive layers from aggressive compression:
                  </p>
                  <ul class="text-sm text-gray-700 space-y-1">
                    <li>• <strong>lm_head:</strong> Final output layer affecting generation quality</li>
                    <li>• <strong>router/gate:</strong> Expert selection mechanism</li>
                    <li>• <strong>gate_proj:</strong> Gating network components</li>
                  </ul>
                </div>

                <div class="bg-white p-6 rounded-lg border-l-4 border-gold-500">
                  <h4 class="font-semibold text-navy-900 mb-3 flex items-center">
                    <i class="fas fa-database text-gold-600 mr-3"></i>
                    Calibration Data Quality
                  </h4>
                  <p class="text-gray-700 text-sm">
                    Use representative calibration data (128-512 samples) that reflects your target application domain.
                    Quality calibration is crucial for maintaining accuracy in quantized models.
                  </p>
                </div>
              </div>
            </div>

            <div>
              <h3 class="font-serif text-xl font-medium text-navy-900 mb-6">Verification &amp; Validation</h3>

              <div class="space-y-6">
                <div class="bg-white p-6 rounded-lg">
                  <h4 class="font-semibold text-navy-900 mb-4">Accuracy Evaluation</h4>
                  <p class="text-gray-700 text-sm mb-4">
                    Thoroughly evaluate compressed models using multiple metrics:
                  </p>
                  <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div class="bg-gray-50 p-4 rounded">
                      <h5 class="font-medium text-navy-800 mb-2">Quantitative Metrics</h5>
                      <ul class="text-xs text-gray-600 space-y-1">
                        <li>• Perplexity on validation set</li>
                        <li>• Task-specific benchmarks</li>
                        <li>• Speed/latency measurements</li>
                      </ul>
                    </div>
                    <div class="bg-gray-50 p-4 rounded">
                      <h5 class="font-medium text-navy-800 mb-2">Qualitative Assessment</h5>
                      <ul class="text-xs text-gray-600 space-y-1">
                        <li>• Generation quality analysis</li>
                        <li>• Error pattern examination</li>
                        <li>• Domain-specific evaluation</li>
                      </ul>
                    </div>
                  </div>
                </div>

                <div class="bg-white p-6 rounded-lg">
                  <h4 class="font-semibold text-navy-900 mb-4">Technical Verification</h4>
                  <p class="text-gray-700 text-sm mb-4">
                    Verify that compression was applied correctly:
                  </p>
                  <ul class="space-y-2 text-sm text-gray-700">
                    <li class="flex items-start">
                      <i class="fas fa-check text-green-600 mt-1 mr-3 text-xs"></i>
                      <span>Check sparsity ratios in targeted layers (should be ~50% for 2:4)</span>
                    </li>
                    <li class="flex items-start">
                      <i class="fas fa-check text-green-600 mt-1 mr-3 text-xs"></i>
                      <span>Verify quantization precision in weight distributions</span>
                    </li>
                    <li class="flex items-start">
                      <i class="fas fa-check text-green-600 mt-1 mr-3 text-xs"></i>
                      <span>Confirm excluded layers remain in FP16 precision</span>
                    </li>
                    <li class="flex items-start">
                      <i class="fas fa-check text-green-600 mt-1 mr-3 text-xs"></i>
                      <span>Validate vLLM compatibility through deployment testing</span>
                    </li>
                  </ul>
                </div>

                <div class="bg-navy-900 text-white p-6 rounded-lg">
                  <h4 class="font-semibold mb-3 flex items-center">
                    <i class="fas fa-lightbulb text-gold-400 mr-3"></i>
                    Pro Tips
                  </h4>
                  <ul class="space-y-2 text-sm">
                    <li>• Start with less aggressive compression and gradually increase</li>
                    <li>• Use regex patterns for precise layer targeting</li>
                    <li>• Consider recovery fine-tuning after sparsity application</li>
                    <li>• Test multiple calibration datasets for optimal results</li>
                    <li>• Monitor for outlier channels that may need special handling</li>
                  </ul>
                </div>
              </div>
            </div>
          </div>

          <div class="mt-12 bg-white p-8 rounded-lg shadow-sm">
            <h3 class="font-serif text-xl font-medium text-navy-900 mb-6">Final Implementation Checklist</h3>
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              <div>
                <h4 class="font-medium text-navy-900 mb-3">Preparation</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Identify expert layer naming patterns</span>
                  </li>
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Prepare representative calibration data</span>
                  </li>
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Verify model architecture understanding</span>
                  </li>
                </ul>
              </div>
              <div>
                <h4 class="font-medium text-navy-900 mb-3">Configuration</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Define compression targets and excludes</span>
                  </li>
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Set appropriate quantization parameters</span>
                  </li>
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Configure sparsity patterns and ratios</span>
                  </li>
                </ul>
              </div>
              <div>
                <h4 class="font-medium text-navy-900 mb-3">Validation</h4>
                <ul class="space-y-2 text-sm text-gray-700">
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Verify compression application</span>
                  </li>
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Evaluate model accuracy</span>
                  </li>
                  <li class="flex items-center">
                    <input type="checkbox" class="mr-2 text-gold-600"/>
                    <span>Test deployment with vLLM</span>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- Footer -->
      <footer class="bg-navy-900 text-white py-12">
        <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
          <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            <div>
              <h3 class="font-serif text-xl font-semibold mb-4">Key Resources</h3>
              <ul class="space-y-2 text-sm text-gray-300">
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">LLM-Compressor Documentation</a>
                </li>
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">vLLM Deployment Guide</a>
                </li>
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">GLM-4.5-Air Model Card</a>
                </li>
              </ul>
            </div>
            <div>
              <h3 class="font-serif text-xl font-semibold mb-4">Technical References</h3>
              <ul class="space-y-2 text-sm text-gray-300">
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">SparseGPT Research Paper</a>
                </li>
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">AWQ Implementation Details</a>
                </li>
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">MoE Architecture Guide</a>
                </li>
              </ul>
            </div>
            <div>
              <h3 class="font-serif text-xl font-semibold mb-4">Community</h3>
              <ul class="space-y-2 text-sm text-gray-300">
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">GitHub Repository</a>
                </li>
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">Discord Community</a>
                </li>
                <li>
                  <a href="#" class="hover:text-gold-400 transition-colors">Issue Tracker</a>
                </li>
              </ul>
            </div>
          </div>
          <div class="border-t border-navy-700 mt-8 pt-8 text-center text-sm text-gray-400">
            <p>This guide provides comprehensive instructions for optimizing GLM-4.5-Air using LLM-Compressor.
              Always validate compressed models before deployment in production environments.</p>
          </div>
        </div>
      </footer>
    </main>

    <script>
        // Mobile TOC Toggle
        function toggleMobileTOC() {
            const toc = document.getElementById('toc');
            const overlay = document.getElementById('toc-overlay');
            toc.classList.toggle('mobile-open');
            overlay.classList.toggle('mobile-open');
        }
        
        function closeMobileTOC() {
            const toc = document.getElementById('toc');
            const overlay = document.getElementById('toc-overlay');
            toc.classList.remove('mobile-open');
            overlay.classList.remove('mobile-open');
        }

        // Close menu when window is resized above 1024px
        window.addEventListener('resize', function() {
            if (window.innerWidth >= 1025) {
                closeMobileTOC();
            }
        });

        // Smooth scrolling and active link highlighting
        document.addEventListener('DOMContentLoaded', function() {
            const tocLinks = document.querySelectorAll('.toc-link');
            const sections = document.querySelectorAll('.section-spacing');

            function updateActiveLink() {
                let current = '';
                sections.forEach(section => {
                    const sectionTop = section.offsetTop;
                    const sectionHeight = section.clientHeight;
                    if (window.scrollY >= sectionTop - 100) {
                        current = section.getAttribute('id');
                    }
                });

                tocLinks.forEach(link => {
                    link.classList.remove('active');
                    if (link.getAttribute('href') === '#' + current) {
                        link.classList.add('active');
                    }
                });
            }

            window.addEventListener('scroll', updateActiveLink);
            updateActiveLink(); // Initial call

            // Smooth scrolling for TOC links
            tocLinks.forEach(link => {
                link.addEventListener('click', function(e) {
                    e.preventDefault();
                    const targetId = this.getAttribute('href').substring(1);
                    const targetElement = document.getElementById(targetId);
                    if (targetElement) {
                        targetElement.scrollIntoView({
                            behavior: 'smooth',
                            block: 'start'
                        });
                    }
                    
                    // Close mobile menu if open
                    closeMobileTOC();
                });
            });
        });
    </script>
  

</body></html>