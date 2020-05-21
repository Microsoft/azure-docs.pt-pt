---
title: 'Quickstart: Criar uma nova app no portal LUIS'
description: Neste arranque rápido, cria-se as partes básicas de uma app, intenções e entidades, bem como testar com a expressão da amostra no portal LUIS.
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 7cf55a7891b7e06c18c80d9d359b19e54f0413a9
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697244"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Quickstart: Criar uma nova app no portal LUIS

Neste arranque rápido, você constrói uma nova app no portal LUIS. Em primeiro lugar, criar as partes básicas de uma app, **intenções,** e **entidades.** Em seguida, teste a aplicação fornecendo uma expressão do utilizador da amostra no painel de teste interativo para obter a intenção prevista.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Criar uma aplicação

1. Selecione **+ Nova aplicação para conversação** a partir da barra de ferramentas de contexto e, em seguida, selecione **+ Nova aplicação para conversação** novamente.

    > [!div class="mx-imgBorder"]
    > [![Screenshot da criação de nova app no portal LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Na janela pop-up, configure a aplicação com as seguintes definições e, em seguida, selecione **Done**.

   |Nome da definição| Valor | Objetivo|
   |--|--|--|
   |Name|`myEnglishApp`|Nome único da aplicação LUIS<br>obrigatório|
   |Cultura|**Inglês**|Linguagem das expressões dos utilizadores, **en-us**<br>obrigatório|
   |Descrição (Opcional)|`App made with LUIS Portal`|Descrição da app<br>opcional|
   |Recurso de previsão (Opcional) |-  |Não selecione. O LUIS dá-lhe uma chave Starter para usar gratuitamente para autoria e 1.000 pedidos de ponto final de previsão. |

   ![Screenshot de introduzir novas definições de aplicativos](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Criar intenções

Depois da aplicação LUIS ser criada, é necessário criar intenções. As intenções são uma forma de classificar o texto dos utilizadores. Por exemplo, uma aplicação de recursos humanos pode ter duas funções. Para ajudar as pessoas:

 1. Encontrar e candidatar-se a empregos
 1. Encontrar formulários para se candidatar a empregos

As duas _intenções_ diferentes da aplicação alinham-se às seguintes intenções:

|Intenção|Texto de exemplo do utilizador<br>conhecido como uma _expressão_|
|--|--|
|Candidatura a Emprego|`I want to apply for the new software engineering position in Cairo.`|
|Formulário findform|`Where is the job transfer form hrf-123456?`|

Para criar intenções, complete os seguintes passos:

1. Depois de a aplicação ser criada, está na página **De Intenções** da secção **Build.** Selecione **Criar**.

   [![Screenshot de selecionar 'Criar' para criar nova intenção](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Introduza o nome da intenção `FindForm` e, em seguida, selecione **Done**.

## <a name="add-an-example-utterance"></a>Adicione um exemplo de expressão

Acrescenta-se exemplos depois de criar intenções. Exemplo de expressões são texto que um utilizador introduz num chat bot ou outra aplicação de cliente. Mapeiam a intenção do texto do utilizador para uma intenção LUIS.

Para este exemplo a intenção da aplicação, as `FindForm` declarações de exemplo incluirão o número do formulário. A aplicação do cliente precisa do número de formulário para satisfazer o pedido do utilizador, pelo que é importante incluí-lo na expressão.

> [!div class="mx-imgBorder"]
> [![Screenshot de introduzir pronunciações de exemplo para a intenção FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adicione as seguintes 15 declarações de exemplo à `FindForm` intenção.

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
* verbo tenso (é, foi, será)
* ordem de palavras


## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para devolver o número de formulário na resposta de previsão do tempo de execução, o número de formulário deve ser extraído como entidade. Como o texto do número de formulário é altamente estruturado, pode utilizar uma entidade de expressão regular. Criar a entidade de expressão regular com os seguintes passos:

1. Selecione **Entidades** do menu à esquerda.

1. Selecione **Criar** na página **Entidades.**

1. Introduza o `FormNumber` nome, selecione o tipo de entidade **Regex.**

1. Introduza a expressão regular, `hrf-[0-9]{6}` no campo **Regex.** Esta entrada corresponde aos caracteres literais, `hrf-` e permite exatamente seis dígitos, em seguida, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de criar entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)


    Esta entidade extrai qualquer texto que corresponda à expressão regular em qualquer uma das intenções.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicione declarações exemplo à intenção de Nenhum

A intenção **de "Nenhum"** é a intenção de recuo e não deve ser deixada vazia. Esta intenção deve conter uma expressão por cada 10 exemplos que adicionou para as outras intenções da app.

As declarações de exemplo da **Nenhuma** intenção devem estar fora do domínio da aplicação do seu cliente.

1. Selecione **Intenções** a partir do menu esquerdo e, em seguida, selecione **Nenhuma** da lista de intenções.

1. Adicione as seguintes declarações de exemplo à intenção:

   |Nenhuma intenção exemplo pronunciamentos|
   |--|
   |`Barking dogs are annoying`|
   |`Penguins in the ocean`|

   Para esta aplicação, estas expressões de exemplo estão fora do domínio. Se o seu domínio inclui animais, ou o oceano, então deve usar diferentes pronunciações de exemplo para a intenção **de Ninguém.**

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Olhe para a entidade de expressão regular nas declarações de exemplo

1. Verifique se a entidade está encontrada na intenção **FindForm** selecionando **Intenções** do menu esquerdo. Em seguida, selecione a intenção **FindForm.**

   A entidade é marcada onde aparece nas declarações de exemplo.

   > [!div class="mx-imgBorder"]
   > [![Screenshot de todas as expressões de exemplo marcadas com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Teste a sua nova app com o painel de teste interativo

Utilize o painel de **teste** interativo no portal LUIS para validar que a entidade é extraída de novas expressões que a app ainda não viu.

1. Selecione **Teste** a partir do menu superior direito.

1. Adicione uma nova expressão e, em seguida, prima Enter:

   ```Is there a form named hrf-234098```

    **Selecione Inspecionar** para ver as previsões da entidade.

   > [!div class="mx-imgBorder"]
   > ![Screenshot de testar nova expressão em painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   A principal intenção prevista é corretamente **FindForm** com mais de 90% de confiança (0,977). A entidade **FormNumber** é extraída com um valor de hrf-234098.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com este quickstart e não estiver a avançar para o próximo quickstart, selecione **Minhas aplicações** a partir do menu de navegação de topo. Em seguida, selecione a caixa de verificação esquerda da aplicação da lista e selecione **Eliminar** a partir da barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [2. Implementar uma aplicação](get-started-portal-deploy-app.md)
