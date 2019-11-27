---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483002"
---
Comece a usar o serviço de detector de anomalias criando um dos recursos do Azure abaixo.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Criar um recurso de avaliação (abre em uma nova guia)</a>
    * Nenhuma assinatura do Azure necessária: 
    * Válido por sete dias, gratuitamente. Depois de se inscrever, uma chave de avaliação e um ponto de extremidade estarão disponíveis no [site do Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Essa é uma ótima opção se você quiser experimentar o detector de anomalias, mas não tem uma assinatura do Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Criar um recurso de detector de anomalias (abre em uma nova guia)</a>:
    * Disponível por meio do portal do Azure até que você exclua o recurso.
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
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, reinicie a janela do console.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source ~/.bashrc` a partir da janela da consola para que as alterações entrem em vigor.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Edite seu `.bash_profile`e adicione a variável de ambiente:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.

***