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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67184635"
---
A tabela a seguir descreve cada uma das principais cotas, limites, padrões e restrições no Agendador do Azure.

| Resource | Descrição do limite |
| -------- | ----------------- |
| **Tamanho do trabalho** | O tamanho máximo do trabalho é 16.000. Se uma operação PUT ou PATCH resultar em um tamanho de trabalho maior do que esse limite, um código de status de solicitação inválido 400 será retornado. | 
| **Coleções de trabalhos** | O número máximo de coleções de trabalhos por assinatura do Azure é 200.000. | 
| **Trabalhos por coleção** | Por padrão, o número máximo de trabalhos é cinco trabalhos em uma coleção de trabalhos gratuita e trabalhos de 50 em uma coleção de trabalhos padrão. <p>Você pode alterar o número máximo de trabalhos em uma coleção de trabalhos. Todos os trabalhos em uma coleção de trabalhos são limitados ao valor definido na coleção de trabalhos. Se você tentar criar mais trabalhos do que a cota máxima de trabalhos, a solicitação falhará com um código de status de conflito 409. | 
| **Hora de início** | O "tempo até a hora de início" máximo é de 18 meses. |
| **Intervalo de recorrência** | O intervalo de recorrência máximo é de 18 meses. | 
| **Frequência** | Por padrão, a cota de frequência máxima é de uma hora em uma coleção de trabalhos gratuita e um minuto em uma coleção de trabalhos padrão. <p>Você pode tornar a frequência máxima em uma coleção de trabalhos inferior ao máximo. Todos os trabalhos na coleção de trabalhos são limitados ao valor definido na coleção de trabalhos. Se você tentar criar um trabalho com uma frequência maior do que a frequência máxima na coleção de trabalhos, a solicitação falhará com um código de status de conflito 409. | 
| **Tamanho do corpo** | O tamanho máximo de corpo para uma solicitação é de 8.192 caracteres. |
| **Tamanho da URL de solicitação** | O tamanho máximo para uma URL de solicitação é de 2.048 caracteres. |
| **Contagem de cabeçalho** | A contagem máxima de cabeçalhos é de 50 cabeçalhos. | 
| **Tamanho do cabeçalho agregado** | O tamanho máximo do cabeçalho agregado é de 4.096 caracteres. |
| **Cedido** | O tempo limite da solicitação é estático, ou seja, não configurável. e é de 60 segundos para ações HTTP. Para operações de execução mais longa, siga os protocolos assíncronos HTTP. Por exemplo, retorne um 202 imediatamente, mas continue trabalhando em segundo plano. | 
| **Histórico de tarefas** | O corpo máximo de resposta armazenado no histórico de trabalhos é de 2.048 bytes. |
| **Retenção do histórico de trabalhos** | O histórico de trabalhos é mantido por até dois meses ou até as últimas 1.000 execuções. | 
| **Retenção de trabalho concluída e com falha** | Os trabalhos concluídos e com falha são mantidos por 60 dias. |
||| 

