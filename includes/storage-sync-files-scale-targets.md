---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184466"
---
| Resource | Destino | Limite de disco rígido |
|----------|--------------|------------|
| Serviços de sincronização de armazenamento por região | 20 serviços de sincronização de armazenamento | Sim |
| Grupos de sincronização por serviço de sincronização de armazenamento | 100 grupos de sincronização | Sim |
| Servidores registados por serviço de sincronização de armazenamento | servidores de 99 | Sim |
| Pontos finais da cloud por grupo de sincronização | ponto final da 1 cloud | Sim |
| Pontos finais do servidor por grupo de sincronização | 50 pontos finais do servidor | Não |
| Pontos finais do servidor por servidor | 30 pontos finais do servidor | Sim |
| Objetos de sistema (diretórios e arquivos) por grupo de sincronização de ficheiros | objetos de milhões de 25 | Não |
| Número máximo de ficheiro objetos de sistema (diretórios e arquivos) num diretório | 1 milhão de objetos | Sim |
| Máximo de objeto (diretórios e arquivos) descritor tamanho de segurança | 64 KiB | Sim |
| Tamanho dos ficheiros | 100 GiB | Não |
| Tamanho mínimo do ficheiro para um ficheiro para ser colocado em camadas | 64 KiB | Sim |
| Sessões de sincronização simultâneas | Agente de v4 e posterior: O limite varia com base nos recursos de sistema disponíveis. <BR> Agente de v3: Duas sessões de sincronização do Active Directory por processador ou um máximo de oito sessões de sincronização do Active Directory por servidor. | Sim

> [!Note]  
> Um ponto de final do Azure File Sync pode dimensionar até o tamanho de uma partilha de ficheiros do Azure. Se for atingido o limite de tamanho da partilha de ficheiros do Azure, sincronização não será capaz de operar.