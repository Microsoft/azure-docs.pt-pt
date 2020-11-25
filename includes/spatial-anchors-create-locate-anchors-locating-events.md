---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 5360cbb7bdfbdc59e87366e73a891b5c2583672e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993061"
---
Após a criação do seu observador, o `AnchorLocated` evento disparará para todas as âncoras solicitadas. Este evento dispara quando uma âncora está localizada, ou se a âncora não pode ser localizada. Se esta situação acontecer, a razão será indicada no estado. Depois de todas as âncoras para um observador serem processadas, encontradas ou não encontradas, então o `LocateAnchorsCompleted` evento disparará. Há um limite de 35 identificadores por observador. 
