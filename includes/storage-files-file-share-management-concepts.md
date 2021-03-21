---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/26/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8d26b6c92462abc13c42257d2c6e571156eacc0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011761"
---
As ações de ficheiros Azure são implantadas em contas de armazenamento, que são *objetos* de alto nível que representam um conjunto partilhado de armazenamento. Este conjunto de armazenamento pode ser usado para implantar várias partilhas de ficheiros, bem como outros recursos de armazenamento, tais como recipientes blob, filas ou mesas. Todos os recursos de armazenamento que são implantados numa conta de armazenamento partilham os limites que se aplicam a essa conta de armazenamento. Para ver os limites atuais para uma conta de armazenamento, consulte [os objetivos de escalabilidade e desempenho dos Ficheiros Azure](../articles/storage/files/storage-files-scale-targets.md).

Existem dois tipos principais de contas de armazenamento que utilizará para implementações de Ficheiros Azure: 
- **Contas de armazenamento versão geral 2 (GPv2): As** contas de armazenamento GPv2 permitem-lhe implantar ações de ficheiros Azure em hardware standard/hard disk (baseado em HDD). Além de armazenar ações de ficheiros Azure, as contas de armazenamento GPv2 podem armazenar outros recursos de armazenamento, tais como recipientes blob, filas ou mesas. 
- **Contas de armazenamento de filestorage**: As contas de armazenamento de filestorage permitem-lhe implantar ações de ficheiros Azure em hardware baseado em discos de estado premium/sólido (baseado em SSD). As contas de arquiteação de ficheiros só podem ser utilizadas para armazenar ações de ficheiros Azure; nenhum outros recursos de armazenamento (recipientes blob, filas, mesas, etc.) podem ser implantados numa conta de arquiteta. Apenas as contas de filestorage podem implementar as ações de ficheiro smb e NFS.

Existem vários outros tipos de conta de armazenamento que pode encontrar no portal Azure, PowerShell ou CLI. Dois tipos de conta de armazenamento, contas de armazenamento BlockBlobStorage e BlobStorage, não podem conter ações de ficheiroS Azure. Os outros dois tipos de conta de armazenamento que pode ver são a versão 1 (GPv1) e as contas clássicas de armazenamento, ambas podem conter ações de ficheiros Azure. Embora o GPv1 e as contas clássicas de armazenamento possam conter ações de ficheiros Azure, a maioria das novas funcionalidades dos Ficheiros Azure estão disponíveis apenas em contas de armazenamento de GPv2 e FileStorage. Por isso, recomendamos apenas usar contas de armazenamento de GPv2 e FileStorage para novas implementações, e atualizar as contas de armazenamento GPv1 e clássicas se já existirem no seu ambiente.  