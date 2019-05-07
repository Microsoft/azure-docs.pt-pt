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
ms.openlocfilehash: b480f9720cabddbba9e9c35c12ca1f8dda7ab5ab
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190013"
---
| Resource | Destino | Limite de disco rígido |
|----------|--------------|------------|
| Serviços de sincronização de armazenamento por região | 20 serviços de sincronização de armazenamento | Sim |
| Grupos de sincronização por serviço de sincronização de armazenamento | 100 grupos de sincronização | Sim |
| Servidores registados por serviço de sincronização de armazenamento | servidores de 99 | Sim |
| Pontos finais da cloud por grupo de sincronização | ponto final da 1 cloud | Sim |
| Pontos finais do servidor por grupo de sincronização | 50 pontos finais do servidor | Não |
| Pontos finais do servidor por servidor | 30 pontos finais do servidor | Sim |
| Tamanho do ponto final | 4 TiB | Não |
| Objetos de sistema (diretórios e arquivos) por grupo de sincronização de ficheiros | objetos de milhões de 25 | Não |
| Número máximo de ficheiro objetos de sistema (diretórios e arquivos) num diretório | 1 milhão de objetos | Sim |
| Máximo de objeto (diretórios e arquivos) descritor tamanho de segurança | 64 KiB | Sim |
| Tamanho dos ficheiros | 100 GiB | Não |
| Tamanho mínimo do ficheiro para um ficheiro para ser colocado em camadas | 64 KiB | Sim |
| Sessões de sincronização simultâneas | Agente de v4 e posterior: O limite varia com base nos recursos de sistema disponíveis. <BR> Agente de v3: Duas sessões de sincronização do Active Directory por processador ou um máximo de oito sessões de sincronização do Active Directory por servidor. | Sim
