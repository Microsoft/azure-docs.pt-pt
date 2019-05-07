---
title: 'Início rápido: Criar uma nova aplicação no portal do LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido, vai criar uma nova aplicação no portal do LUIS. Crie as partes básicas de uma aplicação, objetivos e entidades. Teste a aplicação fornecendo uma expressão de utilizador de exemplo no painel de teste interativa para obter a intenção prevista. Criar uma aplicação é gratuita; ele não requer uma subscrição do Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2e4ff7dc97e3ee72336bd4c081caf1aa1a62bc56
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146562"
---
# <a name="quickstart-create-a-new-app-in-the-luis-portal"></a>Início rápido: Criar uma nova aplicação no portal do LUIS

Neste início rápido, vai criar uma nova aplicação [portal de LUIS](https://www.luis.ai). Primeiro cria as partes básicas de uma aplicação, **intenções**, e **entidades**. Em seguida, testar a aplicação fornecendo uma expressão de utilizador de exemplo no painel de teste interativa para obter a intenção prevista.

Criar uma aplicação é gratuita e não requer uma subscrição do Azure. Quando estiver pronto para implementar a sua aplicação, consulte a [início rápido para implementar uma aplicação](get-started-portal-deploy-app.md). Mostra como criar um recurso de serviço dos serviços cognitivos do Azure e atribua-o para a aplicação.

## <a name="create-an-app"></a>Criar uma aplicação

1. Abra o [portal de LUIS](https://www.luis.ai) num navegador e o início de sessão. Se for a primeira vez que iniciar sessão, terá de criar uma conta de utilizador do portal de LUIS gratuita.

1. Selecione **criar nova aplicação** da barra de ferramentas de contexto.

   [![Criar nova aplicação no portal do LUIS](./media/get-started-portal-build-app/create-app-in-portal.png)](./media/get-started-portal-build-app/create-app-in-portal.png#lightbox)

1. Na janela de pop-up, configurar a aplicação com as seguintes definições e, em seguida, selecione **feito**.

   |Nome da definição| Value | Objetivo|
   |--|--|--|
   |Name|`myEnglishApp`|Nome exclusivo da aplicação LUIS<br>obrigatório|
   |Cultura|**Inglês**|Linguagem de expressões de usuários, **en-us**<br>obrigatório|
   |Descrição|`App made with LUIS Portal`|Descrição da aplicação<br>opcional|
   | | | |

   ![Introduza novas definições de aplicações](./media/get-started-portal-build-app/create-new-app-settings.png)

## <a name="create-intents"></a>Criar intenções

Depois de criar a aplicação do LUIS, terá de criar objetivos. Objetivos são uma forma de categorizar texto dos utilizadores. Por exemplo, uma aplicação de recursos humanos pode ter duas funções. Para ajudar as pessoas:

 1. Localizar e aplicam-se a tarefas
 1. Encontre formas para se candidatar a tarefas

A aplicação do dois diferentes _intenções_ se alinham com os seguintes objetivos:

|Intenção|Texto de exemplo do utilizador<br>conhecido como um _expressão_|
|--|--|
|ApplyForJob|`I want to apply for the new software engineering position in Cairo.`|
|FindForm|`Where is the job transfer form hrf-123456?`|

Para criar intenções, conclua os seguintes passos:

1. Depois da aplicação é criada, estará no **intenções** página do **criar** secção. Selecione **Create new intent** (Criar nova intenção).

   [![Selecione o botão Criar novo intenção](./media/get-started-portal-build-app/create-new-intent-button.png)](./media/get-started-portal-build-app/create-new-intent-button.png#lightbox)

1. Introduza o nome de intenção `FindForm`e, em seguida, selecione **feito**.

   ![Introduza o nome de intenção de FindForm](./media/get-started-portal-build-app/create-new-intent-dialog.png)

## <a name="add-an-example-utterance"></a>Adicionar uma expressão de exemplo

Adicionar expressões de exemplo depois de criar objetivos. Expressões com de exemplo são texto que um utilizador introduz num chatbot ou outra aplicação de cliente. A intenção de texto do utilizador podem ser mapeados para uma intenção de LUIS.

Para esta aplicação de exemplo `FindForm` intenção, expressões de exemplo irão incluir o número de formulário. A aplicação cliente tem o número de forma a satisfazer o pedido do utilizador, pelo que é importante para incluí-lo na expressão.

[![Introduza expressões de exemplo para a intenção de FindForm](./media/get-started-portal-build-app/add-example-utterance.png)](./media/get-started-portal-build-app/add-example-utterance.png#lightbox)

Adicione as seguintes expressões de 15 de exemplo para o `FindForm` intenção.

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

Por predefinição, estas expressões de exemplo variam das seguintes formas:

* comprimento da expressão
* Pontuação
* Escolha do Word
* tempo verbal do verbo (é, foi, irá ser)
* ordem das palavras



## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

Para devolver o número de forma a resposta de previsão do tempo de execução, o formulário tem de ser marcado como uma entidade. Uma vez que o texto de número de forma altamente é estruturado, pode marcá-lo com uma entidade de expressão regular. Crie a entidade com os seguintes passos:

1. Selecione **entidades** no menu à esquerda.

1. Selecione **criar nova entidade** sobre o **entidades** página.

1. Introduza o nome `Human Resources Form Number`, selecione o **Regex** entidade escreva e introduza a expressão regular, `hrf-[0-9]{6}`. Esta entrada corresponde aos carateres literais, `hrf-`e permite exatamente de 6 dígitos.

   ![Introduza as informações da entidade para a entidade de expressão regular](./media/get-started-portal-build-app/create-regular-expression-entity.png)

1. Selecione **Done** (Concluído).

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar expressões de exemplo para a intenção None

O **None** intenção é a intenção de contingência e não deve ser deixada em branco. Este visa deve conter uma expressão para todas as expressões de 10 de exemplo que tenha adicionado para os outros objetivos da aplicação.

O **None** expressões com de exemplo da intenção devem estar fora do seu domínio de aplicativo de cliente.

1. Selecione **intenções** no menu à esquerda e, em seguida, selecione **nenhum** na lista de objetivos.

1. Adicione as seguintes expressões de exemplo para o objetivo:

   |Nenhum expressões de exemplo de intenção|
   |--|
   |Cães que ladram são irritantes|
   |Encomendar uma piza para mim|
   |Pinguins no oceano|

   Para esta aplicação de recursos humanos, estas expressões de exemplo estão fora do domínio. Se o seu domínio de recursos humanos incluem animais, comida ou o oceano, então deve usar expressões de exemplo diferente para o **None** intenção.

## <a name="train-the-app"></a>Preparar a aplicação

No menu no canto superior direito, selecione **Train** para aplicar as intenções e entidades modelo muda para a versão atual da aplicação.

## <a name="look-at-the-regular-expression-entity-in-the-example-utterances"></a>Examinar a entidade de expressão regular nas expressões de exemplo

1. Certifique-se de que a entidade é encontrada no **FindForm** intenção selecionando **intenções** no menu à esquerda. Em seguida, selecione **FindForm** intenção.

   A entidade é marcada como onde aparece nas expressões de exemplo. Se pretender ver o texto original em vez do nome de entidade, alternar **vista de entidades** da barra de ferramentas.

   [![Todas as expressões de exemplo marcados com entidades](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png)](./media/get-started-portal-build-app/all-example-utterances-marked-with-entities.png#lightbox)

## <a name="test-your-new-app-with-the-interactive-test-pane"></a>Testar a nova aplicação com o painel de teste interativa

Utilizar o interactive **teste** painel no portal do LUIS para validar que a entidade é extraída do novo expressões com a aplicação já vimos ainda.

1. Selecione **teste** no menu do canto superior direito.

1. Adicionar uma nova expressão e, em seguida, prima Enter:

   ```Is there a form named hrf-234098```

   ![Testar a nova expressão no painel de teste](./media/get-started-portal-build-app/test-new-utterance.png)

   Parte superior prever a intenção é corretamente **FindForm** com mais de 90% confiança (0.977). O **número do formulário de recursos humanos** entidade é extraída com um valor de hrf 234098.

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver concluído este guia de introdução e não são passar para o próximo início rápido, selecione **as minhas aplicações** no menu de navegação superior. Em seguida, selecione a caixa de verificação à esquerda da aplicação na lista e selecione **eliminar** da barra de ferramentas de contexto acima da lista.

[![Eliminar aplicação da minha lista de aplicações](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [2. Implementar uma aplicação](get-started-portal-deploy-app.md)
