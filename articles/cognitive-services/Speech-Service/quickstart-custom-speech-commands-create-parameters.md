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
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80348535"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Quickstart: Criar um Comando Personalizado com parâmetros (Pré-visualização)

No [artigo anterior,](./quickstart-custom-speech-commands-create-new.md)criámos um novo projeto de Comandos Personalizados para responder a comandos sem parâmetros.

Neste artigo, vamos alargar esta aplicação com parâmetros para que possa manusear ligar e desligar vários dispositivos.

## <a name="create-parameters"></a>Criar Parâmetros

1. Abra o projeto [que criamos anteriormente](./quickstart-custom-speech-commands-create-new.md)
1. Porque o Comando vai agora ligar e desligar, mude o nome do Comando para "TurnOnOff"
   - Paire sobre o nome do Comando e selecione o ícone de edição para alterar o nome
1. Crie um novo parâmetro para representar se o utilizador quer ligar ou desligar o dispositivo
   - Selecione o `+` ícone ao lado da secção Parâmetros

   > [!div class="mx-imgBorder"]
   > ![Criar parâmetro](media/custom-speech-commands/create-on-off-parameter.png)

   | Definição            | Valor sugerido     | Descrição                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | OnOff               | Um nome descritivo para o seu parâmetro                                                                     |
   | É Global          | sem controlo           | Caixa de verificação indicando se um valor para este parâmetro é globalmente aplicado a todos os Comandos do projeto |
   | Necessário           | verificado             | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando          |
   | Modelo de resposta  | "- Dentro ou fora?"      | Um pedido para pedir o valor deste parâmetro quando não é conhecido                                       |
   | Tipo               | Cadeia              | O tipo de parâmetro, como número, corda ou hora da data                                               |
   | Configuração      | Lista de Cordas         | Para cordas, uma Lista de Cordas limita as inputs a um conjunto de valores possíveis                                      |
   | Valores da lista de cordas | on, off             | Para um parâmetro da Lista de Cordas, o conjunto de valores possíveis e os seus sinónimos                                |

   - Em seguida, `+` selecione novamente o ícone para adicionar um segundo parâmetro para representar o nome dos dispositivos. Para este exemplo, uma televisão e um fã

   | Definição            | Valor sugerido       | Descrição                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | Dispositivo sujeito         | Um nome descritivo para o seu parâmetro                                                                     |
   | É Global          | sem controlo             | Caixa de verificação indicando se um valor para este parâmetro é globalmente aplicado a todos os Comandos do projeto |
   | Necessário           | verificado               | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando          |
   | Modelo de resposta  | "- Que dispositivo?"     | Um pedido para pedir o valor deste parâmetro quando não é conhecido                                       |
   | Tipo               | Cadeia                | O tipo de parâmetro, como número, corda ou hora da data                                               |
   | Configuração      | Lista de Cordas           | Para cordas, uma Lista de Cordas limita as inputs a um conjunto de valores possíveis                                      |
   | Valores da lista de cordas | tv, fã               | Para um parâmetro da Lista de Cordas, o conjunto de valores possíveis e os seus sinónimos                                |
   | Sinónimos (tv)      | televisão, televisão     | Sinónimos opcionais para cada valor possível de um parâmetro de lista de cordas                                      |

## <a name="add-sample-sentences"></a>Adicionar frases de amostra

Com parâmetros, é útil adicionar frases de amostra que cobrem todas as combinações possíveis. Por exemplo:

1. Informação completa do parâmetro -`"turn {OnOff} the {SubjectDevice}"`
1. Informação parcial do parâmetro -`"turn it {OnOff}"`
1. Sem informações de parâmetros -`"turn something"`

As frases de amostra com diferentes quantidades de informação permitem que a aplicação de Comandos Personalizados resolva resoluções de um tiro e resoluções multi-voltacom informações parciais.

Com isso em mente, editar as Frases da Amostra para utilizar os parâmetros como sugerido abaixo.

> [!TIP]
> Na amostra de frases do editor use aparelhos encaracolados para se referir aos seus parâmetros. - `turn {OnOff} the {SubjectDevice}`Utilize a conclusão do separador para se referir aos parâmetros previamente criados.

> [!div class="mx-imgBorder"]
> ![Frases de amostra com parâmetros](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Adicionar parâmetros à regra de Conclusão

Modificar a regra de Conclusão que criou no [quickstart anterior:](./quickstart-custom-speech-commands-create-new.md)

1. Adicione uma nova Condição e selecione o parâmetro necessário. Selecione ambos `OnOff` e`SubjectDevice`
1. Editar a ação de `OnOff` `SubjectDevice`resposta à fala para usar e:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Experimente

Abra o painel de chat do Teste e experimente algumas interações.

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
