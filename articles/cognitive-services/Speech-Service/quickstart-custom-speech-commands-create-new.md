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
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 80eb0a2018ece23de80e8eb9c4a68c149b590440
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74111361"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Início rápido: criar um comando personalizado (versão prévia)

Neste artigo, você aprenderá a criar e testar um aplicativo de comandos personalizados hospedado.
O aplicativo reconhecerá um expressão como "ligar a TV" e responderá com uma mensagem simples "Ok, ligando a TV".

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura de fala. [Experimente o serviço de fala gratuitamente](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Durante a visualização, somente a região westus2 tem suporte para chaves de assinatura.

- Uma chave de criação de [reconhecimento vocal](https://www.luis.ai/home) (Luis):
  1. Abra um navegador da Web e navegue até a [portal do Azure](https://portal.azure.com)
  1. Selecione criar um recurso
  1. Procure e selecione [reconhecimento vocal](https://aka.ms/sc-luis-all)
  1. Selecione criação em opções de criação
  1. Depois que o recurso for implantado, vá para o recurso e copie a chave da seção início rápido ou chaves

      > [!div class="mx-imgBorder"]
      > ![criar recurso de criação](media/custom-speech-commands/resources-lu-authoring.png)

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
   > ![criar novo projeto](media/custom-speech-commands/create-new-project.png)

1. Insira o nome do projeto e o idioma e, em seguida, selecione **Avançar** para continuar
1. Insira sua chave de criação do LUIS
1. Depois de criado, selecione seu projeto

Sua exibição agora deve ser uma visão geral do aplicativo de comandos personalizados.

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

> [!div class="mx-imgBorder"]
> ![criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Definição    | Valor sugerido                        | Descrição                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Nome da Regra  | "ConfirmationResponse"                 | Um nome que descreve a finalidade da regra          |
| Condições | Nenhum                                   | Condições que determinam quando a regra pode ser executada    |
| Ações    | SpeechResponse "Ok, ligando a TV" | A ação a ser tomada quando a condição da regra for verdadeira |

## <a name="try-it-out"></a>Experimentar

Teste o comportamento usando o painel testar chat.

> [!div class="mx-imgBorder"]
> ![teste com Webchat](media/custom-speech-commands/create-basic-test-chat.png)

- Você digita: "ligar a TV"
- Resposta esperada: "Ok, ligando a TV"

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
