---
title: Personalizar as configurações de rede para uma VM de failover | Microsoft Docs
description: Fornece uma visão geral de personalizar as configurações de rede para uma VM de failover na replicação de VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 8038f7c909cfeaf15039afa7335dd6b0460a2622
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293457"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizar as configurações de rede da VM do Azure de destino

Este artigo fornece orientação sobre como personalizar as configurações de rede na VM (máquina virtual) do Azure de destino quando você estiver replicando e recuperando VMs do Azure de uma região para outra, usando [Azure site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Recursos de rede com suporte

Você pode fornecer as seguintes configurações de recurso-chave para a VM de failover ao replicar VMs do Azure:

- [Balanceador de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) para a sub-rede e para a NIC

 > [!IMPORTANT]
  > Essas configurações têm suporte apenas na operação de failover no momento e não no failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de planejar suas configurações do lado de recuperação com antecedência.
- Crie os recursos de rede com antecedência. Forneça-o como uma entrada para que Azure Site Recovery serviço possa honrar essas configurações e garantir que a VM de failover obedeça a essas configurações.

## <a name="customize-failover-networking-configurations"></a>Personalizar configurações de rede de failover

1. Vá para **itens replicados**. 
2. Selecione a VM do Azure desejada.
3. Selecione **computação e rede** e selecione **Editar**. Observe que as definições de configuração da NIC incluem os recursos correspondentes na origem. 

     ![Personalizar as configurações de rede de failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecione **Editar** próximo à NIC que você deseja configurar. Na próxima folha que é aberta, selecione os recursos previamente criados correspondentes no destino.

    ![Editar a configuração da NIC](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Selecione **OK**.

Agora, Site Recovery honrará essas configurações e garantirá que a VM no failover esteja conectada ao recurso selecionado por meio da NIC correspondente.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unable-to-view-or-select-a-resource"></a>Não é possível exibir ou selecionar um recurso

Se você não puder selecionar ou exibir um recurso de rede, siga as seguintes verificações e condições:

- O campo de destino para um recurso de rede será habilitado somente se a VM de origem tiver uma entrada correspondente. Isso se baseia no princípio de que, para um cenário de recuperação de desastres, você desejaria a versão exata ou reduzida da sua fonte.
- Para cada recurso de rede, alguns filtros são aplicados na lista suspensa para garantir que a VM de failover possa se conectar ao recurso selecionado e a confiabilidade do failover seja mantida. Esses filtros são baseados nas mesmas condições de rede que teriam sido verificadas quando você configurou a VM de origem.

Validações do balanceador de carga interno:

- A assinatura e a região do balanceador de carga e a VM de destino devem ser iguais.
- A rede virtual associada ao balanceador de carga interno e a da VM de destino devem ser iguais.
- O SKU de IP público da VM de destino e o SKU do balanceador de carga interno devem ser os mesmos.
- Se a VM de destino estiver configurada para ser colocada em uma zona de disponibilidade, verifique se o balanceador de carga tem redundância de zona ou parte de qualquer zona de disponibilidade. (Os balanceadores de carga de SKU básicos não dão suporte a zonas e não serão mostrados na lista suspensa nesse caso.)
- Verifique se o balanceador de carga interno tem um pool de back-end criado previamente e uma configuração de front-end.


Endereço IP público:
    
- A assinatura e a região do IP público e a VM de destino devem ser iguais.
- O SKU de IP público da VM de destino e o SKU do balanceador de carga interno devem ser os mesmos.

Grupo de segurança de rede:
- A assinatura e a região do grupo de segurança de rede e a VM de destino devem ser iguais.


> [!WARNING]
> Se a VM de destino estiver associada a um conjunto de disponibilidade, você precisará associar o IP público e o balanceador de carga interno da mesma SKU com a do IP público e do balanceador de carga interno da VM no conjunto de disponibilidade. Caso contrário, o failover pode não ter sido executado.
