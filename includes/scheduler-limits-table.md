---
title: incluir ficheiro
description: incluir ficheiro
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: b3788ede23a423bebf96661ea88b227bfb5fdf4c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553874"
---
A tabela seguinte descreve cada um dos principais quotas, limites, predefinições e limitações no agendador do Azure.

| Recurso | Descrição de limite |
| -------- | ----------------- |
| **Tamanho de tarefa** | O tamanho de tarefas máxima é de 16 000. Se um PUT ou uma operação de correção resulta num maior do que este limite de tamanho de tarefa, é devolvido um código de estado 400 de pedido inválido. | 
| **Coleções de tarefas** | O número máximo de coleções de tarefas por subscrição do Azure é a 200.000. | 
| **Tarefas por coleção** | Por predefinição, o número máximo de tarefas é cinco tarefas numa coleção de tarefas gratuitas e 50 tarefas numa coleção de tarefas padrão. <p>Pode alterar o número máximo de trabalhos numa coleção de tarefas. Todas as tarefas numa coleção de tarefas estão limitadas ao valor definido na coleção de tarefas. Se está tentando criar mais tarefas do que a quota de máximo de tarefas, o pedido falha com um código de estado 409 conflito. | 
| **Hora a hora de início** | O máximo de "hora a hora de início" é 18 meses. |
| **Intervalo de periodicidade** | O intervalo de periodicidade máximo é 18 meses. | 
| **Frequência** | Por predefinição, a quota de frequência máxima é uma hora de uma coleção de tarefas gratuitas e uma coleção de tarefas padrão de um minuto. <p>Pode fazer a frequência máxima numa coleção de tarefas inferior ao máximo. Todas as tarefas na coleção de tarefas estão limitadas ao valor definido na coleção de tarefas. Se tentar criar uma tarefa com uma freqüência mais alta do que a frequência máxima sobre a coleção de tarefas, o pedido falha com um código de estado 409 conflito. | 
| **Tamanho do corpo** | O tamanho máximo de corpo de um pedido é 8.192 carateres. |
| **Tamanho de URL do pedido** | O tamanho máximo para um URL do pedido é de 2048 carateres. |
| **Contagem de cabeçalho** | A contagem de cabeçalho máximo é de 50 cabeçalhos. | 
| **Tamanho do cabeçalho de agregação** | O tamanho do cabeçalho de agregação máximo é 4,096 carateres. |
| **Tempo limite** | O tempo limite do pedido é estático, ou seja, não configurável. e é de 60 segundos para ações de HTTP. Para mais operações em execução, siga os protocolos assíncronos do HTTP. Por exemplo, retorna a indicação 202 imediatamente, mas continuar a trabalhar em segundo plano. | 
| **Histórico de tarefas** | O corpo de resposta máximo armazenado no histórico de tarefas é 2.048 bytes. |
| **Retenção do histórico de tarefa** | Histórico de tarefas é mantido para até dois meses ou até as última 1000 execuções. | 
| **Retenção de tarefas concluídas e falhadas** | Tarefas concluídas e falhadas são mantidas durante 60 dias. |
||| 

