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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75392425"
---
A tabela seguinte descreve cada uma das principais quotas, limites, incumprimentos e acelerações no Programador Azure.

| Recurso | Limitar a descrição |
| -------- | ----------------- |
| **Tamanho do trabalho** | O tamanho máximo do trabalho é de 16.000. Se uma operação PUT ou PATCH resultar num tamanho de trabalho maior do que este limite, é devolvido um código de estado de 400 Bad Request. | 
| **Coleções de emprego** | O número máximo de coleções de emprego por subscrição do Azure é de 200.000. | 
| **Empregos por coleção** | Por defeito, o número máximo de postos de trabalho é de cinco postos de trabalho numa coleção de empregos gratuitos e 50 empregos numa coleção normal de emprego. Pode alterar o número máximo de empregos numa coleção de emprego. Todos os empregos numa coleção de emprego estão limitados ao valor estabelecido na coleção de empregos. Se tentar criar mais postos de trabalho do que a quota máxima de emprego, o pedido falha com um código de estatuto de conflito 409. | 
| **Hora de começar a hora** | O tempo máximo de "hora de início" é de 18 meses. |
| **Extensão de recorrência** | O limite máximo de recorrência é de 18 meses. | 
| **Frequência** | Por predefinição, a quota de frequência máxima é de uma hora numa coleção de emprego gratuito e um minuto numa coleção de emprego padrão. <p>Pode fazer a frequência máxima numa coleção de emprego inferior à máxima. Todos os postos de trabalho na coleção de emprego supõem-se ao valor estabelecido na cobrança de postos de trabalho. Se tentar criar um emprego com uma frequência superior à frequência máxima na recolha de emprego, o pedido falha com um código de estado de conflito 409. | 
| **Tamanho do corpo** | O tamanho máximo do corpo para um pedido é de 8.192 chars. |
| **Solicitar tamanho url** | O tamanho máximo para um URL de pedido é de 2.048 chars. |
| **Contagem de cabeçalhos** | A contagem máxima de cabeçalho soa a 50 cabeçalhos. | 
| **Tamanho agregado do cabeçalho** | O tamanho máximo agregado do cabeçalho é de 4.096 chars. |
| **Intervalo** | O tempo de trabalho é estático, isto é, não configurável. e é de 60 segundos para as ações http. Para operações de execução mais longas, siga os protocolos assíncronos HTTP. Por exemplo, devolva um 202 imediatamente, mas continue a trabalhar em segundo plano. | 
| **Histórico de tarefas** | O corpo de resposta máxima armazenado na história do trabalho é de 2.048 bytes. |
| **Retenção de histórico de emprego** | O histórico de empregos é mantido por até dois meses ou até às últimas 1.000 execuções. | 
| **Retenção de emprego concluída e defeituosa** | Os trabalhos concluídos e falhados são mantidos durante 60 dias. |
||| 

