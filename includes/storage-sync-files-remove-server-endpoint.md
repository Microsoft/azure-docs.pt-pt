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
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391754"
---
Não: a remoção de um ponto de extremidade do servidor não é como reinicializar um servidor! Remover e recriar o ponto de extremidade do servidor quase nunca é uma solução apropriada para corrigir problemas com sincronização, camadas de nuvem ou outros aspectos de Sincronização de Arquivos do Azure. A remoção de um ponto de extremidade do servidor é uma operação destrutiva. Isso pode resultar em perda de dados, caso os arquivos em camadas existam fora do namespace do ponto de extremidade do servidor. Veja [por que arquivos em camadas existem fora do namespace do ponto de extremidade do servidor](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) para obter mais informações. Ou pode resultar em arquivos inacessíveis para arquivos em camadas que existem dentro do namespace do ponto de extremidade do servidor. Esses problemas não serão resolvidos quando o ponto de extremidade do servidor for recriado. Os arquivos em camadas podem existir no namespace do ponto de extremidade do servidor, mesmo que você nunca tenha o nível de nuvem habilitado. É por isso que é recomendável não remover o ponto de extremidade do servidor, a menos que você queira parar de usar Sincronização de Arquivos do Azure com essa pasta específica ou tenha sido instruído explicitamente a fazer isso por um engenheiro da Microsoft. Para obter mais informações sobre como remover pontos de extremidade do servidor, consulte [remover um ponto final do servidor](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
