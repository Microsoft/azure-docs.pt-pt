---
title: Variáveis de ambiente
description: definir variáveis ambientais
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 8bcce81808530c124b73b3527609a08f869b99c4
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997610"
---
Utilizando a sua chave e ponto final a partir do recurso que criou, crie duas variáveis ambientais para autenticação:

* `FORM_RECOGNIZER_KEY`- A chave de recursos para autenticar os seus pedidos.
* `FORM_RECOGNIZER_ENDPOINT`- O ponto final do recurso para o envio de pedidos de API. Vai ficar assim: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

>[!NOTE]
> Os pontos finais dos recursos não experimentais criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado mostrado abaixo. Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Utilize as seguintes instruções para definir variáveis ambientais no seu sistema operativo.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

Depois de adicionar as variáveis ambientais, feche e reabra a janela da consola.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite a sua `.bash_profile` , e adicione a variável ambiental:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

Depois de adicionar as variáveis ambientais, corra `source .bash_profile` da janela da consola para tornar as alterações eficazes.
***