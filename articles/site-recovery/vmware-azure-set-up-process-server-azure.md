---
title: Configurar um servidor de processo VMware/falha física na Recuperação do Site Azure
description: Este artigo descreve como configurar um servidor de processo sintetizar em Azure, para falhar os VMs Azure para vMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083957"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor de processos no Azure para reativação pós-falha

Depois de falhar em VMware VMs ou servidores físicos para o Azure utilizando a Recuperação do [Site,](site-recovery-overview.md)pode revê-los no local quando estiver a funcionar novamente. Para falhar, é necessário configurar um servidor de processo temporário em Azure, para lidar com a replicação do Azure para as instalações. Pode eliminar este VM depois de o failback estar completo.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o processo de [reproteção](vmware-azure-reprotect.md) e [recuo.](vmware-azure-failback.md)

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processo sintetizar em Azure

1. No cofre > A Infraestrutura> de **Recuperação**do Site**Gere** > os Servidores de**Configuração,** selecione o servidor de configuração.
2. Na página do servidor, clique + **servidor de processos**
3. Em Adicionar página de servidor de **processo,** e selecione para implementar o servidor de processo no Azure.
4. Especifique as definições do Azure, incluindo a subscrição utilizada para a failover, um grupo de recursos, a região Azure utilizada para a falha, e a rede virtual em que estão localizados os VMs Azure. Se utilizou várias redes Azure, precisa de um servidor de processo em cada uma.

   ![Adicionar item de galeria de servidor de processos](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. No **nome do Servidor**, nome de **utilizador**, e **palavra-passe**, especifique um nome para o servidor de processo, e credenciais que serão atribuídas permissões de Administração no servidor.
5. Especifique uma conta de armazenamento a utilizar para os discos VM do servidor, a sub-rede na qual o VM do servidor de processo sairá, e o endereço IP do servidor que será atribuído quando o VM começar.
6. Clique no botão **OK** para começar a implementar o VM do servidor de processos. O servidor de processo será implantado em Standard_A8_v2 SKU. Certifique-se de que este VM SKU está disponível para a sua subscrição.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registar o servidor de processo (em execução em Azure) para um Servidor de Configuração (em execução no local)

Depois de o VM do servidor de processos estar a funcionar, é necessário registá-lo com o servidor de configuração no local, da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


