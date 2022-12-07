# floating-ui scale bug demo
## Get Started
yarn install;
yarn dev;

## explain
The project provides two pages;
http://localhost:5173/#/origin: This page is an example of the effect of float-ui without body scaling

*scroll the page to the bottom right*

![origin page performance](./origin_page.png)


http://localhost:5173/#/scale：This page is an example of the body scaling effect of float-ui
![scale page performance](./scale_page.png)
Through the comparison of the above two examples, it can be clearly seen that the position calculation in the scale scene is incorrect