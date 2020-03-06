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
ms.openlocfilehash: 350c2bf3c4d0fc0a16f1b393e7c8d8a372679797
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331149"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Ao utilizar qualquer versão SDK do Speech antes de 1.9.0, o [OpenSSL](https://www.openssl.org) está configurado dinamicamente para a versão do sistema de anfitriões. Em versões posteriores do Speech SDK, o OpenSSL (versão [1.1.1b)](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)está está estático ligado à biblioteca central do Speech SDK.

Para garantir a conectividade, verifique se os certificados OpenSSL foram instalados no seu sistema. Executar um comando:
```bash
openssl version -d
```

A saída dos sistemas baseados em Ubuntu/Debian deve ser:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Verifique se existe `certs` subdiretório no âmbito do OPENSSLDIR. No exemplo acima, seria `/usr/lib/ssl/certs`.

* Se houver `/usr/lib/ssl/certs` e contiver muitos ficheiros de certificados individuais (com `.crt` ou extensão `.pem`), não há necessidade de mais ações.

* Se o OPENSSLDIR for outra coisa que não `/usr/lib/ssl` e/ou exista um único ficheiro de pacote de certificado em vez de vários ficheiros individuais, é necessário definir uma variável ambiental SSL adequada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- O OPENSSLDIR é `/opt/ssl`. Existe `certs` subdiretório com muitos ficheiros `.crt` ou `.pem`.
Definir variável ambiental `SSL_CERT_DIR` apontar para `/opt/ssl/certs` antes de executar um programa que usa o SDK do Discurso. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- O OPENSSLDIR é `/etc/pki/tls` (como nos sistemas baseados em RHEL/CentOS). Existe `certs` subdiretório com um ficheiro de pacote de certificados, por exemplo, `ca-bundle.crt`.
Detete a variável ambiental `SSL_CERT_FILE` apontar nesse ficheiro antes de executar um programa que usa o SDK do Discurso. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Sobre o Discurso SDK](speech-sdk.md)
