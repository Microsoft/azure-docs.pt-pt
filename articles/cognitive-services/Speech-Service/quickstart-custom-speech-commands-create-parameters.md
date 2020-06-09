---
title: 'Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados com parâmetros - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, irá adicionar parâmetros a uma aplicação de Comandos Personalizados para que possa ligar e desligar vários dispositivos.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509309"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados com parâmetros

No [artigo anterior,](./quickstart-custom-speech-commands-create-new.md)criou uma aplicação de Comandos Personalizados simples sem parâmetros.

Neste artigo, irá estender a aplicação com parâmetros para que possa ligar e desligar vários dispositivos.

## <a name="create-parameters"></a>Criar parâmetros

1. Abra o projeto que criou no [artigo anterior.](./quickstart-custom-speech-commands-create-new.md)

   Vamos editar o comando existente para que possa ser usado para ligar e desligar vários dispositivos.
1. Porque o comando irá agora lidar tanto ligado como desligado, rebatiza-o para **TurnOnOff**.
   1. No painel esquerdo, selecione o comando **TurnOn** e, em seguida, selecione o botão elipse **(...**) ao lado do **comando Novo** na parte superior do painel.
   
   1. Selecione **Renomear**. Na janela **de comando Do nome,** altere o **nome** para **TurOnOff**. Selecione **Guardar**.

1. Crie um parâmetro para representar se o utilizador quer ligar ou desligar o dispositivo.
   1. **Selecione Adicione** na parte superior do painel central. Na lista de recuos, selecione **Parâmetro**.
   1. No painel direito, na secção **Parâmetros,** adicione um valor na caixa **Nome.**
   1. **Selecione Necessário**. Na resposta Add para uma janela **de parâmetros necessária,** selecione **Editor Simples**. Na primeira caixa **de variação,** insira este texto:
        ```
        On or Off?
        ```
   1. Selecione **Atualizar**.

       > [!div class="mx-imgBorder"]
       > ![Criar resposta de parâmetros necessária](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. Configure o resto das propriedades do parâmetro da seguinte forma:
       

    | Configuração      | Valor sugerido     | Descrição                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **Nome**               | **OnOff**           | Um nome descritivo para o parâmetro.                                                                  |
    | **É Global**          | Ilibado       | Uma caixa de verificação que indica se um valor para o parâmetro é aplicado globalmente a todos os comandos da aplicação.|
    | **Necessário**           | Selecionado         | Uma caixa de verificação que indica se é necessário um valor para o parâmetro.  |
    | **Resposta para parâmetro requerido**      |**Editor simples - > Ligado ou Desligado?**      | Um pedido para pedir o valor do parâmetro quando não é conhecido. |
    | **Tipo**               | **String**          | O tipo de parâmetro. Por exemplo, Número, Corda, Hora da Data, Geografia.   |
    | **Configuração**      | **Aceitar valores de entrada predefinidos do catálogo interno** | Para as cordas, esta definição limita as entradas a um conjunto de valores possíveis. |
    | **Valores de entrada predefinidos**     | **em**, **fora**             | Um conjunto de possíveis valores e seus pseudónimos.         |
       


    > [!div class="mx-imgBorder"]
    > ![Criar parâmetro](media/custom-speech-commands/create-on-off-parameter.png)

1. Selecione **Guardar** para guardar as definições.

 1. **Selecione Adicione** novamente para adicionar um segundo parâmetro. Este parâmetro representa o nome do dispositivo. Utilize estas definições:
   

       | Definição            | Valor sugerido       | Descrição                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **Nome**               | **SujeitoDevice**         | Um nome descritivo para parâmetro.                                                                     |
       | **É Global**          | Ilibado             | Uma caixa de verificação que indica se um valor para o parâmetro é aplicado globalmente a todos os comandos da aplicação. |
       | **Necessário**           | Selecionado               | Uma caixa de verificação que indica se é necessário um valor para o parâmetro.          |
       | **Editor simples**      | **Que dispositivo?**    | Um pedido para pedir o valor do parâmetro quando não é conhecido.                                       |
       | **Tipo**               | **String**                | O tipo de parâmetro. Por exemplo, Número, Corda, Hora da Data, Geografia.                                                |
       | **Configuração**      | **Aceitar valores de entrada predefinidos do catálogo interno** | Para as cordas, esta definição limita as entradas a um conjunto de valores possíveis.       |
       | **Valores de entrada predefinidos** | **tv,** **fã**               | Um conjunto de possíveis valores e seus pseudónimos.                               |
       | **Pseudónimos** (tv)      | **televisão,** **televisão**     | Pseudónimos opcionais para cada um dos valores de entrada predefinidos.                                 |

## <a name="add-example-sentences"></a>Adicionar frases de exemplo

Para comandos que têm parâmetros, é útil adicionar frases de exemplo que cobrem todas as combinações possíveis. Por exemplo:

- Informação completa dos parâmetros:`turn {OnOff} the {SubjectDevice}`
- Informação parcial do parâmetro:`turn it {OnOff}`
- Sem informação sobre parâmetros:`turn something`

Frases de exemplo que têm diferentes quantidades de informação permitem que a aplicação de Comandos Personalizados resolva resoluções de um tiro e resoluções multi-voltas que tenham informações parciais.

Com isso em mente, edite as frases de exemplo para usar os parâmetros como sugerido aqui:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> No editor de frases Exemplo, utilize aparelhos para se referir aos seus parâmetros: `turn {OnOff} the {SubjectDevice}` .
>
> Utilize o separador para a conclusão automática definido por parâmetros previamente criados.

## <a name="add-parameters-to-completion-rules"></a>Adicionar parâmetros às regras de conclusão

Modifique a regra de conclusão que criou no [arranque rápido anterior](./quickstart-custom-speech-commands-create-new.md).

1. Na secção **Condições,** **selecione Adicionar uma condição**.
1. Na janela **New Condition,** na lista **Tipo,** selecione **os parâmetros necessários**. Na lista, selecione **OnOff** e **SubjectDevice**.
1. Selecione **Criar**.
1. Na secção **Ações,** edite a ação de **resposta da fala enviar** por parte da ação e selecione o botão de edição. Desta vez, use os novos `OnOff` e `SubjectDevice` parâmetros:

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>Experimente
1. Selecione **Train** no topo do painel direito.

1. Quando o treino estiver feito, selecione **Teste**.
    
    Aparecerá uma janela **de teste.**

1. Tente algumas interações.

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Quickstart: Use comandos personalizados com voz personalizada (pré-visualização)](./quickstart-custom-speech-commands-select-custom-voice.md)
