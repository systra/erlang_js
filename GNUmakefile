
all:
	$(REBAR) get-deps compile

verbose:
	$(REBAR) compile verbose=1

clean: c_src_clean
	rm -rf tests_ebin docs
	$(REBAR) clean

c_src:
	cd c_src; $(MAKE)

c_src_clean:
	cd c_src; $(MAKE) clean

test: all
	$(REBAR) eunit skip_deps=true

run: all
	erl -pa ebin -pa deps/*/ebin -boot start_sasl

docs: all
	@mkdir -p docs
	@./build_docs.sh

.PHONY: c_src c_src_clean docs

include rebar.mk
