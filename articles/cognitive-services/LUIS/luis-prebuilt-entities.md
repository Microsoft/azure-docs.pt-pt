---
title: Entidades pré-concebidas
titleSuffix: Azure Cognitive Services
description: LUIS inclui um conjunto de entidades previamente concebidas para reconhecimento de tipos comuns de informações, como as datas, horas, números, medidas e moeda. Suporte de entidade pré-criados varia consoante a cultura da sua aplicação LUIS.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cfc4ff58cfeb65f80f9ac5ce2dd532defde5ef8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58119093"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Entidades previamente concebidas para reconhecer os tipos de dados comuns

LUIS inclui um conjunto de entidades previamente concebidas para reconhecimento de tipos comuns de informações, como datas, horas, números, medidas e moeda. 

## <a name="add-a-prebuilt-entity"></a>Adicionar uma entidade pré-criados

1. Abra a sua aplicação ao clicar em seu nome na **as minhas aplicações** página e, em seguida, clique em **entidades** no lado esquerdo. 

1. Sobre o **entidades** página, clique em **adicionar entidade pré-criados**.

1. Na **adicionar entidades pré-concebidas** diálogo caixa, selecione o datetimeV2 entidade pré-criados. 

    ![Adicionar a caixa de diálogo de entidade predefinidos](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Selecione **Done** (Concluído).

## <a name="publish-the-app"></a>Publicar a aplicação

A maneira mais fácil para ver o valor de uma entidade pré-criados é a consulta do ponto de extremidade publicado. 

1. Na barra de ferramentas superior, selecione **publicar**. Publicar no **produção**. 

1. Quando a notificação de êxito verde é apresentada, selecione o **consulte a lista de pontos de extremidade** ligação para ver os pontos de extremidade.

1. Selecione um ponto de extremidade. É aberto um novo separador do browser para esse ponto final. Mantenha o separador do browser aberta e avance para o **teste** secção.

## <a name="test"></a>Teste
Depois da entidade é adicionada, não é necessário preparar a aplicação. 

Teste a intenção de novo no ponto final adicionado por um valor para o **p** parâmetro. Utilize a seguinte tabela para expressões sugeridas para **p**:

|Expressão de teste|Valor de entidade|
|--|:--|
|Programar um vôo de amanhã|2018-10-19|
|Cancelar a marcação de 3 de Março|LUIS devolveu o mais recente 3 de Março no passado (2018-03-03) e 3 de Março no futuro (2019-03-03) porque a expressão não especificou um ano.|
|Agendar uma reunião em 10 am|10:00:00|

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>Marcar entidades que contém um token de entidade predefinidos
 Se tiver texto, como `HH-1234`, que pretende marcar como uma entidade personalizada _e_ tiver [número pré-criados](luis-reference-prebuilt-number.md) adicionada ao modelo, não será possível marcar a entidade personalizada no portal do LUIS. Pode marcá-la com a API. 

 Para marcar esse tipo de token, onde parte dele já está marcado com uma entidade pré-criados, remova a entidade pré-concebidos a partir da aplicação do LUIS. Não precisa de preparar a aplicação. Em seguida, marque o token com sua própria entidade personalizada. Em seguida, adicione a entidade criados previamente para a aplicação do LUIS.

 Por outro exemplo, considere a expressão como uma lista de preferências de classe: `I want first year spanish, second year calculus, and fourth year english lit.` Se a aplicação do LUIS tiver adicionado, o Ordinal de Prebuild `first`, `second`, e `fourth` já serão assinaladas com ordinais. Se quiser capturar o ordinal e a classe, pode criar uma entidade composta e encapsular o Ordinal criados previamente e a entidade personalizada para o nome da classe.

## <a name="next-steps"></a>Passos Seguintes
> [!div class="nextstepaction"]
> [Referência de entidade predefinidos](./luis-reference-prebuilt-entities.md)
