---
title: 'Quickstart: Criar um Comando Personalizado (Pré-visualização) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, cria e testa uma aplicação de Comandos Personalizados hospedado.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155592"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Quickstart: Criar um Comando Personalizado (Pré-visualização)

Neste artigo, você aprenderá a criar e testar uma aplicação de Comandos Personalizados hospedado.
A aplicação reconhecerá uma expressão como "ligar a televisão" e responder com uma simples mensagem "Ok, ligando a televisão".

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Discurso.

Se não tiver uma subscrição de discurso, pode criar uma navegando para o Estúdio da [Fala](https://speech.microsoft.com/) e selecionando **Criar um Recurso**de Fala .

  > [!div class="mx-imgBorder"]
  > [![Criar um](media/custom-speech-commands/create-new-subscription.png) projeto](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Durante a pré-visualização, apenas a região de Westus2 é apoiada.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vá ao Estúdio de Discurso para Comandos Personalizados

1. Abra o seu navegador web e navegue para o [Speech Studio](https://speech.microsoft.com/)
1. Insira as suas credenciais para iniciar sessão no portal

   - A visão padrão é a sua lista de subscrições do Discurso
     > [!NOTE]
     > Se não vir a página de subscrição selecionada, pode navegar lá escolhendo "Recursos de Fala" a partir do menu de definições na barra superior.

1. Selecione a subscrição do Discurso e, em seguida, selecione **Ir para estúdio**
1. Selecione **Comandos Personalizados (Pré-visualização)**

A vista padrão é uma lista das aplicações de Comandos Personalizados que criou.

## <a name="create-a-custom-commands-project"></a>Criar um projeto de Comandos Personalizados

1. Selecione **Novo projeto** para criar um novo projeto

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Introduza o nome e a linguagem do projeto.
1. Selecione um recurso de autoria. Se não houver recursos de autor válidos, crie um selecionando **Criar um novo recurso.**

   > [!div class="mx-imgBorder"]
   > ![Criar um grupo de recursos](media/custom-speech-commands/create-new-resource.png)

   1. Introduza o nome de recurso, grupo, localização e nível de preços.

         > [!NOTE]
         > Pode criar grupos de recursos introduzindo o nome de grupo de recursos desejado no campo "Grupo de Recursos". O grupo de recursos será criado quando a **Create** for selecionada.

1. Clique em **Criar** para criar o seu projeto.
1. Uma vez criado, selecione o seu projeto.

A sua visão deverá agora ser uma visão geral da sua aplicação De Comandos Personalizados.

## <a name="update-luis-resources-optional"></a>Atualização de Recursos LUIS (Opcional)

Pode atualizar o conjunto de recursos de autoria na nova janela do projeto e definir um recurso de previsão utilizado para reconhecer entradas durante o tempo de execução.

> [!NOTE]
> Terá de definir um recurso de previsão antes de a sua aplicação solicitar previsões para além dos 1.000 pedidos fornecidos pelo recurso de autoria.

> [!div class="mx-imgBorder"]
> ![Definir recursos LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Navegue para o painel de Recursos LUIS selecionando **Definições** do painel esquerdo e, em seguida, **RECURSOS LUIS** a partir do painel central.
1. Selecione um recurso de previsão ou crie um selecionando **Criar novo recurso**
1. Selecione **Guardar**

## <a name="create-a-new-command"></a>Criar um novo Comando

Agora pode criar um Comando. Vamos usar um exemplo que terá uma `turn on the tv`única expressão, e `Ok, turning on the TV`responder com a mensagem.

1. Crie um novo Comando `+` selecionando o ícone ao lado dos comandos e dê-lhe o nome`TurnOn`
1. Selecione **Guardar**

> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-speech-commands/create-add-command.png)

Um Comando é um conjunto de:

| Grupo            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frases de amostra | Declarações exemplo que o utilizador pode dizer para desencadear este Comando                                                                 |
| Parâmetros       | Informação necessária para completar o Comando                                                                                |
| Regras de Conclusão | As ações a tomar para cumprir o Comando. Por exemplo, responder ao utilizador ou comunicar com outro serviço web |
| Regras Avançadas   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |

### <a name="add-a-sample-sentence"></a>Adicione uma frase de amostra

Vamos começar com as Frases de Amostra e dar um exemplo do que o utilizador pode dizer:

```
turn on the tv
```

Por enquanto, não temos parâmetros para podermos passar às Regras de Conclusão.

### <a name="add-a-completion-rule"></a>Adicione uma regra de conclusão

Adicione agora uma Regra de Conclusão para responder ao utilizador indicando que está a ser tomada uma ação.

1. Crie uma nova Regra `+` de Conclusão selecionando o ícone ao lado das Regras de Conclusão
1. Insira o nome da regra
1. Adicionar uma ação
   1. Crie uma nova Ação de `+` Resposta ao Discurso, selecionando o ícone ao lado de Ações e selecione`SpeechResponse`
   1. Insira a resposta

   > [!NOTE]
   > O texto regular deve começar com um traço. Para mais detalhes, vá [aqui](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta da Fala](media/custom-speech-commands/create-speech-response-action.png)

1. Clique em **Guardar** para salvar a regra

> [!div class="mx-imgBorder"]
> ![Criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Definição    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome da Regra  | "Resposta de confirmação"                   | Um nome que descreve o propósito da regra          |
| Condições | Nenhuma                                     | Condições que determinam quando a regra pode ser executada    |
| Ações    | Resposta do discurso "- Ok, ligando a TV" | A ação a tomar quando a condição da regra é verdadeira |

## <a name="try-it-out"></a>Experimente

Teste o comportamento utilizando o painel de chat test.

> [!div class="mx-imgBorder"]
> ![Teste com chat web](media/custom-speech-commands/create-basic-test-chat.png)

- Escreve: "Ligue a televisão"
- Resposta esperada: "Ok, ligando a televisão"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)
