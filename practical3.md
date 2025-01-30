Before you start....
--------------------

You will need to download a new restart file prior to embarking on the
experiments with modern ocean circulation.

Change to the cgenie\_output directory and type:

\$ wget \--no-check-certificate
http://www.seao2.info/cgenie/labs/UoB.2015/LAB\_2.SPIN.tar.gz

This downloads an archived/compressed copy of the 10,000 year spin-up
experiment LAB\_2.SPIN. Extract the contents of this archive by typing:

\$ tar xfzv LAB\_2.SPIN.tar.gz

You'll then need to change directory back to genie-main to run the
model.

Tracking Ocean Circulation
--------------------------

You are going to run a brief experiment in which you will be injecting a
conservative 'dye' tracer in the ocean to have a look at the model ocean
circulation.

**Note that as the model will run rather SLOWER than in the snowball
configuration, you might want to think carefully of making use of
cluster queuing possibilities (i.e. running multiple experiments at once
in the background).**

**[North Atlantic]{.underline}**

The ocean biogeochemistry module ('BIOGEM') in cGENIE provides a
framework for applying time- and spatially-variable 'forcings' of the
Earth system -- fluxes or boundary conditions that can be prescribed for
any gas, dissolved substance (including temperature and salinity), or
particulate matter. Examples include freshwater input (== a negative
salinity flux forcing) of the North Atlantic to alter ocean circulation,
fossil fuel CO2 emissions to the atmosphere (== a CO2 gas flux forcing),
or aeolian iron supply to the surface ocean (a 2-D dust flux forcing).

For example: view the user-config file: LAB\_2.colorinjection -- you
will see the following lines (under the heading: '\# \-\-- FORCINGS
\-\--')

bg\_par\_forcing\_name=\"pyyyyz.Fred\"

bg\_par\_force\_point\_i=22

bg\_par\_force\_point\_j=33

bg\_par\_force\_point\_k=8

bg\_par\_ocn\_force\_scale\_val\_48=0.0

The first line points cGENIE to a directory located
in cgenie.muffin/genie-forcings that contains a set of files that define
what geochemical property is going to be altered plus information about
how the magnitude of the forcing changes with time. There are then three
lines (bg\_par\_force\_point\_i=20, \...) that specify the location in
the ocean of the geochemical forcing is going to be applied. The point
sources are specified in (i,j,k) coordinates, which in this case is
(22,33,08).

For the ocean model resolution we are using, the grid is 36×36×16,
longitude (i) is counted from left-to-right (1 to 36); latitude (j) is
counted from bottom-to-top (1 to 36); level depth (k) is counted
downwards from top-to-bottom (16 down to 1). Thus, (22,33,08) is a
release of tracer in the North Atlantic, a little south of Greenland,
and intermediate depth (level = 8 out of 16). Refer to the Figure (GENIE
grid.pdf below) for how the horizontal and vertical grid is specified.
Finally, there is a scaling parameter
(bg\_par\_ocn\_force\_scale\_val\_48) which modifies the magnitude of
the flux to be applied (in units of mol yr-1). (Section 4 in the User
Manual (see Useful Resources) describes the original and most flexible
provision for applying time dependent forcings.)

BIOGEM has two tracers defined for this purpose -- 'blue' and 'red'.
Make a copy of the userconfig file LAB\_2.colorinjection to something
that you will remember e.g. LAB\_2.colorinjection\_tracertest1 then,
with WinSCP or Cyberduck & Atom, edit the parameter controlling the flux
of red dye to read:

bg\_par\_ocn\_force\_scale\_val\_48=1.0E12

which specifies a flux of 1.0x1012 (mol yr-1) rather than zero as given.

Note that the base-config you will be using is different from
previously: cgenie.eb\_go\_gs\_ac\_bg.worjh2.rb -- this specifies a 16
vertical levels ocean and also includes seasonality of solar insolation.
So, initially at least, you need to run this interactively (i.e. at the
command line) rather than submitting it to the cluster. As before, once
you have run this once, then you can submit later runs to the cluster.

 

Run the model for whatever you like, 20 years will probably do. Use the
restart experiment you have just downloaded to start from:

\$ ./runmuffin.sh cgenie.eb\_go\_gs\_ac\_bg.worjh2.rb LABS
LAB\_2.colorinjection\_tracertest1 20 LAB\_2.SPIN

Note: Occasionally PuTTY throws a wobbly and doesn\'t wrap around
properly at the command line, meaning even though you have pasted in the
above absolutely correctly, it looks weird and will say \"file not
found\" or similar. As far as I know, there is no easy way round this,
other than to manually type in the above, rather than copying and
pasting.  Uhhhhhhhhh.

Now view the results in Panoply. Focus on how the Red tracer
distribution evolves in th/4 e 3D time-slice files saved
in netCDF format (fields\_biogem\_3d.nc). You can follow the progress of
the dye (and hence diagnose the properties of ocean circulation in the
model) by plotting vertical and/or horizontal slices that go through (or
near) the cell location in which you inject the dye tracer in the
3D netCDF file. The variable is called \'ocn\_colr\'. Note that Panoply
appears to 'count' the ocean layers in the opposite direction to the way
in which the ocean model is actually counting them -- the correct
definition is with '1' being very deepest level possible.

You can also view the tracer distributions in terms of a water-column
integrated tracer inventory (netCDF variable name: ocn\_int\_colr; long
name: colr water-column integrated tracer inventory) in the
2D netCDF output fields\_biogem\_2d.nc. (See: Sabine et al. \[2004\] for
the use of water column integrals in the context of the distribution of
anthropogenic CO2 uptake and storage.)

Changes in tracer inventory with time can be tracked in the time-series
file stored in your model output: biogem\_series\_ocn\_colr.res.

You can also plot the overturning circulation from the 2D netCDF file --
variable phys\_opsi == global overturning streamfunction, phys\_opsia ==
overturning in the Atlantic to provide a visualization of the
large-scale ocean circulation that drives tracer movement.

Spend a little while altering the flux in your config
file (bg\_par\_ocn\_force\_scale\_val\_48) and/or location
(bg\_par\_force\_point\_i, bg\_par\_force\_point\_j, bg\_par\_force\_point\_k)
of dye input. Note how you can use 'tracers' to help diagnose (and
better understand) the circulation of the ocean.

**[Mediterranean Sea\
]{.underline}**

An interesting (honest!) and illustrative exercise is to use the dye
tracer to pick out the path taken by Mediterranean Intermediate Water.
Despite the low resolution of the cGENIE ocean circulation model
component and the highly restricted representation of the Mediterranean,
the model does predict a salty Mediterranean as a consequence of
precipitation minus evaporation in this basin (and its catchments) being
negative. And this water makes its way out in the subsurface into the
Atlantic.

Simply specify a dye injection somewhere in the Mediterranean (be
careful with the restricted depth of the Mediterranean -- if you inject
too deeply (into the crust!) then you will not see anything! Refer to
the figure for the depth level (k) number of the maximum depth of the
water column in each location), and it is better to inject it relatively
close to the opening of the gateway (try some different locations and
see which ones produce a reasonably instructive tracing of Mediterranean
outflow). Run for e.g. 20 or 50 years (from the provided spin-up). Then:

\(1) View the dye-tagged plume of Mediterranean Intermediate Water by
plotting a lat-lon slice (from the 3D netCDF file). This will give you
the depth of the plume. You can also view the water-column integrated
distribution (2D netCDF).

\(2) Try viewing the plume via a lat-depth slice. Refer to the figure to
determine the 'i' value up the Atlantic that will just graze the edge of
what passes for Spain at this low model resolution. 

Which direction does it head after exiting the Mediterranean? Is this
'realistic'?

"Poking the climate beast" \[Wally Broecker\]
---------------------------------------------

**[\'Hosing the N Atlantic\']{.underline}**

Instead of adding a dye tracer, you could add fresh water to assess the
sensitivity of the Atlantic Meridional Overturning Circulation (AMOC) to
collapse in a classic 'hosing' experiment. The user-config file for this
is called: LAB\_2.hosing. The default (i,j) location of the flux input
is the same (as the dye tracer), but now the injection at the surface
(level: k=16). Note that the forcing of the salinity tracer is negative
(freshwater = negative salinity compared to sea-water)!

To orientate you in freshwater forcing
space: bg\_par\_ocn\_force\_scale\_val\_2= -2.0E17 should be sufficient
to make 'stuff happen' and quickly. BUT, this is a pretty extreme flux.
Much more than this and the model may crash or at the very least, you'll
be left with a large freshwater pond in the North Atlantic ...

*A brief note on units \... the freshwater forcing is implemented as
negative salinity, just to really screw with your mind. The generic
internal cGENIE model units for the forcing end up as PSU kg-1 yr-1.
Which sort of does not make much sense \... Start, by thinking of a
value of bg\_par\_ocn\_force\_scale\_val\_2 of -34.9 as equivalent to
taking all the salt out of 1 kg of freshwater (since the mean global
salinity is 34.9 PSU). Or equivalently, since the ocean volume is fixed,
an applied forcing value of -34.9 is equivalent to adding 1 kg of
freshwater to a (surface) box. So, a value
of bg\_par\_ocn\_force\_scale\_val\_2 of -3.49x10^4^ (-3.49E04) would be
a flux of 1 m3 yr-1 (1000 kg m-3) of freshwater. So, if we set
(bg\_par\_ocn\_force\_scale\_val\_2=-1.0E18), the freshwater flux is:*

*1.0x10^18^/3.49x10^4^ = 2.8653x10^13^ m3 yr-1.*

*The literature invariably gives freshwater fluxes in units
of Sv (10^6^ m3 s-1). So in the example, the freshwater flux is:
9.0797x10^5^m3 s-1 (365.25x24x3600 = 31557600 s yr-1). Or 0.9 Sv. Read
the literature ... but generally, fluxes of ca. 0.05 Sv and larger (and
to quite specific places) are applied in models in order to induce a
collapse of the AMOC. ***\
**

Again, rename the user-config to something memorable for you, then run
the model for e.g. 20 years using the same restart e.g.:

\$ ./runmuffin.sh cgenie.eb\_go\_gs\_ac\_bg.worjh2.rb LABS
LAB\_2.hosing\_test1 20 LAB\_2.SPIN

20 years should be long enough to see a collapse start to occur, but you
might want to run the model for longer (and it can be submitted as a
job, of course). Running for longer will also allow you to have a
smaller, less extreme (and maybe more realistic) freshwater input flux.

The most obvious property of the Earth system to follow is the Atlantic
overturning strength (biogem\_series\_misc\_opsi.res). The AMOC
stream-function (in fields\_biogem\_2d.nc 2-D time-slice netCDF results
file, field: phys\_opsia) is also illustrative. You can also try and
identify the salinity anomaly (see below for how to create anomaly
plots) due to freshwater input in the 3D salinity tracer field.

 

There are also important impacts on surface air temperatures and maybe
sea-ice extent (in fields\_biogem\_2d.nc). Note the importance (sort of)
of the AMOC in transporting heat to the N Atlantic region (another
example of where the film The Day After Tomorrow was not entirely
inaccurate in this particular respect). Be aware of the possibility of
climate impacts far from the location of fresh water forcing. Look out
for any significant-looking impacts on sea-ice extent, etc.

 

Share figures with your neighbours and discuss!

A little Panoply visualisation exercise
---------------------------------------

To more easily assess some of these impacts (and for other sorts of
analysis) it is possible to create an anomaly (difference) map in
Panoply:

1\. First open a dataset, e.g., atm\_temp (surface air temperature) in
the 2D netCDF file. You can either double-click the variable name, or,
with the variable name highlighted, click the 'Create Plot' icon.

2\. Now, with the atm\_temp still selected (and the first plot window
still open), click on the 'Combine Plot' icon. A dialogue box will
appear and ask you to select a plot to combine the new one with. Make
sure the name of your first plot window is selected/highlighted. Click
'Combine'.

3\. You now have a plot window that by default it is showing you the
difference between two identical (in time) slices. The two different
slices are labelled Array 1 (LH side) and Array 2 (RH side). Keep one
array (Array 1) fixed to the initial (year 1 (centered on 0.5)) and vary
the year in the second array (Array 2). Note that you can select in
Panoply whether Array 1 -- Array 2 is plotted, or Array 2 -- Array 1, or
various proportional or relative differences.

Note that you can \"Fit to Data\" in the Scale tab, but better is to
define a Scale Range yourself and centre the scale so that no change is
white, picking a sensible colour scale from the Colour Table. E.g. with
positive (warm) deviations = red and negative = blue (an often used
convention in climate field plotting). *NEO\_giss\_temp\_anom* might be
a good option for this atmospheric temperature example.

 

Share an anomaly plot with your neighbours and explore the plotting
possibilities!

 

You can also plot ocean current fields which is sort-of fun and maybe
even informative(!):

1\. In the 3D netCDF file, the three components of ocean velocity are
represented by the variables: ocean velocity -- u (Eastwards), ocean
velocity -- v (Northwards), and ocean velocity -- w (upwards). 2. Open
up velocity -- u. Chose 'lon-lat'.

2\. Select/highlight velocity -- v. and click on the 'Combine Plot' icon
(as per before).

3\. Rather than a difference map, which is what you get by default, i.e.
'Array 1 -- Array 2' -- from the dropdown menu (next to the
'Interpolate' button) select 'Vector Magnitude'.

4\. You should have a color contoured (or not if you prefer plotting
without contouring on) map of ocean current speed, with velocity vectors
(direction and magnitude) overlain. You'll need to re-scale the velocity
vectors to properly see them -- from the 'Vectors' tab -- change the
'Reference Value' to e.g. 0.1.

When fresh-water hosing -- look out for impacts on the N. Atlantic
current system associated with the AMOC.

5\. You can repeat this for deeper depth levels in the ocean -- e.g.
between about 1500 and 2000 m is a good place to go looking for the
Western boundary current (and AMOC return) in the model (such as it
exists at this low resolution) but you'll need to re-scale the Reference
Value again (e.g. to 0.01 to less).\
\
\
Again, share an anomaly plot with your neighbours and explore the
plotting possibilities!

Optional: Hosing
----------------

What is the largest freshwater flux that can be sustained without
'collapsing' the AMOC? Is there a 'threshold' ('tipping point') of
freshwater input, beyond which the AMOC rapidly decreases in strength?
Is the precise location of the freshwater input important (i.e., try
tipping it in somewhere else)? What would you expect to see in
the paleo (e.g., ice core) record of both hemispheres if such
a shutdown occurred in the past? Are any other major regions of deep
water formation (where are they) sensitive to freshwater perturbation
and what are the consequences (could it happen in the future)?

Optional: Anti-Hosing
---------------------

There are questions concerning past changes in the AMOC as to whether it
is 'pushed' or 'pulled'. i.e. if the AMOC shoals in depth and/or
weakens, is it because its production has weakened, or as Antarctic
Bottom water (AABW) strengthened and 'pushed' it out of the way (to
shallower depths)? What you might try then is to inject salt in the
Southern Ocean as opposed to fresh water in the North Atlantic. All you
need do is pick an appropriate grid point (this is worth thinking about
carefully and maybe testing different locations) and rather than giving
the parameter bg\_par\_ocn\_force\_scale\_val\_2 a negative value, you
give it a positive one. (Start by trying similar magnitudes of value as
before and see what happens.) Is the AMOC (for the same magnitude of
forcing) more sensitive to being 'pushed' or 'pulled'? (Obviously the
answer will very much depend on where the perturbations are being
applied.)

Optional: Ocean circulation response to transient warming (or cooling)
----------------------------------------------------------------------

A current concern regarding global warming is the ocean circulation
response to a strong warming of the surface, as it is assumed (and
demonstrated in models) to result in surface stratification of the
ocean, likely restricting the nutrient supply to phytoplankton and
reducing ventilation of the ocean interior with dissolved oxygen. You
can explore the transient response of ocean circulation to warming by
simply adjusting the radiative forcing parameter used in the snowball
Earth experiments: ea\_radfor\_scl\_co2. By default in the modern
continental configuration it has a value of 1.0, corresponding to 278
ppm atmospheric CO2. A value of 2.0 would reflect warming equivalent to
556 ppm CO2. And 3.0 more like an end-of-the-century warming. Note that
you are applying the warming instantaneously by manipulating the climate
system in this way and hence the changes will be more extreme than those
occurring over the time-scale of this century. Also note that a cooling
could be applied instead. A user-config -- LAB\_2.EXAMPLE -- is provided
as a template for these experiments. Potentially interesting properties
of the Earth system to look at include sea-ice extent and AMOC strength
(in the ASCII time-series files), and the overturning streamfunction and
sea-ice extent (2-D netCDF output). How much radiative forcing is
required to collapse the AMOC? What atmospheric CO2 value does this
approximately correspond to?
