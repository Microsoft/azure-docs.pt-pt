---
title: Redimensionar o conjunto de capacidade ou um volume para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve como alterar o tamanho de um conjunto de capacidade ou um volume.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: b-juche
ms.openlocfilehash: f08eaee038ad18c600826dea6fe0fd85935de59a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452913"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um conjunto de capacidade ou um volume
Pode alterar o tamanho de um conjunto de capacidade ou um volume, se necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensione o conjunto de capacidade 

Pode alterar o tamanho do conjunto de capacidade em incrementos de 4 TiB ou diminui. Redimensionar o conjunto de capacidade altera a capacidade de ficheiros do Azure NetApp comprado.

1. No painel conta do NetApp de gerir, clique no agrupamento de capacidade que deseja redimensionar. 
2. Faça duplo clique no nome do conjunto de capacidade ou clique no ícone "..." no final da linha do conjunto de capacidade para exibir o menu de contexto. 
3. Utilize as opções de menu de contexto redimensionar ou eliminar o conjunto de capacidade.

## <a name="resize-a-volume"></a>Redimensionar um volume

Pode alterar o tamanho de um volume, se necessário. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.

1. No painel da conta de NetApp gerir, clique em **Volumes**. 
2. Faça duplo clique o nome do volume que deseja redimensionar ou clique no ícone "..." no final da linha do volume para exibir o menu de contexto.
3. Utilize as opções de menu de contexto para redimensionar ou eliminar o volume.

