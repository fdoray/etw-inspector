# ETW Inspector

A tool to ease the analysis of ETW traces.

## Requirements (third party):

* [CMake](http://www.cmake.org/)
* [gmock](https://code.google.com/p/googlemock/) and [gtest](https://code.google.com/p/googletest/):
  ``svn checkout http://googlemock.googlecode.com/svn/trunk/ third_party/gmock``

## Building

```
cd etw-inspector
svn checkout http://googlemock.googlecode.com/svn/trunk/ third_party/gmock
cmake .
make
```
