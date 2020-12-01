---
title: Obtenha resposta predefinitiva - QnA Maker
description: A resposta por defeito é devolvida quando não há correspondência com a pergunta. Pode querer alterar a resposta predefinida da resposta padrão.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 5aab021ab5194b4af18e3ff1b2c154ed74710353
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96346125"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Alterar a resposta padrão para um recurso do QnA Maker

A resposta padrão para uma base de conhecimento deve ser devolvida quando uma resposta não é encontrada. Se estiver a utilizar uma aplicação do cliente, como o [serviço Azure Bot,](/azure/bot-service/bot-builder-howto-qna)também poderá ter uma resposta por defeito separada, indicando que nenhuma resposta cumpriu o limiar de pontuação.

## <a name="types-of-default-answer"></a>Tipos de resposta predefinido

Existem dois tipos de resposta padrão na sua base de conhecimento. É importante entender como e quando cada um é devolvido de uma consulta de previsão:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

|Tipos de respostas predefinidos|Descrição da resposta|
|--|--|
|Resposta KB quando não é determinada resposta|`No good match found in KB.` - Quando a [API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) não encontra uma resposta correspondente à pergunta, a `DefaultAnswer` definição do serviço App é devolvida. Todas as bases de conhecimento no mesmo recurso QnA Maker partilham o mesmo texto de resposta predefinido.<br>Pode gerir a definição no portal Azure, através do serviço App, ou com as APIs REST para [obter](/rest/api/appservice/webapps/listapplicationsettings) ou [atualizar](/rest/api/appservice/webapps/updateapplicationsettings) a definição.|
|Texto de instrução de acompanhamento rápido|Ao utilizar um pedido de seguimento num fluxo de conversação, pode não precisar de uma resposta no par QnA porque pretende que o utilizador selecione a partir das indicações de seguimento. Neste caso, defina texto específico definindo o texto de resposta predefinido, que é devolvido com cada previsão para as indicações de seguimento. O texto destina-se a exibir como texto instrutivo a seleção de instruções. Um exemplo para este texto de resposta predefinido é `Please select from the following choices` . Esta configuração é explicada nas próximas secções deste documento. Também pode definir como parte da definição de base de conhecimento da utilização da `defaultAnswerUsedForExtraction` [API REST](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

|Tipos de respostas predefinidos|Descrição da resposta|
|--|--|
|Resposta KB quando não é determinada resposta|`No good match found in KB.` - Quando a [API GenerateAnswer](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) não encontra uma resposta correspondente à pergunta, apresenta uma resposta de texto predefinido. Na QnA Maker gerida (Pré-visualização) pode definir este texto nas **Definições** da sua base de conhecimento. <br><br> ![QnA Maker gerido (Preview) definir resposta padrão](../media/qnamaker-how-change-default-answer/qnamaker-v2-change-default-answer.png)|
|Texto de instrução de acompanhamento rápido|Ao utilizar um pedido de seguimento num fluxo de conversação, pode não precisar de uma resposta no par QnA porque pretende que o utilizador selecione a partir das indicações de seguimento. Neste caso, defina texto específico definindo o texto de resposta predefinido, que é devolvido com cada previsão para as indicações de seguimento. O texto destina-se a exibir como texto instrutivo a seleção de instruções. Um exemplo para este texto de resposta predefinido é `Please select from the following choices` . Esta configuração é explicada nas próximas secções deste documento. Também pode definir isto como parte de uma definição de base de conhecimento com `defaultAnswerUsedForExtraction` a utilização da [API REST](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

---

### <a name="client-application-integration"></a>Integração de aplicações ao cliente

Para uma aplicação ao cliente, como um bot com o **serviço Azure Bot,** pode escolher entre os seguintes cenários comuns:

* Use a definição da base de conhecimento
* Use texto diferente na aplicação do cliente para distinguir quando uma resposta é devolvida, mas não cumpre o limiar de pontuação. Este texto pode ser armazenado em código ou pode ser armazenado na lista de definições da aplicação do cliente.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Desa cos para definir a resposta predefinida do pedido de acompanhamento quando criar base de conhecimento

Quando cria uma nova base de conhecimento, o texto de resposta predefinido é uma das definições. Se optar por não o definir durante o processo de criação, pode alterá-lo mais tarde com o seguinte procedimento.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Alterar a resposta padrão do pedido de seguimento no portal QnA Maker

A resposta predefinitiva da base de conhecimento é devolvida quando nenhuma resposta é devolvida do serviço QnA Maker.

1. Inscreva-se no [portal QnA Maker](https://www.qnamaker.ai/) e selecione a sua base de conhecimentos na lista.
1. Selecione **Definições** da barra de navegação.
1. Alterar o valor do texto de **resposta predefinido** na secção **'Gerir o conhecimento'.**

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Screenshot do portal QnA Maker, página de Definições, com caixa de texto de resposta predefinida realçada.":::

1. **Selecione Guardar e treinar** para salvar a mudança.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma base de conhecimento](../How-to/manage-knowledge-bases.md)