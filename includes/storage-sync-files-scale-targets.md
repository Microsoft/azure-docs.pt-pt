---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74796013"
---
| Recurso | Visar | Limite rígido |
|----------|--------------|------------|
| Serviços de sincronização de armazenamento por região | 20 serviços de sincronização de armazenamento | Sim |
| Grupos de sincronização por serviço de sincronização de armazenamento | 100 grupos de sincronização | Sim |
| Servidores registrados por serviço de sincronização de armazenamento | servidores de 99 | Sim |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | Sim |
| Pontos de extremidade do servidor por grupo de sincronização | 50 pontos de extremidade do servidor | Não |
| Pontos de extremidade do servidor por servidor | 30 pontos de extremidade do servidor | Sim |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 100 milhões objetos | Não |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 5 milhões objetos | Sim |
| Tamanho do descritor de segurança máximo de objetos (diretórios e arquivos) | 64 KiB | Sim |
| Tamanho dos ficheiros | 100 GiB | Não |
| Tamanho mínimo de arquivo para que um arquivo seja em camadas | V9: com base no tamanho do cluster do sistema de arquivos (tamanho de cluster de sistema de arquivos duplo). Por exemplo, se o tamanho do cluster do sistema de arquivos for 4 KB, o tamanho mínimo do arquivo será 8 KB.<br> V8 e mais antigo: 64 KiB  | Sim |

> [!Note]  
> Um ponto de extremidade Sincronização de Arquivos do Azure pode ser escalado verticalmente para o tamanho de um compartilhamento de arquivos do Azure. Se o limite de tamanho do compartilhamento de arquivos do Azure for atingido, a sincronização não será capaz de operar.
