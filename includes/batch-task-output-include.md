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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127533"
---
Uma tarefa em execução no Azure Batch pode produzir dados de saída quando é executada. Tarefa de dados de saída, muitas vezes, precisa de ser armazenados durante recuperação por outras tarefas no trabalho, a aplicação de cliente que executou a tarefa, ou ambos. Tarefas escrever dados de saída para o sistema de ficheiros de um nó de computação do Batch, mas todos os dados no nó são perdidas quando ele é recriar a imagem ou quando o nó deixa o conjunto. Tarefas também podem ter um período de retenção de ficheiros, após o qual são eliminados quaisquer ficheiros criados pela tarefa. Por esses motivos, é importante manter a saída de tarefa que precisará mais tarde para um arquivo de dados, tal como [armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Para obter opções de armazenamento no Batch, consulte a [descrição geral da funcionalidade do Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
