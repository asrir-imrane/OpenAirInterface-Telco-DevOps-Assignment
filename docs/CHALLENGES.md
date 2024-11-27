# Comparison Between My Dockerfile and the Default Dockerfile

This document highlights the modifications I made in my Dockerfile compared to the default Dockerfile and provides explanations for these changes.

---

## Modifications and Explanations

### 1. **Base Image Configuration**
- **My Dockerfile**:
  ```dockerfile
  ARG BASE_IMAGE=ubuntu:jammy
  FROM $BASE_IMAGE AS oai-nrf-base
  ```
  - Uses `ubuntu:jammy` (Ubuntu 22.04) as the base image.

- **Default Dockerfile**:
  ```dockerfile
  ARG BASE_IMAGE=ubuntu:jammy
  FROM $BASE_IMAGE as oai-nrf-base
  ```
  - Identical configuration.

- **Reason**: I did not make any changes to the base image to maintain consistency and compatibility.

---

### 2. **Dependency Installation**
- **My Dockerfile**:
  ```dockerfile
  RUN apt-get update && \
      DEBIAN_FRONTEND=noninteractive apt-get upgrade --yes && \
      DEBIAN_FRONTEND=noninteractive apt-get install --yes \
        psmisc \
        git \
        cmake \
        build-essential \
        libssl-dev \
        pkg-config \
      && rm -rf /var/lib/apt/lists/*
  ```
  - Includes additional dependencies such as `cmake`, `build-essential`, `libssl-dev`, and `pkg-config`.

- **Default Dockerfile**:
  ```dockerfile
  RUN apt-get update && \
      DEBIAN_FRONTEND=noninteractive apt-get upgrade --yes && \
      DEBIAN_FRONTEND=noninteractive apt-get install --yes \
        psmisc \
        git \
      && rm -rf /var/lib/apt/lists/*
  ```
  - A minimal set of dependencies (`psmisc` and `git`).

- **Reason**: I added these additional dependencies to support the building of the CPR library and compiling the NRF service, ensuring compatibility with custom builds.

---

### 3. **CPR Dependency Installation**
- **My Dockerfile**:
  ```dockerfile
  RUN git clone https://github.com/libcpr/cpr.git && \
      cd cpr && \
      mkdir build && cd build && \
      cmake .. -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=/usr/local && \
      make -j$(nproc) && \
      make install && \
      ldconfig
  ```
  - Installs the CPR library from source to enable advanced HTTP client features.

- **Default Dockerfile**:
  - Does not include CPR library installation.

- **Reason**: I explicitly added CPR as a dependency for advanced HTTP client functionality, which is a project-specific requirement.

---

### 4. **Script Configuration**
- **My Dockerfile**:
  ```dockerfile
  RUN echo "Converting all scripts to Unix-style line endings..." && \
      find . -type f -name "*" -exec dos2unix {} \; && \
      chmod +x ./build_nrf && \
      bash ./build_nrf --install-deps --force && \
      ldconfig
  ```
  - Ensures scripts use Unix-style line endings and are executable.

- **Default Dockerfile**:
  ```dockerfile
  RUN ./build_nrf --install-deps --force && \
      ldconfig
  ```
  - Runs the build script directly without additional script adjustments.

- **Reason**: I accounted for potential issues with line endings and ensured all scripts are executable to add robustness for cross-platform development.

---

### 5. **Health Check and Ports**
- **My Dockerfile**:
  ```dockerfile
  HEALTHCHECK --interval=10s \
              --timeout=15s \
              --retries=6 \
      CMD /openair-nrf/bin/healthcheck.sh
  ```
  - Includes a health check script to monitor the service.

- **Default Dockerfile**:
  ```dockerfile
  HEALTHCHECK --interval=10s \
              --timeout=15s \
              --retries=6 \
      CMD /openair-nrf/bin/healthcheck.sh
  ```
  - Identical configuration.

- **Reason**: I did not modify the health check configuration to ensure consistency.

---

### 6. **Additional Debug Tools**
- **My Dockerfile**:
  ```dockerfile
  RUN apt-get update && \
      DEBIAN_FRONTEND=noninteractive apt-get upgrade --yes && \
      DEBIAN_FRONTEND=noninteractive apt-get install --yes \
        tzdata \
        net-tools \
        tcpdump \
        iputils-ping \
        bc \
        libldap-2.?-? \
        libkrb5-3 \
        libgssapi-krb5-2 \
        libcurl4-gnutls-dev \
        librtmp1 \
        libpsl5 \
        libboost-thread1.7?.0 \
        libboost-chrono1.7?.0 \
      && rm -rf /var/lib/apt/lists/*
  ```
  - Includes tools for debugging and mandatory libraries for NRF execution.

- **Default Dockerfile**:
  ```dockerfile
  RUN apt-get update && \
      DEBIAN_FRONTEND=noninteractive apt-get upgrade --yes && \
      DEBIAN_FRONTEND=noninteractive apt-get install --yes \
        tzdata \
        psmisc \
        net-tools \
        tcpdump \
        iputils-ping \
        bc \
        libldap-2.?-? \
        libkrb5-3 \
        libgssapi-krb5-2 \
        libcurl4-gnutls-dev \
        librtmp1 \
        libpsl5 \
        libboost-thread1.7?.0 \
        libboost-chrono1.7?.0 \
      && rm -rf /var/lib/apt/lists/*
  ```
  - Includes similar tools.

- **Reason**: I ensured debugging tools and mandatory libraries are present to guarantee smooth operation and troubleshooting of the NRF service.

---

## Conclusion
My Dockerfile introduces enhancements such as additional dependencies (e.g., CPR library), Unix-style script adjustments, and a robust build process to accommodate specific project needs. These changes aim to improve the reliability and functionality of the NRF service in diverse environments.
