---
title: Criar e eliminar empregos num cluster Azure Stream Analytics
description: Saiba como gerir os trabalhos do Stream Analytics num cluster Azure Stream Analytics
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 09a67d11ac4daf3e87a50ee2171f1ca49060c5bf
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018127"
---
# <a name="add-and-remove-jobs-in-an-azure-stream-analytics-cluster"></a>Adicione e remova os empregos num cluster Azure Stream Analytics

Você pode executar vários trabalhos Azure Stream Analytics em um cluster Stream Analytics. Gerir postos de trabalho num cluster é um processo simples de 2 passos: adicionar o trabalho ao cluster e iniciar o trabalho. Este artigo mostra-lhe como adicionar e remover empregos de um cluster existente. Siga o quickstart para [criar um cluster Stream Analytics](create-cluster.md) se ainda não tiver um.

## <a name="add-a-stream-analytics-job-to-a-cluster"></a>Adicione um trabalho stream Analytics a um cluster

Apenas os trabalhos stream analytics existentes podem ser adicionados aos clusters. Siga o quickstart [para aprender a criar um emprego](stream-analytics-quick-create-portal.md) usando o portal Azure. Uma vez que tenha um trabalho que queira adicionar a um cluster, use os seguintes passos para adicionar o trabalho ao seu cluster.

1. No portal Azure, localize e selecione o seu cluster Stream Analytics.

1. Em **Definições**, selecione **stream analytics jobs**. Em seguida, **selecione Adicionar o trabalho existente**.

1. Selecione a subscrição e o trabalho stream Analytics que pretende adicionar ao cluster. Apenas os trabalhos stream Analytics que estão na mesma região que o cluster podem ser adicionados ao cluster.

   ![Adicionar trabalho ao cluster](./media/manage-jobs-cluster/add-job.png)

1. Depois de ter adicionado o trabalho ao cluster, navegue para o recurso de trabalho e [inicie o trabalho.](start-job.md#azure-portal) O trabalho começará então a funcionar no seu agrupamento.

Pode fazer todas as outras operações, tais como monitoramento, alerta e registos de diagnóstico, a partir da página de recursos de trabalho stream Analytics.

## <a name="remove-a-stream-analytics-job-from-a-cluster"></a>Remova um trabalho de Stream Analytics de um cluster

Os trabalhos de Stream Analytics devem estar num estado parado antes de poderem ser removidos do aglomerado. Se o seu trabalho ainda estiver a decorrer, pare o trabalho antes de passar para os seguintes passos.

1. Localize e selecione o seu cluster Stream Analytics.

1. Em **Definições**, selecione **stream analytics jobs**.

1. Selecione os trabalhos que pretende remover do cluster e, em seguida, selecione **Remover**.

   ![remover o trabalho do cluster](./media/manage-jobs-cluster/remove-job.png)

   Quando um trabalho é removido de um cluster Stream Analytics, ele retorna ao ambiente padrão multi-inquilino.

## <a name="next-steps"></a>Próximos passos

Agora sabe como adicionar e remover empregos no seu cluster Azure Stream Analytics. Em seguida, pode aprender a gerir pontos finais privados e escalar os seus clusters:

* [Escalar um cluster Azure Stream Analytics](scale-cluster.md)
* [Gerir pontos finais privados num cluster Azure Stream Analytics](private-endpoints.md)
