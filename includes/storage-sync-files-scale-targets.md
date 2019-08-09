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
ms.openlocfilehash: 46bf9851ccf8877885d9f98a023aa95ebdc533bc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857465"
---
| Resource | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de sincronização de armazenamento por região | 20 serviços de sincronização de armazenamento | Sim |
| Grupos de sincronização por serviço de sincronização de armazenamento | 100 grupos de sincronização | Sim |
| Servidores registrados por serviço de sincronização de armazenamento | servidores de 99 | Sim |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | Sim |
| Pontos de extremidade do servidor por grupo de sincronização | 50 pontos de extremidade do servidor | Não |
| Pontos de extremidade do servidor por servidor | 30 pontos de extremidade do servidor | Sim |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 25 milhões objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 5 milhões objetos | Sim |
| Tamanho do descritor de segurança máximo de objetos (diretórios e arquivos) | 64 KiB | Sim |
| Tamanho do ficheiro | 100 GiB | Não |
| Tamanho mínimo de arquivo para que um arquivo seja em camadas | 64 KiB | Sim |
| Sessões de sincronização simultâneas | Agente v4 e posterior: O limite varia de acordo com os recursos do sistema disponíveis. <BR> Agente V3: Duas sessões de sincronização ativas por processador ou um máximo de oito sessões de sincronização ativas por servidor. | Sim

> [!Note]  
> Um ponto de extremidade Sincronização de Arquivos do Azure pode ser escalado verticalmente para o tamanho de um compartilhamento de arquivos do Azure. Se o limite de tamanho do compartilhamento de arquivos do Azure for atingido, a sincronização não será capaz de operar.
