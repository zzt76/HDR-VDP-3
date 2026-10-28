# Preserved upstream documentation and notices

## hdrvdp3.py:1

HDR-VDP-2 compute visually significant differences between an image pair.

diff = HDRVDP( test, reference, color_encoding, pixels_per_degree, options )

Parameters:
test - image to be tested (e.g. with distortions)

reference - reference image (e.g. without distortions)

color_encoding - color representation for both input images. See below.

pixels_per_degree - visual resolution of the image. See below.

options - cell array with { ‘option’, value } pairs. See the list of options below. Note if unknown
or misspelled option is passed, no warning message is issued.

The function returns a structure with the following fields:
 Q - Quality correlate (higher value, lower quality).
 Q_MOS -[Depreciated, do not use] Mean-opinion-score prediction (from 0 to 100, where 100 is the highest quality)

Test and references images are matrices of size (height, width, channel_count). If there is only one channel, it is
assumed to be achromatic (luminance) with D65 spectra. The values must be given in absolute luminance units (cd/m^2).
If there are three color channels, they are assumed to be RGB of an LCD display with red, green and blue LED backlight.
If different number of color channels is passed, their spectral emission curve should be stored in a comma separated
text file and its name should be passed as ‘spectral_emission’ option.

Note that the current version of HDR-VDP does NOT take color differences into account. Spectral channels are used to
properly compute luminance sensed by rods and cones.

COLOR ENCODING:

HDR-VDP-2 requires that the color encoding is specified explicitly to avoid mistakes when passing images. HDR-VDP
operates on absolue physical units, not pixel values that can be found in images. Therefore, it is necessary to specify
how the metric should interpret input images. The available options are:

‘luminance’ - images contain absolute luminance values provided in photopic cd/m^2. The images must contain exactly one
color channel.

‘luma-display’ - images contain grayscale pixel values, sometimes known as gamma-corrected luminance. The images must
contain exactly one color channel and the maximum pixel value must be 1 (not 256). It corresponds to a gray-scale
channel in YCrCb color spaces used for video encoding. Because ‘luma’ alone does not specify luminance, HDR-VDP-2
assumes the following display model:

L = 99 * V^2.2 + 1,

where L is luminance and V is luma. This corresponds to a display with 2.2 gamma, 100 cd/m^2 maximum luminance and
1 cd/m^2 black level.

‘sRGB-display’ - use this color encoding for standard (LDR) color images. This encoding assumes an sRGB display, with
100 cd/m^2 peak luminance and 1 cd/m^2 black level. Note that this is different from sRGB->XYZ transform, which assumes
the peak luminance of 80 cd/m^2 and 1 cd/m^2 black level. The maximum pixel value must be 1 (not 256).

‘rgb-bt.709’ - use this color space for HDR images AFTER they are at least roughly calibrated in absolute photometric
units. The encoding assumes the ITU-R BT.709 RGB color primaries (the same as for sRGB), but also non-gamma corrected
RGB color space.

‘XYZ’ - input image is provided as ABSOLUTE trichromatic color values in CIE XYZ (1931) color space. The Y channel must
be equal luminance in cd/m^2.

PIXELS_PER_DEGREE:
This argument specifies the angular resolution of the image in terms of the number of pixels per one visual degree.
It will change with a viewing distance and display resolution. A typical value for a stanard resolution computer display
 is around 30. A convenience function hdrvdp_pix_per_deg can be used to compute pixels per degree parameter.

OPTIONS: The options must be given as name-value pairs in a cell array. Default values are given in square brackets.

‘surround_l’ - [mean value of each color channel] luminance/intensity of the surround, which is assumed to be uniform
outside the input images. The default value is 1e-5 (10^-5), which corresponds to almost absolute darkness. Note that
surround_l=0 should be avoided. It is unrealistic to expect absolutely no light in physically plausible environment.
The special value -1 makes the surround surround_l equal to the geometric mean of the image.

‘spectral_emission’ -name of the comma separated file that contains spectral emission curves of color channels of
reference and test images.

‘rgb_display’ - [ccfl-lcd] use this option to specify one of the predefined emission spectra for typical displays.
Availaeble options are: crt - a typical CRT display ccfl-lcd - an LCD display with CCFL backlight led-lcd - an
LCD display with LED backlight

The following are the most important options used to fine-tune and calibrate HDR-VDP:
‘peak_sensitivity’ - absolute sensitivity of the HDR-VDP
‘mask_p’ - excitation of the visual contrast masking model
‘mask_q’ - inhibition of the visual contrast masking model

EXAMPLE: The following example creates a luminance ramp (gradient), distorts it with a random noise and computes
detection probabilities using HDR-VDP.

reference = logspace( log10(0.1), log10(1000), 512 )’ * ones( 1, 512 );
test = reference .* (1 + (rand( 512, 512 )-0.5)*0.1);
res = hdrvdp( test, reference, ‘luminance’, 30, { ‘surround_l’, 13 } );
clf; imshow( hdrvdp_visualize( res.P_map, test ) );

BUGS and LIMITATIONS:

If you suspect the predictions are wrong, check first the Frequently Asked Question section on the HDR-VDP-2 web-site
(http://hdrvdp.sourceforge.net). If it does not help, post your problem to the
HDR-VDP discussion group: http://groups.google.com/group/hdrvdp (preffered) or send an e-mail directly to the author.

REFERENCES

The metric is described in the paper: Mantiuk, Rafat, Kil Joong Kim, Allan G. Rempel, and Wolfgang Heidrich. ?
HDR-VDP-2: A Calibrated Visual Metric for Visibility and Quality Predictions in All Luminance Conditions.?
ACM Trans. Graph (Proc. SIGGRAPH) 30, no. 4 (July 01, 2011): 1. doi:10.1145/2010324.1964935.

When refering to the metric, please cite the paper above and include the version number, for example
“HDR-VDP 2.2.0”. To check the version number, see the ChangeLog.txt. To check the version in the code, call
hdrvdp_version.txt.

Copyright © 2011, Rafal Mantiuk mantiuk@gmail.com

Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted,
provided that the above copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED “AS IS” AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT,
INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN
AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE
OF THIS SOFTWARE.

Zhendong Li 06.23 deliver into python code

## hdrvdp3.py:212


    Report if any color are out of native color gamut and clamp them
    

## hdrvdp3.py comment

    # if 'options' not in dir():
    #     options = {}


## hdrvdp3.py comment

    # Compute spatially - and orientation - selective bands
    # Process reference first to reuse bb_padvalue


## hdrvdp3.py comment

        # REMOVED: Transform CSF linear sensitivity to the non - linear space of the
        # photoreceptor response
        # CSF_b = CSF_b. * 1. / hdrvdp_joint_rod_cone_sens_diff(10. ^ log_La_rs, metric_par);


## hdrvdp3.py comment

                # Find the dominant frequency and use the sensitivity for
                # that frequency (more robust than fft filtering used
                # before)


## hdrvdp3.py comment

    # Check if the image is in plausible range and report a warning if not.
    # This is because the metric is often misused and used for with
    # non-absolute luminace data.


## hdrvdp_visual_pathway.py:1

HDRVDP_VISUAL_PATHWAY (internal) Process image along the visual pathway to compute normalized perceptual response

img - image data (can be multi-spectral) name - string with the name of this map (shown in warnings and error messages)
options - cell array with the ‘option’, value pairs bands - CSF normalized freq. bands

Copyright © 2011, Rafal Mantiuk mantiuk@gmail.com

Permission to use, copy, modify, and/or distribute this software for any purpose with or without fee is hereby granted,
provided that the above copyright notice and this permission notice appear in all copies.

THE SOFTWARE IS PROVIDED “AS IS” AND THE AUTHOR DISCLAIMS ALL WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL
IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT,
INDIRECT, OR CONSEQUENTIAL DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA OR PROFITS, WHETHER IN AN
ACTION OF CONTRACT, NEGLIGENCE OR OTHER TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR PERFORMANCE OF
THIS SOFTWARE.


## hdrvdp_visual_pathway.py comment

        # Different than in the paper because integration is done in the log
        # domain - requires substitution with a Jacobian determinant


## hdrvdp_visual_pathway.py comment

#
#


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Precompute
    # precompute common variables and structures or take them from cache
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

    # if (metric_par.do_mtf):
    #     mtf_filter = hdrvdp_mtf(rho2, metric_par)  # only the last param is adjusted
    # else:
    #     mtf_filter = np.ones(rho2.shape)


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Precompute photoreceptor non - linearity
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Optical Transfer Function
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Photoreceptor spectral sensitivity
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

    # Color space conversion
    # a = np.dot(np.reshape(L_O, (width * height, img_ch)), M_img_lmsr)


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Adapting luminance
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

        # light reduction due to senile miosis (Unified Formula based on
        # the Stanley and Davies function [Watson & Yellott 2012]


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Photoreceptor non - linearity
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

        # Remove the DC component, from
        # cone and rod pathways separately
        # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

    # TODO - check if there is a better way to do it
    # cones


## hdrvdp_visual_pathway.py comment

    # mm = np.mean(P_C)
    # P_C = P_C - mm
    # rods
    # mm = np.mean(P_LMR[:, :, 2])


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Achromatic response
    # == == == == == == == == == == == == == == == == =


## hdrvdp_visual_pathway.py comment

    # == == == == == == == == == == == == == == == == =
    # Multi - channel decomposition
    # == == == == == == == == == == == == == == == == =


## hdrvdp_iturgb2native.py:7


    Find a transformation matrix from one of the standard ITU color spaces into the native color space of the display, given its emission spectra.
    The matrix operates on relative values: RGB=[1 1 1] result in Y=1
    
