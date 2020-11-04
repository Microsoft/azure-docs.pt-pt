---
title: Conecte-se a um espaço de trabalho synapse usando links privados
description: Este artigo irá ensiná-lo a conectar-se ao seu espaço de trabalho Azure Synapse usando links privados
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee915346a93a600ea352be42dc14e0464525c025
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312257"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Conecte-se ao seu espaço de trabalho Azure Synapse utilizando links privados (pré-visualização)

Este artigo irá ensiná-lo a criar um ponto final privado para o seu espaço de trabalho Azure Synapse. Consulte [links privados e pontos finais privados](https://docs.microsoft.com/azure/private-link/) para saber mais.

## <a name="step-1-register-network-resource-provider"></a>Passo 1: Fornecedor de recursos da Rede de Registo

Se ainda não o fez, registe o fornecedor de recursos da Rede. O registo de um fornecedor de recursos configura a sua subscrição para trabalhar com o fornecedor de recursos. Escolha *o Microsoft.Network* na lista de fornecedores de recursos quando se [registar](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types). Se o fornecedor de recursos da Rede já estiver registado, então dirijam-se ao passo 2.

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Passo 2: Abra o seu espaço de trabalho Azure Synapse no portal Azure

Selecione **a ligação de ponto final privado** sob **Segurança**. 
![Abra espaço de trabalho da Azure Synapse no portal Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

No ecrã seguinte selecione **+ Ponto final privado**.

![Open Private endpoint no portal Azure](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Passo 3: Selecione os detalhes da sua subscrição e da região

No **separador Básicos** na janela **Criar um ponto final privado,** escolha o seu Grupo de **Subscrição** e **Recursos.** Dê um **nome** ao ponto final privado que pretende criar. Selecione a **Região** onde pretende que o ponto final privado seja criado.

Os pontos finais privados são criados numa sub-rede. A subscrição, o grupo de recursos e a região selecionada filtram as sub-redes de pontos finais privados. Selecione **Seguinte: >de recursos** quando terminar.
![Selecione detalhes de subscrição e região 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Passo 4: Selecione os seus detalhes do espaço de trabalho Azure Synapse

Selecione **Connect a um recurso Azure no meu diretório** no **separador Recursos.** Selecione a **Subscrição** que contém o seu espaço de trabalho Azure Synapse. O **tipo de recurso** para criar pontos finais privados para um espaço de trabalho Azure Synapse é *microsoft.Synapse/workspaces*.

Selecione o seu espaço de trabalho Azure Synapse como **o Recurso**. Cada espaço de trabalho da Azure Synapse tem três **sub-recursos target** para o seguinte: Sql, SqlOnDemand e Dev.

Selecione **Seguinte: Configuração>** avançar para a próxima parte da configuração.
![Selecione detalhes de subscrição e região 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

No **separador Configuração,** selecione a **rede Virtual** e a **Sub-rede** na qual o ponto final privado deve ser criado. Também precisa de criar um registo DNS que mapeia para o ponto final privado.

Selecione **Sim** para **Integrar com a zona privada de DNS** para integrar o seu ponto final privado com uma zona privada de DNS. Se não tiver uma zona de DNS privada associada à sua Rede Virtual Microsoft Azure, então é criada uma nova zona privada de DNS. Selecione **'Rever + criar'** quando for feito.

![Selecione detalhes de subscrição e região 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Quando a implementação estiver concluída, abra o seu espaço de trabalho Azure Synapse no portal Azure e selecione **ligações privatepoint**. São apresentados os novos nomes de ligação privados e de ponto final associados ao ponto final privado.

![Selecione detalhes de subscrição e região 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [Rede Virtual gerida do espaço de trabalho](./synapse-workspace-managed-vnet.md)

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)

[Criar pontos finais privados geridos para as suas fontes de dados](./how-to-create-managed-private-endpoints.md)
