---
title: Utilizar ações de pedido e resposta | Documentos da Microsoft
description: Descrição geral do pedido e resposta de Acionador e ação numa aplicação lógica do Azure
services: ''
documentationcenter: ''
author: jeffhollan
manager: erikre
editor: ''
tags: connectors
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2016
ms.author: jehollan
ms.openlocfilehash: 2479db2abcb578eb380655346582392770606b39
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552185"
---
# <a name="get-started-with-the-request-and-response-components"></a>Comece com os componentes de solicitação e resposta
Com os componentes de pedidos e respostas numa aplicação lógica, pode responder em tempo real a eventos.

Pode, por exemplo:

* Responda a um pedido HTTP com dados de uma base de dados no local através de uma aplicação lógica.
* Acione uma aplicação de lógica de um evento de webhook externo.
* Chame uma aplicação lógica com uma ação de solicitação e resposta a partir de outra aplicação de lógica.

Para começar a utilizar as ações de pedido e resposta numa aplicação lógica, consulte o artigo [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="use-the-http-request-trigger"></a>Utilizar o acionador de pedido de HTTP
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho que está definido numa aplicação lógica. [Saiba mais sobre os acionadores](connectors-overview.md).

Eis um exemplo de sequência de como configurar a um pedido HTTP no Estruturador da aplicação lógica.

1. Adicionar o acionador **pedido - pedido de HTTP de uma quando é recebido** na sua aplicação lógica. Opcionalmente, pode fornecer um esquema JSON (usando uma ferramenta como o [JSONSchema.net](https://jsonschema.net)) para o corpo do pedido. Isso permite que o designer gerar tokens para propriedades no pedido de HTTP.
2. Adicione outra ação para que pode salvar a aplicação lógica.
3. Depois de guardar a aplicação lógica, pode obter o URL do pedido HTTP do cartão pedido.
4. Um HTTP POST (pode usar uma ferramenta como o [Postman](https://www.getpostman.com/)) para o URL aciona a aplicação lógica.

> [!NOTE]
> Se não definir uma ação de resposta, um `202 ACCEPTED` resposta será retornada imediatamente para o chamador. Pode utilizar a ação de resposta para personalizar uma resposta.
> 
> 

![Acionador de resposta](./media/connectors-native-reqres/using-trigger.png)

## <a name="use-the-http-response-action"></a>Utilize a ação de resposta de HTTP
A ação de resposta HTTP só é válida quando usá-lo num fluxo de trabalho que é acionado por um pedido HTTP. Se não definir uma ação de resposta, um `202 ACCEPTED` resposta será retornada imediatamente para o chamador.  Pode adicionar uma ação de resposta em qualquer passo no fluxo de trabalho. A aplicação lógica apenas mantém a solicitação de entrada abertas por um minuto para que uma resposta.  Depois de um minuto, se nenhuma resposta foi enviada a partir do fluxo de trabalho (e existe uma ação de resposta na definição), um `504 GATEWAY TIMEOUT` é retornado ao chamador.

Eis como adicionar uma ação de resposta HTTP:

1. Selecione o **novo passo** botão.
2. Escolher **adicionar uma ação**.
3. Na caixa de pesquisa de ação, escreva **resposta** para listar a ação de resposta.
   
    ![Selecione a ação de resposta](./media/connectors-native-reqres/using-action-1.png)
4. Adicione todos os parâmetros que são necessários para a mensagem de resposta HTTP.
   
    ![Concluir a ação de resposta](./media/connectors-native-reqres/using-action-2.png)
5. Clique no canto superior esquerdo da barra de ferramentas para guardar e a aplicação lógica irá guardar e publicar (ativar).

## <a name="request-trigger"></a>Acionador de pedido
Aqui estão os detalhes para o acionador que este conector suporta. Existe um acionador único pedido.

| Acionador | Descrição |
| --- | --- |
| Pedir |Ocorre quando é recebido um pedido HTTP |

## <a name="response-action"></a>Ação de resposta
Aqui estão os detalhes para a ação que este conector suporta. Existe uma ação única resposta que só pode ser utilizada quando ele é acompanhado por um acionador de pedido.

| Ação | Descrição |
| --- | --- |
| Resposta |Devolve uma resposta ao pedido de HTTP correlacionado |

### <a name="trigger-and-action-details"></a>Detalhes do acionador e ação
As tabelas seguintes descrevem os campos de entrada para o acionador e ação e o correspondente de saída detalhes.

#### <a name="request-trigger"></a>Acionador de pedido
Segue-se um campo de entrada para o acionador de pedidos HTTP de entrada.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Esquema JSON |schema |O esquema JSON do corpo do pedido HTTP |

<br>

**Detalhes de saída**

Seguem-se detalhes de saída para o pedido.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| Cabeçalhos |objeto |Cabeçalhos do pedido |
| Corpo |objeto |Objeto de solicitação |

#### <a name="response-action"></a>Ação de resposta
Seguem-se os campos de entrada para a ação de resposta de HTTP. A * significa que é um campo obrigatório.

| Nome a apresentar | Nome da propriedade | Descrição |
| --- | --- | --- |
| Código de estado * |statusCode |O código de estado HTTP |
| Cabeçalhos |Cabeçalhos |Um objeto JSON de qualquer cabeçalho de resposta para incluir |
| Corpo |corpo |O corpo da resposta |

## <a name="next-steps"></a>Passos Seguintes
Agora, experimente a plataforma e [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pode explorar os outros conectores disponíveis no logic apps examinando nossos [lista APIs](apis-list.md).

