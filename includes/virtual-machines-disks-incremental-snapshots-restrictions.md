---
title: incluir ficheiro
description: incluir ficheiro
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95992905"
---
- Atualmente, os instantâneos incrementais não podem ser movidos entre subscrições.
- Atualmente só pode gerar SAS URIs de até cinco instantâneos de uma determinada família instantânea em qualquer momento.
- Não é possível criar uma imagem incremental para um disco específico fora da subscrição do disco.
- Podem ser criados até sete instantâneos incrementais por disco a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.
- Não é possível obter as alterações entre as imagens tiradas antes e depois de alterar o tamanho do disco dos pais através do limite 4 TB. Por exemplo, tomou um instantâneo incremental-a quando o tamanho de um disco era de 2 TB. Agora aumentou o tamanho do disco para 6 TB e depois tirou outro snapshot incremental instantâneo-b. Não é possível obter as alterações entre snapshot-a e snapshot-b. Tem de descarregar novamente a cópia completa do snapshot-b criado após o redimensionamento. Posteriormente, pode obter as alterações entre snapshot-b e instantâneos criados após o snapshot-b. 
