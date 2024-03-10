
# cace + cicsim

Both cace and cicsim are simulation orchestration tools. The venn diagram
overlap between cace and cicsim is large, and it would be nice if cicsim
supported some of the replcements used in cace, so in the latest cicsim I've
added expression and replacement options. See
<https://github.com/wulffern/cicsim/pull/21> for details



## Cloning and Setup

Get latest cicsim 
``` bash
git clone git@github.com:wulffern/cicsim.git
cd cicsim
python3 -m pip install --upgrade --user .
cd ..
``` 

Get technology setup

```bash
git clone git@github.com:wulffern/tech_sky130B.git

``` 

Get my fork of instramp

```bash
git clone git@github.com:wulffern/sky130_ef_ip__instramp.git
cd sky130_ef_ip__instramp
git checkout cicsim
cd ..
```

## Running 

See the Makefile for details.

``` bash
cd sky130_ef_ip__instramp/sim/instramp
make typical
```

## Details

cicsim does not read the cace file, I've hand-written the replace.yaml file
based on what I saw in the cace file. A script could be written to make the
replace.yaml from the cace file if someone wanted to do that.

