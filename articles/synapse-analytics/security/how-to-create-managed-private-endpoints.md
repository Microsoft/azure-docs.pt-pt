---
title: Crie um ponto final privado gerido para ligar aos resultados da sua fonte de dados
description: Este artigo irá ensiná-lo a criar um ponto final privado gerido para as suas fontes de dados a partir de um espaço de trabalho Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: fae958542f1bc391f285104d80d1e19131470abe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065478"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Crie um ponto final privado gerido para a sua fonte de dados (pré-visualização)

Este artigo irá ensiná-lo a criar um ponto final privado gerido para a sua fonte de dados em Azure. Consulte [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md) para saber mais.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Passo 1: Abra o seu espaço de trabalho Azure Synapse no portal Azure

Pode criar um ponto final privado gerido para a sua fonte de dados a partir do Azure Synapse Studio. Selecione o **separador Visão Geral** no portal Azure e selecione **Launch Synapse Studio**.
![Lançamento Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Passo 2: Navegue para o separador Redes Virtuais Geridos no Estúdio Synapse

No Azure Synapse Studio, selecione o **separador Gerir** a partir da navegação à esquerda. Selecione **Redes Virtuais Geridas** e, em seguida, selecione **+ Novo**.
![Criar um novo ponto final privado gerido](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Passo 3: Selecione o tipo de fonte de dados

Selecione o tipo de fonte de dados. Neste caso, a fonte de dados-alvo é uma conta ADLS Gen2. Selecione **Continuar**.
![Selecione um tipo de fonte de dados-alvo](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Passo 4: Introduzir informações sobre a fonte de dados

Na janela seguinte, insira informação sobre a fonte de dados. Neste exemplo, estamos a criar um ponto final privado gerido para uma conta ADLS Gen2. Insira um **nome** para o ponto final privado gerido. Forneça uma **subscrição Azure** e um **nome de conta de Armazenamento.** Selecione **Criar**.
![Insira detalhes da fonte de dados-alvo](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Passo 5: Verifique se o seu ponto final privado gerido foi criado com sucesso

Depois de submeter o pedido, verá o seu estado. Para verificar o sucesso da criação do seu ponto final privado gerido, verifique o seu *Estado de Provisionamento*. Pode ter de esperar 1 minuto e selecionar **Refresh** para atualizar o estado de provisionamento. Pode ver que o ponto final privado gerido para a conta ADLS Gen2 foi criado com sucesso.

Pode também ver que o *Estado de Aprovação* está *pendente.* O proprietário do recurso-alvo pode aprovar ou negar o pedido de ligação de ponto final privado. Se o proprietário aprovar o pedido de ligação de ponto final privado, então é estabelecido um link privado. Se for negado, então não está estabelecida uma ligação privada.
![Estado do pedido de criação de ponto final privado gerido](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [os pontos finais privados geridos](./synapse-workspace-managed-private-endpoints.md)