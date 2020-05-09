---
title: Crie um ponto final privado gerido para se ligar aos resultados da fonte de dados.
description: Este artigo irá ensiná-lo a criar um ponto final privado gerido para as suas fontes de dados a partir de um espaço de trabalho Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: d6a0a84050cb8f1b3ad0c04aa66c5b3797282f2e
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983232"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Crie um ponto final privado gerido para a sua fonte de dados (pré-visualização)

Este artigo irá ensiná-lo a criar um ponto final privado gerido para a sua fonte de dados em Azure. Consulte [pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passo 1: Abra o seu espaço de trabalho Azure Synapse no portal Azure

Pode criar um ponto final privado gerido para a sua fonte de dados do Azure Synapse Studio. Selecione o separador **Overview** no portal Azure e selecione **Launch Synapse Studio**.
![Lançar Estúdio Azure Synapse](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Passo 2: Navegar para o separador Redes Virtuais Geridas no Estúdio Synapse

No Estúdio Azure Synapse, selecione o separador **Manage** a partir da navegação à esquerda. Selecione **Redes Virtuais Geridas** e, em seguida, selecione **+ Nova**.
![Criar um novo ponto final privado gerido](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Passo 3: Selecione o tipo de fonte de dados

Selecione o tipo de fonte de dados. Neste caso, a fonte de dados alvo é uma conta ADLS Gen2. Selecione **Continuar**.
![Selecione um tipo de fonte de dados-alvo](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Passo 4: Introduza informações sobre a fonte de dados

Na janela seguinte, introduza informação sobre a fonte de dados. Neste exemplo, estamos a criar um ponto final privado gerido para uma conta ADLS Gen2. Introduza um **nome** para o ponto final privado gerido. Forneça uma **assinatura Azure** e um nome de **conta de armazenamento.** Selecione **Criar**.
![Introduza detalhes de origem de dados-alvo](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Passo 5: Verifique se o seu ponto final privado gerido foi criado com sucesso

Depois de submeter o pedido, verá o seu estado. Para verificar se foi criada a criação bem sucedida do seu ponto final privado gerido, verifique o seu *Estado de Provisionamento.* Pode ter de esperar 1 minuto e selecionar **Refresh** para atualizar o estado de provisionamento. Pode ver que o ponto final privado gerido para a conta ADLS Gen2 foi criado com sucesso.

Pode também ver que o Estado de *Aprovação* está *pendente.* O proprietário do recurso-alvo pode aprovar ou negar o pedido de ligação de ponto final privado. Se o proprietário aprovar o pedido de ligação de ponto final privado, então é estabelecido um link privado. Se for negado, então não é estabelecido um elo privado.
![Estatuto de pedido de criação de pontofinal privado gerido](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)