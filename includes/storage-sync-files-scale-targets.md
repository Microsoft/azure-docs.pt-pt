---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 47eae616ffc62e42448da23fb02152dae17aa548
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116800"
---
| Recurso | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 100 Serviços de Sincronização de Armazenamento | Sim |
| Grupos sincronizados por serviço de sincronização de armazenamento | 200 grupos de sincronização | Sim |
| Servidores registados por Serviço de Sincronização de Armazenamento | 99 servidores | Sim |
| Pontos finais da nuvem por grupo de sincronização | 1 ponto final de nuvem | Sim |
| Pontos finais do servidor por grupo de sincronização | 50 pontos finais do servidor | Não |
| Pontos finais do servidor por servidor | 30 pontos finais do servidor | Sim |
| Objetos do sistema de ficheiros (diretórios e ficheiros) por grupo de sincronização | 100 milhões de objetos | Não |
| Número máximo de objetos do sistema de ficheiros (diretórios e ficheiros) num diretório | 5 milhões de objetos | Sim |
| Tamanho máximo do descritor de segurança de objetos (diretórios e ficheiros) | 64 KiB | Sim |
| Tamanho dos ficheiros | 100 GiB | Não |
| Tamanho mínimo do ficheiro para um ficheiro a ser nivelado | V9 e mais recente: Baseado no tamanho do cluster do sistema de ficheiros (tamanho do cluster do sistema de ficheiros duplos). Por exemplo, se o tamanho do cluster do sistema de ficheiros for de 4kb, o tamanho mínimo do ficheiro será de 8kb.<br> V8 e mais velho: 64 KiB  | Sim |

> [!Note]  
> Um ponto final Azure File Sync pode escalar até ao tamanho de uma partilha de ficheiros Azure. Se o limite de tamanho da ação do ficheiro Azure for atingido, a sincronização não poderá funcionar.
