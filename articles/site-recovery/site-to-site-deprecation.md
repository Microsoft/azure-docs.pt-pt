---
title: Depreciação da recuperação de desastres entre sites geridos pelo cliente (com VMM) utilizando a Recuperação do Site Azure [ Microsoft Docs
description: Detalhes sobre a próxima depreciação de DR entre sites pertencentes ao cliente usando Hyper-V e entre sites geridos pela SCVMM para Azure e opções alternativas
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: rajanaki
ms.openlocfilehash: 208177d10e9002fafe2495710da229541a11a43e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77661675"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Depreciação da recuperação de desastres entre sites geridos pelo cliente (com VMM) utilizando a Recuperação do Site Azure

Este artigo descreve o próximo plano de depreciação, as implicações correspondentes e as opções alternativas disponíveis para os clientes para o seguinte cenário:

DR entre sites de propriedade de clientes geridos pelo System Center Virtual Machine Manager (SCVMM) usando a Recuperação do Site

> [!IMPORTANT]
> Os clientes são aconselhados a tomar as medidas de reparação o mais cedo possível para evitar qualquer perturbação no seu ambiente. 

## <a name="what-changes-should-you-expect"></a>Que mudanças deve esperar?

- A partir de março de 2020, receberá notificações do portal Azure & comunicações de email com a próxima depreciação da replicação site-to-site de Hiper-V VMs. A depreciação está prevista para março de 2023.

- Se tiver uma configuração existente, não haverá impacto na configuração.

- Uma vez que os cenários são depreciados a menos que o cliente siga as abordagens alternativas, as replicações existentes podem ser interrompidas. Os clientes não poderão visualizar, gerir ou realizar quaisquer operações relacionadas com dr através da experiência azure de recuperação do site no portal Azure.
 
## <a name="alternatives"></a>Alternativas 

Abaixo estão as alternativas que o cliente pode escolher para garantir que a sua estratégia de DR não seja impactada uma vez que o cenário é depreciado. 

- Opção 1 (Recomendado): Opte por começar a [utilizar o Azure como alvo DR](hyper-v-vmm-azure-tutorial.md).


- Opção 2: Opte por continuar com a replicação site-to-site utilizando a solução de [réplica hiper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)subjacente, mas não poderá gerir as configurações de DR utilizando a Recuperação do Site Azure no portal Azure. 


## <a name="remediation-steps"></a>Passos de reparação

Se optar por seguir a Opção 1, execute os seguintes passos:

1. [Desative a proteção de todas as máquinas virtuais associadas aos VMMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Utilize a **replicação desativar e remova** a opção ou executar as scripts mencionadas para garantir que as definições de replicação no local estão limpas. 

2. [Desregistre todos os servidores VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) da configuração de replicação site-to-site.

3. [Prepare os recursos azure](tutorial-prepare-azure-for-hyperv.md) para permitir a replicação dos seus VMs.
4. [Preparar servidores Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md)
5. [Configurar a replicação para os VMs na nuvem VMM](hyper-v-vmm-azure-tutorial.md)
6. Opcional mas recomendado: [Executar um berbequim DR](tutorial-dr-drill-azure.md)

Se optar por optar pela Opção 2 de utilizar a réplica Hyper-V, execute os seguintes passos:

1. Em **itens protegidos,** > **Replicated Items**clique à direita na máquina > **desativação**da replicação .
2. Na **replicação de sactivar,** selecione **Remover**.

    Isto remove o item replicado da Recuperação do Sítio Azure (a faturação está parada). A configuração de replicação na máquina virtual no local **não será** limpa. 

## <a name="next-steps"></a>Passos seguintes
Planeie a depreciação e escolha uma opção alternativa que seja mais adequada para a sua infraestrutura e negócio. Caso tenha alguma dúvida sobre isso, contacte o Microsoft Support

