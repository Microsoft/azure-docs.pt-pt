---
title: Configurar um servidor de processos no Azure para a VM de VMware e a reativação pós-falha do servidor físico com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como configurar um servidor de processos no Azure, a reativação pós-falha de VMs do Azure para VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 037f0ff64b114ce9341702564147825099695aa0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58105646"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor de processos no Azure para reativação pós-falha

Depois de efetuar a ativação pós-falha de VMs de VMware ou servidores físicos para o Azure com [recuperação de Site](site-recovery-overview.md), pode efetuar a ativação-los para o site no local quando for novamente em funcionamento. Para efetuar a reativação pós-falha, terá de configurar um servidor de processo temporário no Azure, para processar a replicação do Azure para o local. É possível eliminar esta VM após a conclusão da reativação pós-falha.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o [nova proteção](vmware-azure-reprotect.md) e [reativação pós-falha](vmware-azure-failback.md) processo.

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processos no Azure

1. No cofre > **infraestrutura do Site Recovery**> **gerir** > **Configuration Servers**, selecione o servidor de configuração.
2. Na página do servidor, clique em **+ servidor de processos**
3. Na **Adicionar servidor de processos** página e selecionar a opção para implementar o servidor de processos no Azure.
4. Especifique as definições do Azure, incluindo a subscrição utilizada para ativação pós-falha, um grupo de recursos, a região do Azure utilizada para a ativação pós-falha e a rede virtual na qual estão localizadas as VMs do Azure. Se tiver utilizado a várias redes do Azure, precisa de um servidor de processo em cada uma.

   ![Adicionar item de galeria do servidor de processo](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Na **nome do servidor**, **nome de utilizador**, e **palavra-passe**, especifique um nome para o servidor de processos e as credenciais que serão atribuídas as permissões de administrador no servidor.
5. Especifique uma conta de armazenamento a ser utilizado para os discos VM do servidor, a sub-rede na qual a VM do servidor de processo estarão localizado e o endereço IP do servidor que será atribuído quando a VM é iniciado.
6. Clique em **OK** botão para iniciar a implementação da VM do servidor de processo.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registar o servidor de processos (em execução no Azure) para um servidor de configuração (em execução no local)

Depois da VM do servidor de processo está em execução, tem de registá-lo com o servidor de configuração no local, da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


