---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2019
ms.openlocfilehash: b20ee2df1f468323c3298854371c97950c017e49
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57788910"
---
Consultas para o contentor são faturadas ao escalão de preço do recurso do Azure utilizado para o `<ApiKey>`.

Contentores de serviços cognitivos não estão licenciados para executar sem a ser ligado ao Azure para medição. Os clientes têm de ativar os contentores comunicar informações de faturação com o serviço de medição em todos os momentos. Contentores de serviços cognitivos não enviar dados de cliente (por exemplo, a imagem ou texto que está a ser analisado) para a Microsoft. O contentor de relatórios de utilização sobre a cada 10 a 15 minutos.

O `docker run` utiliza os argumentos a seguir para fins de faturação:

| Opção | Descrição |
|--------|-------------|
| `ApiKey` | A chave de API do recurso de serviço cognitivo utilizada para controlar informações de faturas.<br/>O valor desta opção tem de ser definido para uma chave de API para o recurso aprovisionado especificado no `Billing`. |
| `Billing` | O ponto final do recurso de serviço cognitivo utilizado para controlar informações de faturas.<br/>O valor desta opção tem de ser definido para o ponto final do URI de um recurso do LUIS Azure aprovisionado.|
| `Eula` | Indica que aceite a licença para o contentor.<br/>O valor desta opção tem de ser definido `accept`. |

> [!IMPORTANT]
> Todas as três opções tem de ser especificadas com valores válidos ou não inicia o contentor.
