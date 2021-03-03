---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730652"
---
Ligue-se ao Seu Windows VM utilizando o Protocolo de Ambiente de Trabalho Remoto (RDP) através do IP que passou durante a criação de VM.

1. No seu cliente, abra o PDR. 
1. Vá para **iniciar** e, em seguida, escreva **mstsc**.
1. No painel de ligação ao ambiente de **trabalho remoto,** insira o endereço IP do VM e as credenciais de acesso utilizadas no ficheiro de parâmetros VM e, em seguida, selecione **Connect**.

   ![Screenshot do painel de ligação ao ambiente de trabalho remoto para ligar via RDP ao seu VM do Windows.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Talvez precise de aprovar a ligação a uma máquina não fidedquiríssia. 

Está agora inscrito no seu VM que funciona no aparelho. 
