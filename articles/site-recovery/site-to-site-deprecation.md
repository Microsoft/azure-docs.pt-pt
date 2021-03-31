---
title: Depreciação da recuperação de desastres entre sites geridos pelo cliente (com VMM) usando Azure Site Recovery | Microsoft Docs
description: Detalhes sobre a depreciação futura de DR entre sites de propriedade do cliente usando Hyper-V e entre sites geridos pela SCVMM para Azure e opções alternativas
services: site-recovery
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 02/25/2020
ms.author: sharrai
ms.openlocfilehash: 9ffe7a3158b1de6828350947dcf81ef41d08708d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87421846"
---
# <a name="deprecation-of-disaster-recovery-between-customer-managed-sites-with-vmm-using-azure-site-recovery"></a>Depreciação da recuperação de desastres entre sites geridos pelo cliente (com VMM) usando a recuperação do site Azure

Este artigo descreve o próximo plano de depreciação, as implicações correspondentes e as opções alternativas disponíveis para os clientes para o seguinte cenário:

DR entre sites de propriedade de clientes geridos por System Center Virtual Machine Manager (SCVMM) usando a Recuperação do Site

> [!IMPORTANT]
> Os clientes são aconselhados a tomar as medidas de reparação o mais cedo possível para evitar qualquer perturbação no seu ambiente. 

## <a name="what-changes-should-you-expect"></a>Que mudanças deve esperar?

- A partir de março de 2020, receberá notificações do portal Azure & comunicações por e-mail com a próxima depreciação da replicação site-to-site de VMs Hiper-V. A depreciação está prevista para março de 2023.

- Se tiver uma configuração existente, não haverá impacto na configuração.

- Uma vez que os cenários são depreciados a menos que o cliente siga as abordagens alternativas, as replicações existentes podem ser interrompidas. Os clientes não poderão visualizar, gerir ou realizar quaisquer operações relacionadas com DR através da experiência de Recuperação do Site Azure no portal Azure.
 
## <a name="alternatives"></a>Alternativas 

Abaixo estão as alternativas que o cliente pode escolher para garantir que a sua estratégia de DR não seja impactada uma vez que o cenário é depreciado. 

- Opção 1 (Recomendado): Escolha começar a [utilizar o Azure como alvo DR](hyper-v-vmm-azure-tutorial.md).


- Opção 2: Opte por continuar com a replicação site-to-site utilizando a [solução de réplica Hiper-V](/windows-server/virtualization/hyper-v/manage/set-up-hyper-v-replica)subjacente , mas não será capaz de gerir as configurações de DR utilizando a Recuperação do Site Azure no portal Azure. 


## <a name="remediation-steps"></a>Passos de remediação

Se optar por escolher escolher a Opção 1, execute os seguintes passos:

1. [Desative a proteção de todas as máquinas virtuais associadas aos VMMs](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-secondary-vmm-server-using-the-system-center-vmm-to-vmm-scenario). Utilize a **replicação de desativação e remova** a opção ou execute os scripts mencionados para garantir que as definições de replicação no local sejam limpas. 

2. [Desagregar todos os servidores VMM](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server) da configuração de replicação site-to-site.

3. [Prepare recursos Azure](tutorial-prepare-azure-for-hyperv.md) para permitir a replicação dos seus VMs.
4. [Preparar servidores Hyper-V no local](hyper-v-prepare-on-premises-tutorial.md)
5. [Configurar a replicação para os VMs na nuvem VMM](hyper-v-vmm-azure-tutorial.md)
6. Opcional, mas recomendado: [Executar um berbequim DR](tutorial-dr-drill-azure.md)

Se optar por optar pela opção 2 de utilizar a réplica Hyper-V, execute os seguintes passos:

1. Em **Itens Protegidos**  >  **Itens Replicados,** clique com o botão direito na máquina > **Desativar a replicação**.
2. Na **replicação de desativação**, selecione **Remover**.

    Isto remove o item replicado da Recuperação do Local de Azure (a faturação está interrompida). A configuração de replicação na máquina virtual no local **não será** limpa. 

## <a name="next-steps"></a>Passos seguintes
Planeie a depreciação e escolha uma opção alternativa que seja mais adequada para a sua infraestrutura e negócios. Caso tenha dúvidas sobre isso, contacte o Microsoft Support

