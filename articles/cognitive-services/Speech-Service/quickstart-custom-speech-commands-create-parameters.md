---
title: 'Quickstart: Criar um Comando Personalizado com Parâmetros (Pré-visualização) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você adicionará parâmetros a uma aplicação de Comandos Personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853601"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>Quickstart: Criar uma aplicação de Comandos Personalizados com parâmetros (Pré-visualização)

No [artigo anterior,](./quickstart-custom-speech-commands-create-new.md)criou uma aplicação simples de Comandos Personalizados sem parâmetros.

Neste artigo, irá alargar esta aplicação para utilizar os parâmetros para que possa manusear a ligação e desligar vários dispositivos.

## <a name="create-parameters"></a>Criar Parâmetros

1. Abra o projeto [que criou anteriormente](./quickstart-custom-speech-commands-create-new.md)
1. Vamos editar o comando existente para ligar e desligar vários dispositivos.
1. Porque o Comando vai agora andar de e `TurnOnOff`para cima, renomear o Comando para .
   - No painel esquerdo, selecione o `TurnOn` `...` comando e `+ New command` clique no ícone ao lado na parte superior do painel.
   
   - Selecione `Rename` o ícone. No pop-up do **comando Rename,** mude **de nome** para `TurOnOff`. Em seguida, selecione **Guardar**.

1. Em seguida, cria um novo parâmetro para representar se o utilizador quer ligar ou desligar o dispositivo.
   - Selecione `+ Add` o ícone presente na parte superior do painel médio. A partir da descida, selecione **Parameter**.
   - No painel mais à direita, pode ver a secção de configuração de **Parâmetros.**
   - Adicionar valor para **Nome**.
   - Verifique a caixa de verificação **necessária.** Na resposta Adicionar para uma janela **de parâmetro seletiva,** selecione **Editor Simples** e à **Primeira variação,** adicione
        ```
        On or Off?
        ```
   - Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Criar resposta de parâmetro sintetária necessária](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - Em seguida, vamos configurar o resto das propriedades do `Save` parâmetro da seguinte forma e selecionar para guardar configurações de todas as configurações para o parâmetro.
       

       | Configuração      | Valor sugerido     | Descrição                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | OnOff           | Um nome descritivo para parâmetro                                                                           |
       | É Global          | sem controlo       | Caixa de verificação indicando se um valor para este parâmetro é globalmente aplicado a todos os Comandos na aplicação|
       | Necessário           | verificado         | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando |
       | Resposta para parâmetro sinuoso      |Editor simples - > On or Off?      | Um pedido para pedir o valor deste parâmetro quando não é conhecido |
       | Tipo               | String          | O tipo de parâmetro, como Número, Corda, Hora da Data ou Geografia   |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para Strings, isto limita as inputs a um conjunto de valores possíveis |
       | Valores de entrada predefinidos     | on, off             | Conjunto de valores possíveis e seus pseudónimos         |
       
        > [!div class="mx-imgBorder"]
        > ![Criar parâmetro](media/custom-speech-commands/create-on-off-parameter.png)

   - Em seguida, `+ Add` selecione novamente o ícone para adicionar um segundo parâmetro para representar o nome dos dispositivos com a seguinte configuração.
   

       | Definição            | Valor sugerido       | Descrição                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | Name               | Dispositivo sujeito         | Um nome descritivo para parâmetro                                                                     |
       | É Global          | sem controlo             | Caixa de verificação indicando se um valor para este parâmetro é globalmente aplicado a todos os Comandos na aplicação |
       | Necessário           | verificado               | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando          |
       | Editor simples      | Que dispositivo?    | Um pedido para pedir o valor deste parâmetro quando não é conhecido                                       |
       | Tipo               | String                | O tipo de parâmetro, como Número, Corda, Hora da Data ou Geografia                                                |
       | Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | Para cordas, uma Lista de Cordas limita as inputs a um conjunto de valores possíveis       |
       | Valores de entrada predefinidos | tv, fã               | Conjunto de valores possíveis e seus pseudónimos                               |
       | Pseudónimos (tv)      | televisão, televisão     | Pseudónimos opcionais para cada valor possível dos valores de entrada predefinidos                                 |

## <a name="add-example-sentences"></a>Adicionar frases exemplo

Com comandos com parâmetros, é útil adicionar frases de exemplo que cobrem todas as combinações possíveis. Por exemplo:

1. Informação completa do parâmetro -`turn {OnOff} the {SubjectDevice}`
1. Informação parcial do parâmetro -`turn it {OnOff}`
1. Sem informações de parâmetros -`turn something`

As frases de exemplo com diferentes graus de informação permitem que a aplicação de Comandos Personalizados resolva resoluções de um tiro e resoluções multi-voltacom informações parciais.

Com isso em mente, edite as frases de exemplo para usar os parâmetros como sugerido abaixo.

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> Nas frases exemplo o editor usa aparelhos encaracolados para se referir aos seus parâmetros. - `turn {OnOff} the {SubjectDevice}`Utilize o separador para a conclusão automática apoiada por parâmetros previamente criados.

## <a name="add-parameters-to-completion-rules"></a>Adicione parâmetros às regras de conclusão

Modificar a regra de Conclusão que criámos no [arranque rápido anterior.](./quickstart-custom-speech-commands-create-new.md)

1. Na secção **Condições,** adicione uma nova condição selecionando **+ Adicione uma condição**
1. No novo pop-up **New** `Required parameters` Condition , Selecione a partir do **drop-down tipo.** Na lista de verificação `OnOff` abaixo, verifique ambos e `SubjectDevice`.
1. Clique em **Criar**.
1. Na secção **Ações,** edite a ação de resposta da fala enviar existente, pairando sobre a ação e clicando no ícone de edição. Desta vez, vamos fazer uso `OnOff` de `SubjectDevice` novos criados e parâmetros

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Experimente
1. Selecione `Train` o ícone presente em cima do painel direito.

1. Uma vez, o `Test`treino completa, selecione .
    - Aparecerá um novo teste da sua janela de **candidatura.**
    - Tente algumas interações.

        - Entrada: desligue a televisão
        - Saída: Ok, desligar a televisão        
        - Entrada: desligue a televisão
        - Saída: Ok, desligar a televisão
        - Entrada: desligá-lo
        - Saída: Qual dispositivo?
        - Entrada: a televisão
        - Saída: Ok, desligar a televisão

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Quickstart: Use comandos personalizados com voz personalizada (pré-visualização)](./quickstart-custom-speech-commands-select-custom-voice.md)
