---
title: Como configurar o OpenSSL para Linux
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o OpenSSL para o Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: 42960c25c4124203b64646fdc5cbca833b246e21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81683171"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Quando utilizar qualquer versão SDK do Discurso antes do 1.9.0, o [OpenSSL](https://www.openssl.org) está configurado dinamicamente para a versão do sistema de anfitrião. Em versões posteriores do Speech SDK, o OpenSSL (versão [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)está estático ligado à biblioteca central do SDK do discurso.

Para garantir a conectividade, verifique se os certificados OpenSSL foram instalados no seu sistema. Executar um comando:
```bash
openssl version -d
```

A saída dos sistemas baseados em Ubuntu/Debian deve ser:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verifique se existe `certs` subdireção em OPENSSLDIR. No exemplo acima, `/usr/lib/ssl/certs` seria.

* Se houver `/usr/lib/ssl/certs` e contiver muitos ficheiros de certificados individuais (com `.crt` ou `.pem` extensão), não há necessidade de mais ações.

* Se o OPENSSLDIR for outra coisa que `/usr/lib/ssl` não seja e/ou exista um único ficheiro de pacote de certificados em vez de vários ficheiros individuais, é necessário definir uma variável de ambiente SSL adequada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- OPENSSLDIR é `/opt/ssl` . Há `certs` subdireção com muitos `.crt` ou `.pem` ficheiros.
Desaponte a variável `SSL_CERT_DIR` ambiente para apontar antes de executar um programa que utiliza o `/opt/ssl/certs` SDK do discurso. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR é `/etc/pki/tls` (como em sistemas baseados em RHEL/CentOS). Existe `certs` subdiretório com um ficheiro de pacote de certificados, por exemplo `ca-bundle.crt` .
Desaponte a variável ambiente `SSL_CERT_FILE` para apontar para esse ficheiro antes de executar um programa que utiliza o SDK de discurso. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Vale também a pena notar que algumas distribuições de Linux não têm uma variável de ambiente TMP ou TMPDIR definida. Isto fará com que o SDK de voz descarregue sempre a Lista de Revogação de Certificados (CRL), em vez de caching o CRL para o disco para reutilização até expirarem. Para melhorar o desempenho inicial da ligação pode [criar uma variável ambiental chamada TMPDIR e defini-la para o caminho do seu diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Acerca do SDK de Voz](speech-sdk.md)
