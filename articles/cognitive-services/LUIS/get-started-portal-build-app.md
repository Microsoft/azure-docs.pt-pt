---
title: 'Quickstart: Criar uma nova app no portal LUIS'
description: Neste arranque rápido, cria-se as partes básicas de uma app, intenções e entidades, bem como testar com a expressão da amostra no portal LUIS.
ms.topic: quickstart
ms.date: 03/24/2020
ms.openlocfilehash: f0c8f0c77f832e049dfc494f82e90edb61a8cb2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80244619"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Quickstart: Criar uma nova app no portal LUIS

Neste arranque rápido, você constrói uma nova app no portal LUIS. Em primeiro lugar, criar as partes básicas de uma app, **intenções,** e **entidades.** Em seguida, teste a aplicação fornecendo uma expressão do utilizador da amostra no painel de teste interativo para obter a intenção prevista.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Criar uma aplicação

1. Selecione **+ Nova aplicação para conversação** a partir da barra de ferramentas de contexto e, em seguida, selecione **Nova aplicação para conversação**.

    > [!div class="mx-imgBorder"]
    > [![Criar nova app no portal LUIS](./media/create-app-in-portal.png)](./media/create-app-in-portal.png#lightbox)

1. Na janela pop-up, configure a aplicação com as seguintes definições e, em seguida, selecione **Done**.

   |Nome da definição| Valor | Objetivo|
   |--|--|--|
   |Nome|`myEnglishApp`|Nome único da aplicação LUIS<br>necessário|
   |Cultura|**Inglês**|Linguagem das expressões dos utilizadores, **en-us**<br>necessário|
   |Descrição (Opcional)|`App made with LUIS Portal`|Descrição da app<br>opcional|
   |Recurso de previsão (Opcional) |-  |Não selecione. O LUIS dá-lhe uma chave Starter para usar gratuitamente para autoria e 1.000 pedidos de ponto final de previsão. |

   ![Introduza novas definições de aplicativos](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Criar intenções

Depois da aplicação LUIS ser criada, é necessário criar intenções. As intenções são uma forma de categorizar o texto dos utilizadores. Por exemplo, uma aplicação de recursos humanos pode ter duas funções. Para ajudar as pessoas:

 1. Encontrar e candidatar-se a empregos
 1. Encontrar formulários para se candidatar a empregos

As duas _intenções_ diferentes da aplicação alinham-se às seguintes intenções:

|Intenção|Texto de exemplo do utilizador<br>conhecido como uma _expressão_|
|--|--|
|Candidatura a Emprego|`I want to apply for the new software engineering position in Cairo.`|
|Formulário findform|`Where is the job transfer form hrf-123456?`|

Para criar intenções, complete os seguintes passos:

1. Depois de a aplicação ser criada, está na página **De Intenções** da secção **Build.** Selecione **Criar**.

   [![Selecione Criar para criar novas intenções](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Introduza o `FindForm`nome da intenção e, em seguida, selecione **Done**.

## <a name="add-an-example-utterance"></a>Adicione um exemplo de expressão

Acrescenta-se exemplos depois de criar intenções. Exemplo de expressões são texto que um utilizador introduz num chat bot ou outra aplicação de cliente. Mapeiam a intenção do texto do utilizador para uma intenção LUIS.

Para este exemplo `FindForm` a intenção da aplicação, as declarações de exemplo incluirão o número do formulário. A aplicação do cliente precisa do número de formulário para satisfazer o pedido do utilizador, pelo que é importante incluí-lo na expressão.

> [!div class="mx-imgBorder"]
> [![Introduza pronunciações de exemplo para a intenção FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adicione as seguintes 15 declarações de exemplo à `FindForm` intenção.

|#|Expressões de exemplo|
|--|--|
|1|À procura de hrf-123456|
|2|Onde estão os recursos humanos formados hrf-234591?|
|3|hrf-345623, onde está|
|4|É possível enviar-me hrf-345794|
|5|Preciso do HRF-234695 para me candidatar a um emprego interno?|
|6|O meu empresário precisa de saber que me vou candidatar a um emprego na HRF-234091|
|7|Para onde envio hrf-234918? Recebo uma resposta por e-mail que foi recebida?|
|8|hrf-234555|
|9|Quando é que o HRF-234987 foi atualizado?|
|10|Uso o formulário hrf-876345 para me candidatar a cargos de engenharia|
|11|Foi submetida uma nova versão do HRF-765234 para o meu req aberto?|
|12|Uso hrf-234234 para empregos internacionais?|
|13|hrf-234598 erro ortográfico|
|14|será editado hrf-234567 para novos requisitos|
|15|hrf-123456, hrf-123123, hrf-234567|

Por design, estas expressões de exemplo variam das seguintes formas:

* comprimento de expressão
* pontuação
* escolha de palavras
* verbo tenso (é, foi, será)
* ordem de palavras



## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para devolver o número de formulário na resposta de previsão de tempo de execução, o formulário deve ser marcado como uma entidade. Como o texto do número de formulário é altamente estruturado, pode marcá-lo usando uma entidade de expressão regular. Criar a entidade com os seguintes passos:

1. Selecione **Entidades** do menu à esquerda.

1. Selecione **Criar** na página **Entidades.**

1. Introduza `Human Resources Form Number`o nome, selecione o tipo de entidade **Regex** e, em seguida, selecione **Next**.

   ![Criar entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Introduza a expressão regular **(RegEx)** expressão, `hrf-[0-9]{6}`. Esta entrada corresponde aos `hrf-`caracteres literais, e permite exatamente 6 dígitos, em seguida, selecione **Criar**.

   ![Insira expressão regular para entidade](./media/get-started-portal-build-app/create-regular-expression-entity-with-expression.png)


## <a name="add-example-utterances-to-the-none-intent"></a>Adicione declarações exemplo à intenção de Nenhum

A intenção **de "Nenhum"** é a intenção de recuo e não deve ser deixada vazia. Esta intenção deve conter uma expressão por cada 10 exemplos que adicionou para as outras intenções da app.

As declarações de exemplo da **Nenhuma** intenção devem estar fora do domínio da aplicação do seu cliente.

1. Selecione **Intenções** a partir do menu esquerdo e, em seguida, selecione **Nenhuma** da lista de intenções.

1. Adicione as seguintes declarações de exemplo à intenção:

   |Nenhuma intenção exemplo pronunciamentos|
   |--|
   |Cães que ladram são irritantes|
   |Encomendar uma piza para mim|
   |Pinguins no oceano|

   Para esta aplicação, estas expressões de exemplo estão fora do domínio. Se o seu domínio inclui animais, alimentos ou o oceano, então deve usar diferentes pronunciações de exemplo para a intenção **de Ninguém.**

## <a name="train-the-app"></a>Preparar a aplicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Olhe para a entidade de expressão regular nas declarações de exemplo

1. Verifique se a entidade está encontrada na intenção **FindForm** selecionando **Intenções** do menu esquerdo. Em seguida, selecione a intenção **FindForm.**

   A entidade é marcada onde aparece nas declarações de exemplo. Se quiser ver o texto original em vez do nome da entidade, alternar **entidades Ver** a partir da barra de ferramentas.

   > [!div class="mx-imgBorder"]
   > [![Todos os exemplos marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Teste a sua nova app com o painel de teste interativo

Utilize o painel de **teste** interativo no portal LUIS para validar que a entidade é extraída de novas expressões que a app ainda não viu.

1. Selecione **Teste** a partir do menu superior direito.

1. Adicione uma nova expressão e, em seguida, prima Enter:

   ```Is there a form named hrf-234098```

    **Selecione Inspecionar** para ver as previsões da entidade.

   > [!div class="mx-imgBorder"]
   > ![Teste nova expressão no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   A principal intenção prevista é corretamente **FindForm** com mais de 90% de confiança (0,977). A entidade número de formulário de **recursos humanos** é extraída com um valor de hrf-234098.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com este quickstart e não estiver a avançar para o próximo quickstart, selecione **Minhas aplicações** a partir do menu de navegação de topo. Em seguida, selecione a caixa de verificação esquerda da aplicação da lista e selecione **Eliminar** a partir da barra de ferramentas de contexto acima da lista.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [2. Implementar uma aplicação](get-started-portal-deploy-app.md)
