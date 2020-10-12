---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77474175"
---
As consultas ao contentor são cobradas no nível de preços do recurso Azure que é usado para `ApiKey` .

Os recipientes Azure Cognitive Services não estão licenciados para funcionar sem estarem ligados ao ponto final de medição/faturação. Deve permitir que os recipientes comuniquem sempre informações de faturação com o ponto final de faturação. Os contentores dos Serviços Cognitivos não enviam dados dos clientes, como a imagem ou texto que está a ser analisado, para a Microsoft.

### <a name="connect-to-azure"></a>Ligar ao Azure

O contentor precisa que os valores do argumento da faturação corram. Estes valores permitem que o recipiente se conecte ao ponto final de faturação. O recipiente relata o uso a cada 10 a 15 minutos. Se o recipiente não ligar ao Azure dentro da janela de tempo permitida, o recipiente continua a funcionar, mas não serve consultas até que o ponto final de faturação seja restaurado. A ligação é tentada 10 vezes ao mesmo tempo de 10 a 15 minutos. Se não conseguir ligar-se ao ponto final de faturação dentro dos 10 tentativas, o recipiente deixa de servir os pedidos.

### <a name="billing-arguments"></a>Argumentos de faturação

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` O <span class="docon docon-navigate-external x-hidden-focus"></span> </a> comando iniciará o contentor quando as três opções seguintes forem fornecidas com valores válidos:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave API do recurso Serviços Cognitivos que é usado para rastrear informações de faturação.<br/>O valor desta opção deve ser definido como uma chave API para o recurso provisionado especificado em `Billing` . |
| `Billing` | O ponto final do recurso dos Serviços Cognitivos que é usado para rastrear a informação de faturação.<br/>O valor desta opção deve ser definido como uri ponto final de um recurso Azure provisionado.|
| `Eula` | Indica que aceitou a licença para o contentor.<br/>O valor desta opção deve ser definido para **ser aceite.** |
