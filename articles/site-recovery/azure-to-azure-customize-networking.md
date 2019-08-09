---
title: Personalizar as configurações de rede para a VM de failover | Microsoft Docs
description: Fornece uma visão geral de personalizar as configurações de rede para a VM de failover na replicação de VMs do Azure usando o Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 907a698a675a039dfdc852210adecb94c7bfab25
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886900"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Personalizar as configurações de rede da VM do Azure de destino

Este artigo fornece orientação sobre como personalizar as configurações de rede na VM do Azure de destino quando você está replicando e recuperando VMs do Azure de uma região para outra, usando [Azure site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Antes de começar

Saiba como Site Recovery fornece recuperação de desastre para [esse cenário](azure-to-azure-architecture.md).

## <a name="support-networking-resources"></a>Suporte a recursos de rede

As seguintes configurações de recursos principais podem ser fornecidas para a VM de failover durante a replicação de VMs do Azure.

- [Balanceador de carga interno](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [IP público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) para a sub-rede e para a NIC

## <a name="pre-requisites"></a>Pré-requisitos

- Certifique-se de planejar suas configurações do lado de recuperação com antecedência.
- Você precisa criar os recursos de rede com antecedência. Forneça-o como uma entrada para que Azure Site Recovery serviço possa honrar essas configurações e garantir que a VM de failover obedeça a essas configurações.

## <a name="steps-to-customize-failover-networking-configurations"></a>Etapas para personalizar as configurações de rede de failover

1. Navegue até **itens replicados**. 
2. Clique na VM do Azure desejada.
3. Clique em **computação e rede**e **Editar**. Você observará que as definições de configuração da NIC incluem os recursos correspondentes na origem. 

     ![Personalizar](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Clique em **Editar** próximo à NIC que você deseja configurar. Na próxima folha que é aberta, selecione os recursos previamente criados correspondentes no destino.

    ![NIC-busca detalhada](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Clique em **OK**.

Agora, Site Recovery honrará essas configurações e garantirá que a VM no failover esteja conectada ao recurso selecionado por meio da NIC correspondente.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="unable-to-view-or-select-a-resource"></a>Não é possível exibir ou selecionar um recurso

Se não for possível selecionar ou exibir um recurso de rede, consulte as seguintes verificações & condições:

- O campo de destino para um recurso de rede só será habilitado se a VM de origem tiver uma entrada correspondente. Isso se baseia no princípio de que, para um cenário de recuperação de desastres, você desejaria a versão exata ou reduzida da sua origem.
- Para cada um dos recursos de rede em questão, alguns filtros são aplicados na lista suspensa para garantir que a VM de failover possa se conectar ao recurso selecionado e a confiabilidade do failover seja mantida. Esses filtros são baseados nas mesmas condições de rede que teriam sido verificadas quando você configurou a VM de origem.

Validações do balanceador de carga interno:

1. A assinatura e a região de LB e a VM de destino devem ser iguais.
2. A rede virtual associada ao Load Balancer interno e a do VMshould de destino são iguais.
3. O SKU de IP público da VM de destino e o SKU do balanceador interno devem ser os mesmos.
4. Se a VM de destino estiver configurada para ser colocada em uma zona de disponibilidade, verifique se o balanceador de carga tem redundância de zona ou parte de qualquer zona de disponibilidade. (Os balanceadores de carga de SKU básicos não dão suporte a zonas e não serão mostrados na lista suspensa nesse caso.)
5. Certifique-se de que o balanceador interno tenha um pool de back-end pré-criado e uma configuração de front-end.


Endereço IP público:
    
1. A assinatura e a região de IP público e a VM de destino devem ser iguais.
2. O SKU de IP público da VM de destino e o SKU do balanceador interno devem ser os mesmos.

Grupo de segurança de rede:
1. A assinatura e a região do grupo de segurança de rede e a VM de destino devem ser iguais.


> [!WARNING]
> Se a VM de destino estiver associada a um conjunto de disponibilidade, você precisará associar o IP público/balanceador de carga interno da mesma SKU que o balanceador de carga interno/IP público da VM no conjunto de disponibilidade. A falha em fazer isso pode resultar em falha de failover.
