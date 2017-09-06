# Dependency Management for iOS

- [CocoaPods](#cocoapods)
  - [Installing CocoaPods](#installing-cocoapods)
  - [Adding frameworks](#adding-frameworks)
  - [Useful command](#useful-command)
  - [Useful keys](#useful-keys)
  - [pod install vs. pod update](#pod-install-vs-pod-update)
  - [Library vs. framework](#library-vs-framework)
- [Carthage](#carthage)
  - [Installing](#installing)
  - [Adding frameworks](#adding-frameworks)
  - [Running a project that uses Carthage](#running-a-project-that-uses-carthage)
  - [Cartfile Format](#cartfile-format)
  - [Carthage directories](#carthage-directories)
- [Carthage vs. CocoaPods](#carthage-vs-cocoapods)
- [Semantic Versioning](#semantic-versioning)
- [Sources](#sources)

## CocoaPods 

### Installing CocoaPods: 

1. run '**sudo gem install cocoapods**' in console
2. **pod setup --verbose**

### Adding frameworks:

1. **Close** Xcode

2. **Navigate** to the directory that contains your project

3. **pod init**

4. **Add** your pods

5. **pod install --verbose** (verbose for more information about installing)

6. Use **.xcworkspace** for this project from now on

### Useful command: 

* **pod outdated** - Show outdated project dependencies
* **pod update** - It will update the pod to the latest version possible (as long as it matches the version restrictions in your **Podfile**)
* **pod clean** - Remove Podfile.lock, Pods/ and *.xcworkspace (**plugin**)
* **pod list** - List pods
* **pod try NAME|URL** - Downloads the Pod with the given **NAME** (or Git *URL*), install its dependencies if needed and opens its demo project. If a Git URL is provided the head of the repo is used.

### Useful keys: 

*  **--silent** - Show nothing
* **--version** - Show the version of the tool
* **--verbose** - Show more debugging information

### pod install vs. pod update: 

* Use ***pod install*** to install new pods in your project. Even if you already have a **Podfile** and ran ***pod install*** before; so even if you are just adding/removing pods to a project already using CocoaPods.

* Use **pod update [PODNAME]** only when you want to update pods to a newer version

#### **You should always commit & push your *Podfile.lock* file.**

### Library vs. framework: 

* **Dynamic frameworks**, allow code, images and other assets to be bundled together. **Static libraries** *aren’t* allowed to contain any resources such as images or asset.
* **Dynamic frameworks** have namespace classes and **static libraries** don’t.

(This means your framework **must** include the necessary Swift runtime libraries. As a consequence, pods written in Swift must be created as dynamic frameworks. If Apple allowed Swift static libraries, it would cause duplicate symbols across different libraries that use the same standard runtime dependencies.)

# Carthage

### Installing:

* [Installer](https://github.com/Carthage/Carthage/releases)
* From [source](https://github.com/Carthage/Carthage)
* 'brew install carthage' in console

### Adding frameworks:

1. **Create 'Cartfile'** in text editor (but don't use TextEdit because it use so-called “smart quotes” instead of straight quotes)

2. **Add** frameworks as described below

3. run '**carthage update --platform iOS**'

4. On your application targets “**General**” settings tab, in the “**Linked Frameworks and Libraries**” section, drag and drop each framework you want to use from the **Carthage/Build** folder on disk.

5. On your application targets “**Build Phases**” settings tab, click the “**+**” icon and choose “**New Run Script Phase**”. Create a Run Script in which you specify your shell (ex: **/bin/sh**), add the following contents to the script area below the shell:
	```/usr/local/bin/carthage copy-frameworks```
  
6. Add the paths to the frameworks you want to use under “**Input Files**”, e.g.:

```
$(SRCROOT)/Carthage/Build/iOS/Result.framework
$(SRCROOT)/Carthage/Build/iOS/ReactiveSwift.framework
$(SRCROOT)/Carthage/Build/iOS/ReactiveCocoa.framework
```

7. Add the paths to the copied frameworks to the “**Output Files**”, e.g.:
```
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/Result.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/ReactiveSwift.framework
$(BUILT_PRODUCTS_DIR)/$(FRAMEWORKS_FOLDER_PATH)/ReactiveCocoa.framework
```

### Running a project that uses Carthage: 

After you’ve finished the above steps and pushed your changes, other users of the project only need to fetch the repository and run **carthage bootstrap** to get started with the frameworks you’ve added.

### Cartfile Format:

- **Dependency origin**:
  
  This tells Carthage where to fetch a dependency from. Carthage supports two types of origins:
  - **github** - specify a Github project in the **Username/ProjectName** format (Example: Alamofire/AlamofireImage)
  - **git** - for generic Git repositories hosted elsewhere

- **Dependency Version**: 
  
  This is how you tell Carthage which version of a dependency you’d like to use. 
  - **== 1.0** means “Use exactly version 1.0”
  - **>= 1.0** means “Use version 1.0 or higher”
  - **~> 1.0** means “Use any version that’s compatible with 1.0″, essentially meaning any version up until the next major release.
  
- **branch name / tag name / commit name**: 

  This means “Use this specific git branch / tag / commit”. For example, you could specify master, or a commit has like **5c8a74a**. 

If you don’t specify a version, then Carthage will just use the latest version that’s compatible with your other dependencies.

### Carthage directories: 

When you run **carthage update**, Carthage creates a couple of files and directories for you:

![Carthage directory](https://github.com/alspirichev/CocoaPods-Carthage/blob/master/carthage.png)

- **Cartfile.resolved**: 

  This file is created to serve as a companion to the Cartfile. It defines exactly which versions of your dependencies Carthage selected for installation. It’s **strongly recommended to commit** this file to your version control repository
  
- Carthage directory, containing two subdirectories:
  - **Build**: This contains the built framework for each dependency.
  - **Checkouts**: This is where Carthage checks out the source code for each dependency that’s ready to build into frameworks. 

# Carthage vs. CocoaPods 

* CocoaPods (by default) automatically creates and updates an Xcode workspace for your application and all dependencies. Carthage builds framework binaries using **xcodebuild**, but leaves the responsibility of integrating them up to the user.

* Carthage has been created as a **decentralized** dependency manager. There is no central list of projects, which reduces maintenance work and avoids any central point of failure.

* CocoaPods projects must also have what’s known as a podspec file, which includes metadata about the project and specifies how it should be built. Carthage uses **xcodebuild** to build dependencies, instead of integrating them into a single workspace, it doesn’t have a similar specification file but your dependencies must include their own Xcode project that describes how to build their products.

# Semantic Versioning 

The three numbers are defined as major, minor, and patch version numbers.

![Semantic Versioning](https://github.com/alspirichev/CocoaPods-Carthage/blob/master/sem_versioning.png)

Given a version number **MAJOR**.**MINOR**.**PATCH**, increment the:

1. *MAJOR* version when you make **incompatible** API changes,
2. *MINOR* version when you add functionality in a **backwards-compatible** manner
3. *PATCH* version when you make **backwards-compatible bug fixes**.

(If a pod’s version number is less than *1.0.0*, it’s considered to be a **beta version**, and minor number increases may include **backwards incompatible** changes.)

# Sources 

* [Cocoapods](https://cocoapods.org/)
* [Carthage](https://github.com/Carthage/Carthage)
* [raywenderlich.com](https://www.raywenderlich.com/156971/cocoapods-tutorial-swift-getting-started)
* [semver.org](http://semver.org/)
