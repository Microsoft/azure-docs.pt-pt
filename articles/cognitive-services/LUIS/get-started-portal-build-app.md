---
title: 'Início rápido: Criar nova aplicação com o Portal de LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido, vai criar uma nova aplicação no portal do LUIS. Criar as partes básicas de uma aplicação, objetivos e entidades, em seguida, testar, fornecendo uma expressão de utilizador de exemplo no painel de teste interativa para obter a intenção prevista. Criar uma aplicação é gratuita; ele não requer uma subscrição do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: e97be28261d28c2a72e507adcdac0248691745c7
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786869"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Início rápido: Criar uma nova aplicação no portal do LUIS

Neste início rápido, vai criar uma nova aplicação no [portal de LUIS](https://www.luis.ai). Criar as partes básicas de uma aplicação, **intenções** e **entidades**, em seguida, testar, fornecendo uma expressão de utilizador de exemplo no painel de teste interativa para obter a intenção prevista.

Criar uma aplicação é gratuita; ele não requer uma subscrição do Azure. Quando estiver pronto para implementar a sua aplicação, em seguida, pode criar um recurso de serviço dos serviços cognitivos do Azure e atribua-o para a aplicação. Esse processo de implantação está no [próximo início rápido](get-started-portal-deploy-app.md).

## <a name="create-app"></a>Criar aplicação 

1. Abra o [portal de LUIS](https://www.luis.ai) num navegador e o início de sessão. Se esta for a primeira a iniciar sessão, terá de criar uma conta de utilizador do portal de LUIS gratuita.

1. Selecione **criar nova aplicação** da barra de ferramentas de contexto.

    [![Criar nova aplicação no portal do LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Na janela de pop-up, configurar a aplicação com as seguintes definições, em seguida, selecione **feito**.

    |Nome da definição| Value | Objetivo|
    |--|--|--|
    |Name|`myEnglishApp`|Nome exclusivo da aplicação LUIS<br>obrigatório|
    |Cultura|**Inglês**|Linguagem de expressões de usuários, **en-us**<br>obrigatório|
    |Descrição|`App made with LUIS Portal`|Descrição da aplicação<br>opcional|

    ![Introduza novas definições de aplicações](./media/get-started-portal-build-app/create-new-app-settings.png)


## <a name="create-intent"></a>Criar uma intenção 

Depois desta aplicação é criada, a próxima etapa é criar objetivos. Objetivos são uma forma de categorizar texto dos utilizadores. Se tiver uma aplicação de recursos humanos que tem duas funções: primeiro para ajudar as pessoas a localizar e aplicam-se para as tarefas e, em segundo lugar, para encontrar formas para se candidatar a tarefas, estas duas diferentes _intenções_ se alinham com os seguintes objetivos:

|Intenção|Texto de exemplo do utilizador<br>conhecido como um _expressão_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

1. Depois da aplicação é criada, estará no **intenções** página do **criar** secção. Selecione **Create new intent** (Criar nova intenção). 

    [![Selecione o botão Criar novo intenção](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Introduza o nome de intenção `FindForm` e selecione **feito**.

    ![Introduza o nome de intenção de FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-example-utterance"></a>Adicionar a expressão de exemplo 

Depois de criar a intenção, a próxima etapa é adicionar expressões de exemplo. Este é o texto, inserido pelo usuário num chatbot ou outra aplicação de cliente, que mapeia a intenção de texto do utilizador para uma intenção de LUIS. 

Para esta aplicação de exemplo `FindForm` intenção, expressões de exemplo irão incluir o número de formulário, o que é importantes informações dentro da expressão que a aplicação cliente tem por ordem para fullfil a solicitação do usuário. 

Adicione as seguintes expressões de quinze de exemplo para o `FindForm` intenção. 

|#|Expressões de exemplo|
|--|--|
|1|Está à procura de hrf 123456|
|2|Onde posso encontrar os recursos humanos formulário hrf-234591?|
|3|hrf-345623, em que é|
|4|É possível enviar-me hrf 345794|
|5|É necessário hrf-234695 para se candidatar a uma tarefa interno?|
|6|Meu gerente precisa de saber o que estou aplicando para uma tarefa com hrf 234091|
|7|Onde posso enviar hrf 234918? Posso obter uma resposta de e-mail que foi recebido?|
|8|hrf-234555|
|9|Quando foi atualizado hrf 234987?|
|10|Uso hrf-876345 de formulário para se candidatar a engenharia de posições|
|11|Era uma nova versão do hrf-765234 submetido para a minha req open?|
|12|Uso hrf 234234 para tarefas internacionais?|
|13|Erro de ortografia hrf 234598|
|14|hrf 234567 ser editado para novos requisitos|
|15|hrf-123456, hrf-123123, hrf-234567|

Estas expressões de exemplo variam de propósito das seguintes formas:

* comprimento da expressão
* Pontuação
* Escolha do Word
* tempo verbal do verbo (é, foi, irá ser)
* ordem das palavras

[![Introduza expressões de exemplo para a intenção de FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

## <a name="create-regular-expression-entity"></a>Criar a entidade de expressão regular 

Para obter o número de formulário devolvido como parte da resposta de previsão do tempo de execução, o formulário deve ser marcado como uma entidade. Uma vez que o texto de número de forma altamente está estruturado, podem ser marcado com uma entidade de expressão regular. Crie a entidade com os seguintes passos. 

1. Selecione **entidades** no menu de navegação à esquerda. 

1. Selecione **criar nova entidade** na página de entidades.

1. Introduza o nome `Human Resources Form Number`, selecione o **Regex** entidade escreva e introduza a expressão regular, `hrf-[0-9]{6}`. Isso corresponde aos carateres literais, `hrf-`e permite exatamente de 6 dígitos. 

    ![Introduza as informações da entidade para a entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selecione **Done** (Concluído). 

## <a name="add-example-utterances-to-none-intent"></a>Adicionar expressões de exemplo para nenhum intenção

O **None** intenção é a intenção de contingência e não deve ser deixada em branco. Este objetivo deverá ter 1 expressão para cada 10 no resto dos objetivos da aplicação. 

O **None** expressões com de exemplo da intenção devem estar fora do seu domínio de aplicativo de cliente. 

1. Selecione **intenções** no menu à esquerda, em seguida, selecione **nenhum** na lista de objetivos.

1. Adicione as seguintes expressões de exemplo para o objetivo:

    |Nenhum expressões de exemplo de intenção|
    |--|
    |Cães que ladram são irritantes|
    |Encomendar uma piza para mim|
    |Pinguins no oceano|

    Para esta aplicação de recursos humanos, estas expressões de exemplo estão fora do domínio. Se o domínio de recursos humanos, incluindo, animais, comida ou oceano, em seguida, essas expressões de exemplo não devem ser utilizadas para o **None** intenção. 

## <a name="train-the-app"></a>Preparar a aplicação

No painel de navegação superior direito, selecione **Train** para aplicar as intenções e entidades modelo muda para a versão atual da aplicação. 

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examinar a entidade de expressão regular nas expressões de exemplo

1. Certifique-se de que a entidade é encontrada no **FindForm** intenção selecionando **intenções** no menu à esquerda, em seguida, selecione **FindForm** intenção. 

    A entidade é marcada como onde aparece nas expressões de exemplo. Se pretender ver o texto original, em vez do nome de entidade, alternar **vista de entidades** da barra de ferramentas.

    [![Todas as expressões de exemplo marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testar a nova aplicação com o painel de teste interativa

Utilizar o interactive **teste** painel no portal do LUIS para validar a entidade é extraído do novo expressões com a aplicação já vimos ainda.

1. Selecione **teste** no menu superior direito.

1. Adicionar uma nova expressão, em seguida, prima Enter:

    ```Is there a form named hrf-234098```

    ![Testar a nova expressão no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

    Parte superior prever a intenção é corretamente **FindForm** com mais de 90% confiança (0.977) e o **número de formulário de recursos humanos** entidade é extraída com um valor de hrf 234098. 

## <a name="clean-up-resources"></a>Limpar recursos
Quando tiver terminado com este início rápido e não passar para o próximo início rápido, selecione **as minhas aplicações** no menu de navegação superior. Em seguida, selecione a caixa de verificação à esquerda da aplicação na lista e selecione **eliminar** da barra de ferramentas de contexto acima da lista. 

[![Eliminar aplicação da minha lista de aplicações](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [2. Implementar uma aplicação](get-started-portal-deploy-app.md)