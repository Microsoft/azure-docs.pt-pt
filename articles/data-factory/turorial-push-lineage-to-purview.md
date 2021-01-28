---
title: Emitir dados de linhagem do Data Factory para o Azure Purview
description: Saiba como empurrar os dados da linhagem data factory para a Azure Purview
services: data-factory
ms.author: lle
author: lrtoyou1223
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: e87a9d677fee94d410099db1da80a56b5539048c
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935456"
---
# <a name="push-data-factory-lineage-data-to-azure-purview-preview"></a>Empurre os dados da linhagem da Data Factory para a Azure Purview (Pré-visualização)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Neste tutorial, você usará a interface de utilizador da Data Factory (UI) para criar um pipeline que execute atividades e reporte dados de linhagem para a conta Azure Purview. Em seguida, pode ver todas as informações de linhagem na sua conta Azure Purview.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura Azure**. Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.
* **Fábrica de Dados Azure.** Se não tiver uma Fábrica de Dados Azure, consulte [Criar uma Fábrica de Dados Azure](./quickstart-create-data-factory-portal.md).
* **Conta Azure Purview**. A conta 'Purview' captura todos os dados de linhagem gerados pela fábrica de dados. Se não tiver uma conta Azure Purview, consulte [Create a Azure Purview](../purview/create-catalog-portal.md).


## <a name="run-data-factory-activities-and-push-lineage-data-to-azure-purview"></a>Executar atividades na Fábrica de Dados e empurrar dados de linhagem para a Azure Purview
### <a name="step-1--connect-data-factory-to-your-purview-account"></a>Passo 1: Ligue a Fábrica de Dados à sua conta Desemorça
Faça login na sua conta Purview no portal Purview, vá ao **Centro de Gestão**. Escolha **a Data Factory** em **ligações externas** e clique em **Novo** botão para criar uma ligação a uma nova Fábrica de Dados. 
[![Screenshot para criar uma ligação entre a Data Factory e a conta ](./media/data-factory-purview/connect-adf-to-purview.png) Purview ](./media/data-factory-purview/connect-adf-to-purview.png#lightbox)

Na página popup, pode escolher a Data Factory que pretende ligar a esta conta Purview. 
![Screenshot para uma nova ligação](./media/data-factory-purview/new-adf-purview-connection.png)

Pode verificar o estado depois de criar a ligação. **Ligado** significa que a ligação entre a Data Factory e esta Purga está ligada com sucesso. 
> [!NOTE]
> É necessário atribuir-lhe qualquer uma das funções abaixo na conta Purview e na **função de contribuinte da** Fábrica de Dados para criar a ligação entre a Data Factory e a Azure Purview.
> - Proprietário
> - Administrador de Acesso dos Utilizadores

### <a name="step-2-run-copy-and-dataflow-activities-in-data-factory"></a>Passo 2: Executar as atividades de Cópia e Fluxo de Dados na Fábrica de Dados
Pode criar atividades de pipelines, copy e dataflow na Data Factory. Não precisa de nenhuma configuração adicional para a captura de dados de linhagem. Os dados da linhagem serão automaticamente capturados durante a execução das atividades.
![Screenshot da atividade copy and dataflow ](./media/data-factory-purview/adf-activities-for-lineage.png) Se não souber como criar atividades de Copy e Dataflow, consulte [os dados de cópia do armazenamento de Azure Blob para uma base de dados na Base de Dados Azure SQL utilizando a Azure Data Factory](./tutorial-copy-data-portal.md) e [Transforme dados utilizando fluxos de dados de mapeamento](./tutorial-data-flow.md).

### <a name="step-3-run-execute-ssis-package-activities-in-data-factory"></a>Passo 3: Executar executar atividades de pacote SSIS na Fábrica de Dados
Pode criar oleodutos, executar atividades de pacote SSIS na Data Factory. Não precisa de nenhuma configuração adicional para a captura de dados de linhagem. Os dados da linhagem serão automaticamente capturados durante a execução das atividades.
![Screenshot da atividade do Pacote SSIS executo](./media/data-factory-purview/ssis-activities-for-lineage.png)

Se não souber como criar atividades do Pacote SSIS Execute, consulte [os Pacotes SSIS executados em Azure.](./tutorial-deploy-ssis-packages-azure.md)

### <a name="step-4-view-lineage-information-in-your-purview-account"></a>Passo 4: Ver informações de linhagem na sua conta Purview
Volte para a sua Conta de Visão. Na página inicial, **selecione Browse assets**. Escolha o ativo que deseja e clique no separador Linhagem. Verá toda a informação de linhagem.
[![Screenshot da conta ](./media/data-factory-purview/view-dataset.png) Depview ](./media/data-factory-purview/view-dataset.png#lightbox)

Pode ver os dados de linhagem para a atividade copy.
[![Screenshot da linhagem ](./media/data-factory-purview/copy-lineage.png) copy ](./media/data-factory-purview/copy-lineage.png#lightbox)

Também pode ver dados de linhagem para a atividade do Dataflow.
[![Screenshot da linhagem ](./media/data-factory-purview/dataflow-lineage.png) dataflow ](./media/data-factory-purview/dataflow-lineage.png#lightbox)

> [!NOTE] 
> Para a linhagem da atividade do Dataflow, apenas apoiamos a fonte e a pia. A linhagem para a transformação do Dataflow ainda não é suportada.

Também pode ver dados de linhagem para executar a atividade do Pacote SSIS.
[![Screenshot da linhagem ](./media/data-factory-purview/ssis-lineage.png) SSIS ](./media/data-factory-purview/ssis-lineage.png#lightbox)

> [!NOTE] 
> Para a linhagem da atividade do Pacote SSIS Executo, apenas apoiamos a fonte e o destino. A linhagem para a transformação ainda não está apoiada.

## <a name="next-steps"></a>Próximos passos
[Guia do utilizador da linhagem do catálogo](../purview/catalog-lineage-user-guide.md)

[Ligue a Fábrica de Dados à Azure Purview](connect-data-factory-to-azure-purview.md)