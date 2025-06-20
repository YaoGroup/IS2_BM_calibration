# IS2_BM_calibration
For downloading and calibrating IceSAT-2 and Bedmap thickness data (in preparation for injecting into PINNs in diffice_JAX for viscosity inversion).

**Bedmap data download:** [This site](https://bedmap.scar.org/) lets you visualize Bedmap 1, 2, and 3 tracks on Antarctica to decide which tracks/data to download for your ice shelf. You can then download the data with these links: [Bedmap1](https://ramadda.data.bas.ac.uk/repository/entry/show?entryid=synth:925ac4ec-2a9d-461a-bfaa-6314eb0888c8:L0JFRE1BUDFfMTk2Ni0yMDAwX0FJUl9CTTE=), [Bedmap 2](https://ramadda.data.bas.ac.uk/repository/entry/show?entryid=0f90d926-99ce-43c9-b536-0c7791d1728b), [Bedmap 3](https://ramadda.data.bas.ac.uk/repository/entry/show?entryid=a72a50c6-a829-4e12-9f9a-5a683a1acc4a). For a viscosity inversion problem, you’ll want the thickness profiles (not raw radar data), so it’s best to download the relevant shapePoints gpkg files. For example, we needed data from INGV 2003 TALOS-DOME, PRIC 2016 CHA2 & 2017 CHA3, and RNRF 1971 LAMBERT-AMERY for the Amery ice shelf. 
*Optional: Convert these gpkgs to gpqs using gpkg_to_gpq.py to speed up data loading later.*

**IceSAT-2 data download:** See *icesat2_download.ipynb* to extract relevant IceSAT-2 data using Earthaccess and save it as one combined gpq.

**Bedmap/IceSAT-2 comparison & calibration:** *IS2_BM_comp.ipynb* loads in IceSAT-2 and Bedmap data, converts IceSAT-2 heights to thicknesses using Eqn (1) from [Chartrand+23](https://www.cambridge.org/core/journals/journal-of-glaciology/article/comparison-of-contemporaneous-airborne-altimetry-and-icethickness-measurements-of-antarctic-ice-shelves/6D47F810CA81FB90827EDED14107E853), then calibrates IceSAT-2 thicknesses to the Bedmap dataset by optimizing the firn-air column thickness and density parameters and saves the new IceSAT-2 data as a gpq.

**Training the PINN:** To solve for ice shelf viscosity, I modified the [train_pinns_iso example notebook](https://github.com/YaoGroup/DIFFICE_jax/blob/main/examples/colab/train_pinns_iso.ipynb) from diffice_JAX. My modified notebooks are *train_pinns_iso_ATL06_amery.ipynb* (for IceSAT-2) and *train_pinns_iso_bedmap.ipynb* (for Bedmap). 

The big changes are in the "Data Loading" section, as I adjusted the original grid to overlap the Bedmap and IceSAT-2 tracks respectively and injected these thickness tracks into the PINN directly. 
Another addition are the functions *save_training_snapshots* and *create_animation_gifs*, which are used to save plots of PINN-inferred viscosity and residuals through iterations of optimization and animate them into videos. Ideally this code should be run on with GPUs on Sherlock with 100k Adam iterations and 200k L-BFGS, saving snapshots every 1000 iterations.

Example videos created from these snapshots include *viscosity_Adam_training.mp4*, *e1_residual_Adam_training.mp4*, and *e1_residual_Adam_training.mp4*. These were created by PINNs trained on Bedmap data.

**Extra:** *gpkg_to_gpq.py* converts gpkg files to gpqs :-)
