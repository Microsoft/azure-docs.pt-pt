---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "71180068"
---
Depois de o seu `AnchorLocated` observador ser criado, o evento disparará por cada âncora solicitada. Este evento dispara quando uma âncora está localizada, ou se a âncora não pode ser localizada. Se esta situação acontecer, a razão será indicada no estado. Depois de todas as âncoras para um observador serem `LocateAnchorsCompleted` processadas, encontradas ou não encontradas, então o evento disparará. Há um limite de 35 identificadores por observador. 
