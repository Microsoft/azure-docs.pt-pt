---
title: 'Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados - serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, irá criar e testar uma aplicação de pré-visualização de comandos personalizados hospedado. A aplicação processará as expressões.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142355"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados

Neste arranque rápido, aprenderá a criar e testar uma aplicação de Comandos Personalizados.
A aplicação processará expressões como "ligue a televisão" e responda com uma simples mensagem como "Ok, ligar a televisão".

## <a name="prerequisites"></a>Pré-requisitos

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Criar um recurso de discurso azul<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Neste momento, os Comandos Personalizados apenas suportam subscrições de discurso nas regiões oeste, oeste2 e neur.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vá ao Estúdio de Fala para Comandos Personalizados

1. Num navegador web, vá ao [Speech Studio.](https://speech.microsoft.com/)
1. Introduza as suas credenciais para iniciar sessão no portal.

   A vista padrão é a sua lista de subscrições do Discurso.
    > [!NOTE]
    > Se não vir a página de subscrição selecionada, pode chegar lá selecionando **recursos de discurso** no menu de definições no topo do ecrã.

1. Selecione a subscrição do Discurso e, em seguida, selecione **Ir para o Estúdio**.
1. Selecione **Comandos Personalizados**.

     A vista predefinida é uma lista das aplicações de Comandos Personalizados que tem na subscrição selecionada.

## <a name="create-a-custom-commands-project"></a>Criar um projeto de Comandos Personalizados

1. Selecione **novo projeto** para criar um projeto.

   > [!div class="mx-imgBorder"]
   > ![Criar um projeto](media/custom-speech-commands/create-new-project.png)

1. Na caixa **Nome,** insira um nome de projeto.
1. Na lista **de idiomas,** selecione um idioma.
1. Na lista **de recursos de autoria LUIS,** selecione um recurso de autoria. Se não houver recursos de autoria válidos, crie um selecionando **Criar novo recurso de autoria LUIS.**

   > [!div class="mx-imgBorder"]
   > ![Criar um grupo de recursos](media/custom-speech-commands/create-new-resource.png)

   1. Na caixa **Nome de Recursos,** insira o nome do recurso.
   1. Na lista **do Grupo de Recursos,** selecione um grupo de recursos.
   1. Na lista **de localização,** selecione uma localização.
   1. Na lista **de Preços,** selecione um nível.

      > [!NOTE]
      > Pode criar um grupo de recursos introduzindo um nome de grupo de recursos na caixa **do Grupo de Recursos.** O grupo de recursos será criado quando selecionar **Criar**.

1. Selecione **Criar**.
1. Depois de o projeto ser criado, selecione-o.

    Deverá agora consultar uma visão geral da sua nova aplicação de Comandos Personalizados.

## <a name="update-luis-resources-optional"></a>Atualizar os recursos LUIS (opcional)

Pode atualizar o recurso de autoria que selecionou na janela do **projeto Novo** e definir um recurso de previsão. O recurso de previsão é utilizado para reconhecimento quando a sua aplicação De Comandos Personalizados é publicada. Não precisa de um recurso de previsão durante as fases de desenvolvimento e teste.

1. Selecione **Definições** no painel esquerdo e, em seguida, selecione **os recursos LUIS** no painel central.
1. Selecione um recurso de previsão ou crie um selecionando **Criar novo recurso**.
1. Selecione **Guardar**.
    
    > [!div class="mx-imgBorder"]
    > ![Definir recursos LUIS](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Como o recurso de autoria suporta apenas 1.000 pedidos de previsão por mês, terá de definir um recurso de previsão LUIS antes de publicar a sua aplicação de Comandos Personalizados.


## <a name="create-a-command"></a>Criar um comando

Vamos criar um comando simples que leve uma única `turn on the tv` expressão, e responda com a `Ok, turning on the tv` mensagem.

1. Crie um comando selecionando **Novo comando** na parte superior do painel esquerdo. A nova janela **de comando** abre-se.
1. Na caixa **Nome,** insira **TurnOn**.
1. Selecione **Criar**.

O painel do meio lista as propriedades do comando:


| Configuração            | Descrição                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Frases de exemplo** | Exemplos de expressões que o utilizador pode dizer para ativar o comando.                                                                 |
| **Parâmetros**       | Informação necessária para completar o comando.                                                                                |
| **Regras de conclusão** | Ações que serão tomadas para cumprir o comando. Por exemplo, responder ao utilizador ou comunicar com outro serviço web. |
| **Regras de interação**   | Regras adicionais para lidar com situações mais específicas ou complexas.                                                              |


> [!div class="mx-imgBorder"]
> ![Criar um comando](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Adicionar frases de exemplo

Vamos começar com a secção **de frases exemplo.** Vamos dar um exemplo do que o utilizador pode dizer.

1. Selecione **frases exemplo** no painel do meio. 
1. No painel direito, adicione exemplos:

    ```
    turn on the tv
    ```

1. **Selecione Guarde** na parte superior do painel.

Por enquanto, não temos parâmetros, por isso podemos passar para a secção **de regras de conclusão.**

### <a name="add-a-completion-rule"></a>Adicione uma regra de conclusão

Agora adicione uma regra de conclusão que tenha a seguinte configuração. Esta regra diz ao utilizador que está a ser tomada uma ação de cumprimento.


| Definição    | Valor sugerido                          | Descrição                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Nome**  | **ConfirmaçãoResponse**                  | Um nome que descreve o propósito da regra.          |
| **Condições** | Nenhum                                     | Condições que determinam quando a regra pode ser executada.    |
| **Ações**    | **Envie resposta de discurso -> Ok, ligando a televisão** | A ação a tomar quando a condição de regra é verdadeira. |

1. Crie uma nova regra de conclusão selecionando **Adicione** na parte superior do painel médio.
1. Na caixa **Nome**, introduza um nome.
1. Adicione uma ação.
   1. Criar uma ação selecionando **Adicionar uma ação** na secção **Ações.**
   1. Na janela **De Ação de Edição,** na lista **Tipo,** selecione **Enviar resposta da fala**.
   1. Em **Resposta**, selecione **Editor Simples**. Na primeira caixa **de variação,** insira **Ok, ligando a televisão.**

   > [!div class="mx-imgBorder"]
   > ![Criar uma resposta](media/custom-speech-commands/create-speech-response-action.png)

1. **Selecione Guardar** para guardar a regra.
1. De volta à secção **de regras de Conclusão,** selecione **Guardar** para guardar todas as alterações. 

> [!div class="mx-imgBorder"]
> ![Criar uma regra de conclusão](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Experimente

Teste o comportamento utilizando o painel de chat de teste.
1. Selecione **Train** no topo do painel direito.
1. Depois do treino, selecione **Teste**. Aparece um novo teste à sua janela **de aplicação.**
    - **Insira ligar a televisão**
    - Resposta esperada: **Ok, ligar a televisão**


> [!div class="mx-imgBorder"]
> ![Teste o comportamento](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)
