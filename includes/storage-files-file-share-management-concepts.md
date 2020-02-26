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
ms.openlocfilehash: a67ad4c5010cf93ff55123013a35c697ce5971f8
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77597889"
---
As ações de ficheiros Azure são implantadas em contas de armazenamento, que são *objetos*de alto nível que representam um conjunto partilhado de armazenamento. Este conjunto de armazenamento pode ser usado para implementar várias partilhas de ficheiros, bem como outros recursos de armazenamento, tais como recipientes de bolhas, filas ou mesas. Todos os recursos de armazenamento que são implantados numa conta de armazenamento partilham os limites aplicáveis a essa conta de armazenamento. Para ver os limites atuais de uma conta de armazenamento, consulte [a escalabilidade e os objetivos](../articles/storage/files/storage-files-scale-targets.md)de desempenho do Azure Files .

Existem dois tipos principais de contas de armazenamento que utilizará para implementações de Ficheiros Azure: 
- Contas de armazenamento da **versão 2 (GPv2)** de propósito geral : As contas de armazenamento GPv2 permitem-lhe implementar ações de ficheiroS Azure em hardware baseado em disco standard/rígido (baseado em HDD). Além de armazenar ações de ficheiros Azure, as contas de armazenamento Da GPv2 podem armazenar outros recursos de armazenamento, tais como contentores blob, filas ou mesas. 
- Contas de armazenamento de **ficheiros**: As contas de armazenamento de ficheiros permitem-lhe implementar ações de ficheiroS Azure em hardware baseado em discos premium/sólidos (baseados em SSD). As contas de FileStorage só podem ser utilizadas para armazenar ações de ficheiros Azure; nenhum outro recursos de armazenamento (recipientes de blob, filas, mesas, etc.) pode ser implantado numa conta fileStorage.

Existem vários outros tipos de conta de armazenamento que pode encontrar no portal Azure, PowerShell ou CLI. Dois tipos de conta de armazenamento, as contas de armazenamento BlockBlobStorage e BlobStorage, não podem conter ações de ficheiros Azure. Os outros dois tipos de conta de armazenamento que pode ver são a versão 1 (GPv1) e as contas de armazenamento clássicas, ambas com partilhas de ficheiros Azure. Embora o GPv1 e as contas de armazenamento clássicas possam conter ações de ficheiros Azure, a maioria das novas funcionalidades dos Ficheiros Azure estão disponíveis apenas nas contas de armazenamento GPv2 e FileStorage. Por isso, recomendamos apenas usar as contas de armazenamento GPv2 e FileStorage para novas implementações, e atualizar as contas de armazenamento GPv1 e clássicas se já existirem no seu ambiente.  