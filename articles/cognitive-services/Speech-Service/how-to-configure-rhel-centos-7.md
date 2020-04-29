---
title: Como configurar o RHEL/CentOS 7 - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar o RHEL/CentOS 7 para que o SDK do discurso possa ser utilizado.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80639165"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Configure RHEL/CentOS 7 para SDK de Fala

Red Hat Enterprise Linux (RHEL) 8 x64 e CentOS 8 x64 são oficialmente apoiados pela versão 1.10.0 do Speech SDK e posteriormente. Também é possível utilizar o SDK de Fala no RHEL/CentOS 7 x64, mas isso requer a tualização do compilador C++ (para desenvolvimento C++) e a biblioteca de tempo de funcionação C++ partilhada no seu sistema.

Para verificar a versão do compilador C++, executar:

```bash
g++ --version
```

Se o compilador estiver instalado, a saída deve ficar assim:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Esta mensagem permite-lhe saber que a versão principal do GCC 4 está instalada. Esta versão não tem suporte total para o padrão C++ 11, que o SDK de Fala utiliza. Tentar compilar um programa C++ com esta versão GCC e os cabeçalhos Do Discurso SDK resultarão em erros de compilação.

Também é importante verificar a versão da biblioteca de tempo de execução C++ partilhada (libstdc++). A maior parte do SDK do discurso é implementada como bibliotecas nativas de C++, o que significa que depende do libstdc++ independentemente da linguagem que usa para desenvolver aplicações.

Para encontrar a localização do libstdc++ no seu sistema, corra:

```bash
ldconfig -p | grep libstdc++
```

A saída em baunilha RHEL/CentOS 7 (x64) é:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Com base nesta mensagem, você vai querer verificar as definições da versão com este comando:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

A saída deve ser:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

O SDK do Discurso requer **CXXABI_1.3.9** e **GLIBCXX_3.4.21**. Pode encontrar esta informação executando `ldd libMicrosoft.CognitiveServices.Speech.core.so` as bibliotecas Speech SDK do pacote Linux.

> [!NOTE]
> Recomenda-se que a versão de GCC instalada no sistema seja de, pelo **menos, 5.4.0,** com bibliotecas de tempo de funcionação correspondentes.

## <a name="example"></a>Exemplo

Este é um comando de amostra que ilustra como configurar RHEL/CentOS 7 x64 para desenvolvimento (C++, C#, Java, Python) com o Discurso SDK 1.10.0 ou mais tarde:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Acerca do SDK de Voz](speech-sdk.md)
