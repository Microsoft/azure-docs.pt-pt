---
title: Configurar regras de firewall IP
description: Um artigo que o ensina a configurar as regras de firewall IP em Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 12966503bfa17030fc4ebcb4d790a2f4655142a2
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116539"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Regras de firewall IP Azure Synapse Analytics

Este artigo irá explicar as regras de firewall IP e ensiná-lo a configurá-las em Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Regras de firewall IP

As regras de firewall de IP concedem ou negam o acesso à área de trabalho do Synapse com base no endereço IP de origem de cada pedido. Pode configurar as regras de firewall de IP para a área de trabalho. As regras de firewall IP configuradas ao nível do espaço de trabalho aplicam-se a todos os pontos finais públicos do espaço de trabalho (piscinas SQL dedicadas, piscina SQL sem servidor e desenvolvimento).

## <a name="create-and-manage-ip-firewall-rules"></a>Criar e gerir regras de firewall de IP

Existem duas formas de as regras de firewall IP serem adicionadas a um espaço de trabalho synapse. Para adicionar uma firewall IP ao seu espaço de trabalho, selecione **Segurança + networking** e verifique **Permitir ligações de todos os endereços IP** durante a criação do espaço de trabalho.

![Screenshot que realça o botão De Segurança + rede.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![A configuração IP do portal Azure Synapse.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

Também pode adicionar regras de firewall IP a um espaço de trabalho synapse após a criação do espaço de trabalho. Selecione **Firewalls** sob **segurança** do portal Azure. Para adicionar uma nova regra de firewall IP, dê-lhe um nome, Start IP e End IP. Selecione **Guardar** quando terminar.

![Configuração IP do espaço de trabalho Azure Synapse no portal Azure.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Ligar ao Synapse a partir da própria rede

Pode ligar-se ao seu espaço de trabalho Synapse utilizando o Synapse Studio. Também pode utilizar o SQL Server Management Studio (SSMS) para se conectar aos recursos SQL (piscinas SQL dedicadas e piscina SQL sem servidor) no seu espaço de trabalho.

Certifique-se de que a firewall da sua rede e computador local permite a comunicação de saída nas portas TCP 80, 443 e 1443 para o Synapse Studio.

Além disso, você precisa permitir a comunicação de saída na porta UDP 53 para o Synapse Studio. Para ligar utilizando ferramentas como SSMS e Power BI, deve permitir a comunicação de saída na porta TCP 1433.

Se estiver a utilizar a definição de política de ligação de redirecionamento predefinido, poderá ter de permitir a comunicação de saída em portas adicionais. Pode saber mais sobre as políticas de ligação [aqui.](../../azure-sql/database/connectivity-architecture.md#connection-policy)

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho Azure Synapse](../quickstart-create-workspace.md)

Criar um espaço de trabalho Azure Synapse com uma [Rede Virtual de espaço de trabalho gerido](./synapse-workspace-managed-vnet.md)