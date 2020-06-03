---
title: Adicionar confirmações numa aplicação de pré-visualização de comandos personalizados - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar confirmações aos comandos numa aplicação de pré-visualização de comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 1cb0624012b22b6cae2c98bfa6ddc9495e09615d
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310473"
---
# <a name="add-confirmations-to-a-command-in-a-custom-commands-preview-application"></a>Adicionar confirmações a um comando numa aplicação de pré-visualização de comandos personalizados

Neste artigo, você vai aprender como criar confirmações para comandos numa aplicação de pré-visualização de comandos personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos nos seguintes artigos:
> [!div class="checklist"]
> * [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados](./quickstart-custom-speech-commands-create-new.md)
> * [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Criar um comando SetAlarm

Para demonstrar confirmações, crie um novo comando que desencadee um alarme.

1. No [Speech Studio,](https://speech.microsoft.com/)abra a aplicação de pré-visualização de comandos personalizados que criou.
1. Crie um novo comando **SetAlarm.**
1. Adicione um parâmetro **DateTime** que tenha a seguinte configuração:

   | Definição                           | Valor sugerido                     |  Descrição                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | **Nome**                              | **DateTime**                                | Nome descritivo para o parâmetro                                |
   | **Necessário**                          | Assinalado                                 | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o comando |
   | **Resposta para um parâmetro necessário**   | **Editor simples - > A que horas?**                              | Um pedido para pedir o valor deste parâmetro quando não se sabe |
   | **Tipo**                              | **DateTime**                                | Tipo de parâmetro, como Número, Cadeia, DataTime ou Geografia   |
   | **Predefinições de data**                     | Se faltar a data, use a data de hoje.            | Valor predefinido da variável a utilizar se não for fornecido pelo utilizador  |  
   | **Incumprimentos do tempo**                     | Se faltar a hora, use o início do dia     |  Valor predefinido da variável a utilizar se não for fornecido pelo utilizador|

1. Adicione algumas frases de exemplo.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adicione uma regra de conclusão para confirmar o resultado. Utilize a seguinte configuração:

   | Definição    | Valor sugerido                               |Descrição                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | **Nome da regra**  | **Definir alarme**                                               |    Um nome que descreve o propósito da regra |
   | **Ações**    | **Envie resposta de fala -> OK, conjunto de alarme para {DateTime}**    |A ação a tomar quando a condição de regra é verdadeira

## <a name="try-it-out"></a>Experimente

1. Selecione **Train** no topo do painel direito.

1. Depois do treino, selecione **Teste**e, em seguida, experimente as seguintes interações:
    - Entrada: Definir o alarme para amanhã ao meio-dia
    - Saída: OK, alarme definido para 2020-05-02 12:00:00
    - Entrada: Definir um alarme
    - Saída: A que horas?
    - Entrada: 17h
    - Saída: OK, alarme definido para 2020-05-01 17:00:00

## <a name="add-interaction-rules-for-the-confirmation"></a>Adicionar regras de interação para a confirmação

Crie confirmações adicionando regras de interação.

1. No seu comando **SetAlarm,** **selecione Adicione** no painel central e, em seguida, selecione **regras de interação**  >  **Confirme o comando**.

    Esta regra solicita ao utilizador que confirme a data e a hora do alarme. Utilize estas definições:

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Confirmar a data**                                                                | Um nome que descreve o propósito da regra          |
   | **Condições**            | **Parâmetro necessário -> Data**                                                    | Condições que determinam quando a regra pode ser executada    |   
   | **Ações**               | **Enviar resposta de fala -> Tem a certeza de que pretende configurar um alarme para {DateTime}?**     | A ação a tomar quando a condição de regra é verdadeira |
   | **Expectativas**          | **Esperando confirmação do utilizador**                                                 | Expectativa para a próxima volta                      |
   | **Estado pós-execução**  | **Aguarde a entrada do utilizador**                                                            | Estado para o utilizador após a volta                  |
  
      > [!div class="mx-imgBorder"]
      > ![Criar resposta de parâmetros necessária](media/custom-speech-commands/add-validation-set-temperature.png)

1. Adicione uma regra de interação para uma confirmação aceite (o utilizador disse "sim"). Utilize a seguinte configuração:

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Confirmação aceite**                                                            | Um nome que descreve o propósito da regra          |
   | **Condições**            | **A confirmação foi bem sucedida & parâmetro necessário -> Data**                      | Condições que determinam quando a regra pode ser executada    |   
   | **Estado pós-execução** | **Executar regras de conclusão**                                                          | Estado do utilizador após a curva                   |

1. Adicione uma regra de interação para uma confirmação negada (o utilizador disse "não"). Utilize a seguinte configuração:

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Confirmação negada**                                                                   | Um nome que descreve o propósito da regra          |
   | **Condições**            | **Confirmação foi negada & Parâmetro Necessário -> Data**                               | Condições que determinam quando a regra pode ser executada    |   
   | **Ações**               | **Valor de parâmetro claro -> DataTime & Enviar resposta da fala -> Sem problema, a que horas então?**  | A ação a tomar quando a condição de regra é verdadeira |
   | **Estado após execução** | **Aguarde a entrada**                                                                   | Estado do utilizador após a curva                   |
   | **Expectativas**          | **Esperando a entrada de parâmetros do utilizador -> DataTime**                           | Expectativa para a próxima volta                      |

## <a name="try-out-the-changes"></a>Experimente as alterações

1. Selecione **Comboio**.

1. Depois do treino ser feito, selecione **Teste**e, em seguida, experimente estas interações:

    - Entrada: Definir o alarme para amanhã ao meio-dia
    - Saída: Tem a certeza de que pretende definir um alarme para 2020-05-02 12:00:00?
    - Entrada: Não
    - Saída: Sem problema, a que horas então?
    - Entrada: 17h
    - Saída: "Tem certeza de que quer definir um alarme para 2020-05-01 17:00:00?"
    - Entrada: Sim
    - Saída: OK, alarme definido para 2020-05-01 17:00:00

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Adicione uma correção de um passo a um comando numa aplicação de pré-visualização de comandos personalizados](./how-to-custom-speech-commands-one-step-correction.md)
