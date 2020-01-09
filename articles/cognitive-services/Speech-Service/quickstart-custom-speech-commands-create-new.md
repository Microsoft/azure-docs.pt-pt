---
title: 'Início rápido: criar um comando personalizado (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você cria e testa um aplicativo de comandos personalizados hospedado.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: d8e28b88757fa7557b04ee471ede17012094bb9e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446896"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Início rápido: criar um comando personalizado (versão prévia)

Neste artigo, você aprenderá a criar e testar um aplicativo de comandos personalizados hospedado.
O aplicativo reconhecerá um expressão como "ligar a TV" e responderá com uma mensagem simples "Ok, ligando a TV".

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura de fala. 

Se você não tiver uma assinatura de fala, poderá criar uma navegando até o [Speech Studio](https://speech.microsoft.com/) e selecionando **criar um recurso de fala**.

  > [!div class="mx-imgBorder"]
  > [![criar um projeto](media/custom-speech-commands/create-new-subscription.png)](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Durante a visualização, há suporte apenas para a região westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ir para o Speech Studio para comandos personalizados

1. Abra o navegador da Web e navegue até o [Speech Studio](https://speech.microsoft.com/)
1. Insira suas credenciais para entrar no portal

   - A exibição padrão é sua lista de assinaturas de fala
     > [!NOTE]
     > Se você não vir a página Selecionar assinatura, poderá navegar até lá escolhendo "recursos de fala" no menu configurações na barra superior.

1. Selecione sua assinatura de fala e, em seguida, selecione **ir para estúdio**
1. Selecionar **comandos personalizados (visualização)**

O modo de exibição padrão é uma lista dos aplicativos de comandos personalizados que você criou.

## <a name="create-a-custom-commands-project"></a>Criar um projeto de comandos personalizados

1. Selecione **novo projeto** para criar um novo projeto

   > [!div class="mx-imgBorder"]
   > ![criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Insira o nome do projeto e o idioma.
1. Selecione um recurso de criação. Se não houver recursos de criação válidos, crie um selecionando **criar novo recurso**.

   > [!div class="mx-imgBorder"]
   > ![Criar um grupo de recursos](media/custom-speech-commands/create-new-resource.png)

   1. Insira o nome do recurso, o grupo, o local e o tipo de preço.

         > [!NOTE]
         > Você pode criar grupos de recursos inserindo o nome do grupo de recursos desejado no campo "grupo de recursos". O grupo de recursos será criado quando a **criação** for selecionada.

1. Clique em **criar** para criar seu projeto.
1. Depois de criado, selecione seu projeto.

Sua exibição agora deve ser uma visão geral do aplicativo de comandos personalizados.

## <a name="update-luis-resources-optional"></a>Atualizar recursos do LUIS (opcional)

Você pode atualizar o conjunto de recursos de criação na janela novo projeto e definir um recurso de previsão usado para reconhecer entradas durante o tempo de execução. 

> [!NOTE]
> Você precisará definir um recurso de previsão antes que seu aplicativo solicite previsões além das 1.000 solicitações fornecidas pelo recurso de criação.

> [!div class="mx-imgBorder"]
> ![definir recursos de LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Navegue até o painel recursos do LUIS selecionando **configurações** no painel esquerdo e, em seguida, **Luis recursos** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **criar novo recurso**
1. Selecione **Guardar**

## <a name="create-a-new-command"></a>Criar um novo comando

Agora você pode criar um comando. Vamos usar um exemplo que irá pegar um único expressão, `turn on the tv`e responder com a mensagem `Ok, turning on the TV`.

1. Crie um novo comando selecionando o ícone de `+` ao lado de comandos e dê a ele o nome `TurnOn`
1. Selecione **Guardar**

> [!div class="mx-imgBorder"]
> ![criar um comando](media/custom-speech-commands/create-add-command.png)

Um comando é um conjunto de:

| Grupo            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frases de Exemplo | Exemplo declarações o usuário pode dizer para disparar esse comando                                                                 |
| Parâmetros       | Informações necessárias para concluir o comando                                                                                |
| Regras de conclusão | As ações a serem executadas para atender ao comando. Por exemplo, para responder ao usuário ou comunicar-se com outro serviço Web |
| Regras avançadas   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |

### <a name="add-a-sample-sentence"></a>Adicionar uma sentença de exemplo

Vamos começar com frases de exemplo e fornecer um exemplo do que o usuário pode dizer:

```
turn on the tv
```

Por enquanto, não temos parâmetros para que possamos passar para as regras de conclusão.

### <a name="add-a-completion-rule"></a>Adicionar uma regra de conclusão

Agora, adicione uma regra de conclusão para responder ao usuário indicando que uma ação está sendo executada.

1. Crie uma nova regra de conclusão selecionando o ícone de `+` ao lado das regras de conclusão
1. Insira o nome da regra
1. Adicionar uma ação
   1. Crie uma nova ação de resposta de fala selecionando o ícone de `+` ao lado de ações e selecione `SpeechResponse`
   1. Insira a resposta

   > [!NOTE]
   > O texto regular deve começar com um traço. Para obter mais detalhes, acesse [aqui](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![criar uma resposta de fala](media/custom-speech-commands/create-speech-response-action.png)

1. Clique em **salvar** para salvar a regra

> [!div class="mx-imgBorder"]
> ![criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Definição    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome da Regra  | "ConfirmationResponse"                   | Um nome que descreve a finalidade da regra          |
| Condições | Nenhuma                                     | Condições que determinam quando a regra pode ser executada    |
| Ações    | SpeechResponse "-Ok, ligando a TV" | A ação a ser tomada quando a condição da regra for verdadeira |

## <a name="try-it-out"></a>Experimentar

Teste o comportamento usando o painel testar chat.

> [!div class="mx-imgBorder"]
> Teste de ![com](media/custom-speech-commands/create-basic-test-chat.png) de bate-papo na Web

- Você digita: "ligar a TV"
- Resposta esperada: "Ok, ligando a TV"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
