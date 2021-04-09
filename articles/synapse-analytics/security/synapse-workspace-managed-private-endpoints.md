---
title: Pontos finais privados geridos
description: Um artigo que explica pontos finais privados geridos no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 65794c695fa4b36586b23a308845b1f12a20b7cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98569951"
---
# <a name="synapse-managed-private-endpoints"></a>Sinapse Geridos pontos finais privados

Este artigo explicará os pontos finais privados geridos na Azure Synapse Analytics.

## <a name="managed-private-endpoints"></a>Pontos finais privados geridos

Os pontos finais privados geridos são pontos finais privados criados numa Rede Virtual Gerida associada ao seu espaço de trabalho Azure Synapse. Os pontos finais privados geridos estabelecem uma ligação privada com os recursos da Azure. Azure Synapse gere estes pontos finais privados em seu nome. Pode criar pontos finais privados geridos a partir do seu espaço de trabalho Azure Synapse para aceder aos serviços Azure (como Azure Storage ou Azure Cosmos DB) e serviços de cliente/parceiro hospedados pela Azure.

Quando utiliza pontos finais privados geridos, o tráfego entre o seu espaço de trabalho Azure Synapse e outros recursos Azure atravessa inteiramente a rede de espinha dorsal da Microsoft. Os pontos finais privados geridos protegem contra a exfiltração de dados. Um ponto final privado gerido utiliza o endereço IP privado da sua Rede Virtual Gerida para efetivamente trazer o serviço Azure que o seu espaço de trabalho Azure Synapse está a comunicar na sua Rede Virtual. Os pontos finais privados geridos são mapeados para um recurso específico em Azure e não em todo o serviço. Os clientes podem limitar a conectividade a um recurso específico aprovado pela sua organização. 

Saiba mais sobre [links privados e pontos finais privados.](../../private-link/index.yml)

>[!IMPORTANT]
>Os pontos finais privados geridos são suportados apenas em espaços de trabalho Azure Synapse com uma Rede Virtual de espaço de trabalho gerido.

>[!NOTE]
>Ao criar um espaço de trabalho Azure Synapse, pode optar por associar uma Rede Virtual Gerida ao mesmo. Se optar por ter uma Rede Virtual Gerida associada ao seu espaço de trabalho, também pode optar por limitar o tráfego de saída do seu espaço de trabalho a apenas alvos aprovados. Tem de criar pontos finais privados geridos para estes alvos. 


Uma ligação de ponto final privado é criada num estado "pendente" quando cria um ponto final privado gerido em Azure Synapse. Um fluxo de trabalho de aprovação é iniciado. O proprietário do recurso de ligação privada é responsável por aprovar ou rejeitar a ligação. Se o proprietário aprovar a ligação, o link privado é estabelecido. Mas, se o proprietário não aprovar a ligação, então o link privado não será estabelecido. Em qualquer dos casos, o ponto final privado gerido será atualizado com o estado da ligação. Apenas um ponto final privado gerido num estado aprovado pode ser utilizado para enviar tráfego para o recurso de ligação privada que está ligado ao ponto final privado gerido.

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>Pontos finais privados geridos para piscina SQL dedicada e piscina SQL sem servidor

Piscina SQL dedicada e piscina SQL sem servidor são capacidades analíticas no seu espaço de trabalho Azure Synapse. Estas capacidades utilizam infraestruturas multi-arrendatários que não são implantadas na [Rede Virtual do espaço de trabalho gerido.](./synapse-workspace-managed-vnet.md)

Quando um espaço de trabalho é criado, a Azure Synapse cria dois pontos finais privados geridos no espaço de trabalho, um para piscina SQL dedicada e outro para piscina SQL sem servidor. 

Estes dois pontos finais privados geridos estão listados no Synapse Studio. **Selecione Gerir** na navegação esquerda e, em seguida, selecione **Pontos finais privados geridos** para vê-los no Estúdio.

O ponto final privado gerido que visa a piscina SQL *chama-se sinapse-ws-sql-- \<workspacename\>* e aquele que visa a piscina SQL sem servidor é chamado *de sinapse-ws-sqlOnDemand-- \<workspacename\>*.

![Pontos finais privados geridos para piscina SQL dedicada e piscina SQL sem servidor](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

Estes dois pontos finais privados geridos são automaticamente criados para si quando cria o seu espaço de trabalho Azure Synapse. Não és cobrado por estes dois pontos finais privados geridos.

## <a name="next-steps"></a>Passos seguintes

Para saber mais, avance para os [pontos finais privados Create Managed para o seu artigo Fontes de dados.](./how-to-create-managed-private-endpoints.md)