---
title: Rede virtual gerida em Azure Synapse Analytics
description: Um artigo que explica a rede virtual gerida no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b46ce6f6164479853bc762cb1ca45d67f7f80930
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2020
ms.locfileid: "85194370"
---
# <a name="azure-synapse-analytics-managed-virtual-network-preview"></a>Azure Synapse Analytics Rede Virtual Gerida (pré-visualização)

Este artigo explicará a Rede Virtual Gerida no Azure Synapse Analytics.

## <a name="managed-workspace-vnet"></a>VNet de espaço de trabalho gerido

Ao criar o seu espaço de trabalho Azure Synapse, pode optar por associá-lo a um VNet. O VNet associado ao seu espaço de trabalho é gerido pela Azure Synapse. Este VNet é chamado de *VNet de espaço de trabalho gerido*.

O espaço de trabalho gerido VNet proporciona-lhe valor de quatro formas:

- Com um VNet de espaço de trabalho gerido pode descarregar o fardo de gerir o VNet para Azure Synapse.
- Não é necessário configurar as regras NSG de entrada nos seus próprios VNets para permitir que o tráfego de gestão da Azure Synapse entre no seu VNet. A configuração errada destas regras NSG causa perturbação do serviço para os clientes.
- Não é necessário criar uma sub-rede para os seus clusters Spark com base na carga máxima.
- O VNet do espaço de trabalho gerido juntamente com os pontos finais privados geridos protegem contra a exfiltração de dados. Só é possível criar pontos finais privados geridos num espaço de trabalho que tenha um VNet de espaço de trabalho gerido associado a ele.

Criar um espaço de trabalho com um VNet de espaço de trabalho gerido associado a ele garante que o seu espaço de trabalho está isolado de outros espaços de trabalho. A Azure Synapse oferece várias capacidades analíticas num espaço de trabalho: Integração de dados, Apache Spark, piscina SQL e SQL on demand.

Se o seu espaço de trabalho tiver um VNet de espaço de trabalho gerido, a integração de dados e os recursos de Spark são implantados nele. Um VNet de espaço de trabalho gerido também fornece isolamento ao nível do utilizador para atividades spark porque cada cluster Spark está na sua própria sub-rede.

A piscina SQL e o SQL a pedido são capacidades multi-arrendatários e, portanto, residem fora do espaço de trabalho gerido VNet. Comunicação intra-workspace para piscina SQL e SQL on-demand use links privados Azure. Estas ligações privadas são criadas automaticamente para si quando cria um espaço de trabalho com um VNet de espaço de trabalho gerido associado a ele.

>[!IMPORTANT]
>Não é possível alterar esta configuração do espaço de trabalho após a criação do espaço de trabalho. Por exemplo, não é possível reconfigurar um espaço de trabalho que não tenha um VNet de espaço de trabalho gerido associado a ele e associar-lhe um VNet. Da mesma forma, não é possível reconfigurar um espaço de trabalho com um VNet de espaço de trabalho gerido associado a ele e dissociar o VNet do mesmo.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-vnet"></a>Criar um espaço de trabalho Azure Synapse com um VNet de espaço de trabalho gerido

Se ainda não o fez, registe o fornecedor de recursos da Rede. O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. Escolha *o Microsoft.Network* na lista de fornecedores de recursos quando se [registar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Para criar um espaço de trabalho Azure Synapse que tenha um VNet de espaço de trabalho gerido associado a ele, selecione o **separador Segurança + rede** no portal Azure e verifique a caixa de verificação de rede virtual gerida **enable.**

Se deixar a caixa de verificação desmarcada, o seu espaço de trabalho não terá um VNet associado.

>[!IMPORTANT]
>Só pode utilizar links privados num espaço de trabalho que tenha um VNet de espaço de trabalho gerido.

![Ativar o espaço de trabalho gerido VNet](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)

>[!NOTE]
>Todo o tráfego de saída do VNet do espaço de trabalho gerido, exceto através de pontos finais privados geridos, será bloqueado no futuro. Recomenda-se que crie pontos finais privados geridos para ligar a todas as fontes de dados do Azure externas ao espaço de trabalho. 

Pode verificar se o seu espaço de trabalho Azure Synapse está associado a um VNet de espaço de trabalho gerido selecionando a **Visão Geral** do portal Azure.

![Visão geral do espaço de trabalho no portal Azure](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho Azure Synapse](../quickstart-create-workspace.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
