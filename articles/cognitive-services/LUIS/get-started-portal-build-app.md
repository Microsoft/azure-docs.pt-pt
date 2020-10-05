---
title: 'Quickstart: Criar uma nova app no portal LUIS'
description: Neste arranque rápido, cria-se as partes básicas de uma app, intenções e entidades, bem como teste com a expressão de amostra no portal LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 38fffd7793e1f5bd59ac6dde4499b2eb25009b52
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91303867"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Quickstart: Criar uma nova app no portal LUIS

Neste arranque rápido, constrói-se uma nova app no portal LUIS. Em primeiro lugar, crie as partes básicas de uma app, **intenções**e **entidades.** Em seguida, teste a aplicação fornecendo uma expressão do utilizador da amostra no painel de teste interativo para obter a intenção prevista.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Criar uma aplicação

1. Selecione **+ Nova aplicação para conversação** a partir da barra de ferramentas de contexto e, em seguida, selecione **+ Nova aplicação para conversação** novamente.

    > [!div class="mx-imgBorder"]
    > [![Screenshot da criação de nova app no portal LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Na janela pop-up, configufique a aplicação com as seguintes definições e, em seguida, selecione **Fazer**.

   |Nome da definição| Valor | Objetivo|
   |--|--|--|
   |Name|`myEnglishApp`|Nome exclusivo da aplicação LUIS<br>obrigatório|
   |Cultura|**Inglês**|Linguagem de expressões dos utilizadores, **en-us**<br>obrigatório|
   |Descrição (Opcional)|`App made with LUIS Portal`|Descrição do aplicativo<br>opcional|
   |Recurso de previsão (Opcional) |-  |Não selecione. O LUIS dá-lhe uma chave de arranque para usar gratuitamente para a autoria e 1.000 pedidos de ponto final de previsão. |

   ![Screenshot de introduzir novas definições de aplicações](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Criar intenções

Depois da criação da app LUIS, é necessário criar intenções. As intenções são uma forma de classificar o texto dos utilizadores. Por exemplo, uma aplicação de recursos humanos pode ter duas funções. Para ajudar as pessoas:

 1. Encontrar e candidatar-se a empregos
 1. Encontrar formulários para se candidatar a empregos

As _duas intenções diferentes_ da aplicação alinham-se com as seguintes intenções:

|Intenção|Texto de exemplo do utilizador<br>conhecido como uma _expressão_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|LocalizarForm|`Where is the job transfer form hrf-123456?`|

Para criar intenções, complete os seguintes passos:

1. Após a criação da aplicação, está na página **Intenções** da secção **Build.** Selecione **Criar**.

   [![Screenshot de selecionar 'Criar' para criar novas intenções](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Introduza o nome de `FindForm` intenção, e, em seguida, selecione **Feito**.

## <a name="add-an-example-utterance"></a>Adicione um exemplo de expressão

Adiciona-se palavras de exemplo depois de criar intenções. As palavras exemplo são texto que um utilizador introduz num chat bot ou outra aplicação de cliente. Eles mapeiam a intenção do texto do utilizador para uma intenção LUIS.

Para este `FindForm` exemplo, as intenções da aplicação, exemplo, as expressões incluirão o número do formulário. A aplicação do cliente precisa do número do formulário para satisfazer o pedido do utilizador, por isso é importante incluí-lo na expressão.

> [!div class="mx-imgBorder"]
> [![Screenshot de introduzir palavras de exemplo para a intenção FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adicione as seguintes 15 palavras de exemplo à `FindForm` intenção.

|#|Expressões de exemplo|
|--|--|
|1|`Looking for hrf-123456`|
|2|`Where is the human resources form hrf-234591?`|
|3|`hrf-345623, where is it`|
|4|`Is it possible to send me hrf-345794`|
|5|`Do I need hrf-234695 to apply for an internal job?`|
|6|`Does my manager need to know I'm applying for a job with hrf-234091`|
|7|`Where do I send hrf-234918? Do I get an email response it was received?`|
|8|`hrf-234555`|
|9|`When was hrf-234987 updated?`|
|10|`Do I use form hrf-876345 to apply for engineering positions`|
|11|`Was a new version of hrf-765234 submitted for my open req?`|
|12|`Do I use hrf-234234 for international jobs?`|
|13|`hrf-234598 spelling mistake`|
|14|`will hrf-234567 be edited for new requirements`|
|15|`hrf-123456, hrf-123123, hrf-234567`|

Por design, estas expressões de exemplo variam das seguintes formas:

* comprimento de expressão
* [pontuação](luis-reference-application-settings.md#punctuation-normalization)
* escolha de palavras
* verbo tenso (é, foi, vai ser)
* ordem de palavras


## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para devolver o número do formulário na resposta de previsão do tempo de execução, o número do formulário deve ser extraído como entidade. Como o texto do número do formulário é altamente estruturado, pode utilizar uma entidade de expressão regular. Criar a entidade de expressão regular com os seguintes passos:

1. Selecione **Entidades** do menu à esquerda.

1. Selecione **Criar** na página **De Entidades.**

1. Introduza o `FormNumber` nome, selecione o tipo de entidade **Regex.**

1. Introduza a expressão regular, `hrf-[0-9]{6}` no campo **Regex.** Esta entrada corresponde aos caracteres `hrf-` literais, e permite exatamente seis dígitos, em seguida, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da criação de entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Esta entidade extrai qualquer texto que corresponda à expressão regular em qualquer uma das intenções.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicione palavras de exemplo à intenção de Ninguém

A intenção **de Nenhum** é a intenção de recuo e não deve ser deixada vazia. Esta intenção deve conter uma expressão para cada 10 palavras de exemplo que adicionou para as outras intenções da app.

As declarações de exemplo da **intenção** de Ninguém devem estar fora do domínio da aplicação do seu cliente.

1. Selecione **Intenções** a partir do menu esquerdo e, em seguida, **selecione Nenhum** da lista de intenções.

1. Adicione as seguintes declarações de exemplo à intenção:

   |Nenhuma intenção exemplo declarações|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Para esta aplicação, estas expressões de exemplo estão fora do domínio. Se o seu domínio inclui animais, ou o oceano, então deve usar diferentes palavras de exemplo para a intenção **de Nenhum.**

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Olhe para a entidade de expressão regular nas expressões de exemplo

1. Verifique se a entidade está encontrada na intenção **FindForm**  selecionando **Intenções** a partir do menu esquerdo. Em seguida, selecione a intenção **FindForm.**

   A entidade está marcada onde aparece nas expressões de exemplo.

   > [!div class="mx-imgBorder"]
   > [![Screenshot de todos os exemplos de declarações marcadas com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Teste a sua nova app com o painel de teste interativo

Utilize o painel de **teste** interativo no portal LUIS para validar que a entidade é extraída de novas expressões que a app ainda não viu.

1. Selecione **Teste** a partir do menu superior direito.

1. Adicione uma nova expressão e, em seguida, pressione Enter:

   ```Is there a form named hrf-234098```

    **Selecione Inspecionar** para ver as previsões da entidade.

   > [!div class="mx-imgBorder"]
   > ![Screenshot de testar nova expressão no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   A intenção prevista é corretamente **FindForm** com mais de 90% de confiança (0,977). A entidade **FormNumber** é extraída com um valor de hrf-234098.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este arranque rápido e não estiver a avançar para o próximo quickstart, selecione **As minhas aplicações** a partir do menu de navegação de topo. Em seguida, selecione a caixa de verificação esquerda da aplicação da lista e selecione  **Delete** da barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [2. Implementar uma aplicação](get-started-portal-deploy-app.md)
