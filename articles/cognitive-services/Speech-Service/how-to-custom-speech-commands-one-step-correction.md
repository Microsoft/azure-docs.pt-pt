---
title: 'Como: adicionar uma correção de uma etapa a um comando personalizado (versão prévia) – serviço de fala'
titleSuffix: Azure Cognitive Services
description: Neste artigo, explicamos como implementar correções de uma etapa para um comando em comandos personalizados.
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456456"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>Como: adicionar uma correção de uma etapa a um comando personalizado (versão prévia)

Neste artigo, você aprenderá a adicionar a confirmação de uma etapa a um comando.

A correção de uma etapa é usada para atualizar um comando que acabou de ser concluído.

Ou seja, se você apenas configurou um alarme, poderá mudar de ideia e atualizar a hora do alarme.

- Entrada: Definir alarme para amanhã às 12h
- Saída: "Ok, alarme definido para 12/06/2019 12:00:00"
- Entrada: não, amanhã às às 13:00
- Saída: "Ok

Tenha em mente que isso significa que você, como desenvolvedor, tem um mecanismo para atualizar o alarme em seu aplicativo de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Você deve ter concluído as etapas nos seguintes artigos:

- [Início rápido: criar um comando personalizado (versão prévia)](./quickstart-custom-speech-commands-create-new.md)
- [Início rápido: criar um comando personalizado com parâmetros (versão prévia)](./quickstart-custom-speech-commands-create-parameters.md)
- [Como: adicionar uma confirmação a um comando personalizado (visualização)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>Adicionar as regras avançadas para a correção de uma etapa 

Para demonstrar a correção de uma etapa, Vamos estender o comando **setAlarm** criado no [como confirmações](./how-to-custom-speech-commands-confirmations.md).
 
1. Adicione uma regra avançada para atualizar o alarme anterior. 

    Essa regra solicitará que o usuário confirme a data e a hora do alarme e está esperando uma confirmação (Sim/não) para a próxima vez.

   | Definição               | Valor sugerido                                                  | Descrição                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | Nome da Regra             | Atualizar alarme anterior                                            | Um nome que descreve a finalidade da regra          |
   | Condições            | UpdateLastCommand & parâmetro necessário-DateTime                | Condições que determinam quando a regra pode ser executada    |   
   | Ações               | SpeechResponse-"-Atualizando o alarme anterior para {DateTime}"       | A ação a ser tomada quando a condição da regra for verdadeira |
   | Estado após a execução | Comando completo                                                 | Estado do usuário após a ativação                   |

1. Mova a regra que você acabou de criar para a parte superior das regras avançadas (role sobre a regra no painel e clique na seta para cima).
   > [!div class="mx-imgBorder"]
   > ![adicionar uma validação de intervalo](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> Em um aplicativo real, na seção ações dessa regra, você também enviará uma atividade para o cliente ou chamará um ponto de extremidade HTTP para atualizar o alarme em seu sistema.

## <a name="try-it-out"></a>Experimentar

Selecione o painel de teste e tente algumas interações.

- Entrada: Definir alarme para amanhã às 12h
- Saída: "tem certeza de que deseja definir um alarme para 12/07/2019 12:00:00?"
- Entrada: Sim
- Saída: "Ok, alarme definido para 12/07/2019 12:00:00"
- Entrada: não, amanhã às às 13:00
- Saída: "Atualizando o alarme anterior para 12/07/2019 13:00:00"
