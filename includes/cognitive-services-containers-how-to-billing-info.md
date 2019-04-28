---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60598911"
---
Consultas para o contentor são faturadas ao escalão de preço do recurso do Azure utilizado para o `<ApiKey>`.

Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligada ao ponto final de faturação de medição. Os clientes têm de ativar os contentores comunicar informações de faturação com ponto final de faturação durante todo o tempo. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft. 

### <a name="connecting-to-azure"></a>Ligar ao Azure

O contentor tem dos valores de argumento de faturação para executar. Estes valores permitem que o contentor ligar ao ponto final de faturação. O contentor de relatórios de utilização sobre a cada 10 a 15 minutos. Se o contentor não ligar-se dentro da janela de tempo permitido para o Azure, o contêiner continuará a executar, mas será não servir consultas até que o ponto final de faturação seja restaurado. A ligação é tentada 10 vezes no mesmo intervalo de tempo de 10 a 15 minutos. Se não é possível ligar ao ponto final de faturação dentro de 10 tentativas, o contentor deixa de ser executado. 

### <a name="billing-arguments"></a>Argumentos de faturação

Todos os três das seguintes opções tem de ser especificados com valores válidos para que o `docker run` comando para iniciar o contentor:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de serviço cognitivo utilizada para controlar informações de faturas.<br/>O valor desta opção tem de ser definido para uma chave de API para o recurso aprovisionado especificado no `Billing`. |
| `Billing` | O ponto final do recurso de serviço cognitivo utilizado para controlar informações de faturas.<br/>O valor desta opção tem de ser definido para o ponto final do URI de um recurso do Azure aprovisionado.|
| `Eula` | Indica que aceite a licença para o contentor.<br/>O valor desta opção tem de ser definido `accept`. |


