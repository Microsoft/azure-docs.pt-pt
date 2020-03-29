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
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75456456"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Como: Adicionar uma correção de um passo a um Comando Personalizado (Pré-visualização)

Neste artigo, aprenderá a adicionar uma confirmação de um passo a um comando.

A correção de um passo é usada para atualizar um comando que acabou de ser concluído.

Ou seja, se apenas configurar um alarme, pode mudar de ideias e atualizar a hora do alarme.

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: "Ok, alarme definido para 12/06/2019 12:00:00"
- Entrada: Não, amanhã às 13h
- Saída: "Ok

Tenha em mente que isto implica que você, enquanto desenvolvedor, tem um mecanismo para atualizar o alarme na sua aplicação backend.

## <a name="prerequisites"></a>Pré-requisitos

Deve ter completado os passos nos seguintes artigos:

- [Quickstart: Criar um Comando Personalizado (Pré-visualização)](./quickstart-custom-speech-commands-create-new.md)
- [Quickstart: Criar um comando personalizado com parâmetros (pré-visualização)](./quickstart-custom-speech-commands-create-parameters.md)
- [Como: Adicionar uma confirmação a um Comando Personalizado (Pré-visualização)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Adicione as regras avançadas para a correção de um passo 

Para demonstrar uma correção de um passo, vamos estender o comando **SetAlarm** criado nas [Confirmações Como .](./how-to-custom-speech-commands-confirmations.md)
 
1. Adicione uma regra avançada para atualizar o alarme anterior. 

    Esta regra irá pedir ao utilizador que confirme a data e a hora do alarme e espera uma confirmação (sim/não) para a próxima volta.

   | Definição               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Atualizar o alarme anterior                                            | Um nome que descreve o propósito da regra          |
   | Condições            | AtualizaçãoLastCommand & Parâmetro necessário - DataTime                | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | SpeechResponse - "- Atualizar o alarme anterior para {DateTime}"       | A ação a tomar quando a condição da regra é verdadeira |
   | Estado após execução | Comando completo                                                 | Estado do utilizador após a volta                   |

1. Mova a regra que acabou de criar para o topo das regras avançadas (percorra a regra no painel e clique na seta UP).
   > [!div class="mx-imgBorder"]
   > ![Adicione uma validação de gama](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> Numa aplicação real, na secção Ações desta regra também enviará uma atividade ao cliente ou chamará um ponto final http para atualizar o alarme no seu sistema.

## <a name="try-it-out"></a>Experimente

Selecione o painel de teste e experimente algumas interações.

- Entrada: Desema o alarme para amanhã ao meio-dia
- Saída: "Tem certeza de que quer definir um alarme para 12/07/2019 12:00:00?"
- Entrada: Sim
- Saída: "Ok, alarme definido para 12/07/2019 12:00:00"
- Entrada: Não, amanhã às 13h
- Saída: "Atualizar o alarme anterior para 12/07/2019 13:00:00"
