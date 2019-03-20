---
title: Como utilizar a versão de marcação de mensagens em fila com um modelo de aprendiz de conversação - serviços cognitivos da Microsoft | Documentos da Microsoft
titleSuffix: Azure
description: Saiba como utilizar o controlo de versões e a etiquetagem com um modelo de aprendiz de conversação.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5073d3ab967c4c4e1b90636c247839875a6aa0d7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086314"
---
# <a name="how-to-use-version-tagging"></a>Como utilizar a marcação de versão

Este tutorial ilustra como marcar as versões do seu modelo de aprendiz de conversação e definir qual versão é "dinâmicos".  

## <a name="requirements"></a>Requisitos
Este tutorial requer a utilizar o emulador do Bot Framework para criar o registo de caixas de diálogo, não a IU da Web do registo de caixa de diálogo.  

Este tutorial requer que o tutorial geral Bot está em execução:

    npm run tutorial-general

## <a name="details"></a>Detalhes

As versões marcadas do modelo são estáticas; Não é possível editar ou alterá-los. Ao editar o seu modelo, que sempre está a editar o mestre de versão. Quando adiciona uma nova marca, o aprendiz de conversação captura um instantâneo do modelo nesse ponto no tempo. 

O Bot, irá utilizar a versão do modelo selecionado como a versão de "Live", mas qualquer conversas tem estará visualizáveis apenas quando a "marca edição" é definida como "Principal". Se a propriedade de "Editar etiqueta" do modelo é definida como algo diferente de "Principal", em seguida, pode ver o instantâneo do modelo, mas não é possível alterá-la de qualquer forma.

## <a name="steps"></a>Passos

### <a name="install-the-bot-framework-emulator"></a>Instale o emulador do Bot Framework

1. Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Baixe e instale o emulador.

### <a name="create-a-model"></a>Criar um modelo

1. Do modelo de lista Home Page, clique no `New Model` botão.
2. Na `Name` tipo, de campo "Tutorial-18-controlo de versões", prima enter.
4. No painel esquerdo, clique em "Definições".
5. Copie o conteúdo do campo CONVERSATION_LEARNER_MODEL_ID na área de transferência.

### <a name="configure-the-emulator"></a>Configurar o emulador

1. Na pasta de raiz Aprendiz de conversação, abra o ficheiro de ". env".
2. Adicione uma linha para o ficheiro ". env" como este:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Reinicie o serviço de aprendiz de conversação, sair da linha de comandos e execute novamente:
    - `npm run tutorial-general`
4. No emulador do Bot Framework, criar uma nova configuração de bot, defina o URL de ponto final para `http://localhost:3978/api/messages`

### <a name="version-1"></a>Versão 1

Vamos criar uma única ação para a versão 1.

1. No painel esquerdo da interface do Usuário de Web, clique em "Ações", em seguida, clique no `New Action` botão.
2. Na "Resposta do Bot", insira "Olá daí (versão 1)".
3. Clique no botão `Save`.

Agora podemos irá marcar isto como "Versão 1" do modelo.

1. No painel do lado esquerdo clique em "definições", em seguida, clique nas ![](../media/tutorial18_version_tags.PNG)ícone de "Versão etiquetas" para revelar o `New Tag` botão que deve clicar.
    - Dê-lhe o nome "Versão 1"
1. Em "Live etiqueta" menu pendente selecione "versão 1".  
    - Agora canais com este Bot irão utilizar "Versão 1" do nosso modelo.
    - As entidades, ações e caixas de diálogo Train desse modelo de 1 de versão já não podem ser alteradas.
    - Se selecionar "Versão 1" da "Editar etiqueta de" só poderá ver o modelo e não a editá-lo.
    - Deixe "edição da etiqueta" definida como "Principal", é a única versão do modelo que pode ser editado.

Agora verá "Versão 1" na grelha de "Versão etiquetas".

### <a name="version-2"></a>Versão 2

Agora, editaremos o nosso modelo para distingui-la a partir da versão 1.

1. No painel esquerdo, clique em "Ações".
2. Na grelha de ações, clique em "Olá daí (versão 1)".
3. Alteração de campo de "resposta do Bot" para "Olá daí (versão 2)".
4. Clique no botão `Save`.
5. Clique no botão `New Action`.
6. Em "do Bot de resposta" campo Tipo de "xau xau (versão 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Confirmar o que emulador do Bot Framework está a utilizar a versão 1

1. No emulador Bot Framework, escreva a mensagem, "EI lá".
2. Tenha em atenção que o Bot responde com "Olá daí (versão 1)".
    - Isto verifica que a versão 1 é "dinâmicos".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Ver os registos de conversação na IU de Web de aprendiz de conversação

1. No painel esquerdo, clique no "Registo de caixas de diálogo de"
    - Se não vir quaisquer caixas de diálogo, clique no botão de atualização.
2. Tenha em atenção a etiqueta "Versão 1" na grade.
3. Na grade, clique em "Olá daí (versão 1)"

> [!NOTE]
> Podemos fazer correções escolhendo em todas as funcionalidades de aprendiz de conversação atualmente disponível, no entanto, estas edições serão feitas a mestre e não a versão 1.

Agora viu como funciona o controle de versão e como pode interagir com o Bot a utilizar o emulador do Bot Framework.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Demonstração - reposição de palavra-passe](./demo-password-reset.md)
