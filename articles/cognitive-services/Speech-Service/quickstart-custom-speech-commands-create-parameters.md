---
title: 'Início rápido: criar um comando personalizado com parâmetros (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, você adicionará parâmetros a um aplicativo de comandos personalizados.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 50132593ce3301094ea39546f5661df06a716503
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976592"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Início rápido: criar um comando personalizado com parâmetros (versão prévia)

No [artigo anterior](./quickstart-custom-speech-commands-create-new.md), criamos um novo projeto de comandos personalizados para responder a comandos sem parâmetros.

Neste artigo, estenderemos esse aplicativo com parâmetros para que ele possa lidar com a ativação e a desativação de vários dispositivos.

## <a name="create-parameters"></a>Criar parâmetros

1. Abrir o projeto [que criamos anteriormente](./quickstart-custom-speech-commands-create-new.md)
1. Como o comando agora será manipulado e desativado, renomeie o comando para "TurnOnOff"
   - Passe o mouse sobre o nome do comando e selecione o ícone Editar para alterar o nome
1. Criar um novo parâmetro para representar se o usuário deseja ativar ou desativar o dispositivo
   - Selecione o ícone de `+` ao lado da seção de parâmetros

   > [!div class="mx-imgBorder"]
   > ![criar](media/custom-speech-commands/create-on-off-parameter.png) de parâmetro

   | Definição            | Valor sugerido | Descrição                                                                                               |
   | ------------------ | --------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | OnOff           | Um nome descritivo para seu parâmetro                                                                     |
   | É global          | Desmarcada       | Caixa de seleção que indica se um valor para esse parâmetro é globalmente aplicado a todos os comandos no projeto |
   | Obrigatório           | check         | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando          |
   | Modelo de resposta  | Ativar ou desativar?      | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido                                       |
   | Tipo               | String          | O tipo de parâmetro, como número, Cadeia de caracteres ou data e hora                                               |
   | Configuração      | Lista de cadeias de caracteres     | Para cadeias de caracteres, uma lista String limita as entradas a um conjunto de valores possíveis                                      |
   | Valores da lista de cadeia de caracteres | ativado, desativado         | Para um parâmetro de lista de cadeia de caracteres, o conjunto de valores possíveis e seus sinônimos                                |

   - Em seguida, selecione o ícone de `+` novamente para adicionar um segundo parâmetro para representar o nome dos dispositivos. Para este exemplo, uma TV e um ventilador

   | Definição            | Valor sugerido   | Descrição                                                                                               |
   | ------------------ | ----------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | SubjectDevice     | Um nome descritivo para seu parâmetro                                                                     |
   | É global          | Desmarcada         | Caixa de seleção que indica se um valor para esse parâmetro é globalmente aplicado a todos os comandos no projeto |
   | Obrigatório           | check           | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando          |
   | Modelo de resposta  | Qual dispositivo?     | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido                                       |
   | Tipo               | String            | O tipo de parâmetro, como número, Cadeia de caracteres ou data e hora                                               |
   | Configuração      | Lista de cadeias de caracteres       | Para cadeias de caracteres, uma lista String limita as entradas a um conjunto de valores possíveis                                      |
   | Valores da lista de cadeia de caracteres | TV, ventilador           | Para um parâmetro de lista de cadeia de caracteres, o conjunto de valores possíveis e seus sinônimos                                |
   | Sinônimos (TV)      | televisão, conte | Sinônimos opcionais para cada valor possível de um parâmetro de lista de cadeias de caracteres                                      |

## <a name="add-sample-sentences"></a>Adicionar sentenças de exemplo

Com parâmetros, é útil adicionar frases de exemplo que abrangem todas as combinações possíveis. Por exemplo:

1. Informações completas sobre o parâmetro-`"turn {OnOff} the {SubjectDevice}"`
1. Informações de parâmetro parcial-`"turn it {OnOff}"`
1. Nenhuma informação de parâmetro-`"turn something"`

As frases de exemplo com diferentes quantidades de informações permitem que o aplicativo de comandos personalizados resolva resoluções de uma imagem e resoluções de várias opções com informações parciais.

Com isso em mente, edite as sentenças de exemplo para usar os parâmetros conforme sugerido abaixo.

> [!TIP]
> No editor de sentenças de exemplo, use chaves para fazer referência aos parâmetros. - `turn {OnOff} the {SubjectDevice}` use o preenchimento com Tab para se referir aos parâmetros criados anteriormente.

> [!div class="mx-imgBorder"]
> ![sentenças de exemplo com parâmetros](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Adicionar parâmetros à regra de conclusão

Modifique a regra de conclusão que você criou no [início rápido anterior](./quickstart-custom-speech-commands-create-new.md):

1. Adicione uma nova condição e selecione o parâmetro obrigatório. Selecione `OnOff` e `SubjectDevice`
1. Edite a ação de resposta de fala para usar `OnOff` e `SubjectDevice`:

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Experimentar

Abra o painel Test chat e tente algumas interações.

- Entrada: desligar a TV
- Saída: Ok, desligando a TV

- Entrada: desligar a televisão
- Saída: Ok, desligando a TV

- Entrada: Desative-a
- Saída: qual dispositivo?
- Entrada: a TV
- Saída: Ok, desligando a TV

## <a name="next-steps"></a>Passos seguintes
> [!div class="nextstepaction"]
> [Início rápido: conectar a um aplicativo de comando personalizado com o SDK de fala (versão prévia)](./quickstart-custom-speech-commands-speech-sdk.md)

