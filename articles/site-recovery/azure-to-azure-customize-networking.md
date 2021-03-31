---
title: Personalize as configurações de networking para um | VM de failover Microsoft Docs
description: Fornece uma visão geral das configurações de networking personalizadas para um VM de failover na replicação de VMs Azure usando Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541195"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizar as configurações de rede da VM do Azure de destino

Este artigo fornece orientações sobre a personalização de configurações de rede na máquina virtual Azure (VM) alvo quando está a replicar e a recuperar VMs Azure de uma região para outra, utilizando a Recuperação do [Local Azure.](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

Saiba como a Recuperação do Site proporciona recuperação de desastres para [este cenário.](azure-to-azure-architecture.md)

## <a name="supported-networking-resources"></a>Recursos de networking apoiados

Pode fornecer as seguintes configurações de recursos chave para o VM de failover enquanto replica VMs Azure:

- [Equilibrador interno de carga](../load-balancer/load-balancer-overview.md)
- [IP público](../virtual-network/public-ip-addresses.md)
- [Grupo de segurança da rede](../virtual-network/manage-network-security-group.md) tanto para a sub-rede como para o NIC

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que planeia previamente as configurações laterais de recuperação.
- Crie os recursos de networking com antecedência. Forneça-o como uma entrada para que o serviço de recuperação do local de Azure possa honrar estas definições e garantir que o VM de failover adere a estas definições.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalizar configurações de rede de failover e teste de failover

1. Ir para **itens replicados.** 
2. Selecione o Azure VM desejado.
3. Selecione **Compute and Network** e selecione **Editar.** Note que as definições de configuração DO NIC incluem os recursos correspondentes na fonte. 

     ![Personalize as configurações de rede de failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecione uma rede virtual de falha de teste.
5. A rede failover é Select **Edit** perto do NIC que pretende configurar. Na lâmina seguinte que se abre, selecione os recursos pré-criados correspondentes na falha de teste e na localização de falha.

    ![Editar a configuração NIC](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selecione **OK**.

A Recuperação do Site irá agora honrar estas definições e garantir que o VM na falha está ligado ao recurso selecionado através do NIC correspondente.

Quando ativar o teste de falha através do Plano de Recuperação, perguntará sempre à rede virtual Azure. Esta rede virtual será utilizada para testar falhas nas máquinas que não tenham configurações de falha de teste pré-configuradas.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unable-to-view-or-select-a-resource"></a>Incapaz de ver ou selecionar um recurso

Se não conseguir selecionar ou visualizar um recurso de rede, consulte as seguintes verificações e condições:

- O campo-alvo de um recurso de rede só é ativado se a VM de origem tiver uma entrada correspondente. Isto baseia-se no princípio de que, para um cenário de recuperação de desastres, você quereria a versão exata ou reduzida da sua fonte.
- Para cada recurso de rede, alguns filtros são aplicados na lista de drop-down para garantir que o VM de failover pode ligar-se ao recurso selecionado e a fiabilidade da falha é mantida. Estes filtros baseiam-se nas mesmas condições de rede que teriam sido verificadas quando configuraram a fonte VM.

Validações internas do balançador de carga:

- A subscrição e a região do equilibrador de carga e do VM-alvo devem ser os mesmos.
- A rede virtual associada ao equilibrador de carga interno e a do VM-alvo devem ser as mesmas.
- O SKU IP público da VM e o SKU do balanceador interno de carga devem ser os mesmos.
- Se o VM-alvo estiver configurado para ser colocado numa zona de disponibilidade, verifique se o equilibrador de carga é redundante ou parte de qualquer zona de disponibilidade. (Os equilibradores básicos de carga SKU não suportam zonas e não serão mostrados na lista de recuos neste caso.)
- Certifique-se de que o balançador de carga interno tem uma reserva pré-criada e uma configuração frontal.

Endereço IP público:

- A subscrição e a região do PERÍODO PÚBLICO e a VM-alvo devem ser as mesmas.
- O SKU IP público da VM e o SKU do balanceador interno de carga devem ser os mesmos.

Grupo de segurança de rede:
- A subscrição e a região do grupo de segurança da rede e o VM-alvo devem ser os mesmos.


> [!WARNING]
> Se o VM-alvo estiver associado a um conjunto de disponibilidade, então você precisa associar o IP público e o equilibrador de carga interno do mesmo SKU com o do ip público do outro VM e equilibr de carga interna no conjunto de disponibilidade. Se não o fizeres, o fracasso pode não ter sucesso.
