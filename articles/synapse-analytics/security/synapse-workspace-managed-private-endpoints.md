---
title: Pontos finais privados geridos
description: Um artigo que explica pontos finais privados geridos no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5258b1eab48f71d8d17f52849b5e57b467e7a2da
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460376"
---
# <a name="synapse-managed-private-endpoints"></a>Sinapse Geridos pontos finais privados

Este artigo explicará os pontos finais privados geridos na Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Pontos finais privados geridos

Os pontos finais privados geridos são pontos finais privados criados no espaço de trabalho gerido Microsoft Azure Virtual Network estabelecendo uma ligação privada com os recursos Azure. Azure Synapse gere estes pontos finais privados em seu nome.

Azure Synapse suporta ligações privadas. O link privado permite-lhe aceder aos serviços Azure (como a Azure Storage e a Azure Cosmos DB) e à Azure acolheu serviços de cliente/parceiro da sua Rede Virtual Azure de forma segura.

Quando utiliza uma ligação privada, o tráfego entre a sua Rede Virtual e o espaço de trabalho atravessa inteiramente a rede de espinha dorsal da Microsoft. A Private Link protege contra riscos de exfiltração de dados. Estabelece-se uma ligação privada a um recurso criando um ponto final privado.

O ponto final privado utiliza um endereço IP privado da sua Rede Virtual para efetivamente trazer o serviço para a sua Rede Virtual. Os pontos finais privados são mapeados para um recurso específico em Azure e não em todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado pela sua organização. 

Saiba mais sobre [links privados e pontos finais privados.](https://docs.microsoft.com/azure/private-link/)

>[!IMPORTANT]
>Os pontos finais privados geridos são suportados apenas em espaços de trabalho Azure Synapse com uma Rede Virtual de espaço de trabalho gerido.

>[!NOTE]
>Todo o tráfego de saída da Rede Virtual do espaço de trabalho gerido, exceto através de pontos finais privados geridos, será bloqueado no futuro. Recomenda-se que crie pontos finais privados geridos para ligar a todas as fontes de dados do Azure externas ao espaço de trabalho. 

Uma ligação de ponto final privado é criada num estado "pendente" quando cria um ponto final privado gerido em Azure Synapse. Um fluxo de trabalho de aprovação é iniciado. O proprietário do recurso de ligação privada é responsável por aprovar ou rejeitar a ligação.

Se o proprietário aprovar a ligação, o link privado é estabelecido. Mas, se o proprietário não aprovar a ligação, então o link privado não será estabelecido. Em qualquer dos casos, o ponto final privado gerido será atualizado com o estado da ligação.

Apenas um ponto final privado gerido num estado aprovado pode enviar tráfego para um determinado recurso de ligação privada.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Pontos finais privados geridos para piscina SQL dedicada e piscina SQL sem servidor

Piscina SQL dedicada e piscina SQL sem servidor são capacidades analíticas no seu espaço de trabalho Azure Synapse. Estas capacidades utilizam infraestruturas multi-arrendatários que não são implantadas na [Rede Virtual do espaço de trabalho gerido.](./synapse-workspace-managed-vnet.md)

Quando um espaço de trabalho é criado, a Azure Synapse cria dois pontos finais privados geridos no espaço de trabalho, um para piscina SQL dedicada e outro para piscina SQL sem servidor. 

Estes dois pontos finais privados geridos estão listados no Synapse Studio. **Selecione Gerir** na navegação esquerda e, em seguida, selecione **Pontos finais privados geridos** para vê-los no Estúdio.

O ponto final privado gerido que visa a piscina SQL *chama-se sinapse-ws-sql-- \<workspacename\>* e aquele que visa a piscina SQL sem servidor é chamado *de sinapse-ws-sqlOnDemand-- \<workspacename\>*.

![Pontos finais privados geridos para piscina SQL dedicada e piscina SQL sem servidor](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Estes dois pontos finais privados geridos são automaticamente criados para si quando cria o seu espaço de trabalho Azure Synapse. Não és cobrado por estes dois pontos finais privados geridos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, avance para os [pontos finais privados Create Managed para o seu artigo Fontes de dados.](./how-to-create-managed-private-endpoints.md)
