---
title: Pontos finais privados geridos
description: Um artigo que explica pontos finais privados geridos na Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: dc748260c6e71eaaa469defb227d5cc2748a1345
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83645310"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Synapse Managed pontos finais privados (pré-visualização)

Este artigo irá explicar pontos finais privados geridos na Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Pontos finais privados geridos

Os pontos finais privados geridos são pontos finais privados criados no espaço de trabalho Gerido VNet estabelecendo uma ligação privada aos recursos Azure. A Azure Synapse gere estes pontos finais privados em seu nome.

Azure Synapse suporta ligações privadas. O link privado permite-lhe aceder aos serviços Azure (como o Azure Storage, Azure Cosmos DB e Azure SQL Data Warehouse) e o Azure acolheu de forma segura serviços de clientes/parceiros do seu Azure VNet.

Quando utiliza uma ligação privada, o tráfego entre o VNet e o espaço de trabalho atravessa inteiramente a rede de espinha dorsal da Microsoft. A Private Link protege contra riscos de exfiltração de dados. Estabelece uma ligação privada a um recurso criando um ponto final privado.

O ponto final privado utiliza um endereço IP privado do seu VNet para efetivamente trazer o serviço para o seu VNet. Os pontos finais privados são mapeados para um recurso específico em Azure e não todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado pela sua organização. Saiba mais sobre [links privados e pontos finais privados.](https://docs.microsoft.com/azure/private-link/)

>[!IMPORTANT]
>Os pontos finais privados geridos só são suportados em espaços de trabalho Azure Synapse com um Espaço de Trabalho Gerido VNet.

>[!NOTE]
>Todo o tráfego de saída do espaço de trabalho Gerido VNet, exceto através de pontos finais privados geridos, será bloqueado no futuro. Recomenda-se que crie pontos finais privados geridos para se conectar a todas as fontes de dados do Azure externas ao espaço de trabalho. 

Uma ligação de ponto final privado é criada em um estado "Pendente" quando você cria um ponto final privado gerido em Azure Synapse. É iniciado um fluxo de trabalho de aprovação. O proprietário de recursos de ligação privada é responsável por aprovar ou rejeitar a ligação.

Se o proprietário aprovar a ligação, o link privado é estabelecido. Caso contrário, a ligação privada não será estabelecida. Em qualquer dos casos, o ponto final privado gerido será atualizado com o estado da ligação.

Apenas um ponto final privado gerido num estado aprovado pode enviar tráfego para um determinado recurso de ligação privada.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Pontos finais privados geridos para piscina SQL e SQL a pedido

A piscina SQL e a procura a pedido da SQL são capacidades analíticas no seu espaço de trabalho Azure Synapse. Estas capacidades utilizam infraestruturas multi-inquilinos que não são implantadas no espaço de [trabalho Gerido VNet](./synapse-workspace-managed-vnet.md).

Quando um espaço de trabalho é criado, o Azure Synapse cria dois pontos finais privados geridos para piscina SQL e SQL on-demand nesse espaço de trabalho. 

Estes dois pontos finais privados geridos estão listados no Estúdio Azure Synapse. Selecione **Gerir** na navegação à esquerda e, em seguida, selecione **Redes Virtuais Geridas** para ver então no Estúdio.

O ponto final privado gerido que visa a piscina SQL é chamado *synapse-ws-sql-- \< workspacename \> * e aquele que visa a SQL on-demand é chamado *synapse-ws-sqlOnDemand-- \< workspacename \> *.
![Pontos finais privados geridos para piscina SQL e SQL a pedido](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Estes dois pontos finais privados geridos são automaticamente criados para si quando criar o seu espaço de trabalho Azure Synapse. Não é cobrado por estes dois pontos finais privados geridos.

## <a name="next-steps"></a>Próximos passos

[Crie pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
