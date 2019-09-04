---
title: 'Início rápido: Criar um novo aplicativo no portal do LUIS'
titleSuffix: Azure Cognitive Services
description: Neste guia de início rápido, você cria um novo aplicativo no portal do LUIS. Crie as partes básicas de um aplicativo, intenções e entidades. Teste o aplicativo fornecendo um expressão de usuário de exemplo no painel de teste interativo para obter a intenção prevista. Criar um aplicativo é gratuito; Ele não requer uma assinatura do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 3bdada262df3778ab8781f967fdbd64a780009b2
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70258288"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Início rápido: Criar um novo aplicativo no portal do LUIS

Neste guia de início rápido, você cria um novo aplicativo no [portal do Luis](https://www.luis.ai). Primeiro, você cria as partes básicas de um aplicativo, **intenções**e **entidades**. Em seguida, você testa o aplicativo fornecendo um expressão de usuário de exemplo no painel de teste interativo para obter a intenção prevista.

A criação de um aplicativo é gratuita e não requer uma assinatura do Azure. Quando estiver pronto para implantar seu aplicativo, consulte o guia de [início rápido para implantar um aplicativo](get-started-portal-deploy-app.md). Ele mostra como criar um recurso de serviço do Azure cognitiva e atribuí-lo ao aplicativo.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-an-app"></a>Criar uma aplicação

1. Selecione **criar novo aplicativo** na barra de ferramentas de contexto.

   [![Criar novo aplicativo no portal do LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Na janela pop-up, configure o aplicativo com as seguintes configurações e, em seguida, selecione **concluído**.

   |Nome da definição| Value | Objetivo|
   |--|--|--|
   |Name|`myEnglishApp`|Nome do aplicativo LUIS exclusivo<br>obrigatório|
   |Cultura|**Inglês**|Idioma do declarações dos usuários, **en-US**<br>obrigatório|
   |Descrição|`App made with LUIS Portal`|Descrição do aplicativo<br>opcional|
   | | | |

   ![Inserir novas configurações de aplicativo](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Criar intenções

Depois que o aplicativo LUIS for criado, você precisará criar tentativas. As intenções são uma maneira de categorizar o texto dos usuários. Por exemplo, um aplicativo de recursos humanos pode ter duas funções. Para ajudar as pessoas:

 1. Localizar e aplicar para trabalhos
 1. Localizar formulários a serem aplicados para trabalhos

As duas _intenções_ diferentes do aplicativo se alinham com as seguintes tentativas:

|Intenção|Texto de exemplo do usuário<br>conhecido como um _expressão_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Para criar tentativas, conclua as seguintes etapas:

1. Depois que o aplicativo for criado, você estará na página de **tentativas** da seção de **compilação** . Selecione **Create new intent** (Criar nova intenção).

   [![Selecione o botão Criar nova tentativa](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Insira o nome da intenção `FindForm`, e, em seguida, selecione **concluído**.

   ![Insira o nome da intenção de FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Você adiciona o exemplo declarações depois de criar tentativas. O exemplo declarações é um texto que um usuário insere em um bot de chat ou outro aplicativo cliente. Eles mapeiam a intenção do texto do usuário para uma intenção LUIS.

Para a `FindForm` intenção deste aplicativo de exemplo, o exemplo declarações incluirá o número do formulário. O aplicativo cliente precisa do número do formulário para atender à solicitação do usuário, portanto, é importante incluí-lo no expressão.

[![Insira o exemplo declarações para a intenção de FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adicione os 15 exemplos de declarações a seguir `FindForm` à intenção.

|#|Expressões de exemplo|
|--|--|
|1|Procurando HRF-123456|
|2|Onde está o formulário de recursos humanos HRF-234591?|
|3|HRF-345623, onde está|
|4|É possível enviar-me HRF-345794|
|5|É necessário que o HRF-234695 seja aplicado a um trabalho interno?|
|6|Meu gerente precisa saber que estou aplicando um trabalho com HRF-234091|
|7|Onde posso enviar HRF-234918? Recebo uma resposta por email que foi recebida?|
|8|HRF-234555|
|9|Quando o HRF-234987 foi atualizado?|
|10|Eu uso o formulário HRF-876345 para ser aplicado para posições de engenharia|
|11|Foi uma nova versão do HRF-765234 enviada para o meu req aberto?|
|12|Eu uso o HRF-234234 para trabalhos internacionais?|
|13|erro de ortografia de HRF-234598|
|14|o HRF-234567 será editado para novos requisitos|
|15|HRF-123456, HRF-123123, HRF-234567|

Por design, esses declarações de exemplo variam das seguintes maneiras:

* comprimento de expressão
* Pontuação
* opção de palavra
* o verbo conjugação (is, was, será)
* ordem de palavras



## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para retornar o número do formulário na resposta de previsão de tempo de execução, o formulário deve ser marcado como uma entidade. Como o texto do número do formulário é altamente estruturado, você pode marcá-lo usando uma entidade de expressão regular. Crie a entidade com as seguintes etapas:

1. Selecione **entidades** no menu à esquerda.

1. Selecione **criar nova entidade** na página **entidades** .

1. Insira o nome `Human Resources Form Number`, selecione o tipo de entidade **Regex** e insira a expressão regular, `hrf-[0-9]{6}`. Essa entrada corresponde aos caracteres literais `hrf-`, e permite exatamente 6 dígitos.

   ![Inserir informações de entidade para a entidade expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selecione **Done** (Concluído).

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar declarações de exemplo à intenção None

A tentativa **None** é a tentativa de fallback e não deve ser deixada em branco. Essa intenção deve conter um expressão para cada 10 exemplos de declarações que você adicionou para as outras intenções do aplicativo.

O exemplo de declarações da intenção de **nenhum** deve estar fora do domínio do aplicativo cliente.

1. Selecione **tentativas** no menu à esquerda e, em seguida, selecione **nenhum** na lista de tentativas.

1. Adicione o seguinte exemplo declarações à intenção:

   |Exemplo de intenção de nenhum declarações|
   |--|
   |Cães que ladram são irritantes|
   |Encomendar uma piza para mim|
   |Pinguins no oceano|

   Para este aplicativo de recursos humanos, esses declarações de exemplo estão fora do domínio. Se o seu domínio de recursos humanos incluir animais, comida ou oceano, você deverá usar um exemplo diferente de declarações para a intenção de **nenhum** .

## <a name="train-the-app"></a>Preparar a aplicação

No menu no canto superior direito, selecione **treinar** para aplicar as alterações de modelo de intenção e entidade à versão atual do aplicativo.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examine a entidade expressão regular no exemplo declarações

1. Verifique se a entidade foi encontrada na **FindForm** intenção selecionando **tentativas** no menu à esquerda. Em seguida, selecione **FindForm** intenção.

   A entidade é marcada onde aparece no exemplo declarações. Se você quiser ver o texto original em vez do nome da entidade, alterne a **exibição de entidades** na barra de ferramentas.

   [![Todos os declarações de exemplo marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testar seu novo aplicativo com o painel de teste interativo

Use o painel de **teste** interativo no portal do Luis para validar que a entidade foi extraída do novo declarações o aplicativo ainda não foi visto.

1. Selecione **teste** no menu superior direito.

1. Adicione um novo expressão e pressione ENTER:

   ```Is there a form named hrf-234098```

   ![Testar novo expressão no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   A principal intenção prevista está **FindForm** corretamente com mais de 90% de confiança (0,977). A entidade **número do formulário de recursos humanos** é extraída com um valor de hrf-234098.

## <a name="clean-up-resources"></a>Limpar recursos

Quando você terminar este início rápido e não estiver passando para o próximo início rápido, selecione **meus aplicativos** no menu de navegação superior. Em seguida, marque a caixa de seleção da esquerda do aplicativo na lista e selecione **excluir** na barra de ferramentas de contexto acima da lista.

[![Excluir aplicativo da minha lista de aplicativos](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [2. Implantar um aplicativo](get-started-portal-deploy-app.md)
