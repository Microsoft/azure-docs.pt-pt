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
ms.openlocfilehash: 019e8db54c1cfd9f436f880b8ddbb9bfa31c50bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028516"
---
Uma tarefa em execução no Azure Batch pode produzir dados de saída quando é executado. Os dados de saída de tarefas precisam frequentemente de ser armazenados para recuperação por outras tarefas no trabalho, a aplicação do cliente que executou o trabalho, ou ambas. As tarefas escrevem dados de saída para o sistema de ficheiros de um nó de computação Batch, mas todos os dados do nó perdem-se quando são reesimagem ou quando o nó sai do pool. As tarefas podem também ter um período de retenção de ficheiros, após o qual os ficheiros criados pela tarefa são eliminados. Por estas razões, é importante persistir a saída de tarefas que necessitará mais tarde para uma loja de dados como [o Azure Storage](../articles/storage/index.yml).

Para opções de conta de armazenamento em Batch, consulte [contas batch e contas de armazenamento Azure](../articles/batch/accounts.md#azure-storage-accounts).