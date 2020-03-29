---
title: 'Como: Adicionar validações aos parâmetros de comando personalizados (Pré-visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como adicionar validações a um parâmetro em Comandos Personalizados.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76156459"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Como: Adicionar validações aos parâmetros de comando personalizados (Pré-visualização)

Neste artigo, você aprenderá a adicionar validações aos parâmetros e pedir á correção.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:

- [Quickstart: Criar um Comando Personalizado (Pré-visualização)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando settemperature

Para demonstrar validações, vamos criar um novo Comando que permita ao utilizador definir a temperatura.

1. Abra a sua aplicação de Comandos Personalizados previamente criada no [Estúdio de Discurso](https://speech.microsoft.com/)
1. Criar um novo Conjunto de **ComandoTemperatura**
1. Adicione um parâmetro para a temperatura-alvo
1. Adicione uma validação para o parâmetro de temperatura
   > [!div class="mx-imgBorder"]
   > ![Adicione uma validação de gama](media/custom-speech-commands/validations-add-temperature.png)

   | Definição           | Valor sugerido                                          | Descrição                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Nome              | Temperatura                                              | Um nome descritivo para o seu parâmetro de comando                                                    |
   | Necessário          | true                                                     | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando |
   | Modelo de resposta | "- Que temperatura gostaria?"                     | Um pedido para pedir o valor deste parâmetro quando não é conhecido                              |
   | Tipo              | Número                                                   | O tipo de parâmetro, como número, corda ou hora da data                                      |
   | Validação        | Valor min: 60, Valor Max: 80                             | Para os parâmetros número, a gama permitida de valores para o parâmetro                             |
   | Modelo de resposta | "- Desculpe, só posso definir entre 60 e 80 graus"      | Solicita-me para pedir um valor atualizado se a validação falhar                                       |

1. Adicione algumas frases de amostra

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicione uma regra de conclusão para confirmar o resultado

   | Definição    | Valor sugerido                                           | Descrição                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra  | Mensagem de Confirmação                                      | Um nome que descreve o propósito da regra          |
   | Condições | Parâmetro obrigatório - Temperatura                          | Condições que determinam quando a regra pode ser executada    |
   | Ações    | SpeechResponse - "- Ok, definindo para {Temperatura} graus" | A ação a tomar quando a condição da regra é verdadeira |

> [!TIP]
> Este exemplo usa uma resposta da fala para confirmar o resultado. Por exemplo, ao completar o Comando com uma ação de cliente ver: [Como: Cumprir comandos sobre o cliente com o SDK de Fala (Pré-visualização)](./how-to-custom-speech-commands-fulfill-sdk.md)

## <a name="try-it-out"></a>Experimente

Selecione o painel de teste e experimente algumas interações.

- Entrada: Desajuste a temperatura para 72 graus
- Saída: "Ok, regulação para 72 graus"

- Entrada: Desajuste a temperatura para 45 graus
- Saída: "Desculpe, só posso definir entre 60 e 80 graus"
- Entrada: torná-lo 72 graus em vez
- Saída: "Ok, regulação para 72 graus"

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md)
