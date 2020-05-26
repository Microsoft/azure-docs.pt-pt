---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: JnHs
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: eaa76153fe96b5fd41166b20770e0a969aa9260d
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797109"
---
Uma tarefa em execução no Lote Azure pode produzir dados de saída quando funciona. Os dados de saída de tarefas muitas vezes precisam de ser armazenados para recuperação por outras tarefas no trabalho, a aplicação do cliente que executou o trabalho, ou ambas. As tarefas escrevem dados de saída para o sistema de ficheiros de um nó computacional do Lote, mas todos os dados do nó são perdidos quando são reimageados ou quando o nó sai da piscina. As tarefas podem também ter um período de retenção de ficheiros, após o qual os ficheiros criados pela tarefa são eliminados. Por estas razões, é importante persistir na saída de tarefas que necessitará mais tarde para uma loja de dados como [o Armazenamento Azure.](https://docs.microsoft.com/azure/storage/)

Para opções de conta de armazenamento em Lote, consulte contas de Lote e contas de [Armazenamento Azure](../articles/batch/accounts.md#azure-storage-accounts).
