---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704289"
---
As consultas ao contêiner são cobradas no tipo de preço do recurso do Azure que é usado para o `<ApiKey>`.

Os contêineres de serviços cognitivas do Azure não são licenciados para serem executados sem serem conectados ao ponto de extremidade de cobrança para medição. Você deve habilitar os contêineres para comunicar informações de cobrança com o ponto de extremidade de cobrança em todos os momentos. Os contêineres de serviços cognitivas não enviam dados do cliente, como a imagem ou o texto que está sendo analisado para a Microsoft. 

### <a name="connect-to-azure"></a>Ligar ao Azure

O contêiner precisa dos valores do argumento de cobrança para executar. Esses valores permitem que o contêiner se conecte ao ponto de extremidade de cobrança. O contêiner relata o uso de cada 10 a 15 minutos. Se o contêiner não se conectar ao Azure dentro da janela de tempo permitida, o contêiner continuará a ser executado, mas não atenderá a consultas até que o ponto de extremidade de cobrança seja restaurado. A conexão é tentada 10 vezes ao mesmo tempo de 10 a 15 minutos. Se ele não puder se conectar ao ponto de extremidade de cobrança dentro de 10 tentativas, o contêiner parará de ser executado. 

### <a name="billing-arguments"></a>Argumentos de cobrança

Para o comando `docker run` para iniciar o contêiner, todas as três opções a seguir devem ser especificadas com valores válidos:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de serviços cognitivas que é usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como uma chave de API para o recurso provisionado que é especificado em `Billing`. |
| `Billing` | O ponto de extremidade do recurso de serviços cognitivas que é usado para rastrear informações de cobrança.<br/>O valor dessa opção deve ser definido como o URI do ponto de extremidade de um recurso do Azure provisionado.|
| `Eula` | Indica que você aceitou a licença para o contêiner.<br/>O valor dessa opção deve ser definido como **aceitar**. |


