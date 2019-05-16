---
title: Sobre a configuração, processo e servidores de destino principal do Azure Site Recovery | Documentos da Microsoft
description: Este artigo fornece uma descrição geral da configuração, processo e os servidores de destino principal a utilizar quando configurar a recuperação após desastre de VMs de VMware no local para o Azure com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 6f501e251f0b006bbbb4f64090cac5c3d61b7bf2
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65523552"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sobre os componentes do Site Recovery (configuração, processo, destino principal)

Este artigo descreve a configuração, processo e os servidores de destino principal utilizados ao replicar VMs de VMware e servidores físicos para o Azure com o [recuperação de Site](site-recovery-overview.md) serviço.

## <a name="configuration-server"></a>Servidor de configuração

Para recuperação após desastre de VMs de VMware no local e servidores físicos, terá de uma recuperação de Site de servidor de configuração implementado no local.

**Definição** | **Detalhes** | **Links**
--- | --- | ---
**Componentes**  | A máquina do servidor de configuração é executado todos os componentes do Site Recovery no local, que incluem o servidor de configuração, o servidor de processos e o servidor de destino mestre.<br/><br/> Quando configurar o servidor de configuração, todos os componentes são instalados automaticamente. | [Leitura](vmware-azure-common-questions.md#configuration-server) FAQ do servidor de configuração.
**Função** | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados. | Saiba mais sobre a arquitetura [VMware](vmware-azure-architecture.md) e [servidor físico](physical-azure-architecture.md) recuperação após desastre para o Azure.
**Requisitos de VMware** | Para recuperação após desastre de VMs de VMware no local, tem de instalar e executar o servidor de configuração como no local, VM de VMware de elevada disponibilidade. | [Saiba mais sobre](vmware-azure-deploy-configuration-server.md#prerequisites) os pré-requisitos.
**Implementação de VMware** | Recomendamos que implemente o servidor de configuração com um modelo de OVA transferido. Este método fornece uma simplesmente configurar um servidor de configuração que está em conformidade com todos os requisitos e pré-requisitos.<br/><br/> Se por algum motivo não foi possível implementar uma VM do VMware com um modelo de OVA, pode configurar as máquinas de servidor de configuração manualmente, conforme descrito abaixo para recuperação após desastre de máquina física. | [Implementar](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) com um modelo de OVA.
**Requisitos do servidor físico** | Recuperação de desastres em servidores físicos no local, implementar o servidor de configuração manualmente. | [Saiba mais sobre](physical-azure-set-up-source.md#prerequisites) os pré-requisitos.
**Implementação de servidor físico** | Se ainda não pode ser instalado como uma VM de VMware, pode instalá-lo num servidor físico. | [Implementar](physical-azure-set-up-source.md#set-up-the-source-environment) o servidor de configuração manualmente.


## <a name="process-server"></a>Servidor de processos

**Definição** | **Detalhes** | **Links**
--- | --- | ---
**Implementação**  | Para recuperação após desastre e a replicação de VMs de VMware no local e servidores físicos, terá de um servidor de processos no local. Por predefinição, o servidor de processos está instalado no servidor de configuração quando a implementá-la. | [Saiba mais](vmware-azure-architecture.md?#architectural-components).
**Função (no local** | -Recebe dados de replicação de máquinas ativadas para replicação.<br/> -Otimiza os dados de replicação com colocação em cache, compressão e encriptação e envia-os para o armazenamento do Azure.<br/> -Executa uma instalação push do serviço de mobilidade da recuperação de sites em VMs de VMware no local e servidores físicos que pretende replicar.<br/> -Executa a deteção automática de máquinas no local. | [Saiba mais](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Função (reativação pós-falha do Azure)** | Após a ativação pós-falha do seu site no local, configura um servidor de processos no Azure, como uma VM do Azure, para lidar com a reativação pós-falha para a sua localização no local.<br/><br/> O servidor de processos no Azure é temporário. A VM do Azure podem ser eliminada após a reativação pós-falha é realizada. | [Saiba mais](vmware-azure-set-up-process-server-azure.md).
**Dimensionamento** | Para implementações maiores, no local podem configurar servidores de processos adicionais, de escalamento horizontal. Servidores adicionais horizontalmente de capacidade, ao lidar com grandes quantidades de replicação de máquinas e maiores volumes de tráfego de replicação.<br/><br/> Pode mover máquinas entre dois servidores de processos, para tráfego de replicação de balanceamento de carga. | [Saiba mais](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Servidor de destino mestre

O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.

- Ele é instalado por padrão no servidor de configuração.
- Para implementações maiores, pode adicionar um servidor de destino mestre adicionais, em separado para a reativação pós-falha.


## <a name="next-steps"></a>Passos Seguintes
- Reveja os [arquitetura](vmware-azure-architecture.md) para recuperação após desastre de VMs de VMware e servidores físicos.
- Reveja os [requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) para recuperação após desastre de VMs de VMware e servidores físicos para o Azure. 
