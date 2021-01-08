---
title: Criar um Azure Stream Analytics Cluster quickstart
description: Aprenda a criar um cluster Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: eb36af5b3f1e142405b24ade16516d0268b4ab2f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016563"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Quickstart: Criar um cluster Azure Stream Analytics dedicado usando o portal Azure

Utilize o portal Azure para criar um cluster Azure Stream Analytics. Um [cluster Stream Analytics](cluster-overview.md) é uma implantação de um único inquilino que pode ser usada para casos complexos e exigentes de uso de streaming. Você pode executar vários trabalhos stream Analytics em um cluster Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Quickstart: Criar um trabalho stream Analytics utilizando o portal Azure](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Criar um cluster Stream Analytics

Nesta secção, cria-se um recurso de cluster Stream Analytics.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**. Na caixa de pesquisa *do Marketplace,* escreva e selecione **o cluster Stream Analytics**. Em seguida, selecione **Adicionar**.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Stream Analytics resultado de pesquisa de cluster.":::

1. Na página de **cluster Create Stream Analytics,** insira as definições básicas para o seu novo cluster.

   |Definição|Valor|Descrição |
   |---|---|---|
   |Subscrição|Nome da subscrição|Selecione a subscrição Azure que pretende utilizar para este cluster Stream Analytics. |
   |Grupo de Recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo** e, em seguida, introduza um nome exclusivo para o novo grupo de recursos. |
   |Nome do Cluster|Um nome exclusivo|Insira um nome para identificar o seu cluster Stream Analytics.|
   |Localização|A região mais próxima das suas fontes de dados e pias|Selecione uma localização geográfica para hospedar o seu cluster Stream Analytics. Utilize a localização mais próxima das suas fontes de dados e pias para análises de baixa latência.|
   |Capacidade da Unidade de Streaming|36 a 216 |Determine o tamanho do cluster estimando quantos trabalhos stream Analytics você planeia executar e o total de SUs que o trabalho vai exigir. Pode começar com 36 SUs e, mais tarde, escalar para cima ou para baixo, conforme necessário.|

   ![Criar cluster](./media/create-cluster/create-cluster.png)

1. Selecione **Rever + criar**. Pode saltar as secções **Tags.**

1. Reveja as definições do cluster e, em seguida, **selecione Criar**. A criação de clusters é uma operação de longa duração e pode levar aproximadamente 60 minutos para ser concluída. Aguarde até que a página do portal apresente **A implementação está concluída**. Entretanto, pode criar e desenvolver [trabalhos stream Analytics](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) que queira executar neste cluster se ainda não o fez.

1. Selecione **Ir para o recurso** para ir à página de cluster Stream Analytics.

## <a name="delete-your-cluster"></a>Elimine o seu cluster

Pode eliminar o seu cluster Stream Analytics se não pretender executar quaisquer trabalhos stream Analytics nele. Elimine o seu cluster seguindo os passos no portal Azure:

1. Vá a **stream analytics trabalhos** em **Definições** e pare todos os trabalhos que estão em execução.

1. Vá ao **resumo** do seu agrupamento. **Selecione Eliminar** e seguir as instruções para eliminar o seu cluster.

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, aprendeu a criar um cluster Azure Stream Analytics. Avance para o próximo artigo para aprender a executar um trabalho stream Analytics no seu cluster:

> [!div class="nextstepaction"]
> [Gerir trabalhos stream analytics em um cluster Stream Analytics](manage-jobs-cluster.md)
