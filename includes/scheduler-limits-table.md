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
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75392425"
---
A tabela seguinte descreve cada uma das principais quotas, limites, incumprimentos e aceleradores em Azure Scheduler.

| Recurso | Descrição do limite |
| -------- | ----------------- |
| **Tamanho do trabalho** | O tamanho máximo do trabalho é de 16.000. Se uma operação PUT ou PATCH resultar num tamanho de trabalho superior a este limite, é devolvido um código de estado de 400 Bad Request. | 
| **Coleções de emprego** | O número máximo de coleções de emprego por subscrição da Azure é de 200.000. | 
| **Empregos por coleção** | Por defeito, o número máximo de postos de trabalho é de cinco postos de trabalho numa recolha gratuita de empregos e de 50 postos de trabalho numa recolha normal de emprego. Pode alterar o número máximo de empregos numa coleção de empregos. Todos os empregos numa coleção de empregos estão limitados ao valor fixado na recolha de emprego. Se tentar criar mais postos de trabalho do que a quota máxima de emprego, o pedido falha com um código de estado de conflito 409. | 
| **Hora de começar** | O máximo de "tempo para começar" é de 18 meses. |
| **Extensão de recorrência** | O período máximo de recorrência é de 18 meses. | 
| **Frequência** | Por defeito, a quota de frequência máxima é de uma hora numa recolha gratuita de empregos e de um minuto numa recolha padrão de emprego. <p>Pode fazer a frequência máxima numa recolha de emprego inferior ao máximo. Todos os trabalhos na recolha de emprego estão limitados ao valor fixado na recolha de emprego. Se tentar criar um emprego com uma frequência mais elevada do que a frequência máxima na recolha de emprego, o pedido falha com um código de estado de conflito 409. | 
| **Tamanho do corpo** | O tamanho máximo do corpo para um pedido é de 8.192 chars. |
| **Solicite o tamanho do URL** | O tamanho máximo para um URL de pedido é de 2.048 chars. |
| **Contagem de cabeçalhos** | A contagem máxima de cabeçalho é de 50 cabeçalhos. | 
| **Tamanho agregado do cabeçalho** | O tamanho máximo agregado do cabeçalho é de 4.096 chars. |
| **Intervalo de tempo** | O tempo limite de pedido é estático, isto é, não configurável. e são 60 segundos para as ações HTTP. Para operações de execução mais longas, siga os protocolos HTTP assíncronos. Por exemplo, devolva um 202 imediatamente, mas continue a trabalhar em segundo plano. | 
| **Histórico de tarefas** | O corpo de resposta máxima armazenado na história do trabalho é de 2.048 bytes. |
| **Retenção na história do trabalho** | O histórico de empregos mantém-se até dois meses ou até às últimas mil execuções. | 
| **Retenção de emprego concluída e defeituosa** | Os trabalhos concluídos e defeituosos são mantidos por 60 dias. |
||| 

