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
ms.openlocfilehash: be3ce42ccdb9bedd02b8dead2426ac629fa12ef2
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72391754"
---
Não: remover um ponto final do servidor não é como reiniciar um servidor! Remover e recriar o ponto final do servidor é quase nunca uma solução adequada para corrigir problemas com sincronização, tiering em nuvem ou outros aspetos do Azure File Sync. Remover um ponto final do servidor é uma operação destrutiva. Pode resultar na perda de dados no caso de existirem ficheiros hierárquicos fora do espaço de nome final do servidor. Veja [por que existem ficheiros hierárquicos fora do espaço de nome](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) final do servidor para obter mais informações. Ou pode resultar em ficheiros inacessíveis para ficheiros hierárquicos que existem dentro do espaço de nome final do servidor. Estes problemas não serão resolvidos quando o ponto final do servidor for recriado. Os ficheiros hierárquicos podem existir dentro do espaço de nome final do servidor, mesmo que nunca tenha ativado o tiering em nuvem. É por isso que recomenda que não remova o ponto final do servidor a menos que queira parar de usar o Azure File Sync com esta pasta em particular ou tenha sido explicitamente instruído a fazê-lo por um engenheiro da Microsoft. Para obter mais informações sobre os pontos finais do servidor, consulte [Remover um ponto final](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)do servidor .    
