---
title: Sobre a configuração/processo/servidores-alvo do Azure Site Recovery/process/master
description: Este artigo fornece uma visão geral da configuração, processo e servidores-alvo principais que utilizam ao configurar a recuperação de desastres de VMware VMs para Azure com Azure Site Recovery
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80062095"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sobre os componentes de Recuperação do Local (configuração, processo, alvo principal)

Este artigo descreve a configuração, o processo e os servidores-alvo principais utilizados pelo serviço [de Recuperação](site-recovery-overview.md) de Sítios para replicar VMware VMs e servidores físicos para o Azure.

## <a name="configuration-server"></a>Servidor de configuração

Para a recuperação de desastres de VMware VMware e servidores físicos, implemente um servidor de configuração do site no local.

**Definição** | **Detalhes** | **Ligações**
--- | --- | ---
**Componentes**  | A máquina do servidor de configuração executa todos os componentes de recuperação do local, que incluem o servidor de configuração, o servidor de processo e o servidor alvo principal.<br/><br/> Quando configurar o servidor de configuração, todos os componentes são instalados automaticamente. | [Leia](vmware-azure-common-questions.md#configuration-server) o servidor de configuração FAQ.
**Role** | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados. | Saiba mais sobre a arquitetura para [VMware](vmware-azure-architecture.md) e recuperação de desastres [de servidor físico](physical-azure-architecture.md) para Azure.
**Requisitos de VMware** | Para a recuperação de desastres dos VMware VMware no local, tem de instalar e executar o servidor de configuração como um VMware VM altamente disponível. | [Saiba mais sobre](vmware-azure-deploy-configuration-server.md#prerequisites) os pré-requisitos.
**Implementação de VMware** | Recomendamos que implemente o servidor de configuração utilizando um modelo OVA descarregado. Este método fornece uma forma simples de configurar um servidor de configuração que cumpre todos os requisitos e requisitos.<br/><br/> Se, por alguma razão, não conseguir implementar um VMware VM utilizando um modelo OVA, pode configurar manualmente as máquinas do servidor de configuração, conforme descrito abaixo para a recuperação de desastres físicos da máquina. | [Implementar](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) com um modelo OVA.
**Requisitos do servidor físico** | Para a recuperação de desastres nos servidores físicos no local, implementa manualmente o servidor de configuração. | [Saiba mais sobre](physical-azure-set-up-source.md#prerequisites) os pré-requisitos.
**Implementação de servidor físico** | Se não puder ser instalado como VMware VMware, pode instalá-lo num servidor físico. | [Implemente](physical-azure-set-up-source.md#set-up-the-source-environment) o servidor de configuração manualmente.

## <a name="process-server"></a>Servidor de processo

Um servidor de processo trata os dados de replicação durante o failover e failback, e instala o serviço de Mobilidade para VMware VMware no local e servidores físicos.

**Definição** | **Detalhes** | **Ligações**
--- | --- | ---
**Implementação**  | Por predefinição, quando o servidor de configuração é implantado, o servidor de processo é instalado. <br/><br/> Um servidor de processo no local é necessário para a recuperação de desastres e replicação de VMware VMware no local e servidores físicos. | [Saiba mais](vmware-azure-architecture.md#architectural-components).
**Função (no local)** | Recebe dados de replicação de máquinas ativadas para replicação. <br/><br/> Otimiza os dados de replicação com caching, compressão e encriptação, e envia-os para o Azure Storage. <br/><br/> Executa uma instalação push do serviço de Mobilidade de Recuperação do Site em VMware vMware e servidores físicos que pretende replicar. <br/><br/> Realiza a descoberta automática de máquinas no local. | [Saiba mais](vmware-azure-enable-replication.md).
**Papel (recuo de Azure)** | Após o failover do seu site no local, instalou um servidor de processo em Azure, como um Azure VM, para lidar com falhas no local.<br/><br/> O servidor de processo em Azure é temporário. O Azure VM pode ser eliminado depois de ter sido feito o failback. | [Saiba mais](vmware-azure-set-up-process-server-azure.md).
**Dimensionamento** | Para implementações maiores, no local pode configurar servidores de processos adicionais e de escala. Os servidores adicionais reduzem a capacidade, manuseando um maior número de máquinas de replicação e volumes maiores de tráfego de replicação.<br/><br/> Pode mover máquinas entre dois servidores de processo, de modo a carregar o tráfego de replicação de equilíbrio. | [Saiba mais](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Servidor de destino mestre

O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.

- Por predefinição, o servidor alvo principal é instalado no servidor de configuração.
- Para grandes implementações, pode adicionar um servidor de alvo principal adicional e separado para falha.

## <a name="next-steps"></a>Passos seguintes

- Reveja a [arquitetura](vmware-azure-architecture.md) para a recuperação de desastres de VMware VMs e servidores físicos.
- Reveja os [requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) para a recuperação de desastres de VMware VMs e servidores físicos para Azure.
