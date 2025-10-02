# Automated model building with ModelCraft in Doppio

Modelcraft is an automated model building pipeline
that builds protein chains (via Buccaneer) and nucleic acids (via Nautilus)
into X-ray crystallography and cryo EM density maps
using sequence information alone and doesn’t require a homology model.
Only three inputs are required:

- Whole 3D map or half-maps (CCP4, MRC format)
- Sequences of expected proteins and nucleotides
- Map resolution

Buccaneer works very well with high resolution maps (better than 3 Å)
but models can still be generated from maps up to around 4 Å.
This tutorial will show you how to use the CCP-EM Doppio GUI.

In this example we will use the map for human haemoglobin (64 kDa) from EMDB entry EMD-3650.
You will need the following files:

- `emd_3650_half_map_1.map`
- `emd_3650_half_map_2.map`
- `emd_3650_mask.mrc`
- `5ni1_entry.fasta`

These files are provided in the tutorial data folder.
You can also download the maps from the EMDB website and the FASTA file from PDB entry 5ni1.
The mask is only found in the tutorial folder.

## Part 1) Importing project data (Optional)

You do not have to do this but you can import files
such as maps, atomic coordinates, and sequence to the project in Doppio.

`New Job -> Import -> Import other files`

Do this in separate runs for each of the files listed above.
Choose node types as follows:

| File                    | Node Type  |
|-------------------------|------------|
| emd_3650_half_map_1.map | DensityMap |
| emd_3650_half_map_2.map | DensityMap |
| emd_3650_mask.mrc       | Mask3D     |
| 5ni1_entry.fasta        | Sequence   |

Once done, one can view the maps and contents of the sequence file in the results tab.
It will take a while before the viewer shows some output.
You can give the respective files some keywords too.

## Part 2) Automated model building: Modelcraft

Create a job:

`New Job -> Atomic Model Build -> Modelcraft`

Populate the fields required, when you click on the relevant field, there will be a drop down list showing the files you have imported in Part 1.
For building a model from half maps. XXX is the job number of the import task you ran before. If not, click the  to import the files.

Job title			: EMD3650 with halfmaps
Input map 1		: Import/jobXXX/emd_3650_half_map_1.mrc
	Input map 2		: Import/jobXXX/emd_3650_half_map_2.mrc
	Input map resolution	: 3.4
	Input sequence		: Import/jobXXX/5ni1_entry.fasta
Input map mask		: Import/jobXXX/emd_3650_mask.mrc
	Max cycles of build-refine runs	: 3

For building a model from a whole map.
	Job title			: EMD3650 with whole map
Input map 1		: Import/jobXXX/emd_3650.mrc
	Input map resolution	: 3.4
	Input sequence		: Import/jobXXX/fasta.seq
	Input map mask		: Import/jobXXX/emd_3650_mask.mrc
Max cycles of build-refine runs	: 3

You will notice the option for B-factor to sharpen or blur is only available when there is nothing given for the input map 2. Any value given to this will be applied to the input map 1, we will not use this for this tutorial.
You can try running the jobs with different sharpening/blurring or resolution values. This will give different results.
If you have a molecular replacement model, you can give that as the starting model.
Input mask is optional, the pipeline will generate one from input map 1 if none is given.
If your starting model has ligand you can also provide a ligand restraint dictionary file.
N.B. Model building is stochastic so you may find the job finished before reaching max cycles. This is because, in default, Modelcraft will stop when there are no improvements in FSC average for 4 cycles.

You can also specify the number of threads to be used for the job. Usually, we will use 2 threads, this will speed up some steps in Buccaneer.

3) Click RUN.
This job will take about >20 mins.

4) Results inspection.
Look for the job you want to inspect under the JOBS tab on the left window. Once you have loaded your job, you can see the RESULTS tab on the right window. There will be a table in the first dropped down panel. Then, a viewer with the best model and map overlaid in the second panel. The following two panels show the graphs for residues built and FSC average on each cycle, respectively.
N.B. If the pipeline only ran one build-refine cycle, there would be no graphs.

5) Viewing results on Coot. 
In the I/O tab on the right window, you will see a list of input and output files. By default, the best model is named modelcraft.cif. You can select the file you want to view and any available viewer will be active. The rest of the output from intermediate runs are in their respective folders found in the job’s working directory. The terminal button will open the job directory in a terminal window.

In Coot you use the ‘Display Manager’ to select the maps and model. Use the sequence viewer (Draw → Sequence View) to see the sequence of the built fragments. Clicking on the residue will orientate the view.

Do the fragments look correct? Can you see any errors in the structure, e.g. sequencing errors or register errors, and if so can you manually correct them in Coot? 
Are there incorrectly built sequences that should be removed?
At the end of the fragments, can you manually build additional residues?
If you have time you can ‘walk’ through the structure by pressing space and this moves the focus by one residue at a time. You can then optimise each residue’s fit to density and stereochemistry (see Coot tutorial for further details).

Cheat hint: load the deposited model with the PDB id : 5ni1.
You can compare this fully sequenced model with the model generated by Buccaneer.

When you are ready you can save the edited coordinates (File → Save Coordinates) and run Refmac (see Refmac tutorial).

updated 4/10/2023

## References

Buccaneer references:
Cowtan, K. The Buccaneer software for automated model building. Acta Cryst D 62, 1002-101, 2006
Cowtan, K. Fitting molecular fragments into electron density. Acta Cryst D64, 83-89, 2008.
Hoh, S.W., Burnley, T., & Cowtan, K. Current approaches for automated model building
into cryo-EM maps using Buccaneer with CCP-EM. Acta Cryst D 76, 531-541, 2020.
Bond, P., Cowtan, K. ModelCraft: an advanced automated model-building pipeline using Buccaneer. Acta Cryst D 78, 1090-1098, 2022.

CCP-EM reference:
Burnley, T., Palmer, C.M. & Winn, M. Recent developments in the CCP-EM software suite. Acta Cryst D73, 469-47, 2017.

More on normalized expected map:
Warshamanage, R., Yamashita, K. & Murshudov, G. N. EMDA: A Python package for Electron Microscopy Data Analysis. bioRxiv, 2021.07.26.453750, 2021
Yamashita, K., et al., Cryo-EM single-particle structure refinement and map calculation using Servalcat. Acta Cryst D 77, 1282-1291, 2021

## Contact

Please report any issues or bugs to [ccpem@stfc.ac.uk](mailto:ccpem@stfc.ac.uk).
It’s much appreciated and helps us make the software better.

