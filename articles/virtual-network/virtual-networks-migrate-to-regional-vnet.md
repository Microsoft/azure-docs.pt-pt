---
title: Migrar a rede virtual do Azure (clássica) do grupo de afinidade para a região
description: Saiba como migrar uma rede virtual (clássica) de um grupo de afinidade para uma região.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 57e6c551e1377425dab5509a886a0454b9410a32
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196705"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrar uma rede virtual (clássica) de um grupo de afinidade para uma região

> [!IMPORTANT]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e Clássico](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que a maioria das implantações novas usem o modelo de implantação do Gerenciador de recursos.

Os grupos de afinidade garantem que os recursos criados dentro do mesmo grupo de afinidade sejam fisicamente hospedados por servidores que estão próximos juntos, permitindo que esses recursos se comuniquem mais rapidamente. No passado, os grupos de afinidades eram um requisito para a criação de redes virtuais (clássicas). Nesse momento, o serviço Gerenciador de rede que gerenciava redes virtuais (clássicas) poderia funcionar apenas dentro de um conjunto de servidores físicos ou unidade de escala. Melhorias na arquitetura aumentaram o escopo do gerenciamento de rede para uma região.

Como resultado dessas melhorias de arquitetura, os grupos de afinidades não são mais recomendados ou necessários para redes virtuais (clássicas). O uso de grupos de afinidade para redes virtuais (clássicas) é substituído por regiões. As redes virtuais (clássicas) associadas a regiões são chamadas de redes virtuais regionais.

Recomendamos que você não use grupos de afinidade em geral. Além do requisito de rede virtual, os grupos de afinidades também eram importantes para garantir que os recursos, como computação (clássica) e armazenamento (clássico), fossem colocados próximos uns dos outros. No entanto, com a arquitetura de rede atual do Azure, esses requisitos de posicionamento não são mais necessários.

> [!IMPORTANT]
> Embora ainda seja tecnicamente possível criar uma rede virtual associada a um grupo de afinidade, não há um motivo convincente para isso. Muitos recursos de rede virtual, como grupos de segurança de rede, só estão disponíveis ao usar uma rede virtual regional e não estão disponíveis para redes virtuais associadas a grupos de afinidade.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Editar o arquivo de configuração de rede

1. Exporte o arquivo de configuração de rede. Para saber como exportar um arquivo de configuração de rede usando o PowerShell ou a CLI (interface de linha de comando) do Azure 1,0, consulte [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md#export).
2. Edite o arquivo de configuração de rede, substituindo **AffinityGroup** pelo **local**. Especifique uma [região](https://azure.microsoft.com/regions) do Azure para o **local**.
   
   > [!NOTE]
   > O **local** é a região que você especificou para o grupo de afinidade que está associado à sua rede virtual (clássica). Por exemplo, se sua rede virtual (clássica) estiver associada a um grupo de afinidade que está localizado no oeste dos EUA, quando você migrar, seu **local** deverá apontar para o oeste dos EUA. 
   > 
   > 
   
    Edite as seguintes linhas no arquivo de configuração de rede, substituindo os valores pelos seus próprios: 
   
    **Valor antigo:** \<VirtualNetworkSitename = "VNetUSWest" AffinityGroup = "VNetDemoAG"\> 
   
    **Novo valor:** \<VirtualNetworkSitename = "VNetUSWest" Location = "oeste dos eua"\>
3. Salve as alterações e [importe](virtual-networks-using-network-configuration-file.md#import) a configuração de rede para o Azure.

> [!NOTE]
> Essa migração não causa nenhum tempo de inatividade para seus serviços.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>O que fazer se você tiver uma VM (clássica) em um grupo de afinidade
As VMs (clássicas) que estão atualmente em um grupo de afinidades não precisam ser removidas do grupo de afinidade. Depois que uma VM é implantada, ela é implantada em uma única unidade de escala. Os grupos de afinidade podem restringir o conjunto de tamanhos de VM disponíveis para uma nova implantação de VM, mas qualquer VM existente implantada já está restrita ao conjunto de tamanhos de VM disponíveis na unidade de escala em que a VM é implantada. Como a VM já está implantada em uma unidade de escala, remover uma VM de um grupo de afinidade não tem nenhum efeito na VM.
