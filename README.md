# Guide

## General
* `.gitignore` should be copied from this repository<br>
	The ignored files include
	* `OS Temporary files` (Mac temporary files are included inside)
	* `Development & Distribution Folders`
	* `Vscode`
	* `JetBrains`
	* `Matlab`
	* `Python` (spyder project settings are ignored inside)
	* `waf` (python binary files are ignored in `Python`)
* Modularity<br>
All modules are imported by `git submodule` and stored in the folder `Modules`.
```
git submodule add [repository ssh link] Modules/[repository name]
```
* **Git Branch Management**<br>
`main` is the branch to store the latest code (testing scripts and testing data shouldn't be involved). `debug` is the branch where we edit and test neweset codes (with data stored inside).
    * How to update the change from `debug` to `main`<br>
    First, we need to submit all changes in `debug` to the remote. (Now, we stay at `debug`)
    ```sh
    git checkout main
    git merge debug
    ```
    Then, we need to delete the **testing scripts**, **testing data** and **extra modules** only used in `debug`.<br>
    Any extra module in `Modules/`(files) and `.gitmodules`(import syntax) has to be deleted manually.<br>
    Now, we can submit the change to main.
    ```
    git add .
    git commit -m "merge from debug: [the description of new traits]"
    git push
    ```
* `README.md` category
	* Install **doctoc** `npm i doctoc -g`
	* Create the category `doctoc README.md`

## Interface Guide
* `Python` & `Matlab` should use similar defination of classes, properties and methods (100% similar is the best).
* **CamelCase** naming style
	* class names use capital letters
	* properties and methods use lower cases. 
	* multiple words use `_` to separate
	* private methods, private properties or private functions use `_` in the beginning
	* constant variables use in all uppercase
	* **spacing**<br>
	1 line spacing for class methods or different logic blocks<br>
	2 line spacing for classes and functions (class methods are not included).
* **Modulation Class Programming Rules** (`Mod_xxx`)
	* constructor<br>
	We load all parameters about this modulation itself. For example, OFDM requires subcarrier number, FFT size and subcarrier spacing.
	* modulate<br>
	We load a vector of symbols into.
	* set_channel<br>
	Set the chararistics of the channel, such as delays, Doppler shifts, path gains and etc..
	* pass_channel<br>
	We pass our symbols through the channel. Here, we need to provide a SNR scalar to add the noise (the signal power is nomarlised to 1).
	* demodulate<br>
	We demodulate the and give the `y`
	* get_H<br>
	provide the channel matrix (all variants should be based on this function)
* **Detection Class Programming Rules** (`Detect_xxx`)
	* constructor<br>
	We load the constellation map inside.
	* detect<br>
	We use `y`, `H` and `noise power` to detect. If we need other parameters, we should add them in this process.
* **Nerual Network Detection Class Programming Rules** (`Detect_NN_xxx`)

## C/C++ Guide
* Class Inheritance
	* If the child class does not define its constructor, **it will call its parent's constructor (of no parameter)**
	* If the child class defines its constructor (with/without parameters), **it will call its parent's constructor (of no parameter)**
	* If users define a constructor with parameters in **the parent class**, the compiler won't provide **a default constructor of no parameter** and **its child class** must have a constructor explicitly calling its parent's constructor. Please note that the parent constructor has to have at least one parameter same to parameters in the child constructor.
	```c++
	class father{
	public:
		string f_a;
		father(string f_a){
			this->f_a = f_a;
			cout << "father" << endl;
		}
	};
	class son : public father{
	public:
		string f_a;
		// Wrong! This way presumes that the parent class has a constructor with same parameters but it is not true in this example
		//son(string f_a){
		//}
			
		// The parent only has a constructor with parameters, so we have to explicitly call it in the child constructor
		// Way 1: Call parent constructor with constants
		son(string f_a):father("father"){
			cout << "son (way 1)" << endl;
		}
		// Way 2: Transfer the parameters to the parent construtor
		son(string f_a, int a):father(f_a){
			cout << "son (way 2)" << endl;
		}
	};
	```
* Polymorphism<br>
The parent class can call its method based on its instance of which subclass. However this requires the method in the parent class to be virtual 
```c++
class A{
public:
	// define a virtual method in the parent class to support polymorphism
	virtual void foo(){
		cout<<"A::foo() is called"<<endl;
	}
	// pure virtual function when we don't want to intialize this method in the parent class
      virtual int area() = 0;
};
class B: A{
public:
	void foo(){
		cout<<"B::foo() is called"<<endl;
	}
};
int main(void){
	A *a = new B();
	a->foo();	// Here a is of A, but it uses foo() in B
	return 0;
}
```

## Matlab Guide
* use the handle class insteald of the value class. See [https://au.mathworks.com/help/matlab/matlab_oop/comparing-handle-and-value-classes.html](https://au.mathworks.com/help/matlab/matlab_oop/comparing-handle-and-value-classes.html)
```matlab
% the handle class defination
classdef [your class name] < handle
```
* the dimension format is `[row, column]`

## Python Guide
* use numpy as the base type to do any Math operation
* the dimension format is `[..., (batch-size), row, column]`. Python codes need to support dynamic dimensions (greater than or equal to 2).

## NVM Guide
### Debug
* `***.ps1 cannot be loaded because running scripts is disabled on this system error`
	* Open PowerShell Console by selecting `Run as Administrator`
	* type `Get-ExecutionPolicy` to see if `Restricted` (which is the reason)
	* type `Set-ExecutionPolicy RemoteSigned`
	* type `Y`
### Commands
* `node -v || node --version` check version
* `nvm ls` list installed versions of node (via nvm)
* `nvm install 6.9.2` install specific version of node
* `nvm use 8.0` Use the latest available 8.0.x release
* `nvm run 6.10.3 app.js` Run app.js using node 6.10.3
* `nvm exec 4.8.3 node app.js` Run node `app.js` with the PATH pointing to node 4.8.3
* `nvm alias default 6.9.2` set default version of node
* `nvm alias default node` Always default to the latest available node version on a shell
* `nvm install node` Install the latest available version
* `nvm use node` Use the latest version
* `nvm install --lts` Install the latest LTS version
* `nvm use --lts` Use the latest LTS version
* `nvm set-colors cgYmW` Set text colors to cyan, green, bold yellow, magenta, and white