---
title: Redimensionar a capacidade de piscina ou um volume para Azure NetApp Files Microsoft Docs
description: Aprenda a mudar o tamanho de uma piscina de capacidade ou de um volume. A redimensionamento da capacidade muda a capacidade adquirida dos Ficheiros Azure NetApp.
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
ms.topic: how-to
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7d1c54d05430600e9fffa2659dbaaf6db0b086b5
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2020
ms.locfileid: "87512878"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um conjunto de capacidade ou um volume
Pode alterar o tamanho de uma piscina de capacidade ou de um volume, se necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensionar a piscina de capacidade 

Pode alterar o tamanho da piscina em incrementos ou decréscimos de 1-TiB. No entanto, o tamanho da piscina de capacidade não pode ser inferior a 4 TiB. A redimensionamento da capacidade muda a capacidade adquirida dos Ficheiros Azure NetApp.

1. A partir da lâmina 'Gerir a Conta NetApp', clique na capacidade que pretende redimensionar. 
2. Clique com o botão direito no nome do pool de capacidade ou clique no "..." ícone no final da linha da piscina de capacidade para exibir o menu de contexto. 
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o pool de capacidade.

## <a name="resize-a-volume"></a>Redimensionar um volume

Pode alterar o tamanho de um volume se necessário. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.

1. A partir da lâmina de conta Manage NetApp, clique em **Volumes**. 
2. Clique com o botão direito no nome do volume que pretende redimensionar ou clicar no "..." ícone no final da linha do volume para exibir o menu de contexto.
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o volume.

