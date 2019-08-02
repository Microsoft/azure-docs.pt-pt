---
title: Como usar a marcação de versão com um modelo de Conversation Learner-serviços cognitivas do Azure | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o controle de versão e marcação com um modelo de Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0279363c039e3ec3c2deac3bc7f71c32c547e9d1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703368"
---
# <a name="how-to-use-version-tagging"></a>Como usar a marcação de versão

Este tutorial ilustra como marcar versões do modelo de Conversation Learner e definir qual versão é "dinâmica".  

## <a name="requirements"></a>Requisitos
Este tutorial requer o uso do emulador do bot Framework para criar caixas de diálogo de log, não a IU da Web da caixa de diálogo de log.  

Este tutorial requer que o bot tutorial geral esteja em execução:

    npm run tutorial-general

## <a name="details"></a>Detalhes

As versões marcadas do modelo são estáticas; Você não pode editá-los ou alterá-los. Ao editar seu modelo, você está sempre editando a versão mestre. Quando você adiciona uma nova marca, Conversation Learner captura um instantâneo do modelo nesse momento. 

O bot usará a versão do modelo que você selecionou como a versão "dinâmica", mas todas as conversas que ele tiver serão exibidas somente quando a "marca de edição" estiver definida como "mestre". Se a propriedade "marca de edição" do modelo for definida como algo diferente de "Master", você poderá exibir o instantâneo do modelo, mas não poderá alterá-lo de nenhuma forma.

## <a name="steps"></a>Passos

### <a name="install-the-bot-framework-emulator"></a>Instalar o emulador do bot Framework

1. Vá para [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Baixe e instale o emulador.

### <a name="create-a-model"></a>Criar um modelo

1. Na página inicial da lista de modelos, clique `New Model` no botão.
2. `Name` No campo tipo, "tutorial-18-controle de versão", pressione Enter.
4. No painel esquerdo, clique em "configurações".
5. Copie o conteúdo do campo CONVERSATION_LEARNER_MODEL_ID para a área de transferência.

### <a name="configure-the-emulator"></a>Configurar o emulador

1. Na pasta raiz Conversation Learner, abra o arquivo ". env".
2. Adicione uma linha ao arquivo ". env" da seguinte maneira:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Reinicie o serviço de Conversation Learner saindo do prompt de comando e executando novamente:
    - `npm run tutorial-general`
4. No emulador do bot Framework, crie uma nova configuração de bot, defina a URL do ponto de extremidade como`http://localhost:3978/api/messages`

### <a name="version-1"></a>Versão 1

Vamos criar uma única ação para a versão 1.

1. No painel esquerdo da interface do usuário da Web, clique em "ações" e, `New Action` em seguida, clique no botão.
2. No campo "resposta do bot", digite "Olá, (versão 1)".
3. Clique no botão `Save`.

Agora, marcaremos como "versão 1" do modelo.

1. No painel esquerdo, clique em "configurações" e, em seguida, ![](../media/tutorial18_version_tags.PNG)clique no ícone "marcas de versão" `New Tag` para revelar o botão em que você deve clicar.
    - Nomeie-o como "versão 1"
1. Na lista suspensa "marca dinâmica", selecione "versão 1".  
    - Agora, os canais que usam esse bot usarão a "versão 1" do nosso modelo.
    - As caixas de diálogo entidades, ações e treinamento deste modelo de versão 1 não podem mais ser alteradas.
    - Se você selecionar "versão 1" como a "marca de edição", poderá apenas exibir o modelo e não editá-lo.
    - Deixe a "marca de edição" definida como "mestre", é a única versão do modelo que pode ser editada.

Agora, você verá "versão 1" na grade "marcas de versão".

### <a name="version-2"></a>Versão 2

Agora, iremos editar nosso modelo para distingui-lo da versão 1.

1. No painel esquerdo, clique em "ações".
2. Na grade de ações, clique em "Olá, lá (versão 1)".
3. Altere o campo "resposta do bot" para "Hi" (versão 2) ".
4. Clique no botão `Save`.
5. Clique no botão `New Action`.
6. No campo "resposta do bot", digite "até aqui (versão 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Confirme se o emulador do bot Framework está usando a versão 1

1. No emulador do bot Framework, digite a mensagem "Ei,".
2. Observe que o bot responde com "Hi (versão 1)".
    - Isso verifica se a versão 1 está "ativa".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Exibir os logs de conversa na interface do usuário do Conversation Learner Web

1. No painel esquerdo, clique em "caixas de diálogo de log"
    - Se você não vir nenhuma caixa de diálogo, clique no botão atualizar.
2. Observe a marca "versão 1" na grade.
3. Na grade, clique em "Olá, lá (versão 1)"

> [!NOTE]
> Podemos fazer correções escolhendo de todas as funcionalidades de Conversation Learner disponíveis no momento, no entanto, essas edições serão feitas no mestre e não na versão 1.

Agora você viu como funciona o controle de versão e como é possível interagir com o bot usando o emulador do bot Framework.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Enumerar entidades e definir ações de entidade](./tutorial-enum-set-entity.md)
