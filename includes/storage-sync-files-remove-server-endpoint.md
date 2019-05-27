---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114544"
---
Não: remover um ponto final do servidor não é como reiniciar um servidor! A remover e recriar o ponto final de servidor quase nunca são uma solução apropriada para corrigir problemas com a sincronização, a camada de cloud ou a outros aspectos do Azure File Sync. Remover um ponto final do servidor é uma operação destrutiva e pode resultar na perda de dados no caso do que em camadas ficheiros existir fora do espaço de nomes de ponto final de servidor (veja [por que ficheiros em camadas existem fora do espaço de nomes de ponto final de servidor](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) para obter mais informações) ou em arquivos inacessíveis para os ficheiros em camadas existente no espaço de nomes de ponto final de servidor. Esses problemas não serão resolvido quando o ponto final do servidor é recriado. Ficheiros em camadas podem existir dentro do seu espaço de nomes de ponto final de servidor, mesmo que nunca teve camadas ativado da cloud. Como tal, recomendamos que não remover o ponto final do servidor, a menos que gostaria de deixar de utilizar o Azure File Sync com essa pasta específica ou tenha sido explicitamente instruído para fazer isso por um engenheiro da Microsoft. Para obter mais informações sobre pontos finais do servidor de remover, consulte [remover um ponto de final de servidor](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    