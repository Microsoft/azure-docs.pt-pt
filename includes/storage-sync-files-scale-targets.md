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
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74796013"
---
| Recurso | Destino | Limite difícil |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 20 Serviços de Sincronização de Armazenamento | Sim |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 100 grupos de sincronização | Sim |
| Servidores registados por Serviço de Sincronização de Armazenamento | 99 servidores | Sim |
| Pontos finais de nuvem por grupo de sincronização | 1 ponto final de nuvem | Sim |
| Pontos finais do servidor por grupo de sincronização | 50 pontos finais do servidor | Não |
| Pontos finais do servidor por servidor | 30 pontos finais do servidor | Sim |
| Objetos do sistema de ficheiros (diretórios e ficheiros) por grupo de sincronização | 100 milhões de objetos | Não |
| Número máximo de objetos do sistema de ficheiros (diretórios e ficheiros) num diretório | 5 milhões de objetos | Sim |
| Tamanho máximo do descritor de segurança do objeto (diretórios e ficheiros) | 64 KiB | Sim |
| Tamanho dos ficheiros | 100 GiB | Não |
| Tamanho mínimo do ficheiro para um ficheiro a ser niveado | V9: Baseado no tamanho do cluster do sistema de ficheiros (tamanho duplo do cluster do sistema de ficheiros). Por exemplo, se o tamanho do cluster do sistema de ficheiros for de 4kb, o tamanho mínimo do ficheiro será de 8kb.<br> V8 ou mais: 64 KiB  | Sim |

> [!Note]  
> Um ponto final de Sincronização de Ficheiros Azure pode escalar até ao tamanho de uma partilha de ficheiros Azure. Se o limite de tamanho da partilha de ficheiros Azure for atingido, a sincronização não será capaz de funcionar.
