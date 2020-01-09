---
title: 'Como: Adicionar validações a parâmetros de comando personalizados (visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como adicionar validações a um parâmetro em comandos personalizados.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: c89c388f919ca95a331d1d406f5b1776c127ebad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446918"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Como: Adicionar validações a parâmetros de comando personalizados (visualização)

Neste artigo, você aprenderá a adicionar validações aos parâmetros e solicitar a correção.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:

- [Início rápido: criar um comando personalizado (versão prévia)](./quickstart-custom-speech-commands-create-new.md)
- [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando settemperaturas

Para demonstrar validações, vamos criar um novo comando, permitindo que o usuário defina a temperatura.

1. Abra o aplicativo de comandos personalizados criado anteriormente no [Speech Studio](https://speech.microsoft.com/)
1. Criar um novo comando **Settemperatura**
1. Adicionar um parâmetro para a temperatura de destino
1. Adicionar uma validação para o parâmetro de temperatura
   > [!div class="mx-imgBorder"]
   > ![adicionar uma validação de intervalo](media/custom-speech-commands/validations-add-temperature.png)

   | Definição           | Valor sugerido                                          | Descrição                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nome              | Temperatura                                              | Um nome descritivo para o parâmetro de comando                                                    |
   | Obrigatório          | true                                                     | Caixa de seleção que indica se um valor para esse parâmetro é necessário antes de concluir o comando |
   | Modelo de resposta | "-Que temperatura você deseja?"                     | Um prompt para solicitar o valor desse parâmetro quando ele não for conhecido                              |
   | Tipo              | Número                                                   | O tipo de parâmetro, como número, Cadeia de caracteres ou data e hora                                      |
   | Validação        | Valor mínimo: 60, valor máximo: 80                             | Para parâmetros numéricos, o intervalo de valores permitido para o parâmetro                             |
   | Modelo de resposta | "-Desculpe, só posso definir entre 60 e 80 graus"      | Solicitar um valor atualizado se a validação falhar                                       |

1. Adicionar algumas frases de exemplo

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicionar uma regra de conclusão para confirmar o resultado

   | Definição    | Valor sugerido                                           | Descrição                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra  | Mensagem de confirmação                                      | Um nome que descreve a finalidade da regra          |
   | Condições | Parâmetro necessário-temperatura                          | Condições que determinam quando a regra pode ser executada    |
   | Ações    | SpeechResponse-"-Ok, definindo para {temperatura} graus" | A ação a ser tomada quando a condição da regra for verdadeira |

> [!TIP]
> Este exemplo usa uma resposta de fala para confirmar o resultado. Para obter exemplos sobre como concluir o comando com uma ação de cliente, consulte: [como realizar comandos no cliente com o SDK de fala (versão prévia)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Experimentar

Selecione o painel de teste e tente algumas interações.

- Entrada: defina a temperatura como 72 graus
- Saída: "Ok, definindo como 72 graus"

- Entrada: defina a temperatura como 45 graus
- Saída: "Desculpe, só posso definir entre 60 e 80 graus"
- Entrada: torne-o 72 graus
- Saída: "Ok, definindo como 72 graus"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: adicionar uma confirmação a um comando personalizado (visualização)](./how-to-custom-speech-commands-confirmations.md)