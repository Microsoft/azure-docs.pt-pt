---
title: Configurar um servidor de processo VMware/failback físico no Azure Site Recovery
description: Este artigo descreve como configurar um servidor de processo no Azure para fazer failback de VMs do Azure para VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083957"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor de processo no Azure para failback

Depois de realizar o failover de VMs VMware ou servidores físicos para o Azure usando [site Recovery](site-recovery-overview.md), você poderá fazer failback deles para o site local quando ele estiver em funcionamento novamente. Para fazer failback, você precisa configurar um servidor de processo temporário no Azure para lidar com a replicação do Azure para o local. Você pode excluir essa VM após a conclusão do failback.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o processo de Nova [proteção](vmware-azure-reprotect.md) e [failback](vmware-azure-failback.md) .

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor de processo no Azure

1. No cofre > **site Recovery infraestrutura**> **gerenciar** > **servidores de configuração**, selecione o servidor de configuração.
2. Na página servidor, clique em **+ servidor de processo**
3. Na página **Adicionar servidor de processo** e selecione para implantar o servidor de processo no Azure.
4. Especifique as configurações do Azure, incluindo a assinatura usada para failover, um grupo de recursos, a região do Azure usada para failover e a rede virtual na qual as VMs do Azure estão localizadas. Se você usou várias redes do Azure, precisará de um servidor de processo em cada uma.

   ![Adicionar item da Galeria de servidores de processo](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Em **nome do servidor**, **nome de usuário**e **senha**, especifique um nome para o servidor de processo e as credenciais às quais serão atribuídas permissões de administrador no servidor.
5. Especifique uma conta de armazenamento a ser usada para os discos de VM do servidor, a sub-rede na qual a VM do servidor de processo será localizada e o endereço IP do servidor que será atribuído quando a VM for iniciada.
6. Clique no botão **OK** para iniciar a implantação da VM do servidor de processo. O servidor de processo será implantado em Standard_A8_v2 SKU. Verifique se esse SKU de VM está disponível para sua assinatura.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registrando o servidor de processo (em execução no Azure) em um servidor de configuração (em execução local)

Depois que a VM do servidor de processo estiver em execução, você precisa registrá-la no servidor de configuração local, da seguinte maneira:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


