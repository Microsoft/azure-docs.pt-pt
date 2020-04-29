---
title: Sobre a configuração/configuração de recuperação do site Azure/servidores-alvo-alvo mestre
description: Este artigo fornece uma visão geral da configuração, processo e servidores-alvo principais usando ao configurar a recuperação de vMware no local para Azure com a Recuperação do Site Azure
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: cd5ded18d1a8f1f5fd96212d37725bb5db13002f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062095"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sobre componentes de recuperação do site (configuração, processo, alvo principal)

Este artigo descreve a configuração, o processo e os servidores-alvo principais utilizados pelo serviço de [Recuperação](site-recovery-overview.md) do Site para replicar VMware VMs e servidores físicos para o Azure.

## <a name="configuration-server"></a>Servidor de configuração

Para a recuperação de desastres de VMware VMs e servidores físicos no local, implemente um servidor de configuração de recuperação do site no local.

**Definição** | **Detalhes** | **Ligações**
--- | --- | ---
**Componentes**  | A máquina de servidor de configuração executa todos os componentes de recuperação do site no local, que incluem o servidor de configuração, servidor de processos e servidor de alvo principal.<br/><br/> Quando configura o servidor de configuração, todos os componentes são instalados automaticamente. | [Leia](vmware-azure-common-questions.md#configuration-server) o faq do servidor de configuração.
**Função** | O servidor de configuração coordena as comunicações entre o local e o Azure, e gere a replicação de dados. | Saiba mais sobre a arquitetura para [VMware](vmware-azure-architecture.md) e recuperação de desastres de [servidor físico](physical-azure-architecture.md) para o Azure.
**Requisitos vMware** | Para a recuperação de desastres de VMware VMs no local, deve instalar e executar o servidor de configuração como um VM no local, VMde VM altamente disponível. | [Saiba mais sobre](vmware-azure-deploy-configuration-server.md#prerequisites) os pré-requisitos.
**Implementação vMware** | Recomendamos que implemente o servidor de configuração utilizando um modelo OVA descarregado. Este método fornece uma forma simples de configurar um servidor de configuração que cumpre todos os requisitos e pré-requisitos.<br/><br/> Se por alguma razão não conseguir implementar um VMware VM utilizando um modelo OVA, pode configurar manualmente as máquinas de servidor de configuração, como descrito abaixo para a recuperação de desastres físicos da máquina. | [Desdobre](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template) com um modelo OVA.
**Requisitos do servidor físico** | Para a recuperação de desastres nos servidores físicos no local, implementa o servidor de configuração manualmente. | [Saiba mais sobre](physical-azure-set-up-source.md#prerequisites) os pré-requisitos.
**Implementação física do servidor** | Se não puder ser instalado como VMware VM, pode instalá-lo num servidor físico. | [Implemente](physical-azure-set-up-source.md#set-up-the-source-environment) manualmente o servidor de configuração.

## <a name="process-server"></a>Servidor de processos

Um servidor de processo lida com dados de replicação durante o failover e failback, e instala o serviço mobility para VMware VMware no local e servidores físicos.

**Definição** | **Detalhes** | **Ligações**
--- | --- | ---
**Implementação**  | Por predefinição, quando o servidor de configuração for implementado, o servidor de processo é instalado. <br/><br/> Um servidor de processo no local é necessário para a recuperação de desastres e replicação de VMware VMware no local e servidores físicos. | [Saiba mais](vmware-azure-architecture.md#architectural-components).
**Papel (no local)** | Recebe dados de replicação de máquinas ativadas para replicação. <br/><br/> Otimiza dados de replicação com cache, compressão e encriptação, e envia-os para o Armazenamento Azure. <br/><br/> Realiza uma instalação push do serviço de Mobilidade de Recuperação do Site em VMware VMware no local e servidores físicos que pretende replicar. <br/><br/> Realiza a descoberta automática de máquinas no local. | [Saiba mais](vmware-azure-enable-replication.md).
**Papel (recuo de Azure)** | Depois de falhar no seu site no local, configura um servidor de processo sintetização em Azure, como um VM Azure, para lidar com o failback para a sua localização no local.<br/><br/> O servidor de processos em Azure é temporário. O VM Azure pode ser eliminado após o failback ser feito. | [Saiba mais](vmware-azure-set-up-process-server-azure.md).
**Dimensionamento** | Para implementações maiores, no local pode configurar servidores de processos adicionais e scale-out. Servidores adicionais escalam a capacidade, manuseando um maior número de máquinas de replicação, e maiores volumes de tráfego de replicação.<br/><br/> Pode mover máquinas entre dois servidores de processo, de forma a carregar o tráfego de replicação do equilíbrio. | [Saiba mais](vmware-azure-set-up-process-server-scale.md).

## <a name="master-target-server"></a>Servidor de destino mestre

O servidor de destino principal processa dados de replicação durante a reativação pós-falha a partir do Azure.

- Por predefinição, o servidor de alvo principal está instalado no servidor de configuração.
- Para grandes implementações, pode adicionar um servidor de alvo principal adicional e separado para o failback.

## <a name="next-steps"></a>Passos seguintes

- Reveja a [arquitetura](vmware-azure-architecture.md) para recuperação de desastres de VMware VMs e servidores físicos.
- Reveja os [requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure.
