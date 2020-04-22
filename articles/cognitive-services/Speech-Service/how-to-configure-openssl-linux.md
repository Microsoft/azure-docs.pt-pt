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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683171"
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

Verifique se existe `certs` subdiretório no âmbito do OPENSSLDIR. No exemplo acima, seria. `/usr/lib/ssl/certs`

* Se houver `/usr/lib/ssl/certs` e contiver muitos ficheiros de certificados individuais (com `.crt` ou `.pem` extensão), não há necessidade de mais ações.

* Se o OPENSSLDIR `/usr/lib/ssl` for outra coisa que não e/ou exista um único ficheiro de pacote de certificado em vez de vários ficheiros individuais, é necessário definir uma variável ambiental SSL adequada para indicar onde os certificados podem ser encontrados.

## <a name="examples"></a>Exemplos

- OPENSSLDIR `/opt/ssl`é . Há `certs` subdiretório com `.crt` `.pem` muitos ou ficheiros.
Detete `SSL_CERT_DIR` a `/opt/ssl/certs` variável ambiental para apontar antes de executar um programa que usa o SDK do Discurso. Por exemplo:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR `/etc/pki/tls` é (como em sistemas baseados em RHEL/CentOS). Há `certs` subdiretório com um ficheiro de `ca-bundle.crt`pacote de certificado, por exemplo.
Detete `SSL_CERT_FILE` a variável ambiental para apontar nesse ficheiro antes de executar um programa que usa o SDK do Discurso. Por exemplo:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```
> [!NOTE]
> Vale também a pena notar que algumas distribuições de Linux não têm uma variável ambiental TMP ou TMPDIR definida. Isto fará com que o SDK do Discurso descarregue sempre a Lista de Revogação de Certificados (CRL), em vez de cortar o CRL para o disco para reutilização até expirarem. Para melhorar o desempenho inicial da ligação, pode [criar uma variável ambiental chamada TMPDIR e defini-la no caminho do seu diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Acerca do SDK de Voz](speech-sdk.md)
