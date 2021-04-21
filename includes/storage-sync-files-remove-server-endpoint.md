---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2f50a1d046b7b7d3fd7507ef87f8f29096a4faa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774573"
---
Não: remover um ponto final do servidor não é como reiniciar um servidor! Remover e recriar o ponto final do servidor é quase nunca uma solução adequada para corrigir problemas com sincronização, nivelamento de nuvem ou outros aspetos do Azure File Sync. Remover um ponto final do servidor é uma operação destrutiva. Pode resultar em perda de dados no caso de existirem ficheiros hierárquicos fora do espaço de nome do ponto final do servidor. Veja [por que existem ficheiros hierárquicos fora do espaço de nome do ponto final do servidor](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) para obter mais informações. Ou pode resultar em ficheiros inacessíveis para ficheiros hierárquicos que existam dentro do espaço de nome do ponto final do servidor. Estes problemas não serão resolvidos quando o ponto final do servidor for recriado. Podem existir ficheiros hierárquicos dentro do espaço de nome do ponto final do servidor, mesmo que nunca tenha sido ativado o tiering da nuvem. É por isso que recomendamos que não remova o ponto final do servidor a menos que queira parar de usar O Azure File Sync com esta pasta em particular ou tenha sido explicitamente instruído a fazê-lo por um engenheiro da Microsoft. Para obter mais informações sobre os pontos finais do servidor, consulte [Remover um ponto final do servidor](../articles/storage/file-sync/file-sync-server-endpoint.md#remove-a-server-endpoint).    
