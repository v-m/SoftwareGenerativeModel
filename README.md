# Generalized Double GNC (GDGNC)

## Overview

This project contains scripts used for my PhD research on software graph shape and generated software graphs.

## Dependencies

Those scripts runs on __Python 2__. Following libraries are requires:

 - networkx
 - numpy
 - matplotlib
 - scipy

On Linux:

    aptitude install python-networkx python-numpy

## Running

Script `projectpipe.py` shows an example of full execution of such scripts. This script cannot be directly run without adapting its contents.
It is intended to work with SF100 dataset (http://www.evosuite.org/files/SF100-20120316.tar.gz, accessed on June, 1th 2016) but can be easily adapted to another dataset.

### Extracting software dependencies

Software dependencies extractions can be found in the `graphs/real` folder for my dataset used in my paper (see Research Papers section).

#### Prerequisite

To extract real graph dependencies you need to download [dependency finder](http://sourceforge.net/projects/depfind/). Extract the content of the archive somewhere in your system.

#### Dependency finder XML file

To extract the dependency finder xml file, use:

```
$ xmldepgraph.py <folder> <output_xmlfile> <pathtodepfindbin>
```

For instance: 
```
$ python2 xmldepgraph.py /home/vince/Temp/ant/ ant.xml /home/vince/Temp/depfinder/bin
```

This command will extract the xml file to `<output_xmlfile>`. The file will contains all dependencies contained in the jars found recursively in `<folder>`. Note that `<pathtodepfindbin>` is the absolute path to the `bin` folder of the dependency finder project (ie. if I do extract dependency finder in my home dir: `/home/vince/DependencyFinder-1.2.1-beta4/bin/`).


#### Dependencies

Once you produced an XML file, you can obtain dependencies using:

```
depgraph.py <xmlfile> <outfile> <mode> <granluarity> <depfinder_root> <signaturefilter ...>
```
Let assume I do installed ant in `/home/vince/Temp/ant/` and dependency finder in `/home/vince/Temp/depfinder`. Those two lines will produce `ant.xml` XML file and `and.csv` graph file for internal connections only and at the class granularity:

```
$ python2 src/depgraph.py ant.xml ant.csv internal class /home/vince/Temp/depfinder/bin ant '!'
```

Parameters:

- `<xmlfile>` the input xml file;
- `<outfile>` the output dependency file;
- `<mode>` indicated if only endo dependencies should be considered. To do so, use __internal__. The opposite is obtained using __external__. Use __all__ to consider all;
- `<granluarity>` indicates the granularity of extracted items, can be: __package__, __class__ or __feature__;
  - `<depfinder_root>`  specify the absolute path to the dependency finder bin folder;
  - `<signaturefilter ...>` specify string which validate an item (according to its signature). Use '!' for default package.


### Digraph generation

Generations can be found in the `graphs/experiment-arxiv-1410.7921` and `graphs/generated-examples` folders for my dataset used in my paper (see Research Papers section).

Execute `python2 graphgen.py` to display the help for graph generation.
To generate a graph to the standard output, use:

```
$ python2 src/graphgen.py [graph-type-id] [parameters]
```

Parameters are dependent of the chosen generator. Some generator requires a number of nodes (`nodes=x`), some other a number of edges (`edges=x`). Moreover, almost all generators requires some parameters.

#### Available generators


| Nr | Name | Nodes | Edges | Floats parameters | Constants parameters |
|----|------|-------|-------|-------------------|----------------------|
| 0 |                  GNC |  True | False |  |  
| 1 |               GD-GNC |  True | False |                 p, q |  
| 2 |       Baxter & Frean | False |  True |                gamma |  
| 3 |              Vazquez |  True | False |                    p |  
| 4 |          Dorogovtsev |  True | False |  |                 m, A
| 5 |             Grindrod |  True | False |        alpha, lambda |  
| 6 |         Kumar Linear |  True | False |           copyfactor |                    d
| 7 |          Erdos Renyi |  True | False |                    p |  
| 8 |                R-MAT |  True |  True |           a, b, c, d |  
| 9 |             Bollobas | False |  True |   alpha, beta, gamma |    deltain, deltaout
| 10 |                  Goh |  True |  True |  alpha_in, alpha_out |  

#### Examples

GDGNC (graph type id *1*) requires a number of nodes and two parameters, *p* and *q*, thus we can, by example, generate a graph with 50 nodes, p and q = 0.5 by invoking:

```
python2 graphgen.py 1 nodes=50 p=0.5 q=0.5
```

Other examples:

```
python2 graphgen.py 8 nodes=50 edges=50 a=.2 b=.3 c=.4 d=.9
python2 graphgen.py 9 edges=50 alpha=.2 beta=.3 gamma=.4 deltain=10 deltaout=20
```

### Comparing kolmogorov-smirnov distances of programs

Use `ks_scores.py`:

```
$ python2 src/ks_scores.py <file1> [<fileorfolder2>]
```

This script takes as input a csv file `<file1>` which describe the software graph and a file or a folder containing a set of csv files `<fileorfolder2>` to compare with.
In the case if `<fileorfolder2>` is omitted, then the distance of `<file1>` will be computed with all other programs in the same folder than `<file1>`.

### Computing mann-whitney p-value of generated graphs

Use `mw_scores.py`:

```
mw_scores.py <realcsvfile> <generationfolder> [<<othergenerationfolder>]
```

This script takes as input a csv file `<realcsvfile>` which describe the software graph and two folders (`<generationfolder>` and `<othergenerationfolder>`) each containing a set of csv files of generated graphs with two different algorithms. If `<othergenerationfolder>` is omitted, then the generated graphs will be compared to all other software contained in the same folder than `<realcsvfile>`.

### Comparing software shapes using kolmogorov-smirnov p-value

Use `softwareshape.py`:

```
softwareshape.py <softwaregraph1 ... softwaregraphn>
```

Calculate the komlogorov-smirnov p-value with each pair of software cumulative in-/out- degree distrbution.
Print to the standard output each pair computation details and a summary.

### Plot the software cumulative in-/out- degree distribution

Use `softwareshape_plot.py`:

```
softwareshape_plot.py <graphplotfile> <softwarecsvfile ...>
```

Plot the cumulative degree distribution for the list of softwares <softwarecsvfile ...>. Software graphs are __.csv__ files which describes the graph.
Two files ares produced (one for in- and another for out- degree): `<graphplotfile>_in.pdf` and `<graphplotfile>_out.pdf`.

## Research Papers

This project is used in the following papers:

  - __Vincenzo Musco__, Martin Monperrus, Philippe Preux. A Generative Model of Software Dependency Graphs to Better Understand Software Evolution (http://arxiv.org/abs/1410.7921).

## Data structures

All graphs are descried in CSV files where each line describe a directed edge separated by `;`.


## Contact

See: http://www.vmusco.com or http://www.vincenzomusco.com
