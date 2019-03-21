---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 926fb3e9a2c09d30da549330842d8b7e185674ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57908460"
---
Em **Adicionar vCenter**, especifique um nome amigável para o anfitrião vSphere ou o servidor vCenter e, em seguida, especifique o endereço IP ou FQDN do servidor. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar os pedidos numa porta diferente. Selecione a conta que está a ligar vCenter do VMware ou ao servidor vSphere ESXi. Clique em **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Se estiver a adicionar o servidor VMware vCenter ou anfitrião do VMware vSphere com uma conta que não tem privilégios de administrador no servidor vCenter ou anfitrião, certifique-se de que a conta tem estes privilégios ativados: Centro de dados, arquivo de dados, pasta, anfitrião, rede, recurso, Máquina Virtual e comutador distribuído do vSphere. Além disso, o servidor VMware vCenter necessita do privilégio de vista de armazenamento ativado.
