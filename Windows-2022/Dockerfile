# Dockerfile for building container images for use in the EDK2 CI.
#
# Copyright (c) Microsoft Corporation
# SPDX-License-Identifier: BSD-2-Clause-Patent
#
# This dockerfile contains the definitions for building and testing EDKII based
# firmare projects in VS2022 on Windows. This image is intended to be used on
# X64.

FROM mcr.microsoft.com/windows/servercore:ltsc2025 AS build

# Links for installed downloads.
ARG PYTHON_LINK=https://www.python.org/ftp/python/3.13.1/python-3.13.1-amd64.exe
ARG GIT_LINK=https://github.com/git-for-windows/git/releases/download/v2.52.0.windows.1/Git-2.52.0-64-bit.exe

# Setup CMD as default shell.
SHELL ["cmd", "/S", "/C"]

# Install LLVM-MinGW 20.1.8
RUN curl -L -o "llvm-mingw.zip" "https://github.com/mstorsjo/llvm-mingw/releases/download/20251104/llvm-mingw-20251104-ucrt-x86_64.zip"
RUN powershell -ExecutionPolicy Bypass -Command "Expand-Archive -Path 'llvm-mingw.zip' -DestinationPath 'C:\' -Force"
RUN del /q llvm-mingw.zip

# Install VS2022 Build Tools
RUN curl -SL --output vs_buildtools.exe https://aka.ms/vs/17/release/vs_buildtools.exe
RUN vs_buildtools.exe --quiet --wait --norestart --nocache \
  --add Microsoft.VisualStudio.Component.VC.CoreBuildTools \
  --add Microsoft.VisualStudio.Component.VC.Tools.x86.x64 \
  --add Microsoft.VisualStudio.Component.Windows11SDK.22621 \
  --add Microsoft.VisualStudio.Component.VC.Tools.ARM64
RUN if exist vs_buildtools.exe del /q vs_buildtools.exe

# Install chocolatey
RUN curl -SL --output chocolatey_install.ps1 https://community.chocolatey.org/install.ps1
RUN powershell -ExecutionPolicy Bypass -Command "& './chocolatey_install.ps1'"
RUN del /q chocolatey_install.ps1
ENV PATH="C:\ProgramData\chocolatey\bin;${PATH}"

# Install CLANG/LLVM 20.1.8 from chocolatey
ARG CLANG_VERSION=20.1.8
RUN choco install -y llvm --version=%CLANG_VERSION%

# Install curl
RUN choco install -y curl

# Install Python3
RUN curl -SL --output python_install.exe %PYTHON_LINK%
RUN python_install.exe /quiet InstallAllUsers=1 PrependPath=1 Include_test=0
RUN del /q python_install.exe

# Install Git
RUN curl -SL --output git_install.exe %GIT_LINK%
RUN git_install.exe /VERYSILENT /NORESTART /NOCANCEL /SP-
RUN del /q git_install.exe

# Install NodeJS
RUN curl -SL --output node_install.msi https://nodejs.org/dist/v22.12.0/node-v22.12.0-x64.msi
RUN msiexec.exe /i node_install.msi /quiet
RUN del /q node_install.msi

# Update NPM
RUN npm install -g npm

# Install MarkDownLint
RUN npm install -g markdownlint-cli

# Install CSpell
RUN npm install -g cspell

# Install TianoCore Mingw CLANG 20.1.8
RUN curl -L -o EdkClangSetup-20.1.8.0.exe https://github.com/tianocore/edk2-edkrepo/releases/download/edkclang-v20.1.8/EdkClangSetup-20.1.8.0.exe
RUN EdkClangSetup-20.1.8.0.exe /silent
RUN del /q EdkClangSetup-20.1.8.0.exe
