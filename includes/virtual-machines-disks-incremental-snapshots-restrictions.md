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
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204505"
---
- Atualmente, os instantâneos incrementais não podem ser movidos entre subscrições.
- Atualmente, só pode gerar URIs SAS de até cinco instantâneos de uma determinada família instantânea a qualquer momento.
- Não é possível criar um instantâneo incremental para um disco específico fora da subscrição do disco.
- Até sete instantâneos incrementais por disco podem ser criados a cada cinco minutos.
- Um total de 200 instantâneos incrementais podem ser criados para um único disco.
- Não é possível obter as alterações entre as imagens tiradas antes e depois da alteração do tamanho do disco-mãe através do limite de 4 TB. Tem de voltar a descarregar a cópia completa do instantâneo criado após o redimensionado. Posteriormente, pode obter as alterações entre os instantâneos criados após o redimensionado através do limite de 4 TB. 
