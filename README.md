PyEMD: Earth Mover's Distance for Python
========================================
by Gary Doran (<gary.doran@case.edu>)

Overview
--------
An efficient, accurate, easy-to-use EMD implementation in C with Python
wrapper.

Installation
------------
This package can be installed in two ways (the easy way):

    # If needed:
    # pip install numpy
    # pip install scipy
    pip install -e git+https://github.com/garydoranjr/pyemd.git#egg=misvm

or by running the setup file manually

    git clone [the url for pyemd]
    cd pyemd
    python setup.py install

Note the code depends on the `numpy` and `scipy` packages. So have those
installed first. The build will likely fail if it can't find them. For more
information, see:

 + [NumPy](http://www.numpy.org/): Library for efficient matrix math in Python
 + [SciPy](http://www.scipy.org/): Library for more MATLAB-like functionality

Why?
----
Several Python wrappers for C-based EMD implementations already exist, so why is
another one necessary? There are two popular alternative approaches, each with
their limitations:

  - *Solve a LP with GLPK:* Since the transportation problem is a special case
    of the general LP formulation, it can be solved more efficiently and with
    much less use of memory. This implementation is approximately 7-8 times
    faster than a GLPK-based solution, and uses about 500 MB of memory when the
    GLPK-based solution uses over 10 GB before it crashes my machine.  These
    figures are from problems in which samples of size ~1000 with ~100 features
    are compared using the EMD for the multiple-instance learning problems I
    study.

  - *Wrap Yossi Rubner's Implementation:* There exist several wrappers of Yossi
    Rubner's EMD code, the most popular of which is in the OpenCV library. The
    first limitation of this code is the use of single-precison versus
    double-precision floating point numbers. In practice, I have observed
    significant (on the order of 20-50%) differences between the OpenCV EMD
    implementation and this implementation, which uses double-precision floats.
    I'm not able to confirm whether the difference is entirely caused by a loss
    of precision, or issues with the OpenCV wrapper, but I have validated the
    accuracy of this implementation via a comparison to the GLPK implementation.

PyEMD is a more "Pythonic" EMD implementation than other wrappers. Only the
minimal amount of computation is done in C (the core transportation algorithm).
This means that the distance computation is done in Python using the efficient
SciPy library, and a custom, precomputed distance matrix can be easily provided.

Usage
-----
The EMD implementation can be used simply as:

    >>> from emd import emd
    >>> emd(X, Y)

where `X` and `Y` are each n-dimensional samples of points. Each argument should
be a NumPy array with n columns, but possibly different numbers of rows.

If the sample is weighted, the weights can be specified with optional
`X_weights` and `Y_weights` arguments. By default, uniform weights are used.
Because the EMD is a distance between probability measures, the *total weights
of each of the two samples must sum to 1*.

By default, the Euclidean distance between points is used. However, an optional
argument `distance` takes a string that specifies a valid distance type accepted
by the `scipy.spatial.cdist` function. Alternatively, if
`distance='precomputed'`, then a precomputed distance matrix is expected to be
supplied to the optional argument `D`.

See the docstring for a more formal description of the functionality.

Questions and Issues
--------------------
If you find any bugs or have any questions about this code, please create an
issue on [GitHub](https://github.com/garydoranjr/pyemd/issues), or contact Gary
Doran at <gary.doran@case.edu>. Of course, I cannot guarantee any support for
this software.