Download Link: https://assignmentchef.com/product/solved-machinelearning-exercise-7-k-means-clustering-and-principal-component-analysis
<br>



<h1>Introduction</h1>

In this exercise, you will implement the <em>K</em>-means clustering algorithm and apply it to compress an image. In the second part, you will use principal component analysis to find a low-dimensional representation of face images. Before starting on the programming exercise, we strongly recommend watching the video lectures and completing the review questions for the associated topics.

To get started with the exercise, you will need to download the starter code and unzip its contents to the directory where you wish to complete the exercise. If needed, use the cd command in Octave/MATLAB to change to this directory before starting this exercise.

You can also find instructions for installing Octave/MATLAB in the “Environment Setup Instructions” of the course website.

<h2>Files included in this exercise</h2>

ex7.m – Octave/MATLAB script for the first exercise on <em>K</em>-means ex7 pca.m – Octave/MATLAB script for the second exercise on PCA ex7data1.mat – Example Dataset for PCA ex7data2.mat – Example Dataset for <em>K</em>-means ex7faces.mat – Faces Dataset bird small.png – Example Image displayData.m – Displays 2D data stored in a matrix drawLine.m – Draws a line over an exsiting figure plotDataPoints.m – Initialization for <em>K</em>-means centroids plotProgresskMeans.m – Plots each step of <em>K</em>-means as it proceeds runkMeans.m – Runs the <em>K</em>-means algorithm submit.m – Submission script that sends your solutions to our servers [<em>?</em>] pca.m – Perform principal component analysis

[<em>?</em>] projectData.m – Projects a data set into a lower dimensional space

[<em>?</em>] recoverData.m – Recovers the original data from the projection

[<em>?</em>] findClosestCentroids.m – Find closest centroids (used in <em>K</em>-means)

[<em>?</em>] computeCentroids.m – Compute centroid means (used in <em>K</em>-means)

[<em>?</em>] kMeansInitCentroids.m – Initialization for <em>K</em>-means centroids

<em>? </em>indicates files you will need to complete

Throughout the first part of the exercise, you will be using the script ex7.m, for the second part you will use ex7 pca.m. These scripts set up the dataset for the problems and make calls to functions that you will write. You are only required to modify functions in other files, by following the instructions in this assignment.

<h2>Where to get help</h2>

The exercises in this course use Octave<a href="#_ftn1" name="_ftnref1"><sup>[1]</sup></a> or MATLAB, a high-level programming language well-suited for numerical computations. If you do not have Octave or MATLAB installed, please refer to the installation instructions in the “Environment Setup Instructions” of the course website.

At the Octave/MATLAB command line, typing help followed by a function name displays documentation for a built-in function. For example, help plot will bring up help information for plotting. Further documentation for Octave functions can be found at the <a href="https://www.gnu.org/software/octave/doc/interpreter/">Octave documentation pages</a><a href="https://www.gnu.org/software/octave/doc/interpreter/">.</a> MATLAB documentation can be found at the <a href="https://www.mathworks.com/help/matlab/?refresh=true">MATLAB documentation pages</a><a href="https://www.mathworks.com/help/matlab/?refresh=true">.</a>

We also strongly encourage using the online <strong>Discussions </strong>to discuss exercises with other students. However, do not look at any source code written by others or share your source code with others.

<h1>1          <em>K</em>-means Clustering</h1>

In this this exercise, you will implement the <em>K</em>-means algorithm and use it for image compression. You will first start on an example 2D dataset that will help you gain an intuition of how the <em>K</em>-means algorithm works. After that, you wil use the <em>K</em>-means algorithm for image compression by reducing the number of colors that occur in an image to only those that are most common in that image. You will be using ex7.m for this part of the exercise.

<h2>1.1        Implementing <em>K</em>-means</h2>

The <em>K</em>-means algorithm is a method to automatically cluster similar data examples together. Concretely, you are given a training set {<em>x</em><sup>(1)</sup><em>,…,x</em><sup>(<em>m</em>)</sup>} (where <em>x</em><sup>(<em>i</em>) </sup>∈ R<em><sup>n</sup></em>), and want to group the data into a few cohesive “clusters”. The intuition behind <em>K</em>-means is an iterative procedure that starts by guessing the initial centroids, and then refines this guess by repeatedly assigning examples to their closest centroids and then recomputing the centroids based on the assignments.

The <em>K</em>-means algorithm is as follows:

<table width="527">

 <tbody>

  <tr>

   <td width="527">% Initialize centroids centroids = kMeansInitCentroids(X, K); for iter = 1:iterations% Cluster assignment step: Assign each data point to the% closest centroid. idx(i) corresponds to cˆ(i), the index% of the centroid assigned to example i idx = findClosestCentroids(X, centroids);% Move centroid step: Compute means based on centroid% assignments centroids = computeMeans(X, idx, K);end</td>

  </tr>

 </tbody>

</table>

The inner-loop of the algorithm repeatedly carries out two steps: (i) Assigning each training example <em>x</em><sup>(<em>i</em>) </sup>to its closest centroid, and (ii) Recomputing the mean of each centroid using the points assigned to it. The <em>K</em>-means algorithm will always converge to some final set of means for the centroids. Note that the converged solution may not always be ideal and depends on the initial setting of the centroids. Therefore, in practice the <em>K</em>-means algorithm is usually run a few times with different random initializations. One way to choose between these different solutions from different random initializations is to choose the one with the lowest cost function value (distortion).

You will implement the two phases of the <em>K</em>-means algorithm separately in the next sections.

<h3>1.1.1       Finding closest centroids</h3>

In the “cluster assignment” phase of the <em>K</em>-means algorithm, the algorithm assigns every training example <em>x</em><sup>(<em>i</em>) </sup>to its closest centroid, given the current positions of centroids. Specifically, for every example <em>i </em>we set

<em>c</em><sup>(<em>i</em>) </sup>:= <em>j     </em>that minimizes     ||<em>x</em><sup>(<em>i</em>) </sup>− <em>µ<sub>j</sub></em>||<sup>2</sup><em>,</em>

where <em>c</em><sup>(<em>i</em>) </sup>is the index of the centroid that is closest to <em>x</em><sup>(<em>i</em>)</sup>, and <em>µ<sub>j </sub></em>is the position (value) of the <em>j</em>’th centroid. Note that <em>c</em><sup>(<em>i</em>) </sup>corresponds to idx(i) in the starter code.

Your task is to complete the code in findClosestCentroids.m. This function takes the data matrix X and the locations of all centroids inside centroids and should output a one-dimensional array idx that holds the index (a value in {1<em>,…,K</em>}, where <em>K </em>is total number of centroids) of the closest centroid to every training example.

You can implement this using a loop over every training example and every centroid.

Once you have completed the code in findClosestCentroids.m, the script ex7.m will run your code and you should see the output [1 3 2] corresponding to the centroid assignments for the first 3 examples.

<em>You should now submit your solutions.</em>

<h3>1.1.2       Computing centroid means</h3>

Given assignments of every point to a centroid, the second phase of the algorithm recomputes, for each centroid, the mean of the points that were assigned to it. Specifically, for every centroid <em>k </em>we set

where <em>C<sub>k </sub></em>is the set of examples that are assigned to centroid <em>k</em>. Concretely, if two examples say <em>x</em><sup>(3) </sup>and <em>x</em><sup>(5) </sup>are assigned to centroid <em>k </em>= 2, then you should update

You should now complete the code in computeCentroids.m. You can implement this function using a loop over the centroids. You can also use a loop over the examples; but if you can use a vectorized implementation that does not use such a loop, your code may run faster.

Once you have completed the code in computeCentroids.m, the script ex7.m will run your code and output the centroids after the first step of <em>K</em>means.

<em>You should now submit your solutions.</em>

<h2>1.2        <em>K</em>-means on example dataset</h2>

Figure 1: The expected output.

After you have completed the two functions (findClosestCentroids and computeCentroids), the next step in ex7.m will run the <em>K</em>-means algorithm on a toy 2D dataset to help you understand how <em>K</em>-means works. Your functions are called from inside the runKmeans.m script. We encourage you to take a look at the function to understand how it works. Notice that the code calls the two functions you implemented in a loop.

When you run the next step, the <em>K</em>-means code will produce a visualization that steps you through the progress of the algorithm at each iteration. Press <em>enter </em>multiple times to see how each step of the <em>K</em>-means algorithm changes the centroids and cluster assignments. At the end, your figure should look as the one displayed in Figure 1.

<h2>1.3        Random initialization</h2>

The initial assignments of centroids for the example dataset in ex7.m were designed so that you will see the same figure as in Figure 1. In practice, a good strategy for initializing the centroids is to select random examples from the training set.

In this part of the exercise, you should complete the function kMeansInitCentroids.m with the following code:

% Initialize the centroids to be random examples

% Randomly reorder the indices of examples randidx = randperm(size(X, 1)); % Take the first K examples as centroids centroids = X(randidx(1:K), :);

The code above first randomly permutes the indices of the examples (using randperm). Then, it selects the first <em>K </em>examples based on the random permutation of the indices. This allows the examples to be selected at random without the risk of selecting the same example twice.

<em>You do not need to make any submissions for this part of the exercise.</em>

<h2>1.4        Image compression with <em>K</em>-means</h2>

Figure 2: The original 128×128 image.

In this exercise, you will apply <em>K</em>-means to image compression.       In a

straightforward 24-bit color representation of an image,<a href="#_ftn2" name="_ftnref2"><sup>[2]</sup></a> each pixel is represented as three 8-bit unsigned integers (ranging from 0 to 255) that specify the red, green and blue intensity values. This encoding is often refered to as the RGB encoding. Our image contains thousands of colors, and in this part of the exercise, you will reduce the number of colors to 16 colors.

By making this reduction, it is possible to represent (compress) the photo in an efficient way. Specifically, you only need to store the RGB values of the 16 selected colors, and for each pixel in the image you now need to only store the index of the color at that location (where only 4 bits are necessary to represent 16 possibilities).

In this exercise, you will use the <em>K</em>-means algorithm to select the 16 colors that will be used to represent the compressed image. Concretely, you will treat every pixel in the original image as a data example and use the <em>K</em>-means algorithm to find the 16 colors that best group (cluster) the pixels in the 3dimensional RGB space. Once you have computed the cluster centroids on the image, you will then use the 16 colors to replace the pixels in the original image.

<h3>1.4.1       <em>K</em>-means on pixels</h3>

In Octave/MATLAB, images can be read in as follows:

<table width="527">

 <tbody>

  <tr>

   <td width="527">% Load 128×128 color image (birdsmall.png)A = imread(‘birdsmall.png’);% You will need to have installed the image package to used% imread. If you do not have the image package installed, you% should instead change the following line to%%                                                                    load(‘birdsmall.mat’); % Loads the image into the variable A</td>

  </tr>

 </tbody>

</table>

This creates a three-dimensional matrix A whose first two indices identify a pixel position and whose last index represents red, green, or blue. For example, A(50, 33, 3) gives the blue intensity of the pixel at row 50 and column 33.

The code inside ex7.m first loads the image, and then reshapes it to create an <em>m </em>× 3 matrix of pixel colors (where <em>m </em>= 16384 = 128 × 128), and calls your <em>K</em>-means function on it.

After finding the top <em>K </em>= 16 colors to represent the image, you can now assign each pixel position to its closest centroid using the findClosestCentroids function. This allows you to represent the original image using the centroid assignments of each pixel. Notice that you have significantly reduced the number of bits that are required to describe the image. The original image required 24 bits for each one of the 128×128 pixel locations, resulting in total size of 128×128×24 = 393<em>,</em>216 bits. The new representation requires some overhead storage in form of a dictionary of 16 colors, each of which require 24 bits, but the image itself then only requires 4 bits per pixel location. The final number of bits used is therefore 16 × 24 + 128 × 128 × 4 = 65<em>,</em>920 bits, which corresponds to compressing the original image by about a factor of 6.

1

Figure 3: Original and reconstructed image (when using <em>K</em>-means to compress the image).

Finally, you can view the effects of the compression by reconstructing the image based only on the centroid assignments. Specifically, you can replace each pixel location with the mean of the centroid assigned to it. Figure 3 shows the reconstruction we obtained. Even though the resulting image retains most of the characteristics of the original, we also see some compression artifacts.

<em>You do not need to make any submissions for this part of the exercise.</em>

<h2>1.5        Optional (ungraded) exercise: Use your own image</h2>

In this exercise, modify the code we have supplied to run on one of your own images. Note that if your image is very large, then <em>K</em>-means can take a long time to run. Therefore, we recommend that you resize your images to managable sizes before running the code. You can also try to vary <em>K </em>to see the effects on the compression.

<h1>2          Principal Component Analysis</h1>

In this exercise, you will use principal component analysis (PCA) to perform dimensionality reduction. You will first experiment with an example 2D dataset to get intuition on how PCA works, and then use it on a bigger dataset of 5000 face image dataset.

The provided script, ex7 pca.m, will help you step through the first half of the exercise.

<h2>2.1        Example Dataset</h2>

To help you understand how PCA works, you will first start with a 2D dataset which has one direction of large variation and one of smaller variation. The script ex7 pca.m will plot the training data (Figure 4). In this part of the exercise, you will visualize what happens when you use PCA to reduce the data from 2D to 1D. In practice, you might want to reduce data from 256 to 50 dimensions, say; but using lower dimensional data in this example allows us to visualize the algorithms better.

Figure 4: Example Dataset 1

<h2>2.2        Implementing PCA</h2>

In this part of the exercise, you will implement PCA. PCA consists of two computational steps: First, you compute the covariance matrix of the data.

Then, you use Octave/MATLAB’s SVD function to compute the eigenvectors <em>U</em><sub>1</sub><em>,U</em><sub>2</sub><em>,…,U<sub>n</sub></em>. These will correspond to the principal components of variation in the data.

Before using PCA, it is important to first normalize the data by subtracting the mean value of each feature from the dataset, and scaling each dimension so that they are in the same range. In the provided script ex7 pca.m, this normalization has been performed for you using the featureNormalize function.

After normalizing the data, you can run PCA to compute the principal components. You task is to complete the code in pca.m to compute the principal components of the dataset. First, you should compute the covariance matrix of the data, which is given by:

where <em>X </em>is the data matrix with examples in rows, and <em>m </em>is the number of examples. Note that Σ is a <em>n </em>× <em>n </em>matrix and not the summation operator.

After computing the covariance matrix, you can run SVD on it to compute the principal components. In Octave/MATLAB, you can run SVD with the following command: [U, S, V] = svd(Sigma), where U will contain the principal components and S will contain a diagonal matrix.

Figure 5: Computed eigenvectors of the dataset

Once you have completed pca.m, the ex7 pca.m script will run PCA on the example dataset and plot the corresponding principal components found (Figure 5). The script will also output the top principal component (eigenvector) found, and you should expect to see an output of about [-0.707 -0.707]. (It is possible that Octave/MATLAB may instead output the negative of this, since <em>U</em><sub>1 </sub>and −<em>U</em><sub>1 </sub>are equally valid choices for the first principal component.)

<em>You should now submit your solutions.</em>

<h2>2.3        Dimensionality Reduction with PCA</h2>

After computing the principal components, you can use them to reduce the feature dimension of your dataset by projecting each example onto a lower dimensional space, <em>x</em><sup>(<em>i</em>) </sup>→ <em>z</em><sup>(<em>i</em>) </sup>(e.g., projecting the data from 2D to 1D). In this part of the exercise, you will use the eigenvectors returned by PCA and project the example dataset into a 1-dimensional space.

In practice, if you were using a learning algorithm such as linear regression or perhaps neural networks, you could now use the projected data instead of the original data. By using the projected data, you can train your model faster as there are less dimensions in the input.

<h3>2.3.1       Projecting the data onto the principal components</h3>

You should now complete the code in projectData.m. Specifically, you are given a dataset X, the principal components U, and the desired number of dimensions to reduce to K. You should project each example in X onto the top K components in U. Note that the top K components in U are given by the first K columns of U, that is U reduce = U(:, 1:K).

Once you have completed the code in projectData.m, ex7 pca.m will project the first example onto the first dimension and you should see a value of about 1.481 (or possibly -1.481, if you got −<em>U</em><sub>1 </sub>instead of <em>U</em><sub>1</sub>).

<em>You should now submit your solutions.</em>

<h3>2.3.2       Reconstructing an approximation of the data</h3>

After projecting the data onto the lower dimensional space, you can approximately recover the data by projecting them back onto the original high dimensional space. Your task is to complete recoverData.m to project each example in Z back onto the original space and return the recovered approximation in X rec.

Once you have completed the code in recoverData.m, ex7 pca.m will recover an approximation of the first example and you should see a value of about [-1.047 -1.047].

<em>You should now submit your solutions.</em>

<h3>2.3.3       Visualizing the projections</h3>

Figure 6: The normalized and projected data after PCA.

After completing both projectData and recoverData, ex7 pca.m will now perform both the projection and approximate reconstruction to show how the projection affects the data. In Figure 6, the original data points are indicated with the blue circles, while the projected data points are indicated with the red circles. The projection effectively only retains the information in the direction given by <em>U</em><sub>1</sub>.

<h2>2.4        Face Image Dataset</h2>

In this part of the exercise, you will run PCA on face images to see how it can be used in practice for dimension reduction. The dataset ex7faces.mat contains a dataset<a href="#_ftn3" name="_ftnref3"><sup>[3]</sup></a> X of face images, each 32 × 32 in grayscale. Each row of X corresponds to one face image (a row vector of length 1024). The next step in ex7 pca.m will load and visualize the first 100 of these face images (Figure 7).

Figure 7: Faces dataset

<h3>2.4.1       PCA on Faces</h3>

To run PCA on the face dataset, we first normalize the dataset by subtracting the mean of each feature from the data matrix X. The script ex7 pca.m will do this for you and then run your PCA code. After running PCA, you will obtain the principal components of the dataset. Notice that each principal component in U (each row) is a vector of length <em>n </em>(where for the face dataset, <em>n </em>= 1024). It turns out that we can visualize these principal components by reshaping each of them into a 32 × 32 matrix that corresponds to the pixels in the original dataset. The script ex7 pca.m displays the first 36 principal components that describe the largest variations (Figure 8). If you want, you can also change the code to display more principal components to see how they capture more and more details.

<h3>2.4.2       Dimensionality Reduction</h3>

Now that you have computed the principal components for the face dataset, you can use it to reduce the dimension of the face dataset. This allows you to use your learning algorithm with a smaller input size (e.g., 100 dimensions) instead of the original 1024 dimensions. This can help speed up your learning algorithm.

Figure 8: Principal components on the face dataset

Figure 9: Original images of faces and ones reconstructed from only the top 100 principal components.

The next part in ex7 pca.m will project the face dataset onto only the first 100 principal components. Concretely, each face image is now described by a vector <em>z</em><sup>(<em>i</em>) </sup>∈ R<sup>100</sup>.

To understand what is lost in the dimension reduction, you can recover the data using only the projected dataset. In ex7 pca.m, an approximate recovery of the data is performed and the original and projected face images are displayed side by side (Figure 9). From the reconstruction, you can observe that the general structure and appearance of the face are kept while the fine details are lost. This is a remarkable reduction (more than 10×) in the dataset size that can help speed up your learning algorithm significantly. For example, if you were training a neural network to perform person recognition (gven a face image, predict the identitfy of the person), you can use the dimension reduced input of only a 100 dimensions instead of the original pixels.

<h2>2.5        Optional (ungraded) exercise: PCA for visualization</h2>

Figure 10: Original data in 3D

In the earlier <em>K</em>-means image compression exercise, you used the <em>K</em>-means algorithm in the 3-dimensional RGB space. In the last part of the ex7 pca.m script, we have provided code to visualize the final pixel assignments in this 3D space using the scatter3 function. Each data point is colored according to the cluster it has been assigned to. You can drag your mouse on the figure to rotate and inspect this data in 3 dimensions.

It turns out that visualizing datasets in 3 dimensions or greater can be cumbersome. Therefore, it is often desirable to only display the data in 2D even at the cost of losing some information. In practice, PCA is often used to reduce the dimensionality of data for visualization purposes. In the next part of ex7 pca.m, the script will apply your implementation of PCA to the 3dimensional data to reduce it to 2 dimensions and visualize the result in a 2D scatter plot. The PCA projection can be thought of as a rotation that selects the view that maximizes the spread of the data, which often corresponds to the “best” view.

Figure 11: 2D visualization produced using PCA

<h1>Submission and Grading</h1>

After completing various parts of the assignment, be sure to use the submit function system to submit your solutions to our servers. The following is a breakdown of how each part of this exercise is scored.

<table width="545">

 <tbody>

  <tr>

   <td width="262"><strong>Part</strong></td>

   <td width="197"><strong>Submitted File</strong></td>

   <td width="87"><strong>Points</strong></td>

  </tr>

  <tr>

   <td width="262">Find Closest Centroids</td>

   <td width="197">findClosestCentroids.m</td>

   <td width="87">30 points</td>

  </tr>

  <tr>

   <td width="262">Compute Centroid Means</td>

   <td width="197">computeCentroids.m</td>

   <td width="87">30 points</td>

  </tr>

  <tr>

   <td width="262">PCA</td>

   <td width="197">pca.m</td>

   <td width="87">20 points</td>

  </tr>

  <tr>

   <td width="262">Project Data</td>

   <td width="197">projectData.m</td>

   <td width="87">10 points</td>

  </tr>

  <tr>

   <td width="262">Recover Data</td>

   <td width="197">recoverData.m</td>

   <td width="87">10 points</td>

  </tr>

  <tr>

   <td width="262">Total Points</td>

   <td width="197"> </td>

   <td width="87">100 points</td>

  </tr>

 </tbody>

</table>

You are allowed to submit your solutions multiple times, and we will take only the highest score into consideration.

<a href="#_ftnref1" name="_ftn1">[1]</a> Octave is a free alternative to MATLAB. For the programming exercises, you are free to use either Octave or MATLAB.

<a href="#_ftnref2" name="_ftn2">[2]</a> The provided photo used in this exercise belongs to Frank Wouters and is used with his permission.

<a href="#_ftnref3" name="_ftn3">[3]</a> This dataset was based on a <a href="http://itee.uq.edu.au/~conrad/lfwcrop/">cropped version</a> of the <a href="http://vis-www.cs.umass.edu/lfw/">labeled faces in the wild</a> dataset.<img decoding="async" data-recalc-dims="1" data-src="https://i0.wp.com/www.ankitcodinghub.com/wp-content/uploads/2022/04/826.png?w=980&amp;ssl=1" class="lazyload" src="data:image/gif;base64,R0lGODlhAQABAAAAACH5BAEKAAEALAAAAAABAAEAAAICTAEAOw==">

 <noscript>

  <img decoding="async" src="https://i0.wp.com/www.ankitcodinghub.com/wp-content/uploads/2022/04/826.png?w=980&amp;ssl=1" data-recalc-dims="1">

 </noscript>