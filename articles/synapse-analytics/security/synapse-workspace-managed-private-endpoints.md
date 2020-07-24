---
title: Pontos finais privados geridos
description: Um artigo que explica pontos finais privados geridos no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ecca67cab486c8f3524c8c8d4c221d52689cf62a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070110"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Sinapse Gerido pontos finais privados (pré-visualização)

Este artigo explicará os pontos finais privados geridos na Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Pontos finais privados geridos

Os pontos finais privados geridos são pontos finais privados criados no espaço de trabalho gerido Microsoft Azure Virtual Network estabelecendo uma ligação privada com os recursos Azure. Azure Synapse gere estes pontos finais privados em seu nome.

Azure Synapse suporta ligações privadas. O link privado permite-lhe aceder aos serviços Azure (como a Azure Storage, Azure Cosmos DB e Azure SQL Data Warehouse) e a Azure acolheu serviços de cliente/parceiro da sua Rede Virtual Azure de forma segura.

Quando utiliza uma ligação privada, o tráfego entre a sua Rede Virtual e o espaço de trabalho atravessa inteiramente a rede de espinha dorsal da Microsoft. A Private Link protege contra riscos de exfiltração de dados. Estabelece-se uma ligação privada a um recurso criando um ponto final privado.

O ponto final privado utiliza um endereço IP privado da sua Rede Virtual para efetivamente trazer o serviço para a sua Rede Virtual. Os pontos finais privados são mapeados para um recurso específico em Azure e não em todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado pela sua organização. Saiba mais sobre [links privados e pontos finais privados.](https://docs.microsoft.com/azure/private-link/)

>[!IMPORTANT]
>Os pontos finais privados geridos são suportados apenas em espaços de trabalho Azure Synapse com uma Rede Virtual de espaço de trabalho gerido.

>[!NOTE]
>Todo o tráfego de saída da Rede Virtual do espaço de trabalho gerido, exceto através de pontos finais privados geridos, será bloqueado no futuro. Recomenda-se que crie pontos finais privados geridos para ligar a todas as fontes de dados do Azure externas ao espaço de trabalho. 

Uma ligação de ponto final privado é criada num estado "pendente" quando cria um ponto final privado gerido em Azure Synapse. É iniciado um fluxo de trabalho de aprovação. O proprietário do recurso de ligação privada é responsável por aprovar ou rejeitar a ligação.

Se o proprietário aprovar a ligação, o link privado é estabelecido. Caso contrário, o elo privado não será estabelecido. Em qualquer dos casos, o ponto final privado gerido será atualizado com o estado da ligação.

Apenas um ponto final privado gerido num estado aprovado pode enviar tráfego para um determinado recurso de ligação privada.

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>Pontos finais privados geridos para piscina SQL e SQL a pedido

A piscina SQL e o SQL a pedido são capacidades analíticas no seu espaço de trabalho Azure Synapse. Estas capacidades utilizam infraestruturas multi-arrendatários que não são implantadas na [Rede Virtual do espaço de trabalho gerido.](./synapse-workspace-managed-vnet.md)

Quando um espaço de trabalho é criado, a Azure Synapse cria dois pontos finais privados geridos para a piscina SQL e SQL on-demand nesse espaço de trabalho. 

Estes dois pontos finais privados geridos estão listados no Azure Synapse Studio. **Selecione Gerir** na navegação esquerda e, em seguida, selecione **Redes Virtuais Geridas** para ver então no Estúdio.

O ponto final privado gerido que visa a piscina SQL *chama-se sinapse-ws-sql-- \<workspacename\> * e aquele que visa o SQL a pedido chama-se *sinapse-ws-sqlOnDemand-- \<workspacename\> *.
![Pontos finais privados geridos para piscina SQL e SQL a pedido](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Estes dois pontos finais privados geridos são automaticamente criados para si quando cria o seu espaço de trabalho Azure Synapse. Não és cobrado por estes dois pontos finais privados geridos.

## <a name="next-steps"></a>Passos seguintes

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
