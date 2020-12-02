---
title: Rede virtual gerida
description: Um artigo que explica a rede virtual gerida no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b605c2c9f3c7468d0fe1f2cccd8c3602c556714b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501078"
---
# <a name="azure-synapse-analytics-managed-virtual-network"></a>Azure Synapse Analytics Rede Virtual Gerida

Este artigo explicará a Rede Virtual Gerida no Azure Synapse Analytics.

## <a name="managed-workspace-virtual-network"></a>Rede Virtual de espaço de trabalho gerido

Quando criar o seu espaço de trabalho Azure Synapse, pode optar por associá-lo a uma Rede Virtual Microsoft Azure. A Rede Virtual associada ao seu espaço de trabalho é gerida pela Azure Synapse. Esta Rede Virtual é chamada de *Rede Virtual de espaço de trabalho gerido.*

O espaço de trabalho gerido A Rede Virtual proporciona-lhe valor de quatro formas:

- Com uma Rede Virtual gerida do espaço de trabalho pode descarregar o fardo de gerir a Rede Virtual para Azure Synapse.
- Não é necessário configurar as regras NSG de entrada nas suas próprias Redes Virtuais para permitir que o tráfego de gestão da Azure Synapse entre na sua Rede Virtual. A configuração errada destas regras NSG causa perturbação do serviço para os clientes.
- Não é necessário criar uma sub-rede para os seus clusters Spark com base na carga máxima.
- Rede Virtual gerida do espaço de trabalho juntamente com pontos finais privados geridos protege contra exfiltração de dados. Só é possível criar pontos finais privados geridos num espaço de trabalho que tenha uma Rede Virtual gerida associada a ele.

Criar um espaço de trabalho com uma Rede Virtual gerida associada a ele garante que o seu espaço de trabalho está isolado de outros espaços de trabalho. O Azure Synapse fornece várias capacidades analíticas num espaço de trabalho: integração de dados, piscina Apache Spark sem servidor, piscina SQL dedicada e piscina SQL sem servidor.

Se o seu espaço de trabalho tiver uma Rede Virtual gerida, a integração de dados e os recursos de Spark são implantados nele. Um espaço de trabalho gerido A Rede Virtual também fornece isolamento ao nível do utilizador para atividades spark porque cada cluster Spark está na sua própria sub-rede.

Piscina SQL dedicada e piscina SQL sem servidor são capacidades multi-inquilinos e, portanto, residem fora da Rede Virtual do espaço de trabalho gerido. A comunicação intra-workspace para piscina SQL dedicada e piscina SQL sem servidor usam links privados Azure. Estas ligações privadas são criadas automaticamente para si quando cria um espaço de trabalho com uma Rede Virtual gerida associada ao mesmo.

>[!IMPORTANT]
>Não é possível alterar esta configuração do espaço de trabalho após a criação do espaço de trabalho. Por exemplo, não é possível reconfigurar um espaço de trabalho que não tenha uma Rede Virtual gerida associada a ele e associem-lhe uma Rede Virtual. Da mesma forma, não é possível reconfigurar um espaço de trabalho com uma Rede Virtual gerida associada ao mesmo e dissociar a Rede Virtual a partir dele.

## <a name="create-an-azure-synapse-workspace-with-a-managed-workspace-virtual-network"></a>Criar um espaço de trabalho Azure Synapse com uma Rede Virtual de espaço de trabalho gerido

Se ainda não o fez, registe o fornecedor de recursos da Rede. O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. Escolha *o Microsoft.Network* na lista de fornecedores de recursos quando se [registar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Para criar um espaço de trabalho Azure Synapse que tenha uma Rede Virtual gerida do espaço de trabalho associada a ele, selecione o **separador de Rede** no portal Azure e verifique a caixa de verificação **de rede virtual gerida enable.**

Se deixar a caixa de verificação desmarcada, o seu espaço de trabalho não terá uma Rede Virtual associada a ela.

>[!IMPORTANT]
>Só pode utilizar links privados num espaço de trabalho que tenha uma Rede Virtual gerida.

![Ativar rede virtual de espaço de trabalho gerido](./media/synapse-workspace-managed-vnet/enable-managed-vnet-1.png)


Pode verificar se o seu espaço de trabalho Azure Synapse está associado a uma Rede Virtual de espaço de trabalho gerido selecionando a **Visão Geral** do portal Azure.

![Visão geral do espaço de trabalho no portal Azure](./media/synapse-workspace-managed-vnet/enable-managed-vnet-2.png)

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho Azure Synapse](../quickstart-create-workspace.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
