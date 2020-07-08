---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204505"
---
- Atualmente, os instantâneos incrementais não podem ser movidos entre subscrições.
- Atualmente só pode gerar SAS URIs de até cinco instantâneos de uma determinada família instantânea em qualquer momento.
- Não é possível criar uma imagem incremental para um disco específico fora da subscrição do disco.
- Podem ser criados até sete instantâneos incrementais por disco a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.
- Não é possível obter as alterações entre as imagens tiradas antes e depois da alteração do tamanho do disco-mãe através do limite 4 TB. Tem de descarregar novamente a cópia completa do instantâneo criado após o redimensionamento. Posteriormente, pode obter as alterações entre os instantâneos criados após o redimensionar através do limite de 4 TB. 
