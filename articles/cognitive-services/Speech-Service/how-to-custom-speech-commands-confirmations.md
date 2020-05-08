---
title: 'Como: Adicionar uma confirmação a um comando personalizado (Pré-visualização)'
titleSuffix: Azure Cognitive Services
description: Neste artigo, como implementar confirmações para um comando em Comandos Personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858224"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)

Neste artigo, aprenderá a adicionar uma confirmação a um comando.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:
> [!div class="checklist"]
> *  [Quickstart: Criar um Comando Personalizado (Pré-visualização)](./quickstart-custom-speech-commands-create-new.md)
> * [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>Criar um comando SetAlarm

Para demonstrar confirmações, vamos criar um novo Comando que permita ao utilizador definir um alarme.

1. Abra a sua aplicação de Comandos Personalizados previamente criada no [Speech Studio](https://speech.microsoft.com/).
1. Criar um `SetAlarm`novo Comando.
1. Adicione um parâmetro `DateTime` chamado com a seguinte configuração.

   | Definição                           | Valor sugerido                     |  Descrição                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | Name                              | DateTime                                | Um nome descritivo para parâmetro                                |
   | Necessário                          | verificado                                 | Caixa de verificação indicando se é necessário um valor para este parâmetro antes de completar o Comando |
   | Resposta para parâmetro sinuoso   | Editor simples - > A que horas?                              | Um pedido para pedir o valor deste parâmetro quando não é conhecido |
   | Tipo                              | DateTime                                | O tipo de parâmetro, como Número, Corda, Hora da Data ou Geografia   |
   | Incumprimentos da data                     | Se a data faltar hoje            | Valor predefinido da variável a utilizar se não for fornecido pelo utilizador.  |  
   | Predefinições de tempo                     | Se o tempo faltar ao uso início do dia     |  Valor predefinido da variável a utilizar se não for fornecido pelo utilizador.|

1. Adicione algumas frases de exemplo.
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. Adicione uma regra de conclusão para confirmar o resultado.

   | Definição    | Valor sugerido                               |Descrição                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | Nome da Regra  | Definir alarme                                               |    Um nome que descreve o propósito da regra |
   | Actions (Ações)    | Enviar resposta de discurso - Ok, conjunto de alarme para {DateTime}"    |A ação a tomar quando a condição da regra é verdadeira

## <a name="try-it-out"></a>Experimente

1. Selecione `Train` o ícone presente em cima do painel direito.

1. Uma vez concluído `Test`o treino, selecione .
    - Entrada: Desema o alarme para amanhã ao meio-dia
    - Saída: Ok, alarme definido para 2020-05-02 12:00:00
    - Entrada: Desabote um alarme
    - Saída: A que horas?
    - Entrada: 17h00
    - Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="add-the-advanced-rules-for-confirmation"></a>Adicione as regras avançadas para confirmação

As confirmações são conseguidas através da adição de regras de interações.

1. No comando `SetAlarm` existente, adicione uma regra `+Add` de **interação** por ícone de seleção no painel médio e, em seguida, selecione -> regras de **interação****Confirme**o comando .

    Esta regra irá pedir ao utilizador que confirme a data e a hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmar a data de data                                                                | Um nome que descreve o propósito da regra          |
   | Condições            | Parâmetro necessário -data de data de >                                                    | Condições que determinam quando a regra pode ser executada    |   
   | Actions (Ações)               | Enviar resposta de discurso -> Tem a certeza de que pretende definir um alarme para {DateTime}?     | A ação a tomar quando a condição da regra é verdadeira |
   | Expectativas          | Esperando confirmação do utilizador                                                 | Expectativa para a próxima volta                      |
   | Estado pós-execução  | Aguarde a entrada do utilizador                                                            | Estado para o utilizador após a volta                  |
  
      > [!div class="mx-imgBorder"]
      > ![Criar resposta de parâmetro sintetária necessária](media/custom-speech-commands/add-validation-set-temperature.png)

1. Adicione outra regra de interação para lidar com uma confirmação bem sucedida (o utilizador disse que sim)

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmação aceite                                                            | Um nome que descreve o propósito da regra          |
   | Condições            | A confirmação foi bem sucedida & Necessário parâmetro -data-data ->                      | Condições que determinam quando a regra pode ser executada    |   
   | Estado pós-execução | Executar regras de conclusão                                                          | Estado do utilizador após a volta                   |

1. Adicione uma regra avançada para lidar com uma confirmação negada (o utilizador disse que não)

   | Definição               | Valor sugerido                                                                  | Descrição                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Confirmação negada                                                                   | Um nome que descreve o propósito da regra          |
   | Condições            | A confirmação foi negada & Data de Data de > ->                               | Condições que determinam quando a regra pode ser executada    |   
   | Actions (Ações)               | Valor claro do parâmetro -> DataTempo & Enviar a resposta da fala -> Sem problema, a que horas?  | A ação a tomar quando a condição da regra é verdadeira |
   | Estado após execução | Aguarde a entrada                                                                   | Estado do utilizador após a volta                   |
   | Expectativas          | Esperando a entrada de parâmetros do utilizador -> DataTime                           | Expectativa para a próxima volta                      |

## <a name="try-out-the-changes"></a>Experimente as alterações

Selecione, `Train`aguarde `Test`o treino completo e selecione .

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: Tem certeza de que quer definir um alarme para 2020-05-02 12:00:00?
- Entrada: Não
- Saída: Sem problemas, a que horas então?
- Entrada: 17h00
- Saída: "Tem certeza de que quer definir um alarme para 2020-05-01 17:00:00?".
- Entrada: Sim
- Saída: Ok, alarme definido para 2020-05-01 17:00:00

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Adicionar uma correção de um passo a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-one-step-correction.md)