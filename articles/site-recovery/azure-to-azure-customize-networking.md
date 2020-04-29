---
title: Personalize as configurações de rede para um VM failover [ Microsoft Docs
description: Fornece uma visão geral de configurações de rede personalizadas para um VM failover na replicação de VMs Azure usando a Recuperação do Site Azure.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76292863"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizar as configurações de rede da VM do Azure de destino

Este artigo fornece orientações sobre a personalização das configurações de networking na máquina virtual Azure (VM) alvo quando estiver a replicar e recuperar VMs Azure de uma região para outra, utilizando a Recuperação do [Site Azure.](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

Saiba como a Recuperação do Site proporciona recuperação de desastres para [este cenário.](azure-to-azure-architecture.md)

## <a name="supported-networking-resources"></a>Recursos de rede apoiados

Pode fornecer as seguintes configurações de recursos chave para o VM failover enquanto replica VMs Azure:

- [Equilibrador de carga interna](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Grupo de segurança](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) da rede tanto para a sub-rede como para o NIC

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que planeia as configurações laterais da recuperação com antecedência.
- Crie os recursos de networking com antecedência. Forneça-o como uma entrada para que o serviço de recuperação de sites do Azure possa honrar estas definições e garantir que o VM failover adere a estas definições.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Personalize as configurações de rede failover e teste

1. Vá a **itens replicados.** 
2. Selecione o VivE Azure desejado.
3. **Selecione Compute e Rede** e selecione **Editar**. Note que as definições de configuração NIC incluem os recursos correspondentes na fonte. 

     ![Personalize as configurações de rede failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecione uma rede virtual de failover de teste. Pode optar por deixá-lo em branco e selecionar um no momento da falha do teste.
5. A rede Failover é Select **Edit** perto do NIC que pretende configurar. Na lâmina seguinte que se abre, selecione os recursos pré-criados correspondentes no teste failover e failover localização.

    ![Editar a configuração NIC](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selecione **OK**.

A Recuperação do Site irá agora honrar estas definições e garantir que o VM na failover está ligado ao recurso selecionado através do NIC correspondente.

Quando acionar a falha do teste através do Plano de Recuperação, irá sempre perguntar à rede virtual Azure. Esta rede virtual será utilizada para o teste de falha para as máquinas que não tinham definições de falha de teste pré-configuradas.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unable-to-view-or-select-a-resource"></a>Incapaz de ver ou selecionar um recurso

Se não conseguir selecionar ou visualizar um recurso de networking, consulte as seguintes verificações e condições:

- O campo-alvo de um recurso de rede só está ativado se o VM de origem tiver uma entrada correspondente. Isto baseia-se no princípio de que, para um cenário de recuperação de desastres, você quereria a versão exata ou reduzida da sua fonte.
- Para cada recurso de rede, alguns filtros são aplicados na lista de drop-down para garantir que o VM failover pode ligar-se ao recurso selecionado e a fiabilidade de failover é mantida. Estes filtros baseiam-se nas mesmas condições de rede que teriam sido verificadas quando configurasse o VM de origem.

Validações internas do equilíbriodor de carga:

- A Subscrição e região do equilibrador de carga e o VM-alvo devem ser os mesmos.
- A rede virtual associada ao equilibrador de carga interna e à do VM-alvo deve ser a mesma.
- O IP SKU público da VM alvo e o SKU do equilibrador de carga interno devem ser os mesmos.
- Se o VM alvo estiver configurado para ser colocado numa zona de disponibilidade, verifique se o equilibrador de carga é uma zona redundante ou parte de qualquer zona de disponibilidade. (Os equilibradores básicos de carga SKU não suportam zonas e não serão mostrados na lista de abandono neste caso.)
- Certifique-se de que o equilibrador de carga interna tem uma piscina traseira pré-criada e uma configuração frontal.

Endereço IP público:

- A Subscrição e região do PI público e o VM-alvo devem ser os mesmos.
- O IP SKU público da VM alvo e o SKU do equilibrador de carga interno devem ser os mesmos.

Grupo de segurança de rede:
- A Subscrição e região do grupo de segurança da rede e o VM alvo devem ser os mesmos.


> [!WARNING]
> Se o VM alvo estiver associado a um conjunto de disponibilidade, então precisa associar o IP público e o equilibrador de carga interna do mesmo SKU com o ip público do outro VM e equilibrador de carga interna no conjunto de disponibilidade. Se não o fizeres, o fracasso pode não ter sucesso.
