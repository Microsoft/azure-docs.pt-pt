---
title: Configure regras de firewall IP no Azure Synapse Analytics
description: Um artigo que o ensina a configurar regras de firewall IP no Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 1dfac9a56e3bc299fbb0651ae1cd5644eba83267
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83645821"
---
# <a name="azure-synapse-analytics-ip-firewall-rules-preview"></a>Regras de firewall IP azure Synapse Analytics (pré-visualização)

Este artigo irá explicar as regras de firewall IP e ensiná-lo a configurá-las no Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Regras de firewall IP

As regras de firewall IP concedem ou negam o acesso ao seu espaço de trabalho Synapse com base no endereço IP originário de cada pedido. Pode configurar as regras de firewall IP para o seu espaço de trabalho. As regras de firewall IP configuradas a nível do espaço de trabalho aplicam-se a todos os pontos finais públicos do espaço de trabalho (piscinas SQL, SQL on-demand e Desenvolvimento).

## <a name="create-and-manage-ip-firewall-rules"></a>Criar e gerir regras de firewall IP

Há duas maneiras de as regras de firewall IP serem adicionadas a um espaço de trabalho Synapse. Para adicionar uma firewall IP ao seu espaço de trabalho, selecione **Security + networking** e verifique **Permitir ligações de todos os endereços IP** durante a criação do espaço de trabalho.

![Configuração IP do espaço de trabalho do portal Azure Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Configuração IP do espaço de trabalho do portal Azure Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Também pode adicionar regras de firewall IP a um espaço de trabalho Synapse após a criação do espaço de trabalho. Selecione **Firewalls** sob **segurança** do portal Azure. Para adicionar uma nova regra de firewall IP, dê-lhe um nome, Iniciar IP e End IP. Selecione **Guardar** quando terminar.

![Configuração IP do espaço de trabalho Azure Synapse no portal Azure.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connecting-to-synapse-from-your-own-network"></a>Ligação a Synapse a partir da sua própria rede

Pode ligar-se ao seu espaço de trabalho Synapse usando o Estúdio Synapse. Também pode utilizar o SQL Server Management Studio (SSMS) para se ligar aos recursos SQL (piscinas SQL e SQL on-demand) no seu espaço de trabalho.

Certifique-se de que a firewall na sua rede e computador local permite a comunicação de saída nas portas TCP 80, 443 e 1443 para o Estúdio Synapse.

Além disso, você precisa permitir comunicação de saída na porta UDP 53 para O Estúdio Synapse. Para ligar utilizando ferramentas como SSMS e Power BI, deve permitir a comunicação de saída na porta TCP 1433.

Se estiver a utilizar a definição de política de ligação redirecionamento padrão, poderá ter de permitir a comunicação de saída em portas adicionais. Pode saber mais sobre as políticas de conexão [aqui.](https://docs.microsoft.com/azure/sql-database/sql-database-connectivity-architecture#connection-policy)

## <a name="next-steps"></a>Próximos passos

Criar um espaço de [trabalho Azure Synapse](../quickstart-create-workspace.md)

Crie um espaço de trabalho Azure Synapse com um [VNet gerido](./synapse-workspace-managed-vnet.md)