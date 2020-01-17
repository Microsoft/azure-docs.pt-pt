---
title: Como configurar o OpenSSL para Linux
titleSuffix: Azure Cognitive Services
description: Saiba como configurar o OpenSSL para Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
ms.openlocfilehash: cadf31dede8ee81323076013d00b9431f597bda6
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156493"
---
# <a name="configure-openssl-for-linux"></a>Configurar o OpenSSL para Linux

Ao usar qualquer versão do SDK de fala anterior ao 1.9.0, o [OpenSSL](https://www.openssl.org) é configurado dinamicamente para a versão do sistema host. Em versões posteriores do SDK do Speech, o OpenSSL (versão [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) é vinculado estaticamente à biblioteca principal do SDK de fala.

## <a name="troubleshoot-connectivity"></a>Resolver problemas de conectividade

Se houver falhas de conexão ao usar a versão 1.9.0 do SDK de fala, verifique se o diretório `ssl/certs` existe no diretório `/usr/lib`-que é encontrado no sistema de arquivos do Linux. Se o diretório `ssl/certs` *não existir*, verifique onde OpenSSL está instalado em seu sistema, usando o seguinte comando:

```bash
which openssl
```

Em seguida, localize o diretório OpenSSL `certs` e copie o conteúdo desse diretório para `/usr/lib/ssl/certs` diretório. Em seguida, tente novamente para ver se problemas de conectividade foram resolvidos.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Sobre o SDK de fala](speech-sdk.md)