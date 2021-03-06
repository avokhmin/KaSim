MODEL= $(wildcard *.ka)
DIR=$(shell pwd)
EXPECTED_OUTPUT_PATH=expected-output
DOT=$(wildcard */*.dot)
PDF=$(DOT:.dot=.pdf)
TMP=tmp
SCRIPT=script
ERROR=error 
ERROR_TMP=error_tmp 

EXPECTED_DOT= $(wildcard expected-output/*.dot) 
TESTED_DOT= $(wildcard tmp/*.dot) 
TESTED_REF= $(wildcard tmp/*.ref)
DIFF=$(TESTED_DOT:.dot=.diff) 
CHECK=$(TESTED_DOT:.dot=.error) $(TESTED_REF:.ref=.error)
EXP=$(EXPECTED_DOT:.dot=.ref)

all: test

build: $(EXPECTED_OUTPUT_PATH)
	@make clean_all
	@make $(EXPECTED_OUTPUT_PATH)
	@sh README 

$(EXPECTED_OUTPUT_PATH):
	@mkdir $@

$(TMP): 
	@rm -rf $(TMP) 
	@mkdir $(TMP)

$(SCRIPT): README
	@sed "s/expected-output/tmp/g" $< > $@

test: 
	@make clean > /dev/null
	@make $(TMP) > /dev/null
	@make launch_script 
	@make do_copy > /dev/null
	@make do_diff > /dev/null
	@make error > /dev/null
	@make clean_light > /dev/null

launch_script: $(SCRIPT)
	@sh $< 

do_copy: $(EXP)
do_diff: $(CHECK)


$(EXPECTED_OUTPUT_PATH)/%.ref: $(EXPECTED_OUTPUT_PATH)/%.dot
	@sed '/generation/d' $< > $@ 
	@cp $@ $(TMP)/
	@rm $@

%.ref: 
	@if (test -s $(EXPECTED_OUTPUT_PATH)/$*.dot) then (sed '\generation\d' $(EXPECTED_OUTPUT_PATH)/$*.dot $@) else (touch $@) fi;                                    

%.dot: 
	@touch $@

%.diff:  %.ref %.new
	@diff $^  > $@ || exit 0 

%.new: %.dot
	@sed '/generation/d' $< > $@

%.error_tmp: %.diff 
	@if (test -s $*.diff) then (echo $*.dot has changed > $@ ; cat $*.diff >> $@ ; echo >> $@) else (touch $@) fi; 
	@if (test -s $*.new) then (exit 0) else (echo $*.dot has disappeared > $@ ; echo >> $@) fi; 
	@if (test -s $*.ref) then (exit 0) else (echo $*.dot is new > $@ ; echo >> $@) fi;

%.error: %.error_tmp
	@if (test -s $<) then (cp $< $@) else touch $@ ; fi 
	@rm $<

$(ERROR_TMP): 
	@cat $(TMP)/*.error > $@

$(ERROR): error_tmp 
	@if (test -s $<) then (echo ****$(DIR)/$(MODEL)**** > $@ ; echo  >> $@ ; cat README >> $@ ; echo >> $@ ; cat $< >> $@) else (touch $@) ; fi 

clean:
	@rm -rf $(TMP) $(SCRIPT) $(ERROR) $(ERROR_TMP) 

clean_light:
	@rm -rf $(SCRIPT) $(ERROR_TMP)

clean_all:
	@make clean
	@rm -rf $(EXPECTED_OUTPUT_PATH) 

pdf: $(PDF)

%.pdf: %.dot
	dot -Tpdf $< -o $@
.phony help:
	@echo 'make help:      this help'
	@echo 'make build:     launch KaSim and store the result as a reference'
	@echo 'make test:      launch KaSim and compare the result with the reference files'
	@echo 'make pdf:       build all pdf from existing dot files'
	@echo 'clean:          clean everything but the reference files'
	@echo 'clean_all:      clean everything including the reference files"
