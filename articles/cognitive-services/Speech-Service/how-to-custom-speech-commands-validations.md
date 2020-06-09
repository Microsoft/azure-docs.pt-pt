---
title: Adicionar validações em Pré-visualização de Comandos Personalizados - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar validações a um parâmetro de comando numa aplicação de pré-visualização de comandos personalizados.
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: nitinme
ms.openlocfilehash: eb011510a9f636aea9910a4be445cd094acf0c21
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509581"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Adicionar validações a um parâmetro de comando numa aplicação de pré-visualização de comandos personalizados

Neste artigo, você vai aprender a adicionar validações a parâmetros e solicitações para correção.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos nos seguintes artigos:

> [!div class="checklist"]
 
> * [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados](./quickstart-custom-speech-commands-create-new.md)
> * [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Criar um comando SetTemperature

Para demonstrar validações, crie um novo comando que permita aos utilizadores definir a temperatura.

1. No [Speech Studio,](https://speech.microsoft.com/)abra a aplicação de pré-visualização de comandos personalizados que criou.
1. Crie um novo comando **SetTemperature.**
1. Adicione um parâmetro de temperatura que tenha a seguinte configuração:

   | Configuração de parâmetros           | Valor sugerido    |Descrição                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Nome**              | **Temperatura**                       | Um nome descritivo para parâmetro                                |
   | **Necessário**          | Assinalado                           | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o comando |
   | **Resposta para parâmetro requerido**     | **Editor simples - > Que temperatura gostaria?**  | Um pedido para pedir o valor deste parâmetro quando não se sabe |
   | **Tipo**              | **Number**                            | Tipo de parâmetro, como Número, Cadeia, DataTime ou Geografia   |

1. Adicione uma validação para o parâmetro de temperatura.

    1. Na página de configuração **de Parâmetros** para o parâmetro de temperatura, selecione **Adicionar uma validação** na secção **Validações.**

    1. Na nova janela pop-up de **validação,** configuure a validação da seguinte forma:
  
       | Configuração de parâmetros         | Valor sugerido                                          | Descrição                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Valor Mínimo**        | **60**               | Para os parâmetros número, o valor mínimo que este parâmetro pode assumir |
       | **Valor Máximo**        | **80**               | Para os parâmetros número, o valor máximo que este parâmetro pode assumir |
       | **Resposta de falha - Editor simples**| **Primeira Variação - Desculpe, só posso definir entre 60 e 80 graus**      | A prompt para pedir um novo valor se a validação falhar                                       |

       > [!div class="mx-imgBorder"]
       > ![Adicionar uma validação de gama](media/custom-speech-commands/validations-add-temperature.png)

1. Selecione **Criar**.

1. Adicione algumas frases de exemplo.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Adicione uma regra de conclusão que tenha a seguinte configuração. Esta regra confirma o resultado.

   | Definição    | Valor sugerido                                           |Descrição                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Name       | Mensagem de Confirmação                                      |Um nome que descreve o propósito da regra |
   | **Condições** | **Parâmetros necessários - Temperatura**                       |Condições que determinam quando a regra pode ser executada    |   
   | **Ações**    | **Enviar resposta da fala - Ok, ajustar a temperatura para graus {Temperatura}** | A ação a tomar quando a condição de regra é verdadeira |

> [!TIP]
> Este exemplo usa uma resposta de fala para confirmar o resultado. Por exemplo, ao completar o comando com uma ação do cliente, consulte [Como: Cumprir comandos no cliente com o Discurso SDK](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Experimente

1. Selecione **Comboio**.

1. Depois do treino, selecione **Teste**e, em seguida, experimente estas interações:

    - Entrada: Definir a temperatura para 72 graus
    - Saída: Ok, ajustar a temperatura para 72 graus
    - Entrada: Definir a temperatura para 45 graus
    - Saída: Desculpe, só posso definir entre 60 e 80 graus
    - Entrada: faça 72 graus em vez
    - Saída: Ok, ajustar a temperatura para 72 graus

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione confirmações a um comando numa aplicação de pré-visualização de comandos personalizados](./how-to-custom-speech-commands-confirmations.md)
