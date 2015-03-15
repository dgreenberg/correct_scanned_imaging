Corrects horizontal motion artifacts in imaging acquired through a scanning process.
Motion much faster than the imaging frame rate can be detected and corrected.

Simplest posible use of this function:
[p, ~, ~, ~, ~, ~, ~, data_corrected] = correct_scanned_imaging(data, template_image);

Full range of possible inputs and outputs:
[p, iter_used, corr, failed, settings, xpixelposition, ypixelposition, data_corrected, template_sampled] = ...
  correct_scanned_imaging(data,template_image,subrec,data_extra_channels,frame_list,settings,isbidirec,est_p,pregauss)

Inputs:
data -- 3D array of double precision, h x w x n. n is the number of image
frames. It is assumed that raster scanning has proceed along each row of
data, and that there are h scan lines.

template_image -- an image to which the data will be aligned. it need not be
the same size as each image in data. in theory this should be an image
without motion distortion

subrec -- the rectangular region of template_image to which the raster scan
was targeted. subrec is a vector of form [left right bottom top], and its
values are pixel coordinates in template_image. The first two elements give the
horizontal extent of the rectangle, and the second two its vertical extent.
if subrec is omitted or empty, it will be assumed that the entire template_image
was targeted in the raster scanning. The exception to this is when the numbers of rows in the
imaging data and template are equal but the number of columns is leftexclude less (see below)
in the imaging data, in which case it will be assumed that the non excluded columns of the imaging
data match all columns of the template. note that subrec's values can be negative, when an undisplaced scan only partly overlaps with the template

data_extra_channels -- extra image channels to be corrected according to the motion detected
for the channel in data. must be a 4D array, with the first 3 dimensions matching data and
the 4th dimension for image channel

frame_list -- a list of frames to be corrected in the imaging file. default is all frames

settings -- a structure whos fields contain parameters for motion
correction. Either all the following subfields should be set, or the whole
settings structure should be omitted or empty, in which case default
values will be used. The subfields and their default values are:
  *move_thresh (0.010) -- the converence threshold for changes in
  estimated displacements in pixels
  *corr_thresh (0.75) -- the minimum pearson's correlation value for a
  successful converence
  *max_iter (120) -- the maximum number of allowed Lucas-Kanade iterations
  *scanlinesperparameter (2) -- the number of scanlines between displacement
  stimations, and probably the most important parameter. Higher values
  allow noiser data to be used, while lower values allow faster
  displacement to be corrected.
  *haltcorr (0.995) -- the correlation value for which no further
  improvement will be attempted, even if the convergence criteria are not
  met

isbidirec -- was bidirectional scanning used (true or false), i.e. do even
numbered scan lines go right to left instead of left to right. default false

est_p -- estimated displacements to be used as seed values for gradient
descent. est_p should have n rows and number of columns
2 + 2 * floor(h / settings.scanlinesperparameter)
est_p can be omitted or left empty. Whether or not est_p is given,
displacements of zero will also be attempted as seed values.

pregauss -- amount of Gaussian filtering to apply to data before motion-correcting it.

leftexclude -- how many pixels to ignore at the left of the imaging data

Outputs:
p -- estimated displacement. Each row of p corresponds to one of the n
images. For each image, the displaced position of the focus will be estimated
1 + floor(h / settings.scanlinesperparameter) times per frame. the first
half of each row contains these x displacement values, and the second half
contains y displacement values. the units of the values are pixels of template_image

iter_used -- the number of Lucas Kanade iterations used for each image.

corr -- the final pearson's correlation value between each image and
template_image after convergence.

failed -- a vector containing 1's for images with failed convergence, 0's otherwise

setttings -- the settings that were used for motion correction. This will
contain the input settings and some additional fields. It can be used as
an input to another call of motioncorrect.

xpixelposition, ypixelposition -- the estimated positions of each pixel in
data after motion correction. Its values are pixel coordinates in template_image.
1st and 2nd dimensions are same as data, 3rd dimension corresponds to frame_list.

data_corrected - corrected imaging, will be the same size as template_image
with the 3rd dimension corresponding to frames and the 4th to image channels.
For example, if data has a size of 100 on the 3rd dimensions and
frame_list ranges from 51 to 60, then data_corrected will have a size of 10
on the 3rd dimension.

template_sampled - linearly interpolated values of the template image that correspond to
each pixel of the motion-displaced data. size matches xpixelposition/ypixelposition

--------------------------------------------------------------------------
INFO
For further details, see "Automated correction of fast motion artifacts for two-photon
imaging of awake animals," D.S. Greenberg & J.N.D. Kerr, Journal of
Neuroscience Methods, 2009.
http://dx.doi.org/10.1016/j.jneumeth.2008.08.020

Written and released by David S. Greenberg, Tuebingen, Germany, March 2009.
Last updated September 5, 2013

By usings this code you agree not to distribute it to anyone else, modify
it and then distribute it, or publish the code............... brother.

For general inquiries contact david@tuebingen.mpg.de or jason@tuebingen.mpg.de
This code is not supported, feel free to submit questions, problems, or
bug reports but a timely response may not be possible.
