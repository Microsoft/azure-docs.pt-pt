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
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "67184612"
---
Uma tarefa em execução no lote do Azure pode produzir dados de saída quando ela é executada. Os dados de saída de tarefa geralmente precisam ser armazenados para recuperação por outras tarefas no trabalho, o aplicativo cliente que executou o trabalho ou ambos. As tarefas gravam dados de saída no sistema de arquivos de um nó de computação do lote, mas todos os dados no nó são perdidos quando a imagem é recriada ou quando o nó sai do pool. As tarefas também podem ter um período de retenção de arquivo, após o qual os arquivos criados pela tarefa são excluídos. Por esses motivos, é importante manter a saída da tarefa que você precisará mais tarde para um armazenamento de dados, como o [armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Para obter opções de armazenamento no Batch, consulte a [descrição geral da funcionalidade do Batch](../articles/batch/batch-api-basics.md#azure-storage-account).
