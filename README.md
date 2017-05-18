# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets) == 0.14, but the master branch will probably work just fine
  * Follow the instructions in the [uWebSockets README](https://github.com/uWebSockets/uWebSockets/blob/master/README.md) to get setup for your platform. You can download the zip of the appropriate version from the [releases page](https://github.com/uWebSockets/uWebSockets/releases). Here's a link to the [v0.14 zip](https://github.com/uWebSockets/uWebSockets/archive/v0.14.0.zip).
  * If you have MacOS and have [Homebrew](https://brew.sh/) installed you can just run the ./install-mac.sh script to install this.
* [Ipopt](https://projects.coin-or.org/Ipopt)
  * Mac: `brew install ipopt --with-openblas`
  * Linux
    * You will need a version of Ipopt 3.12.1 or higher. The version available through `apt-get` is 3.11.x. If you can get that version to work great but if not there's a script `install_ipopt.sh` that will install Ipopt. You just need to download the source from [here](https://www.coin-or.org/download/source/Ipopt/).
    * Then call `install_ipopt.sh` with the source directory as the first argument, ex: `bash install_ipopt.sh Ipopt-3.12.1`. 
  * Windows: TODO. If you can use the Linux subsystem and follow the Linux instructions.
* [CppAD](https://www.coin-or.org/CppAD/)
  * Mac: `brew install cppad`
  * Linux `sudo apt-get install cppad` or equivalent.
  * Windows: TODO. If you can use the Linux subsystem and follow the Linux instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/CarND-MPC-Project/releases).



## Basic Build Instructions


1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## The Model
The car drives on the track, which trajectory is approximated with 3rd order polynomial.
Simulator provides the position of the car in global coordinates, as well as speed and heading.
To calculate cross track error (CTE) and heading error (EPSI) I'm working in car coordinates.
In car coordinates initial car's position (x',y',psi') is (0,0,0). Speed remains the same as in global coordinates.
To calculate CTE, I'm transforming waypoints ptsx, ptsy into car coordinates; fit the polynomial in car coordinates and
calculate the CTE as the value of the polynomial function in the point x = 0.
EPSI is -arctan of the first derivative in the point x = 0. State vector is submitted into the optimizator.
Returned value of delta and acceleration are used to actuate the position of the car.

The cost function, used in the optimizer, takes into consideration:
*  the values of CTE and EPSI;
*  the difference between the speed and reference speed (I'm using 80 mph as a reference speed);
*  the values of delta and a;
*  the change of delta and a in next and current moments;
The weights of each element of the cost function were found imperically.

## Timestep Length and Frequency
Timestep Length and Frequency were chosen imperically.
I'm using timestep length N 10 and timestep frequency dt 0.15.
In the mpc_to_line lab the best parameters for me were (7, 0.1). Here the model is more robust when the value of N close to 10.
The rule for choosing dt says it should be equal to our expectation how ofter things changes. 0.15 showed the best performance for my model.

## Model Predictive Control with Latency
The Model Predictive Control handles a 100 millisecond latency which simulate latency between sensors and processing.
I'm using dt > latency to handle the actuations.
