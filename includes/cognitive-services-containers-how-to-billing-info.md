---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77474175"
---
As consultas ao contentor são faturadas no nível de preços do `ApiKey`recurso Azure que é usado para o .

Os recipientes dos Serviços Cognitivos Azure não estão licenciados para funcionar sem estarem ligados ao ponto final de medição/faturação. Deve permitir que os recipientes comuniquem sempre informações de faturação com o ponto final da faturação. Os recipientes dos Serviços Cognitivos não enviam dados dos clientes, como a imagem ou texto que está a ser analisado, para a Microsoft.

### <a name="connect-to-azure"></a>Ligar ao Azure

O recipiente precisa dos valores do argumento de faturação para funcionar. Estes valores permitem que o recipiente se conectem ao ponto final da faturação. O recipiente reporta o uso a cada 10 a 15 minutos. Se o recipiente não ligar ao Azure dentro da janela de tempo permitida, o recipiente continua a funcionar, mas não serve consultas até que o ponto final da faturação seja restaurado. A ligação é tentada 10 vezes no mesmo intervalo de tempo de 10 a 15 minutos. Se não conseguir ligar-se ao ponto final da faturação dentro das 10 tentativas, o recipiente deixa de servir pedidos.

### <a name="billing-arguments"></a>Argumentos de faturação

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` O <span class="docon docon-navigate-external x-hidden-focus"></span> </a> comando iniciará o contentor quando as três seguintes opções forem fornecidas com valores válidos:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave DaPI do recurso dos Serviços Cognitivos que é usado para rastrear informações de faturação.<br/>O valor desta opção deve ser definido para uma chave API `Billing`para o recurso provisionado especificado em . |
| `Billing` | O ponto final do recurso dos Serviços Cognitivos que é usado para rastrear informações de faturação.<br/>O valor desta opção deve ser fixado para o ponto final URI de um recurso Azure provisionado.|
| `Eula` | Indica que aceitou a licença para o contentor.<br/>O valor desta opção deve ser definido para **aceitar**. |
