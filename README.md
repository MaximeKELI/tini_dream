# tini_dream
TINY DREAM

An embedded, Header Only, Stable Diffusion Inference C++ Library
pixlab.io/tiny-dream

td_screen_website
Latest News 🔥

    Tiny Dream 1.7.5 Released - Get Started.

API documentation dependency license

    Introduction
    Features
    Getting Started
    Downloads
    Project Roadmap
    License
    C++ API Reference Guide
    Issues Tracker
    Related Projects

Introducing PixLab's Tiny Dream

Tiny Dream is a header only, dependency free, partially uncensored, Stable Diffusion implementation written in C++ with primary focus on CPU efficiency, and smaller memory footprint. Tiny Dream runs reasonably fast on the average consumer hardware, require only 1.7 ~ 5.5 GB of RAM to execute, does not enforce Nvidia GPUs presence, and is designed to be embedded on larger codebases (host programs) with an easy to use C++ API. The possibilities are literally endless, or at least extend to the boundaries of Stable Diffusion's latent manifold.
Features 🔥
For the extensive list of features, please refer to the official documentation here.

    OpenCV Dependency Free: Only stb_image_write.h from the excellent stb single-header, public domain C library is required for saving images to disk.
    Smallest, Run-Time Memory Footprint for Running Stable Diffusion in Inference.
    Straightforward to Integrate on Existing Codebases: Just drop tinydream.hpp and stb_image_write.h on your source tree with the Pre-trained Models & Assets.
    Reasonably fast on Intel/AMD CPUs (Benchmarks): With TBB threading and SSE/AVX vectorization.
    Support Real-ESRGAN, A Super Resolution Network Upscaler.
    Full Support for Words Priority: Instruct the model to pay attention, and give higher priority to word (keywords) surrounded by parenthesis ().
    Support for Output Metadata: Link meta information to your output images such as copyright notice, comments, or any other meta data you would like to see linked to your image.
    Support for Stable Diffusion Extra Parameters: Adjust Seed resizing & Guidance Scale.

Getting Started with Tiny-Dream 🔥

Integrating Tiny Dream on your existing code base is straightforward. Here is what to do without having to do a lot of tedious reading and configuration:
Download Tiny-Dream

    Download the latest public release of Tiny Dream, and extract the package on a directory of your choice.
    Refer to the downloads section to get a copy of the Tiny Dream source code as well as the Pre-Trained Models & Assets.

Embedding Tiny-Dream

    The Tiny Dream source code comprise only two header files that is tinydream.hpp and stb_image_write.h.
    All you have to do is drop these two C/C++ header files on your source tree, and instantiate a new tinyDream object as shown on the pseudo C++ code below:

#include "tinydream.hpp"
/*
* Main Entry Point. The only required argument is the Positive Prompt.
* Passing a Negative Prompt (words separated by commas) is highly recommended though.
* 
* We recommend that you experiment with different seed & step values
* in order to achieve a desirable result.
* 
* ./tinydream "positive prompt" ["negative prompt"] [seed] [step]
*/
int main(int argc, char *argv[]) 
{
	tinyDream td; // stack allocated tinyDream object

	// Display the library current inference engine, version number, and copyright notice
	std::cout << tinyDream::about() << std::endl;
	
	// At least a positive prompt must be supplied via command line
	if (argc < 2) {
		std::cout << "Missing Positive (and potentially Negative) Prompt: Describe something you'd like to see generated..." << std::endl;
		std::cout << "Example of Prompts:" << std::endl;
		// Example of built-in Positive/Negative Prompts
		auto prompts = tinyDream::promptExample();
		std::cout << "\tPositive Prompt: " << prompts.first << std::endl;
		std::cout << "\tNegative Prompt: " << prompts.second << std::endl;
		return -1;
	}

	// Register a log handler callback responsible of 
	// consuming log messages generated during inference.
	td.setLogCallback(logCallback, nullptr);
	
	// Optionally, set the assets path if the pre-trained models
	// are not extracted on the same directory as your executable
	// The Tiny-Dream assets can be downloaded from: https://pixlab.io/tiny-dream#downloads
	td.setAssetsPath("/path/to/tinydream/assets"); // Remove or comment this if your assets are located on the same directory as your executable
	
	// Optionally, set a prefix of your choice to each freshly generated image name
	td.setImageOutputPrefix("tinydream-");
	
	// Optionally, set the directory where you want
	// the generated images to be stored
	td.setImageOutputPath("/home/photos/");
	
	int seedMax = 90;
	if (argc > 3) {
		/*
		* Seed in Stable Diffusion is a number used to initialize the generation. 
		* Controlling the seed can help you generate reproducible images, experiment
		* with other parameters, or prompt variations.
		*/
		seedMax = std::atoi(argv[3]);
	}
	int step = 30;
	if (argc > 4) {
		/*
		* adjusting the inference steps in Stable Diffusion: The more steps you use,
		* the better quality you'll achieve but you shouldn't set steps as high
		* as possible. Around 30 sampling steps (default value) are usually enough
		* to achieve high-quality images.
		*/
		step = std::atoi(argv[4]);
	}

	/*
	* User Supplied Prompts - Generate an image that matches the input criteria.
	* 
	* Positive Prompt (required): Describe something you'd like to see generated (comma separated words).
	* Negative Prompt (optional): Describe something you don't like to see generated (comma separated words).
	*/
	std::string positivePrompt{ argv[1] };
	std::string negativePrompt{ "" };
	if (argc > 2) {
		negativePrompt = std::string{ argv[2] };
	}

	/*
	* Finally, run Stable Diffusion in inference
	* 
	* The supplied log consumer callback registered previously should shortly receive
	* all generated log messages (including errors if any) during inference.
	* 
	* Refer to the official documentation at: https://pixlab.io/tiny-dream#tiny-dream-method
	* for the expected parameters the tinyDream::dream() method takes.
	*/
	for (int seed = 1; seed < seedMax; seed++) {
		std::string outputImagePath;

		td.dream(
			positivePrompt, 
			negativePrompt, 
			outputImagePath, 
			true, /* Set to false if you want 512x512 pixels output instead of 2048x2048 output */
			seed,
			step
		);

		// You do not need to display the generated image path manually each time via std::cout
		// as the supplied log callback should have already done that.
		std::cout << "Output Image location: " << outputImagePath << std::endl; // uncomment this if too intrusive
	}
	return 0;
}

Learn the Fundamentals (C++ API)

    The above code should be self-explanatory, and easy to understand for the average C++ programmer. The full C++ integration code for a typical application embedding Tiny Dream is located at: pixlab.io/tiny-dream#code-gist.
    As of this release, the library exposes a single class named tinyDream with the following exported methods:
        tinyDream::tinyDream() - Constructor
        tinyDream::dream() - Stable Diffusion Inference
        tinyDream::setImageOutputPath()
        tinyDream::setImageOutputPrefix()
        tinyDream::setLogCallback()
        tinyDream::setAssetsPath()
        tinyDream::promptExample()
        tinyDream::about()
    A step-by-step, detailed integration guide, and call logic of the above methods is located at: pixlab.io/tiny-dream#step-by-step-cpp.

Building Tiny-Dream

    Building Tiny-Dream from source require a modern C++17 compiler such as GCC 7 or later, Clang or Microsoft Visual Studio (MSVC).
    You also need to link to the default backend Tensor library in order to generate the executable.
    As of this release, NCNN is the default tensor library. On our Roadmap, we plan to ditch ncnn to a less bloated tensor library such as SOD or GGML with focus on CPU efficiency.
    Alternatively, you can rely on a build manager such as CMAKE to build the executable for you. The Tiny-Dream repository repository already contain the necessarily CMAKE template to build the executable from source.
    An example of generating a heavy optimized executable without relying on a external build manager is shown just below:

git clone https://github.com/symisc/tiny-dream.git
cd tiny-dream
g++ -o tinydream boilerplate.cpp -funsafe-math-optimizations -Ofast -flto=auto  -funroll-all-loops -pipe -march=native -std=c++17 -Wall -Wextra `pkg-config --cflags --libs ncnn` -lstdc++ -pthread -Wl -flto -fopt-info-vec-optimized
./tinydream "pyramid, desert, palm trees, river, (landscape), (high quality)"

Get the Pre-Trained Models & Assets

    Once your executable built, you will need the Tiny Dream Pre-Trained Models & Assets path accessible to your executable.
    The Tiny Dream assets comprise all pre-trained models (over 2GB as of this release) required by the tinyDream::dream() method in order to run stable diffusion in inference.
    You can download the pre-trained models from the Download section on the PixLab website.
    Once downloaded, extract the assets ZIP archive in a directory of your choice (usually the directory where your executable is located), and set the full path via tinyDream::setAssetsPath() or from the Tiny Dream constructor.

Continue with The C++ API Reference Guide

The Tiny Dream C++ Interface, provides detailed specifications for all of the various methods the Tiny Dream class exports. Once the reader understands the basic principles of operation for Tiny Dream, that document should serve as a reference guide.
TODOs & Roadmap 🔥

As we continue to develop and improve Tiny Dream, we have an exciting roadmap of future addons and enhancements planned. Refer to the Roadmap page at pixlab.io/tiny-dream or the PixLab Blog for the exhaustive list of todos & ongoing progress...

    Move the Tensor library to a non bloated one such as SOD or GGML with focus on CPU performance.
    Provide a Cross-Platform GUI to Tiny Dream implemented in Dear imGUI.
    Provide a Web-Assembly port to the library once the future Tensor library (SOD or GGML) ported to WASM.
    Output SVG, and easy to alter formats (potentially PSD) rather than static PNGs.
    Provide an Android, proof of concept, show-case APK.

Official Docs & Resources
Pre-Trained Models & Assets Downloads 	Getting Started Guide 	Licensing 	C++ API Reference Guide 	Project Roadmap 	Features
Related Projects 🔥

You may find useful the following production-ready projects developed & maintained by PixLab | Symisc Systems:

    SOD - An Embedded, Dependency-Free, Computer Vision C/C++ Library.
    FACEIO - Cross Browser, Passwordless Facial Authentication Framework.
    PixLab Annotate - Online Image Annotation, Labeling & Segmentation Tool.
    ASCII Art - Real-Time ASCII Art Rendering C Library.
    UnQLite - An Embedded, Transactional Key/Value Database Engine.

