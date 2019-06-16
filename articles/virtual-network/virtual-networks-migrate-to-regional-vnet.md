---
title: Migrar uma rede virtual do Azure (clássico) de um grupo de afinidades para uma região | Documentos da Microsoft
description: Saiba como migrar uma rede virtual (clássico) de um grupo de afinidades para uma região.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648640"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrar uma rede virtual (clássico) de um grupo de afinidades para uma região

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo de implementação do Resource Manager.

Grupos de afinidades Certifique-se de que os recursos criados dentro do mesmo grupo de afinidade fisicamente são alojados por servidores que estão próximos, permitindo que estes recursos para comunicar mais rápida. No passado, os grupos de afinidade eram um requisito para a criação de redes virtuais (clássicas). Nessa altura, o serviço de Gestor de rede geridos redes virtuais (clássico) apenas podia trabalhar dentro de um conjunto de servidores físicos ou unidade de escala. Melhorias de arquitetura aumentou o âmbito de gestão de rede para uma região.

Como consequência desses aprimoramento de arquiteturais, grupos de afinidade são já não são recomendados, ou necessários para as redes virtuais (clássico). A utilização de grupos de afinidade para redes virtuais (clássicos) é substituída pelo regiões. Redes virtuais (clássicas) que estão associadas a regiões são chamadas de redes virtuais regionais.

Recomendamos que não use grupos de afinidade em geral. Além do requisito de rede virtual, os grupos de afinidades também foram importantes para utilizar para se certificar de recursos, como computação (clássico) e armazenamento (clássico), foram colocados perto uns dos outros. No entanto, com a arquitetura de rede do Azure atual, esses requisitos de posicionamento já não são necessários.

> [!IMPORTANT]
> Embora seja tecnicamente possível criar uma rede virtual que está associada um grupo de afinidades, não há nenhum motivo convincente para isso. Muitos recursos de rede virtual, como grupos de segurança de rede, só estão disponíveis quando utilizar uma rede virtual regional e não estão disponíveis para as redes virtuais que estão associadas a grupos de afinidade.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Edite o ficheiro de configuração de rede

1. Exporte o ficheiro de configuração de rede. Para saber como exportar um ficheiro de configuração de rede com o PowerShell ou a interface de linha de comandos (CLI) 1.0 do Azure, veja [configurar uma rede virtual com um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md#export).
2. Edite o ficheiro de configuração de rede, substituindo **AffinityGroup** com **localização**. Especificar do Azure [região](https://azure.microsoft.com/regions) para **localização**.
   
   > [!NOTE]
   > O **localização** é a região que especificou para o grupo de afinidade que estão associado com a rede virtual (clássico). Por exemplo, se a rede virtual (clássico) estiver associada um grupo de afinidade que está localizado na região E.U.A. oeste, ao migrar, sua **localização** tem de apontar para E.U.A. oeste. 
   > 
   > 
   
    Edite as seguintes linhas no ficheiro de configuração de rede, substituindo os valores pelos seus próprios: 
   
    **Valor antigo:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Novo valor:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Guardar as alterações e [importar](virtual-networks-using-network-configuration-file.md#import) a configuração de rede para o Azure.

> [!NOTE]
> Esta migração não irá causar qualquer período de inatividade para seus serviços.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>O que fazer se tiver uma VM (clássico) num grupo de afinidades
VMs (clássicas) que estão atualmente num grupo de afinidades não é necessário ser removido do grupo de afinidade. Depois de uma VM é implementada, é implementada para uma unidade de escala único. Grupos de afinidades pode restringir o conjunto de tamanhos VM disponíveis para uma nova implementação de VM, mas qualquer VM existente implementada já está limitado ao conjunto de tamanhos de VM disponíveis na unidade de escala em que a VM é implementada. Uma vez que a VM já está a ser implementada para uma unidade de escala, remover uma VM a partir de um grupo de afinidade não tem efeito na VM.
