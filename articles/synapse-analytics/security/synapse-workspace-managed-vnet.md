---
title: Rede virtual gerida no Azure Synapse Analytics
description: Um artigo que explica a rede virtual gerida na Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 162d96244b01f8c5e1acf224475aadb9508f0aa5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423636"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Rede Virtual Gerida por Análise Synapse Azure (pré-visualização)

Este artigo irá explicar a Rede Virtual Gerida na Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>Espaço de trabalho gerido VNet

Quando criar o seu espaço de trabalho Azure Synapse, pode optar por associá-lo a um VNet. O VNet associado ao seu espaço de trabalho é gerido pela Azure Synapse. Este VNet é chamado de *VNet gerido.*

O espaço de trabalho gerido VNet proporciona-lhe valor de quatro maneiras:

- Com um VNet gerido pode descarregar o fardo de gerir o VNet para Azure Synapse.
- Não é preciso configurar as regras de entrada de NSG nos seus próprios VNets para permitir que o tráfego de gestão azure Synapse entre no seu VNet. A configuração errada destas regras de NSG causa perturbação do serviço para os clientes.
- Não precisas de criar uma sub-rede para os teus aglomerados de Faíscas com base na carga máxima.
- O espaço de trabalho gerido VNet juntamente com pontos finais privados geridos protege contra a exfiltração de dados. Só pode criar pontos finais privados geridos num espaço de trabalho que tenha um VNet gerido associado a ele.

Criar um espaço de trabalho com um VNet gerido associado a ele garante que o seu espaço de trabalho é uma rede isolada de outros espaços de trabalho. O Azure Synapse fornece várias capacidades analíticas num espaço de trabalho: Integração de dados, Apache Spark, piscina SQL e SQL a pedido.

Se o seu espaço de trabalho tiver um VNet de espaço de trabalho gerido, a integração de dados e os recursos spark são implantados nele. Um VNet gerido também fornece isolamento ao nível do utilizador para atividades spark porque cada cluster Spark está na sua própria sub-rede.

A piscina SQL e a procura de SQL são capacidades multi-inquilinos e, portanto, residem fora do espaço de trabalho Gerido VNet. Comunicação intra-workspace para piscina SQL e sQL utiliza links privados Azure. Estes links privados são automaticamente criados para si quando cria um espaço de trabalho com um VNet gerido associado a ele.

>[!IMPORTANT]
>Não é possível alterar esta configuração do espaço de trabalho após a criação do espaço de trabalho. Por exemplo, não é possível reconfigurar um espaço de trabalho que não tenha um VNet gerido associado a ele e associar-lhe um VNet. Da mesma forma, não é possível reconfigurar um espaço de trabalho com um VNet gerido associado a ele e dissociar o VNet do mesmo.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Crie um espaço de trabalho Azure Synapse com um VNet gerido

Para criar um espaço de trabalho Azure Synapse que tenha um VNet gerido associado a ele, selecione o separador **de rede Security +** no portal Azure e verifique a caixa de verificação de rede virtual gerida **enable.**

Se deixar a caixa de verificação desmarcada, o seu espaço de trabalho não terá uma VNet associada a ela.

>[!IMPORTANT]
>Você só pode usar links privados em um espaço de trabalho que tem um VNet de espaço de trabalho gerido.

![Ativar o espaço de trabalho gerido VNet](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Todo o tráfego de saída do espaço de trabalho Gerido VNet será bloqueado no futuro. Recomenda-se que se conectem a todas as suas fontes de dados utilizando pontos finais privados geridos.

Pode verificar se o seu espaço de trabalho Azure Synapse está associado a um VNet gerido, selecionando **a visão geral** do portal Azure.

![Visão geral do espaço de trabalho no portal Azure](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho Azure Synapse](../quickstart-create-workspace.md)

Saiba mais sobre [pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Crie pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
