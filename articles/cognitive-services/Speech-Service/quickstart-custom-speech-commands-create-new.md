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
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872522"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Quickstart: Criar uma aplicação de comandos personalizados (Pré-visualização)

Neste arranque rápido, aprenderá a criar e testar uma aplicação de Comandos Personalizados.
A aplicação criada processará expressões como "ligar a televisão" e responder com uma simples mensagem "Ok, ligando a televisão".

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso azure speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Neste momento, os Comandos Personalizados apenas suportam subscrições de discursos em regiões westus, westus2 e neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vá ao Estúdio de Discurso para Comandos Personalizados

1. Abra o seu navegador web e navegue para o [Speech Studio](https://speech.microsoft.com/)
1. Introduza as suas credenciais para iniciar sessão no portal.

   - A visão padrão é a sua lista de subscrições do Discurso.
     > [!NOTE]
     > Se não vir a página de subscrição selecionada, pode navegar lá escolhendo "Recursos de Fala" a partir do menu de definições na barra superior.

1. Selecione a subscrição do Discurso e, em seguida, selecione **Ir para estúdio**.
1. Selecione **Comandos Personalizados**.

     - A vista padrão é uma lista das aplicações de Comandos Personalizados que tem sob a sua subscrição selecionada.

## <a name="create-a-custom-commands-project"></a>Criar um projeto de Comandos Personalizados

1. Selecione **Novo projeto** para criar um novo projeto.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Insira o nome do projeto.
1. Selecione o idioma a partir da queda para baixo.
1. Selecione um recurso de autor a partir da queda. Se não houver recursos de autor válidos, crie um clicando em **Criar novo recurso**de autor LUIS .

   > [!div class="mx-imgBorder"]
   > ![Criar um grupo de recursos](media/custom-speech-commands/create-new-resource.png)

   - Insira o Nome de Recursos, Grupo de Recursos.
   - Escolha o valor para Localização e Nível de Preços a partir da queda.

      > [!NOTE]
      > Pode criar grupos de recursos introduzindo o nome de grupo de recursos desejado no campo "Grupo de Recursos". O grupo de recursos será criado quando a **Create** for selecionada.

1. Em seguida, selecione **Criar** para criar o seu projeto.
1. Uma vez criado, selecione o seu projeto.

    - A sua visão deverá agora ser uma visão geral da sua aplicação de Comandos Personalizados recém-criada.

## <a name="update-luis-resources-optional"></a>Atualização de Recursos LUIS (Opcional)

Pode atualizar o recurso de autoria que foi definido na nova janela do projeto e definir um recurso de previsão. O recurso de previsão é usado para reconhecimento assim que a sua aplicação de Comandos Personalizados é publicada. Não precisa de um recurso de previsão para as fases de desenvolvimento e teste.

1. Selecione **Definições** a partir do painel esquerdo e, em seguida, navegue para a secção **de recursos LUIS** no painel médio.
1. Selecione um recurso de previsão ou crie um selecionando **Criar um novo recurso**.
1. Selecione **Guardar**.
    
    > [!div class="mx-imgBorder"]
    > ![Definir recursos LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Uma vez que o recurso de autoria suporta apenas 1.000 pedidos de ponto final de previsão por mês, você precisará de definir um recurso de previsão LUIS antes de publicar a sua aplicação De Comandos Personalizados.


## <a name="create-a-new-command"></a>Criar um novo Comando

Vamos criar um simples comando que tome `turn on the tv`uma única expressão, `Ok, turning on the tv`e responda com a mensagem.

1. Crie um novo Comando `+ New command` selecionando o ícone presente em cima da vidraça mais à esquerda. Um novo pop-up aparece intitulado **Novo comando.**
1. Forneça valor **Name** para o `TurnOn`campo Nome como .
1. Selecione **Criar**.

O painel do meio recruta as diferentes propriedades de um comando:


| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frases de exemplo | Declarações exemplo que o utilizador pode dizer para desencadear este Comando                                                                 |
| Parâmetros       | Informação necessária para completar o Comando                                                                                |
| Regras de conclusão | As ações a tomar para cumprir o Comando. Por exemplo, responder ao utilizador ou comunicar com outro serviço web. |
| Regras de interação   | Regras adicionais para lidar com situações mais específicas ou complexas                                                              |


> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Adicionar frases exemplo

Vamos começar com a secção de frases exemplo, e fornecer um exemplo do que o utilizador pode dizer.

1. Selecione Secção de **frases exemplo** a partir do painel médio e no painel mais direito, adicione exemplos:

    ```
    turn on the tv
    ```

1. Selecione `Save` o ícone presente em cima deste painel.

Por enquanto, não temos parâmetros para passarmos à secção de **regras de conclusão.**

### <a name="add-a-completion-rule"></a>Adicione uma regra de conclusão

Adicione agora uma regra de conclusão com a seguinte configuração. Esta regra indica ao utilizador que está a ser tomada uma ação de realização.


| Definição    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome da Regra  | Resposta de confirmação                  | Um nome que descreve o propósito da regra          |
| Condições | Nenhum                                     | Condições que determinam quando a regra pode ser executada    |
| Actions (Ações)    | Resposta do discurso "- Ok, ligando a televisão" | A ação a tomar quando a condição da regra é verdadeira |

1. Crie uma nova regra `+Add` de conclusão selecionando o ícone na parte superior do painel médio.
1. Forneça valor na secção **nome.**
1. Adicionar uma ação
   1. Crie uma nova ação selecionando o **+ Adicione uma ação** na secção **Ações.**
   1. No pop-up **New Action,** selecione `Send speech response` entre as opções de drop-down para **Type**.
   1. Escolha `Simple editor` para o campo **resposta.**
       - No **campo de primeira variação,** fornecer valor para resposta como`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta da Fala](media/custom-speech-commands/create-speech-response-action.png)

1. Clique em **Guardar** para salvar a regra.
1. De volta à secção de **regras de conclusão,** selecione **Guardar** para guardar todas as alterações. 

> [!div class="mx-imgBorder"]
> ![Criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Experimente

Teste o comportamento utilizando o painel de chat test
1. Selecione `Train` o ícone presente em cima do painel direito.
1. Uma vez, o `Test`treino completa, selecione . Aparecerá um novo teste da sua janela de **candidatura.**
    - Escreve: liga a televisão
    - Resposta esperada: Ok, ligando a televisão


> [!div class="mx-imgBorder"]
> ![Teste com chat web](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)
