---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483002"
---
Comece a utilizar o serviço Detetor de Anomalias criando um dos recursos Azure abaixo.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Criar um recurso experimental (abre-se num novo separador)</a>
    * Não é necessária nenhuma subscrição Azure: 
    * Válido por sete dias, de graça. Após a inscrição, uma chave de ensaio e ponto final estará disponível no site do [Azure.](https://azure.microsoft.com/try/cognitive-services/my-apis/) 
    * Esta é uma ótima opção se quiser experimentar o Detetor de Anomalias, mas não tem uma subscrição Azure.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Criar um recurso do Detetor de Anomalias (abre-se num novo separador)</a>:
    * Disponível através do portal Azure até eliminar o recurso.
    * Utilize o nível de preços gratuitos para experimentar o serviço e atualize mais tarde para um nível pago para produção.



### <a name="create-an-environment-variable"></a>Criar uma variável ambiental

>[!NOTE]
> Os pontos finais dos recursos não experimentais criados após 1 de julho de 2019 utilizam o formato de subdomínio personalizado mostrado abaixo. Para mais informações e uma lista completa de pontos finais regionais, consulte [nomes de subdomínio personalizado para Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Utilizando a sua chave e ponto final a partir do recurso que criou, crie duas variáveis ambientais para autenticação:

* `ANOMALY_DETECTOR_KEY`- A chave de recursos para autenticar os seus pedidos.
* `ANOMALY_DETECTOR_ENDPOINT`- O ponto final do recurso para o envio de pedidos de API. Vai ficar assim: 
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

Edite `.bash_profile`a sua , e adicione a variável ambiental:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

Depois de adicionar a variável de ambiente, execute `source .bash_profile` a partir da janela da consola para que as alterações entrem em vigor.

***