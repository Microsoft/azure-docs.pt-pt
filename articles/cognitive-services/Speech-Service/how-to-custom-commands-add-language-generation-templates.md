---
title: 'Como: Usar modelos de geração de linguagem para respostas de fala - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprende a usar modelos de geração de linguagem com comandos personalizados. Os modelos de geração de idiomas permitem-lhe personalizar as respostas enviadas ao cliente e introduzir variação nas respostas.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: sausin
ms.openlocfilehash: 40c5e3474d3992108ef61d34e745bc63c1f7a713
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020951"
---
# <a name="add-language-generation-templates-for-speech-responses"></a>Adicionar modelos de geração de linguagem para respostas de voz

Neste artigo, você aprende a usar modelos de geração de linguagem com comandos personalizados. Os modelos de geração de idiomas permitem-lhe personalizar as respostas enviadas ao cliente e introduzir variação nas respostas. A personalização da geração linguística pode ser alcançada por:

- Utilização de modelos de geração de linguagem
- Utilização de expressões adaptativas

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:

> [!div class="checklist"]
> * [Como: Criar aplicação com comandos simples](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Como: Adicionar parâmetros aos comandos](./how-to-custom-commands-add-parameters-to-commands.md)

## <a name="language-generation-templates-overview"></a>Visão geral dos modelos de geração de linguagem

Os modelos de comandos personalizados são baseados nos [modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates)do BotFramework. Uma vez que os Comandos Personalizados criam um novo modelo LG quando necessário (isto é, para respostas de fala em parâmetros ou ações) não tem de especificar o nome do modelo LG. Então, em vez de definir o seu modelo como:

 ```
    # CompletionAction
    - Ok, turning {OnOff} the {SubjectDevice}
    - Done, turning {OnOff} the {SubjectDevice}
    - Proceeding to turn {OnOff} {SubjectDevice}
 ```

Basta definir o corpo do modelo sem o nome, da seguinte forma.

> [!div class="mx-imgBorder"]
> ![exemplo de editor de modelo](./media/custom-commands/template-editor-example.png)


Esta alteração introduz variação nas respostas de fala enviadas ao cliente. Assim, para a mesma expressão, a resposta de discurso correspondente seria escolhida aleatoriamente das opções fornecidas.

Tirar partido dos modelos LG também permite definir respostas complexas de fala para comandos usando expressões adaptativas. Pode consultar o [formato dos modelos LG](/azure/bot-service/file-format/bot-builder-lg-file-format#templates) para mais detalhes. Os Comandos Personalizados por predefinição suportam todas as capacidades com as seguintes diferenças menores:

* Nas entidades de modelos LG as entidades são representadas como ${entityName}. Em Comandos Personalizados não usamos entidades, mas os parâmetros podem ser usados como variáveis com qualquer uma destas representações ${parâmetroName} ou {parâmetroName}
* A composição e expansão do modelo não são suportadas em Comandos Personalizados. Isto porque nunca edita o `.lg` ficheiro diretamente, mas apenas as respostas dos modelos criados automaticamente.
* As funções personalizadas injetadas pela LG não são suportadas em Comandos Personalizados. As funções predefinidas ainda são suportadas.
* As opções (estrita, substituirNull & lineBreakStyle) não são suportadas em Comandos Personalizados.

## <a name="add-template-responses-to-turnonoff-command"></a>Adicionar respostas de modelo ao comando TurnOnOff

Modificar o comando **TurnOnOff** para adicionar um novo parâmetro com a seguinte configuração:

| Definição            | Valor sugerido       | 
| ------------------ | --------------------- | 
| Nome               | `SubjectContext`         | 
| É Global          | descontrolado             | 
| Necessário           | descontrolado               | 
| Tipo               | String                |
| Valor predefinido      | `all` |
| Configuração      | Aceitar valores de entrada predefinidos do catálogo interno | 
| Valores de entrada predefinidos | `room`, `bathroom`, `all`|

### <a name="modify-completion-rule"></a>Modifique a regra de conclusão

Editar a secção **Ações** da regra de conclusão existente **ConfirmationResponse**. No pop-up de **ação editar,** mude para Editor de **Modelo** e substitua o texto pelo seguinte exemplo.

```
- IF: @{SubjectContext == "all" && SubjectDevice == "lights"}
    - Ok, turning all the lights {OnOff}
- ELSEIF: @{SubjectDevice == "lights"}
    - Ok, turning {OnOff} the {SubjectContext} {SubjectDevice}
- ELSE:
    - Ok, turning the {SubjectDevice} {OnOff}
    - Done, turning {OnOff} the {SubjectDevice}
```

**Treine** e **teste** a sua aplicação da seguinte forma. Note a variação de respostas devido ao uso de múltiplas alternativas do valor do modelo, e também o uso de expressões adaptativas.

* Entrada: ligue a televisão
* Saída: Ok, ligar a televisão
* Entrada: ligue a televisão
* Saída: Feito, ligado na televisão
* Entrada: apare pelas luzes
* Saída: Ok, apagar todas as luzes
* Entrada: desligue as luzes do quarto
* Saída: Ok, desligar as luzes do quarto

## <a name="use-custom-voice"></a>Utilizar a Voz Personalizada

Outra forma de personalizar as respostas de Comandos Personalizados é selecionar uma voz de saída personalizada. Utilize os seguintes passos para mudar a voz predefinida para uma voz personalizada.

1. Na sua aplicação de comandos personalizados, selecione **Definições** a partir do painel esquerdo.
1. Selecione **Voz Personalizada** a partir do painel do meio.
1. Selecione o costume ou a voz pública desejada da mesa.
1. Selecione **Guardar**.

> [!div class="mx-imgBorder"]
> ![Frases de amostra com parâmetros](media/custom-commands/select-custom-voice.png)

> [!NOTE]
> - Para **vozes públicas,** **os tipos neurais** só estão disponíveis para regiões específicas. Para verificar a disponibilidade, consulte [vozes padrão e neurais por região/ponto final.](./regions.md#standard-and-neural-voices)
> - Para **vozes personalizadas,** podem ser criadas a partir da página do projeto Custom Voice. Ver [Começar com Voz Personalizada.](./how-to-custom-voice.md)

Agora a aplicação responderá na voz selecionada, em vez da voz predefinido.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Integre os seus Comandos Personalizados utilizando o SDK de fala](./how-to-custom-commands-setup-speech-sdk.md).