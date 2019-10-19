---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554711"
---
Comece a usar o serviço de detector de anomalias criando um dos recursos do Azure abaixo.

* Um [recurso de avaliação](https://azure.microsoft.com/try/cognitive-services/#decision) (nenhuma assinatura do Azure é necessária): 
    * Válido por sete dias, gratuitamente. Depois de se inscrever, uma chave de avaliação e um ponto de extremidade estarão disponíveis no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Essa é uma ótima opção se você quiser experimentar o detector de anomalias, mas não tem uma assinatura do Azure.

* Um [recurso de detector de anomalias](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * Disponível por meio do [portal do Azure](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) até que você exclua o recurso.
    * Use o tipo de preço gratuito para experimentar o serviço e atualizar mais tarde para uma camada paga para produção.

### <a name="create-an-environment-variable"></a>Criar uma variável de ambiente

>[!NOTE]
> Os pontos de extremidade para recursos de não avaliação criados após 1º de julho de 2019 usam o formato de subdomínio personalizado mostrado abaixo. Para obter mais informações e uma lista completa de pontos de extremidade regionais, consulte [nomes de subdomínio personalizados para serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Usando a chave e o ponto de extremidade do recurso que você criou, crie duas variáveis de ambiente para autenticação:

* `ANOMALY_DETECTOR_KEY`-a chave de recurso para autenticar suas solicitações.
* `ANOMALY_DETECTOR_ENDPOINT`-o ponto de extremidade de recurso para enviar solicitações de API. Ele terá a seguinte aparência: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Use as instruções para seu sistema operacional.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile` e adicione a variável de ambiente:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.

***