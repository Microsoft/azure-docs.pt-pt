---
title: Configurar um servidor de processo VMware/falha física na recuperação do site Azure
description: Este artigo descreve como configurar um servidor de processo em Azure, para falhar VMs de Azure para VMware.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74083957"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Configurar um servidor de processos no Azure para reativação pós-falha

Depois de ter falhado em VMware VMs ou servidores físicos para a Azure utilizando a Recuperação do [Site,](site-recovery-overview.md)pode falhar com eles de volta ao local quando estiver a funcionar novamente. Para falhar, precisa de configurar um servidor de processo temporário em Azure, para lidar com a replicação do Azure para o local. Pode eliminar este VM depois de ter falhado.

## <a name="before-you-start"></a>Antes de começar

Saiba mais sobre o processo [de reproteção](vmware-azure-reprotect.md) e [falha.](vmware-azure-failback.md)

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Implementar um servidor de processo em Azure

1. No cofre > servidores de configuração **de gestão de infraestruturas** >  **de**  >  **recuperação do**local, selecione o servidor de configuração.
2. Na página do servidor, clique **+ Processar servidor**
3. Na página **do servidor de processo de adicionar** e selecione para implementar o servidor de processo no Azure.
4. Especificar as definições de Azure, incluindo a subscrição utilizada para failover, um grupo de recursos, a região Azure utilizada para o failover, e a rede virtual em que estão localizados os VMs Azure. Se utilizar várias redes Azure, precisa de um servidor de processo em cada uma delas.

   ![Adicionar item de galeria de servidor de processo](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Em **nome do Servidor**, nome de **utilizador**e **palavra-passe**, especifique um nome para o servidor de processo e credenciais que serão atribuídas permissões de Administração no servidor.
5. Especifique uma conta de armazenamento a ser utilizada para os discos VM do servidor, a sub-rede na qual o servidor de processo VM será localizado e o endereço IP do servidor que será atribuído quando o VM começar.
6. Clique no botão **OK** para começar a implantar o VM do servidor de processo. O servidor de processo será implantado no Standard_A8_v2 SKU. Certifique-se de que este VM SKU está disponível para a sua subscrição.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>Registar o servidor de processo (em execução em Azure) para um Servidor de Configuração (em execução no local)

Depois de o servidor de processo VM estar em funcionamento, tem de registá-lo com o servidor de configuração no local, da seguinte forma:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


