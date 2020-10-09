---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 08/23/2019
ms.author: rgarcia
ms.openlocfilehash: 9a02dfbf3f2976489c10ccfeb935915a65d8dc84
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "71180068"
---
Após a criação do seu observador, o `AnchorLocated` evento disparará para todas as âncoras solicitadas. Este evento dispara quando uma âncora está localizada, ou se a âncora não pode ser localizada. Se esta situação acontecer, a razão será indicada no estado. Depois de todas as âncoras para um observador serem processadas, encontradas ou não encontradas, então o `LocateAnchorsCompleted` evento disparará. Há um limite de 35 identificadores por observador. 
