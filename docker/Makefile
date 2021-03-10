BUILDER_TAG?=ddls/esp32_platformio_compiler:latest
EXAMPLE?=Basic
EXAMPLES_PATH=examples/WatchFaces
BUILD_PATH=build/
EXAMPLES_OUTPUT_PATH=../$(EXAMPLES_PATH)

.PHONY: all
all: build_docker compile_example

.PHONY: build_docker
build_docker:
	docker build --tag $(BUILDER_TAG) . # --no-cache

.PHONY: push_docker
push_docker: build_docker
	docker push $(BUILDER_TAG)

.PHONY: pull_docker
pull_docker: build_docker
	docker pull $(BUILDER_TAG)

$(BUILD_PATH):
	-mkdir -p $(BUILD_PATH)/src

$(BUILD_PATH)/platformio.ini: $(BUILD_PATH)
	@-mkdir -p $(BUILD_PATH)/src
	@echo "Writing template $@"
	@echo >> $@ "[env:esp32dev]"
	@echo >> $@ "platform = espressif32"
	@echo >> $@ "board = esp32dev"
	@echo >> $@ "framework = arduino"
	@echo >> $@ "lib_deps ="
	@echo >> $@ "    ../../"
	@echo >> $@ "lib_ldf_mode = deep+"
	@echo >> $@ "board_build.partitions = min_spiffs.csv"

.PHONY: clean_build_path
clean_build_path:
	-rm -rf $(BUILD_PATH)

.PHONY: surgical_clean_build_path
surgical_clean_build_path: # this aims to keep the .pio/ folder to cache libs etc between run
	-shopt -u dotglob ; rm -rf $(BUILD_PATH)/*

.PHONY: compile_example
compile_example: surgical_clean_build_path $(BUILD_PATH)/platformio.ini
	cp $(EXAMPLES_OUTPUT_PATH)/$(EXAMPLE)/* $(BUILD_PATH)/src/
	docker run --rm                                                      \
	           "--volume=`cd .. ; pwd`:/project"                         \
	           --workdir /project/docker/$(BUILD_PATH)                   \
	           --env "PLATFORMIO_BUILD_FLAGS=$${PLATFORMIO_BUILD_FLAGS}" \
	           $(BUILDER_TAG)                                            \
	           /bin/bash -c                                              \
	           "pio run && cp .pio/build/esp32dev/firmware.bin /project/docker/$(EXAMPLES_OUTPUT_PATH)/$(EXAMPLE).bin"

.PHONY: compile_all_examples
compile_all_examples:
	EXAMPLE="7_SEG" PLATFORMIO_BUILD_FLAGS="$${PLATFORMIO_BUILD_FLAGS} -DDARKMODE=true"  $(MAKE) compile_example
	cp $(EXAMPLES_OUTPUT_PATH)/7_SEG.bin $(EXAMPLES_OUTPUT_PATH)/7_SEG_DARK.bin
	EXAMPLE="7_SEG" PLATFORMIO_BUILD_FLAGS="$${PLATFORMIO_BUILD_FLAGS} -DDARKMODE=false" $(MAKE) compile_example
	mv $(EXAMPLES_OUTPUT_PATH)/7_SEG.bin $(EXAMPLES_OUTPUT_PATH)/7_SEG_LIGHT.bin
	EXAMPLE="Basic"         $(MAKE) compile_example
	EXAMPLE="DOS"           $(MAKE) compile_example
	EXAMPLE="MacPaint"      $(MAKE) compile_example
	EXAMPLE="Pokemon"       $(MAKE) compile_example
	EXAMPLE="StarryHorizon" $(MAKE) compile_example
	EXAMPLE="Tetris"        $(MAKE) compile_example

