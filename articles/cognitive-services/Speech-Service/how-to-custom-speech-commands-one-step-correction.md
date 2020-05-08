---
title: 'Como: Adicionar uma correção de um passo a um comando personalizado (Pré-visualização) - Serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como implementar correções de um passo para um comando em Comandos Personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858271"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Como: Adicionar uma correção de um passo a um Comando Personalizado (Pré-visualização)

Neste artigo, aprenderá a adicionar uma confirmação de um passo a um comando.

A correção de um passo é usada para atualizar um comando que acabou de ser concluído. Isto é, se apenas configurar um alarme, pode mudar de ideias e atualizar a hora do alarme. Um exemplo de tal caso é o seguinte:

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: Não, amanhã às 13h
- Saída: Ok

Um cenário do mundo real, que é geralmente acompanhado por um cliente executando uma ação como resultado da conclusão do comando - este artigo assume que você, como desenvolvedor, tem um mecanismo para atualizar o alarme na sua aplicação back-end.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:
> [!div class="checklist"]

> * [Quickstart: Criar um Comando Personalizado (Pré-visualização)](./quickstart-custom-speech-commands-create-new.md)
> * [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)
> * [Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>Adicionar regras de interação para correção de um passo 

Para demonstrar a correção de um passo, vamos estender o comando **SetAlarm** criado em [Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md).
1. Adicione uma regra de interação para atualizar o alarme previamente definido. 

    Esta regra irá pedir ao utilizador que confirme a data e a hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

   | Definição               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Atualizar o alarme anterior                                            | Um nome que descreve o propósito da regra          |
   | Condições            | O comando anterior precisa de ser atualizado & Data de Data de Parâmetro -> Exigido                | Condições que determinam quando a regra pode ser executada    |   
   | Actions (Ações)               | Envie resposta de discurso -> Editor Simples -> Atualizar o alarme anterior para {DateTime}      | A ação a tomar quando as condições da regra são verdadeiras |
   | Estado pós-execução | Comando concluído        | Estado do utilizador após a volta                   |

1. Mova a regra que acabou de criar para o topo das regras de `...` interação (selecione a regra no painel e clique na seta UP presente sob o ícone na parte superior do painel médio).
   > [!div class="mx-imgBorder"]
   > ![Adicione uma validação de gama](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > Numa aplicação real, na secção Ações desta regra também enviará uma atividade ao cliente ou chamará um ponto final http para atualizar o alarme no seu sistema.

## <a name="try-it-out"></a>Experimente

Selecione, aguarde `Train` `Test`o treino completo e selecione .

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: Tem certeza de que quer definir um alarme para 2020-05-02 12:00:00
- Entrada: Sim
- Saída: Ok, alarme definido para 2020-05-02 12:00:00
- Entrada: Não, amanhã às 14h
- Saída: Atualizar o alarme anterior para 2020-05-02 14:00:00
