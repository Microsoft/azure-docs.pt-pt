---
title: 'Como: Adicionar validações aos parâmetros de comando personalizados'
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
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857177"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>Como: Adicionar validações aos parâmetros de comando personalizados (Pré-visualização)

Neste artigo, irá adicionar validações a parâmetros e solicitações para correção.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:

> [!div class="checklist"]
> * [Quickstart: Criar um comando personalizado](./quickstart-custom-speech-commands-create-new.md)
> * [Quickstart: Criar um comando personalizado com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando settemperature

Para demonstrar validações, vamos criar um novo Comando que permita aos utilizadores definir a temperatura.

1. Abra a sua aplicação de Comandos Personalizados previamente criada no [Estúdio de Discurso](https://speech.microsoft.com/)
1. Criar um novo Comando`SetTemperature`
1. Adicione um parâmetro para a temperatura-alvo.

   | Configuração do parâmetro           | Valor sugerido    |Descrição                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | Name              | Temperatura                       | Um nome descritivo para parâmetro                                |
   | Necessário          | verificado                           | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando |
   | Resposta para parâmetro sinuoso     | Editor simples - > que temperatura gostaria?  | Um pedido para pedir o valor deste parâmetro quando não é conhecido |
   | Tipo              | Número                            | Tipo de parâmetro, como Número, Corda, Hora da Data ou Geografia   |

1. Adicione uma validação para o parâmetro de temperatura.

    - Na página de configuração dos `Add a validation` **parâmetros** para `Temperature` parâmetros, selecione na secção Validações.
    - Preencha os valores no pop-up de **validação Nova** da seguinte forma e selecione **Criar**.

  
       | Configuração do parâmetro         | Valor sugerido                                          | Descrição                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Valor Mínimo        | 60               | Para os parâmetros número, o valor mínimo que este parâmetro pode assumir |
       | Valor Máximo        | 80               | Para os parâmetros número, o valor máximo que este parâmetro pode assumir |
       | Resposta de falha - Editor simples| Primeira Variação - Desculpe, só posso definir entre 60 e 80 graus      | Solicita-me que peça um novo valor se a validação falhar                                       |

       > [!div class="mx-imgBorder"]
       > ![Adicione uma validação de gama](media/custom-speech-commands/validations-add-temperature.png)

1. Adicione algumas frases de exemplo

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicione uma regra de conclusão para confirmar o resultado

   | Definição    | Valor sugerido                                           |Descrição                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Mensagem de Confirmação                                      |Um nome que descreve o propósito da regra |
   | Condições | Parâmetros necessários -`Temperature`                       |Condições que determinam quando a regra pode ser executada    |   
   | Actions (Ações)    | Enviar resposta da fala -`Ok, setting temperature to {Temperature} degrees` | A ação a tomar quando a condição da regra é verdadeira |

> [!TIP]
> Este exemplo usa uma resposta da fala para confirmar o resultado. Por exemplo, ao completar o Comando com uma ação de cliente ver: [Como: Cumprir comandos sobre o cliente com o SDK de Fala](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>Experimente
1. Selecione `Train` o ícone presente em cima do painel direito.

1. Uma vez, o `Test` treino completa, selecione e experimente algumas interações.

    - Entrada: Desajuste a temperatura para 72 graus
    - Saída: Ok, regulação da temperatura para 72 graus
    - Entrada: Desajuste a temperatura para 45 graus
    - Saída: Desculpe, só posso definir entre 60 e 80 graus
    - Entrada: torná-lo 72 graus em vez
    - Saída: Ok, regulação da temperatura para 72 graus

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md)
