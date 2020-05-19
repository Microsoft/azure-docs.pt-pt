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
ms.openlocfilehash: ba531164e024f96d3bdd23912f3f6e90275edda4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589742"
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

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Com base nesta mensagem, você vai querer verificar as definições da versão com este comando:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

A saída deve ser:

```bash
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

Este é um conjunto de comando de amostra que ilustra como configurar RHEL/CentOS 7 x64 para desenvolvimento (C++, C#, Java, Python) com o Speech SDK 1.10.0 ou mais tarde:

### <a name="1-general-setup"></a>1. Configuração geral

Primeiro instale todas as dependências gerais:

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
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. Compilador C/C++ e bibliotecas de tempo de execução

Instale as embalagens pré-requisitos com este comando:

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> O pacote libmpc-devel foi depreciado na atualização RHEL 7.8. Se a saída do comando anterior incluir uma mensagem
>
> ```bash
> No package libmpc-devel available.
> ```
>
> em seguida, os ficheiros necessários devem ser instalados a partir de fontes originais. Execute os seguintes comandos:
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

Próxima atualização do compilador e bibliotecas de tempo de execução:

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Se o compilador e bibliotecas atualizados precisarem de ser implantados em várias máquinas, pode simplesmente copiá-las de baixo `/usr/local` para outras máquinas. Se apenas forem necessárias as bibliotecas em tempo de execução, os ficheiros `/usr/local/lib64` serão suficientes.

### <a name="3-environment-settings"></a>3. Configurações ambientais

Executar os seguintes comandos para completar a configuração:

```bash
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
