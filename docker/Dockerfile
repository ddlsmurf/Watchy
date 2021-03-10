FROM ubuntu:latest AS ubuntu_apt
RUN apt-get update && apt-get install -y git

FROM ubuntu_apt AS ubuntu_python
RUN apt-get install -y python3-pip && pip3 install -U platformio

# ddls/esp32_platformio_compiler:latest
FROM ubuntu_python AS esp32_platformio_compiler
RUN pio platform install espressif32 --with-package=framework-arduinoespressif32 --with-package=tool-esptoolpy --with-package=toolchain-xtensa32