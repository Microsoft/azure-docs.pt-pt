---
title: 'Estúdio ML (clássico): Migrar para Azure Machine Learning - Consumir pontos finais de gasoduto'
description: Integrar pontos finais de pipeline com aplicações de clientes em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565499"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Consumir pontos finais de gasodutos a partir de aplicações de clientes

Neste artigo, aprende-se a integrar as aplicações dos clientes com os pontos finais Azure Machine Learning. Para obter mais informações sobre a escrita do código de aplicação, consulte [Consumir um ponto final de aprendizagem de máquinas Azure](../how-to-consume-web-service.md).

Este artigo faz parte da série de migração Studio (clássico) para Azure Machine Learning. Para obter mais informações sobre a migração para a Azure Machine Learning, consulte [o artigo visão geral da migração.](migrate-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma área de trabalho do Azure Machine Learning. Criar um espaço de trabalho para [aprendizagem automática Azure.](../how-to-manage-workspace.md#create-a-workspace)
- Um ponto final de aprendizagem da [máquina Azure em tempo real ou ponto final do gasoduto](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Consumir um ponto final em tempo real 

Se implementou o seu modelo como um ponto final em **tempo real,** pode encontrar o seu ponto final REST e código de consumo pré-gerado em C#, Python e R:

1. Vá ao estúdio Azure Machine Learning[(ml.azure.com).](https://ml.azure.com)
1. Vá ao **separador Endpoints.**
1. Selecione o seu ponto final em tempo real.
1. Selecione **Consumir**.

> [!NOTE]
> Também pode encontrar a especificação Swagger para o seu ponto final no separador **Detalhes.** Use a definição de Swagger para entender o seu esquema de ponto final. Para obter mais informações sobre a definição de Swagger, consulte [a documentação oficial da Swagger.](https://swagger.io/docs/specification/2-0/what-is-swagger/)


## <a name="consume-a-pipeline-endpoint"></a>Consumir um ponto final de gasoduto

Há duas formas de consumir um ponto final do gasoduto:

- Rest API chama
- Integração com a Azure Data Factory

### <a name="use-rest-api-calls"></a>Use chamadas API REST

Ligue para o ponto final DO REST a partir da sua aplicação ao cliente. Pode utilizar a especificação Swagger para o seu ponto final para entender o seu esquema:

1. Vá ao estúdio Azure Machine Learning[(ml.azure.com).](https://ml.azure.com)
1. Vá ao **separador Endpoints.**
1. Selecione **pontos finais do Pipeline**.
1. Selecione o seu ponto final do gasoduto.
1. No painel **de visão geral** do ponto final do pipeline, selecione o link na **documentação do ponto de terminação REST**.

### <a name="use-azure-data-factory"></a>Utilize o Azure Data Factory

Pode chamar o seu pipeline de Aprendizagem automática Azure como um passo num oleoduto Azure Data Factory. Para obter mais informações, consulte [executar os oleodutos de aprendizagem automática Azure na Azure Data Factory](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a encontrar esquemas e códigos de amostra para os seus pontos finais do pipeline. Para obter mais informações sobre o consumo de pontos finais da aplicação do cliente, consulte [Consumir um ponto final de aprendizagem de máquinas Azure.](../how-to-consume-web-service.md)

Veja o resto dos artigos na série de migração Azure Machine Learning: 
1. [Visão geral da migração](migrate-overview.md).
1. [Conjunto de dados migrar](migrate-register-dataset.md).
1. [Reconstruir um pipeline de treino studio (clássico).](migrate-rebuild-experiment.md)
1. [Reconstruir um serviço web Studio (clássico).](migrate-rebuild-web-service.md)
1. **Integre um serviço web Azure Machine Learning com aplicações para clientes.**
1. [Migrar Executar O Guião R](migrate-execute-r-script.md).