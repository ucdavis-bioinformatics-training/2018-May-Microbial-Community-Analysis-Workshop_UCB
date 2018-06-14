# Installing the workshop software

**ALL of this can be done on the head node, ganesh**

We are going to install the software needed for this workshop

1. FLASH2 to overlap reads (https://github.com/dstreett/FLASH2)
2. RDP to classify reads into taxon (https://github.com/rdpstaff/RDPTools)
3. dbcAmplicons pipeline for processing amplicon sequences to abundance tables (https://github.com/msettles/dbcAmplicons)

optional

4. bowtie2 (https://github.com/BenLangmead/bowtie2)

**System Requirements**

* git
* java jdk
* ant
* python virtualenv
* biom requires numpy

---

**1\.** First, create a directory for the workshop in your home directory:

    cd
    mkdir mca_example

and two other directors

	mkdir mca_example/src
	mkdir mca_example/bin

**2\.** Now lets add the new bin directory to our PATH in a \.bash_profile file

using your favorite text editor (e.g. using the 'nano' editor, type 'nano ~.bash_profile' to open nano and immediately edit .bash_profile in your home directory) add the line

	export PATH=~/mca_example/bin:$PATH

to a file named \.bash_profile [node the leading \. as its a 'hidden' file], which may or may not already exist in your home. Then save and exit (e.g. using 'nano', type <control-O> to save, then <control-X> to exit). Then on the command line, execute the commands in your .bash_profile file (which normally only get executed when you log in).

	source ~/.bash_profile

---

**3\.** Install **FLASH2** into src and link the exectuable into bin

	cd ~/mca_example/src
	git clone https://github.com/dstreett/FLASH2.git
	cd FLASH2/
	make
	ln -s ~/mca_example/src/FLASH2/flash2 ~/mca_example/bin/.
	# test installation, should see help documentation
	flash2 -h
	cd ..

---

**4\.a** Install apache ant, need for RDP

	cd ~/mca_example/src
	wget http://mirrors.ibiblio.org/apache/ant/binaries/apache-ant-1.10.3-bin.tar.gz
	tar xzvf apache-ant-1.10.3-bin.tar.gz
	ln -s ~/mca_example/src/apache-ant-1.10.3/bin/ant ~/mca_example/bin/.
	cd ..

**4\.b** Install the **Ribsomal Database Project** (RDP) into src

	cd ~/mca_example/src
	module load java/jdk1.8
	git clone https://github.com/rdpstaff/RDPTools.git
	cd RDPTools/
	git submodule init
	git submodule update
	make
	# might have problems retreiving data.tgz file ... if so, try again ...
	# during 9-5 EST(!) ... 
	# test installation, should see help documentation for classify
	java -jar classifier.jar classify
	# this should give you a "Command Error" because you didn\'t specify output
	# ... but it should give you a list of options
	# feel free to move on if the 'make' fails due to data.tgz file
	cd ..

**4\.c** Add the location of classifier.jar as a variable in our \.bash_profile

using your favorite text editor, (nano?), add the line

	module load java/jdk1.8
	export RDP_PATH=~/mca_example/src/RDPTools

to ~/\.bash_profile, then source it

	source ~/.bash_profile  # runs the commands right away, instead of at next login

---

**5\.a** Setup a python virtual environment for dbcAmplicons, in the src directory

	cd ~/mca_example/src
	module load python-libs/2.7.6-ubuntu
	virtualenv dbcA_virtualenv

**5\.b** This lets you set the virtual environment to activate on login by adding it to our \.bash_profile

using your favorite text editor, _nano_ is simple, add the lines

	module load python-libs/2.7.6-ubuntu
	source ~/mca_example/src/dbcA_virtualenv/bin/activate

to a file named ~/\.bash_profile, then source it

	source ~/.bash_profile

You should now see the text "(dbcA_virtualenv)" at the beginning of your prompt.

---

**6\.** Install **dbcAmplicons**

	cd ~/mca_example/src
	pip install biom-format
	git clone https://github.com/msettles/dbcAmplicons.git
	cd dbcAmplicons/
	python setup.py install
	# test installation, should see help documentation
	dbcAmplicons -h  # should show options / usage message
	cd ..

**Optional\.** Test **dbcAmplicons**

You could also test the dbcAmplicons installation by running the script, test_dbAmplicons.sh, under the tests folder (in dbcAmplicions).

  cd ~/mca_example/src/dbcAmplicons/tests/
  ./test_dbAmplicons.sh  # could show some ERRORs / WARNINGs, but otherwise give stats after a few minutes

---

**Lets Review**

We created a directory for the workshop, in that directory we created two folders src and bin. We've installed FLASH2, RDP and dbcAmplicons. We've placed the executable for flash in a bin folder and added the folder to our PATH. We created an environment variable for the RDP classifier. We've created a python virtual environment and then installed the python package biom-format using pip and the dbcAmplions package using setup.py.

1. You should have verified all the software works by viewing their help docs
2. Verify that the flash executable is indeed in the bin folder
3. We've modified your PATH and added 1 new environment variable, RDP_PATH, verify the PATH and the new env variable.
4. We added a multiple lines to your \.bash_profile. How many lines?

Now log out, log back in and verify that each application still works. Ex.

	flash2 -h

To verify RDP and dbcAmplicons use

	java -jar $RDP_PATH/classifier.jar classify
	dbcAmplicons -h

You can check the current version of everything with

	dbcVersionReport.sh

We usually save the output of this in the project file to remind ourselves which versions of software were run for that project.

If for some reason installation failed you can extract a copy from the workshop share

	cd
	cd mca_example
	tar xzvf /share/biocore/workshops/2018_May_MCA/software.tar.gz

You still need to set up the same environment variable in your \.bash_profile. These lines should be in your .bash_profile

  export PATH=~/mca_example/bin:$PATH  
  module load java/jdk1.8  
  export RDP_PATH=~/mca_example/src/RDPTools  
  module load python-libs/2.7.6-ubuntu  
  source ~/mca_example/src/dbcA_virtualenv/bin/activate  

---

**7\.** Last lets copy the workshop data into our home directory.

  cd  
  cd mca_example  
  cp -r /share/biocore/workshops/2018_May_MCA/Illumina_Reads .  

Take a look at the files ... what is inside the Illumina_Reads folder?
