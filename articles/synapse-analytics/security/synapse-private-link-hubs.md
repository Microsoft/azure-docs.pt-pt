---
title: Conecte-se a um Estúdio synapse usando links privados
description: Este artigo irá ensiná-lo a conectar-se ao seu Azure Synapse Studio usando links privados
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 2613a4fd931ad49a4f40a4221ea20e8c25f185fe
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501589"
---
# <a name="connect-to-azure-synapse-studio-using-azure-private-link-hubs"></a>Ligue-se ao Azure Synapse Studio usando hubs de ligação privada Azure 

Este artigo explica como as ligações privadas Azure Synapse Analytics Private Link Hubs são usadas para ligar de forma segura ao Synapse Studio. 

## <a name="azure-private-link-hubs"></a>Hubs de ligação privada Azure 
Pode ligar-se de forma segura ao Azure Synapse Studio a partir da sua rede virtual Azure utilizando links privados. Os hubs de ligação privada Azure Synapse Analytics são recursos Azure que funcionam como conectores entre a sua rede segura e a experiência web do Synapse Studio. 

Existem dois passos para ligar ao Synapse Studio usando links privados. Primeiro, tem de criar um recurso de centros de ligação privado. Em segundo lugar, você deve criar um ponto final privado da sua rede virtual Azure para este centro de ligação privada. Em seguida, pode utilizar pontos finais privados para comunicar de forma segura com o Synapse Studio. Tem de integrar os pontos finais privados com a sua solução DNS, seja a sua solução no local ou o Azure Private DNS. 

## <a name="azure-private-links-hubs-and-azure-synapse-studio"></a>Azure Private Links Hubs e Azure Synapse Studio
Você pode usar um único recurso de link privado Azure Synapse para conectar privadamente a todos os seus espaços de trabalho Azure Synapse Analytics usando Azure Synapse Studio. Os espaços de trabalho não têm de estar na mesma região que o centro de ligação privada Azure Synapse. O recurso Azure Synapse Private link hub também pode ser usado para ligações a espaços de trabalho Synapse em diferentes subscrições ou inquilinos AD Azure.

Você pode criar o seu centro de ligação privada pesquisando por *hubs de ligação privada Synapse* no portal Azure e selecionando **Azure Synapse Analytics (centros de ligação privada)** a partir de Serviços. Siga os passos no guia para saber como [ligar aos recursos do espaço de trabalho a partir de uma rede restrita](./how-to-connect-to-workspace-from-restricted-network.md) para mais detalhes.

>[!NOTE]
>Os centros de ligação privados destinam-se a carregar de forma segura o conteúdo estático Synapse Studio em links privados. Você deve criar **pontos finais separados e privados** para os recursos a que deseja ligar dentro do espaço de trabalho, tais como piscinas SQL a provisionadas/dedicadas, ou piscinas Spark. 

## <a name="azure-private-links-hubs-and-azure-virtual-network"></a>Azure Private Links Hubs e Azure Virtual Network
Tem de ligar a sua rede virtual Azure ao recurso do hub de ligação privada Synapse para garantir a ligação de ponta a ponta ao Synapse Studio. Para isso, tem de criar um ponto final privado da sua rede virtual para o centro de ligação privada que criou. Pode utilizar o portal Azure para o seu link privado e ir à secção private endpoint. Selecione "+ ponto final privado" para criar um novo ponto final privado que se conecta ao seu hub de ligação privada.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-private-endpoint.png" alt-text="Criar um ponto final privado para o centro de ligação privada":::

Certifique-se de escolher o tipo de recurso "Microsoft.Synapse/privateLinkHubs" no separador "Recursos". :::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-resource-type.png" alt-text="Criar um ponto final privado para o centro de ligação privada":::

No separador "Configuração", selecione "privatelink.azuresynapse.net" para Zonas DE DNS Privadas ao integrar-se na sua rede virtual e zona privada de DNS.

:::image type="content" source="./media/synapse-private-link-hubs/synapse-private-links-dns-zones.png" alt-text="Criar um ponto final privado para o centro de ligação privada":::

## <a name="next-steps"></a>Passos seguintes

[Conecte-se aos recursos do espaço de trabalho a partir de uma rede restrita](./how-to-connect-to-workspace-from-restricted-network.md)

Saiba mais sobre [Rede Virtual gerida do espaço de trabalho](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)

[Conecte-se ao espaço de trabalho synapse usando pontos finais privados](./how-to-connect-to-workspace-with-private-links.md)

