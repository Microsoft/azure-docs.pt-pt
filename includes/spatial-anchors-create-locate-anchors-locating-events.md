---
ms.openlocfilehash: f478956c3a220ea4435c4f6e8b1096a1e9fdcadf
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014803"
---
Depois que o observador for criado, `AnchorLocated` o evento será acionado para cada âncora solicitada. Esse evento é acionado quando uma âncora está localizada ou se a âncora não puder ser localizada. Se essa situação ocorrer, o motivo será indicado no status. Depois que todas as âncoras de um inspetor forem processadas, encontradas ou não encontradas, `LocateAnchorsCompleted` o evento será acionado. Há um limite de identificadores de 35 por inspetor. 
