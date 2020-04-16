---
title: Ligue-se a um espaço de trabalho Azure Synapse utilizando links privados
description: Este artigo irá ensiná-lo a conectar-se ao seu espaço de trabalho Azure Synapse usando links privados
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432193"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Ligue-se ao seu espaço de trabalho Azure Synapse utilizando links privados (pré-visualização)

Este artigo irá ensiná-lo a criar um ponto final privado para o seu espaço de trabalho Azure Synapse. Consulte [links privados e pontos finais privados](https://docs.microsoft.com/azure/private-link/) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passo 1: Abra o seu espaço de trabalho Azure Synapse no portal Azure

Selecione **a ligação de ponto final privado** sob **segurança** e, em seguida, selecione + ponto **final privado**.
![Espaço de trabalho Open Azure Synapse no portal Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Passo 2: Selecione os detalhes da sua subscrição e da região

Sob o separador **Basics** na **janela Criar um ponto final privado,** escolha o seu Grupo de **Subscrição** e **Recursos**. Dê um **nome** ao ponto final privado que quer criar. Selecione a **Região** onde pretende que seja criado o ponto final privado.

Os pontos finais privados são criados numa subnet. A subscrição, o grupo de recursos e a região selecionada filtram as subredes de ponto final privado. Selecione **Seguinte: Recursos >** quando feito.
![Selecione detalhes de subscrição e região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Passo 3: Selecione os detalhes do espaço de trabalho Azure Synapse

Selecione **Connect a um recurso Azure no meu diretório** no separador **Recurso.** Selecione a **Subscrição** que contém o seu espaço de trabalho Azure Synapse. O **tipo de Recurso** para criar pontos finais privados para um espaço de trabalho Azure Synapse é *Microsoft.Synapse/workspaces*.

Selecione o seu espaço de trabalho Azure Synapse como **Recurso**. Cada espaço de trabalho Azure Synapse tem três **sub-recursos Target** que pode criar um ponto final privado para: Sql, SqlOnDemand e Dev.

Selecione **Seguinte: Configuração>** para avançar para a próxima parte da configuração.
![Selecione detalhes de subscrição e região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

No separador **Configuração,** selecione a **rede Virtual** e a **Subnet** na qual deve ser criado o ponto final privado. Também precisa de criar um registo DNS que mapeie para o ponto final privado.

Selecione **Sim** para **Integrar com a zona privada dNS** para integrar o seu ponto final privado com uma zona privada de DNS. Se não tiver uma zona privada de DNS associada ao seu VNet, então é criada uma nova zona privada de DNS. Selecione **Review + criar** quando feito.

![Selecione detalhes de subscrição e região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Quando a implementação estiver concluída, abra o seu espaço de trabalho Azure Synapse no portal Azure e selecione **ligações de ponto final privado**. O novo nome de ligação de ponto final privado e de ponto final privado associado ao ponto final privado é mostrado.

![Selecione detalhes de subscrição e região](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o espaço de [trabalho gerido VNet](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Crie pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
