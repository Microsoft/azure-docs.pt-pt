---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 7ba4c90811bd8051ed9c307d9f9fa33e08e69dc7
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67184612"
---
Uma tarefa em execução no Lote Azure pode produzir dados de saída quando funciona. Os dados de saída de tarefas muitas vezes precisam de ser armazenados para recuperação por outras tarefas no trabalho, a aplicação do cliente que executou o trabalho, ou ambas. As tarefas escrevem dados de saída para o sistema de ficheiros de um nó computacional do Lote, mas todos os dados do nó são perdidos quando são reimageados ou quando o nó sai da piscina. As tarefas podem também ter um período de retenção de ficheiros, após o qual os ficheiros criados pela tarefa são eliminados. Por estas razões, é importante persistir na saída de tarefas que necessitará mais tarde para uma loja de dados como [o Armazenamento Azure.](https://docs.microsoft.com/azure/storage/)

Para obter opções de armazenamento no Batch, consulte a [descrição geral da funcionalidade do Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
