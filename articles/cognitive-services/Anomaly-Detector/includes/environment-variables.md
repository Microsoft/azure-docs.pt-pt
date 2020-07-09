---
title: Variáveis ambientais do detetor de anomalias
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/30/2020
ms.author: aahi
ms.openlocfilehash: 4a44df3f2108421c73c161d8f2df37330b9c2d14
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85980225"
---
### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

>[!NOTE]
> Os pontos finais para recursos não experimentais criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado mostrado abaixo. Para obter mais informações e uma lista completa de pontos finais regionais, consulte [os nomes de subdomínio personalizados para serviços cognitivos.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) 

Utilizando a sua chave e ponto final a partir do recurso que criou, crie duas variáveis ambientais para a autenticação:

* `ANOMALY_DETECTOR_KEY`- A chave de recursos para autenticar os seus pedidos.
* `ANOMALY_DETECTOR_ENDPOINT`- O ponto final de recurso para o envio de pedidos de API. Vai ficar assim: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Utilize as instruções para o seu sistema operativo.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável ambiente, reinicie a janela da consola.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite a sua `.bash_profile` variável e adicione a variável ambiental:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.

***