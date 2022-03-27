# SelfDrivingCarEngr-ScanMatchingLocalization

1. Compile the code (Terminal Tab 1)
```
cd /home/workspace/c3-project

cmake .

make
```
2. Launch the simulator in a new terminal tab (Terminal Tab 2)
```
su - student

cd /home/workspace

./run-carla.sh
```
3. Run the project code back in the first tab (Terminal Tab 1)
```
cd /home/workspace/
```
then to run NDT
```
./cloud_loc
```
or to run ICP
```
./cloud_loc 2
```
# Results
## ICP
![ICP](https://user-images.githubusercontent.com/23568809/160275220-69a80e70-296e-44c7-ac8f-26586092fa70.png)

## NDT
![NDT](https://user-images.githubusercontent.com/23568809/160275214-6dfcc8d1-d0aa-4037-8db3-a76dfd352205.png)

