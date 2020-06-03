---
title: Adicionar correção de um passo na pré-visualização de comandos personalizados - serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como adicionar uma correção de um passo para um comando numa aplicação de pré-visualização de comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310432"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>Adicione uma correção de um passo a um comando personalizado numa aplicação de pré-visualização de comandos personalizados

Neste artigo, você vai aprender a adicionar uma confirmação de um passo a um comando em uma aplicação de pré-visualização de comandos personalizados.

A correção de um passo é usada para atualizar um comando que acabou de ser concluído. Quando adicionar uma correção de um passo a um comando de alarme, pode mudar de ideias e atualizar a hora do alarme. Por exemplo:

- Entrada: Definir o alarme para amanhã ao meio-dia
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: Não, amanhã às 13h
- Saída: Ok

> [!NOTE]
> Num cenário real, o cliente executa uma ação como resultado da conclusão do comando. Este artigo pressupõe que tem um mecanismo para atualizar o alarme na sua aplicação back-end.

## <a name="prerequisites"></a>Pré-requisitos

Complete os passos nos seguintes artigos:
> [!div class="checklist"]

> * [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados](./quickstart-custom-speech-commands-create-new.md)
> * [Quickstart: Criar uma aplicação de pré-visualização de comandos personalizados com parâmetros](./quickstart-custom-speech-commands-create-parameters.md)
> * [Como: Adicionar confirmações a um comando numa aplicação de pré-visualização de comandos personalizados](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>Adicionar regras de interação para correção de um passo

Para demonstrar uma correção de um passo, estenda o comando **SetAlarm** que criou em [Como: Adicionar uma confirmação a um comando na Pré-visualização de Comandos Personalizados](./how-to-custom-speech-commands-confirmations.md).

1. Adicione uma regra de interação para atualizar o comando **SetAlarm.**

    Esta regra pede ao utilizador que confirme a data e a hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

   | Definição               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **Nome da regra**             | **Atualizar alarme anterior**                                            | Um nome que descreve o propósito da regra          |
   | **Condições**            | **O comando anterior precisa de ser atualizado & parâmetro necessário -> Data**                | Condições que determinam quando a regra pode ser executada    |   
   | **Ações**               | **Envie resposta de fala -> Editor simples -> Atualizar o alarme anterior para {DateTime}**      | A ação a tomar quando as condições de regra são verdadeiras |
   | **Estado pós-execução** | **Comando concluído**        | Estado do utilizador após a curva                   |

1. No painel, selecione a regra de interação que acabou de criar. Selecione o botão elipse **(...**) no canto superior esquerdo do painel. Em **seguida,** utilize a seta Move up para mover a regra para o topo da lista de **regras de interação.**
   > [!div class="mx-imgBorder"]
   > ![Adicionar uma validação de gama](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > Numa aplicação no mundo real, utilize a secção **Ações** para enviar uma atividade ao cliente ou ligue para um ponto final HTTP para atualizar o alarme no seu sistema.

## <a name="try-it-out"></a>Experimente

1. Selecione **Comboio**.

1. Depois do treino, selecione **Teste**e, em seguida, experimente estas interações:

   - Entrada: Definir o alarme para amanhã ao meio-dia
   - Saída: Tem certeza de que deseja definir um alarme para 2020-05-02 12:00:00
   - Entrada: Sim
   - Saída: Ok, alarme definido para 2020-05-02 12:00:00
   - Entrada: Não, amanhã às 14h
   - Saída: Atualizar o alarme anterior para 2020-05-02 14:00:00
