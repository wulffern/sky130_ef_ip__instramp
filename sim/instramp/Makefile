######################################################################
##        Copyright (c) 2024 Carsten Wulff Software, Norway
## ###################################################################
## Created       : wulff at 2024-3-3
## ###################################################################
##  The MIT License (MIT)
##
##  Permission is hereby granted, free of charge, to any person obtaining a copy
##  of this software and associated documentation files (the "Software"), to deal
##  in the Software without restriction, including without limitation the rights
##  to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
##  copies of the Software, and to permit persons to whom the Software is
##  furnished to do so, subject to the following conditions:
##
##  The above copyright notice and this permission notice shall be included in all
##  copies or substantial portions of the Software.
##
##  THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
##  IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
##  FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
##  AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
##  LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
##  OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
##  SOFTWARE.
##
######################################################################

TBDIR=../../cace
TB=avo
VIEW=Sch
#VIEW=Lay
CELL=CNR_OTA
LIB =CNR_OTA_SKY130NM
OPT=
CELL=

CORNER=typical

TBO=$(subst _,,${TB})

TBS =$(shell ls -1 ../../cace/*.sch)
TBS1=$(subst ../../cace/,,${TBS})
TBS2=$(subst .sch,,${TBS1})


TFUNC= eqvnoise gbw \
	input_offset propdelay  slew_fall slew_rise \
	transient voltage_out avo dccurrent_dyn dccurrent_vdd

#-No Func
# cmrr: Can't handle VVcm
# psrr: Can't handle {Vvdd|mimimum}


all:
	$(foreach tb, ${TFUNC}, ${MAKE} ${CORNER} TB=${tb}; )


netlist:
	test -f ${TBDIR}/${TB}.sch
	xschem -q -x -b -s -n --tcl "set netlist_dir ${PWD};" ${TBDIR}/${TB}.sch
#Replace dut information, .lib PDK root, and temperature
	cat ${TB}.spice |perl -ne "if(m/^XDUT/){print \".include ../xdut.spi\n\"}else{print $$_}"| \
	grep -v "PDK_ROOT"|grep -v temperature|  \
	perl ../../tech/script/removeplus > ${TBO}.spi

	perl ../../tech/script/genxdut ../../netlist/schem/sky130_ef_ip__instramp.spice sky130_ef_ip__instramp

test:
	${MAKE} typical OPT="Debug"

typical: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_typical ${TBO} ${OPT} ${VIEW} Gt Att Tt Vt

slow: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_slow ${TBO} ${OPT} ${VIEW} Gt Ass "Th,Tl" Vl

fast: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_fast ${TBO} ${OPT} ${VIEW} Gt Aff "Th,Tl" Vh

tfs: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_tfs ${TBO} ${OPT} ${VIEW} Gt "Att,Ass,Aff" "Tt,Th,Tl" "Vt,Vl,Vh"

etc: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_etc ${TBO} ${OPT} ${VIEW} Gt "Ass,Aff,Asf,Afs" "Th,Tl" "Vl,Vh"

mc: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_mc --count 10 ${TBO} ${OPT} ${VIEW} Gt "Attmm" "Tt" "Vt"

ntc: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_ntc ${TBO} ${OPT} ${VIEW} Gt "Att,Ass,Aff" "Tt" Vt

temp: netlist
	cicsim run --replace replace.yaml --name ${VIEW}_temp ${TBO} ${OPT} ${VIEW} Gt "Att" "Tt,Th,Tl" Vt

summary:
	cicsim summary --output "README.md"

slide:
	pandoc -s  -t slidy README.md -o README.html


clean:
	-rm -rf output_*
	-rm -rf __pycache__
	-rm *.run
	-rm *.pdf
	-rm *.csv
