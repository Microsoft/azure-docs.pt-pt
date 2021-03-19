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
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577449"
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

## <a name="certificate-revocation-checks"></a>Verificações de revogação de certificados
Ao ligar-se ao Serviço de Fala, o SDK de discurso verificará se o certificado TLS utilizado pelo Serviço de Fala não foi revogado. Para realizar esta verificação, o Speech SDK necessitará de acesso aos pontos de distribuição de CRL para as Autoridades de Certificados utilizadas pela Azure. Neste [documento](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)pode encontrar-se uma lista de possíveis locais de descarregamento de CRL. Se um certificado tiver sido revogado ou o CRL não puder ser descarregado, o Speech SDK abortará a ligação e levantará o evento cancelado.

No caso de a rede de onde o SDK de discurso está a ser utilizado ser configurada de uma forma que não permita o acesso aos locais de descarregamento de CRL, o controlo CRL pode ser desativado ou definido para não falhar se o CRL não puder ser recuperado. Esta configuração é feita através do objeto de configuração utilizado para criar um objeto Recogniser.

Para continuar com a ligação quando um CRL não pode ser recuperado, coloque a propriedade OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Quando definido como "verdadeiro" será feita uma tentativa de recuperar o CRL e se a recuperação for bem sucedida, o certificado será verificado para revogação, se a recuperação falhar, a ligação será permitida para continuar.

Para desativar completamente as verificações de revogação de certificados, desative o OPENSSL_DISABLE_CRL_CHECK do imóvel como "verdadeiro".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Vale também a pena notar que algumas distribuições de Linux não têm uma variável de ambiente TMP ou TMPDIR definida. Isto fará com que o SDK de voz descarregue sempre a Lista de Revogação de Certificados (CRL), em vez de caching o CRL para o disco para reutilização até expirarem. Para melhorar o desempenho inicial da ligação pode [criar uma variável ambiental chamada TMPDIR e defini-la para o caminho do seu diretório temporário escolhido.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Acerca do SDK de Voz](speech-sdk.md)
