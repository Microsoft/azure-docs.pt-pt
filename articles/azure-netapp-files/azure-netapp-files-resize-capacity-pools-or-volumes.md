---
title: Redimensionar a capacidade ou um volume para ficheiros Azure NetApp [ Microsoft Docs
description: Descreve como alterar o tamanho de uma piscina de capacidade ou um volume.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65794619"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um conjunto de capacidade ou um volume
Pode alterar o tamanho de uma piscina de capacidade ou um volume, se necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensionar a piscina de capacidade 

Pode alterar o tamanho da piscina de capacidade em incrementos ou decrementos de 1-TiB. No entanto, o tamanho da piscina de capacidade não pode ser inferior a 4 TiB. A redimensionamento do pool de capacidade altera a capacidade adquirida dos Ficheiros Azure NetApp.

1. A partir da lâmina da Conta Manage NetApp, clique na piscina de capacidade que pretende redimensionar. 
2. Clique no nome da piscina de capacidade ou clique no "..." ícone no final da linha da piscina de capacidade para exibir o menu de contexto. 
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o conjunto de capacidades.

## <a name="resize-a-volume"></a>Redimensionar um volume

Pode alterar o tamanho de um volume conforme necessário. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.

1. A partir da lâmina da Conta Manage NetApp, clique em **Volumes**. 
2. Clique no nome do volume que pretende redimensionar ou clicar no "..." ícone no final da linha do volume para exibir o menu de contexto.
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o volume.

