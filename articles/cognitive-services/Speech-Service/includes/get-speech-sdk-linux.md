---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 14f9d0936a4d1949cf8d7fc69bbb782ee447bdba
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668763"
---
:::row:::
    :::column span="3":::
        O Speech SDK apenas apoia **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**, e **CentOS 7/8** nas seguintes arquiteturas-alvo quando utilizados com Linux:
        - X64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Ao direcionar o Linux ARM64 e utilizar c# - é necessário o .NET Core 3.x (pacote dotnet-sdk-3.x). Se está a apontar para arm32 ou ARM64, python não é suportado.

> [!NOTE]
> As arquiteturas x86 de Ubuntu 16.04, Ubuntu 18.04 e Debian 9 apenas apoiam o desenvolvimento c++ com o Speech SDK.

### <a name="system-requirements"></a>Requisitos de sistema

Para uma aplicação nativa, o SDK do Discurso baseia-se em `libMicrosoft.CognitiveServices.Speech.core.so`. Certifique-se de que a arquitetura alvo (x86, x64) corresponde à aplicação. Dependendo da versão Linux, podem ser necessárias dependências adicionais.

- As bibliotecas partilhadas da biblioteca GNU C (incluindo `libpthreads`a biblioteca de programação POSIX Threads),
- A biblioteca OpenSSL `libssl.so.1.0.2`(ou)`libssl.so.1.0.0`
- A biblioteca partilhada para aplicações`libasound.so.2`ALSA ( )

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debiano 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 e CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK da fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> No RHEL/CentOS 8, siga as instruções sobre como configurar o [OpenSSL para Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
