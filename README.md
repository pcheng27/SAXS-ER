# SAXS-ER Tutorial

What is SAXS-ER?
SAXS-ER is an iterative sampling method for selecting ensembles of large biomolecules that fit the experimental SAXS data. Download it here.

Uncompress the file: tar -xvf SAXS-ER.tar.bz2, there are four folders: ACM-1.3, ACM-2.1, aMD-1.3, aMD-2.1. In ACM-1.3, we select the ensemble using EOM 1.0 after ACM simulation, while we use EOM 2.0 in ACM-2.1; In aMD-1.3, we select the ensemble using EOM 1.0 after aMD simulation, while we use EOM 2.0 in aMD-2.1.


How to use SAXS-ER using ACM?

1) Pre-installed software
   GROMACS (version 4.5,5)  we have placed an excutable mdrun_mpi under the directory /bin that can run ACM simulations.
   ATSAS (version 2.4.3-1)  the version of GAJOE is 1.3 as a component of ATSAS.
   ATSAS (version 2.7.2-5)  the version of GAJOE is 2.1 as a component of ATSAS.
2) Setup the system using GROMACS
   The following files are obtained:
   fbp.ndx     -   index of "System" and "Protein";
   fbp_pr.gro  -   the structure after MD with positional restraints
   pr.pdb      -   the structure containing the solute from fbp_pr.gro
   fbp.top     -   topology file
   md.mdp      -   a template file of MD parameters
   initmp.ami  -   a template file of ACM parematers
3) Go into the Cycle00 and run a preliminary ACM simulation with 100 ps
   cd Cycle00
   cp ../fbp.ndx .
   cp ../fbp_pr.gro .
   cp ../pr.pdb 00.pdb
   cp ../fbp.top .
   cp ../md.mdp cyc00-00.mdp
   cp ../initmp.ami . 

   edit your machinefile
   modify cyc00-00.mdp, add a random number to replace "genseed"
   ./produceAmi.py cyc00-00.ami 00.pdb
   ./grompp-cyc00-00.sh
   nohup ./mdrun-cyc00-00.sh &
4) Run cycles of SAXS-ER
   cd ..
   edit your machinefiles
   edit run-SAXSER.py to change the number of cycles
   nohup ./run-SAXSER.py &
Data processing


When both Chi and Rg converge, you need to do data processing next. In the directory ``fit``, the file ``protein_ca.ndx`` is the index for Protein and the specific C-alpha that you want to fit, and ``fit_ensemble.sh`` is to fit the ensemble.

    cd fit/
   chmod +x fit_ensemble.sh
   ./fit_ensemble.sh ../Cycle??
Then the 20 conformers in the directory 'ensemble' were fitted to the first one using the WW1 domain. you can view them by VMD. (there is also a pre-generated VMD source file "fbp.vmd" for your convenience to see the ensemble) 


Supplementary instruction


``run-SAXSER.py``       - the main program
``module_SAXSER.py``    - the module called by the main program ``run-SAXSER.py``
``produceAmi.py``       - generate ".ami" file from ``initmp.ami`` and corresponding
                          pdb file
``Fbp21ww.dat``         - experimental SAXS data
``input``               - the directory containing input files for EOM
``runCrysol.sh``        - doing crysol calculation
``selectEnsemble.sh``   - using EOM to select ensemble
``Continue Running``    - if you want to continue running after finishing some cycles
                          conuted by ten, you need modify ``run-SAXSER.py``.
                          for example:
                          START=10
                          END=20
Execute `./selectChi.sh` to extract the chi values from each cycle. ``chi.dat`` file is generated, the first column is the order number of cycles, and the second colum is the chi value of every cycle. Execute `xmgrace chi.dat` to get the curve graph of ``chi.dat``.
Execute `./selectRg.sh` to extract Rg values from each cycle. ``Rg.dat`` file is generated, the first column is the order number of cycles, and the second colum is the Rg value of every cycle. Execute `xmgrace Rg.dat` to get the curve graph of ``Rg.dat``.
Execute `./rm_files.sh` to delete some unimportant and unnecessary files with specific suffix to save your disk storage space. However, the files in "GA001/pdbs/", "GA001/curve_1/pdbs/" or "fit/" will not be removed. Please use this script carefully.
    `./rm_files.sh`
                             ``Y``   remove files with the suffix
                             ``n``   do not remove
        ``q or press 'enter' key``   exit the program

How to use SAXS-ER using aMD?


1) Pre-installed software
   AMBER (we use version 14)
   ATSAS (version 2.4.3-1)   the version of GAJOE is 1.3 as a component of ATSAS.
   ATSAS (version 2.7.2-5)   the version of GAJOE is 2.1 as a component of ATSAS.

2) Setup the system using AMBER
   The following files are obtained:
   dsam-md1.rst    - the restart file after heatup precedure
   dsam_wat.prmtop - toplogy file

   You need to run a standard MD to estimate those aMD parameters (we did 200 ns)
   prepare your amd.in

3) Go into the Cycle00 and run a preliminary aMD simulation with 100 ps

   mkdir Cycle00
   cd Cycle00
   cp ../dsam-md1.rst .
   edit your machinefile
   nohup ./cyc00-00.sh &

4) Run cycles of SAXS-ER

   cd ..
   edit your machinefiles
   edit run-SAXSER.py to change the number of cycles
   nohup ./run-SAXSER.py &
Data processing


When both Chi and Rg converge, you need to do data processing next. In the directory ``fit``, the script ``divide_pdbs.py`` is to divide 20 conformers in one file into separate pdb files, and ``fit_ensemble.sh`` is to fit the conformers.
    
    cd fit/
    chmod +x fit_ensemble.sh
    ./fit_ensemble.sh ../Cycle??
    
Then the 20 conformers in the directory 'ensemble' were fitted to the first one using the P4 subdomain, you can view them by VMD. (there is also a pre-generated VMD  source file "riboswitch.vmd" for your convenience to see the ensemble)


Supplementary instruction


``run-SAXSER.py``       - the main program
``module_SAXSER.py``    - the module called by the main program ``run-SAXSER.py``
``RNA_1.dat``           - experimental SAXS data
``input``               - the directory containing input files for EOM
``runCrysol.sh``        - doing crysol calculation
``selectEnsemble.sh``   - using EOM to select ensemble
``Continue Running``    - if you want to continue running after finishing some cycles
                          conuted by ten, you need modify ``run-SAXSER.py``.
                          for example:
                          START=10
                          END=20
Execute `./selectChi.sh` to extract the chi values from each cycle.``chi.dat`` file is generated. The first column is the order number of cycles, and the second colum is the chi value after every cycle. Execute `xmgrace chi.dat` to get the curve graph of ``chi.dat``.

Execute `./selectRg.sh` to extract Rgs from each cycle. ``Rg.dat`` file is generated. The first column is the order number of cycles, and the second colum is the Rg value of every cycle. Execute `xmgrace Rg.dat` to get the curve graph of ``Rg.dat``.

Exectue `./rm_files.sh` to delete some unimportant and unnecessary files with specific suffix to save your disk storage space. However, the files in "GA001/pdbs/", "GA001/curve_1/pdbs/" or "fit/" will not be removed. Please use this script carefully.

    `./rm_files.sh`
                             ``Y``   remove files with the suffix
                             ``n``   do not remove
        ``q or press 'enter' key``   exit the program

