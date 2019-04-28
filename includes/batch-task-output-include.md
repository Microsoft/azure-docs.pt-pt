---
title: incluir ficheiro
description: incluir ficheiro
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7cee3f534e4ab4973a87a9c373a5b83aa2ba9ce2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549950"
---
Uma tarefa em execução no Azure Batch pode produzir dados de saída quando é executada. Tarefa de dados de saída, muitas vezes, precisa de ser armazenados durante recuperação por outras tarefas no trabalho, a aplicação de cliente que executou a tarefa, ou ambos. Tarefas escrever dados de saída para o sistema de ficheiros de um nó de computação do Batch, mas todos os dados no nó são perdidas quando ele é recriar a imagem ou quando o nó deixa o conjunto. Tarefas também podem ter um período de retenção de ficheiros, após o qual são eliminados quaisquer ficheiros criados pela tarefa. Por esses motivos, é importante manter a saída de tarefa que precisará mais tarde para um arquivo de dados, tal como [armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Para obter opções de armazenamento no Batch, consulte a [descrição geral da funcionalidade do Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
