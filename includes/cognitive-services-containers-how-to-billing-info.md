---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: bfda8b83f1bedf11151ba89b58c95347aa35839a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052035"
---
Consultas para o contentor são faturadas ao escalão de preço do recurso do Azure que é utilizado para o `<ApiKey>`.

Contentores de serviços cognitivos do Azure não estão licenciados para executar sem a ser ligada ao ponto final de faturação de medição. Tem de ativar os contentores comunicar informações de faturação com o ponto final de faturação durante todo o tempo. Contentores de serviços cognitivos não enviam dados de cliente, como a imagem ou texto que está a ser analisado, para a Microsoft. 

### <a name="connect-to-azure"></a>Ligar ao Azure

O contentor tem dos valores de argumento de faturação para executar. Estes valores permitem que o contentor ligar ao ponto final de faturação. O contentor de relatórios de utilização sobre a cada 10 a 15 minutos. Se o contentor não ligar ao Azure dentro da janela de tempo permitido, o contentor continua a ser executado, mas não serve consultas até que o ponto final de faturação seja restaurado. A ligação é tentada 10 vezes no mesmo intervalo de tempo de 10 a 15 minutos. Se ele não é possível ligar ao tenta o ponto final de faturação dentro de 10, o contentor deixa de ser executada. 

### <a name="billing-arguments"></a>Argumentos de faturação

Para o `docker run` comando para iniciar o contentor, três das seguintes opções tem de ser especificados com valores válidos:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API do recurso dos serviços cognitivos que é utilizado para controlar informações de faturação.<br/>O valor desta opção tem de ser definido para uma chave de API para o recurso aprovisionado especificado no `Billing`. |
| `Billing` | O ponto final do recurso dos serviços cognitivos que é utilizado para controlar informações de faturação.<br/>O valor desta opção tem de ser definido para o ponto final do URI de um recurso do Azure aprovisionado.|
| `Eula` | Indica que aceite a licença para o contentor.<br/>O valor desta opção tem de ser definido como **aceitar**. |


