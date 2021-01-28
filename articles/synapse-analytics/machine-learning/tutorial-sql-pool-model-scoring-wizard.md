---
title: 'Tutorial: Assistente de pontuação de modelo de machine learning para piscinas SQL dedicadas'
description: Tutorial para como usar o assistente de pontuação do modelo de machine learning para enriquecer dados em piscinas SQL dedicadas.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935231"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Tutorial: Assistente de pontuação de modelo de machine learning (pré-visualização) para piscinas SQL dedicadas

Aprenda a enriquecer facilmente os seus dados em piscinas SQL dedicadas com modelos preditivos de aprendizagem automática. Os modelos que os seus cientistas de dados criam são agora facilmente acessíveis aos profissionais de dados para análise preditiva. Um profissional de dados em Azure Synapse Analytics pode simplesmente selecionar um modelo do registo do modelo Azure Machine Learning para implantação em piscinas Azure Synapse SQL e lançar previsões para enriquecer os dados.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> - Treine um modelo preditivo de aprendizagem automática e registe o modelo no registo do modelo Azure Machine Learning.
> - Utilize o assistente de pontuação SQL para lançar previsões numa piscina SQL dedicada.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Azure Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento Azure Data Lake Gen2 configurada como o armazenamento padrão. Você precisa ser o colaborador de *dados blob* de armazenamento do sistema de ficheiros Data Lake Storage Gen2 com o que trabalha.
- Piscina SQL dedicada no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [criar uma piscina SQL dedicada.](../quickstart-create-sql-pool-studio.md)
- Serviço de aprendizagem automática Azure no seu espaço de trabalho Azure Synapse Analytics. Para mais detalhes, consulte [criar um serviço ligado à Aprendizagem automática Azure em Azure Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Treine um modelo em Azure Machine Learning

Antes de começar, verifique se a sua versão de sklearn é de 0.20.3.

Antes de executar todas as células no caderno, verifique se a instância de computação está em funcionamento.

![Screenshot que mostra a verificação do cálculo Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Vá ao seu espaço de trabalho de Aprendizagem de Máquinas Azure.

1. Baixar [Previsivelmente Dicas de Táxi NYC.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Abra o espaço de trabalho Azure Machine Learning no [Azure Machine Learning Studio.](https://ml.azure.com)

1. Vá a ficheiros de upload **de**  >  **cadernos.** Em seguida, selecione o ficheiro **Predict NYC Taxi Tips.ipynb** que descarregou e faça o upload.
   ![Screenshot do botão para carregar um ficheiro.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Depois de o caderno ser carregado e aberto, selecione **Executar todas as células**.

   Uma das células pode falhar e pedir-lhe para autenticar para Azure. Observe isto nas saídas das células e autença no seu navegador seguindo o link e introduzindo o código. Em seguida, re-executar o caderno.

1. O caderno irá treinar um modelo ONNX e registá-lo com mLflow. Vá a **Models** para verificar se o novo modelo está devidamente registado.
   ![Screenshot que mostra o modelo no registo.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. A execução do caderno também exportará os dados do teste para um ficheiro CSV. Descarregue o ficheiro CSV para o seu sistema local. Mais tarde, importará o ficheiro CSV num pool de SQL dedicado e utilizará os dados para testar o modelo.

   O ficheiro CSV é criado na mesma pasta que o seu ficheiro de portátil. Selecione **Refresh** in File Explorer se não o vir imediatamente.

   ![Screenshot que mostra o ficheiro C S V.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Previsões de lançamento com o assistente de pontuação SQL

1. Abra o espaço de trabalho Azure Synapse com o Synapse Studio.

1. Aceda às contas de armazenamento  >  **ligadas aos**  >  **dados.** Faça o upload `test_data.csv` para a conta de armazenamento predefinido.

   ![Screenshot que mostra seleções para o upload de dados.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Vá para **desenvolver**  >  **scripts SQL**. Crie um novo script SQL para carregar `test_data.csv` na sua piscina SQL dedicada.

   > [!NOTE]
   > Atualize o URL de ficheiro neste script antes de executá-lo.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Carregar dados para piscina SQL dedicada](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Ir ao **Espaço**  >  **de Trabalho de Dados.** Abra o assistente de pontuação SQL clicando à direita na tabela de bilhar SQL dedicada. Selecione **Machine Learning**  >  **Enrich com o modelo existente.**

   > [!NOTE]
   > A opção de machine learning não aparece a menos que tenha um serviço ligado criado para a Azure Machine Learning. (Ver [Pré-requisitos](#prerequisites) no início deste tutorial.)

   ![Screenshot que mostra a opção de aprendizagem automática.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selecione um espaço de trabalho de aprendizagem de máquina azure ligado na caixa de entrega. Este passo carrega uma lista de modelos de machine learning a partir do registo modelo do espaço de trabalho de Aprendizagem de Máquinas Azure escolhido. Atualmente, apenas os modelos ONNX são suportados, pelo que este passo irá apresentar apenas os modelos ONNX.

1. Selecione o modelo que acabou de treinar e, em seguida, **selecione Continue**.

   ![Screenshot que mostra a seleção do modelo Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Mapear as colunas de tabela para as entradas do modelo e especificar as saídas do modelo. Se o modelo for guardado no formato MLflow e a assinatura do modelo for povoada, o mapeamento será feito automaticamente para si utilizando uma lógica baseada na semelhança dos nomes. A interface também suporta mapeamento manual.

   Selecione **Continuar**.

   ![Screenshot que mostra mapeamento de mesa para modelo.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. O código T-SQL gerado está embrulhado dentro de um procedimento armazenado. É por isso que precisa fornecer um nome de procedimento armazenado. O modelo binário, incluindo metadados (versão, descrição e outras informações), será fisicamente copiado do Azure Machine Learning para uma mesa de bilhar SQL dedicada. Por isso, tens de especificar em que mesa para guardar o modelo. 

   Pode escolher a **tabela existente** ou **criar nova.** Quando terminar, selecione **Implementar o modelo + script aberto** para implementar o modelo e gerar um script de previsão T-SQL.

   ![Screenshot que mostra seleções para criar um procedimento armazenado.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Depois de gerar o script, selecione **Executar** para executar a pontuação e obter previsões.

   ![Screenshot que mostra pontuação e previsões.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Próximos passos

- [Quickstart: Criar um novo serviço Azure Machine Learning ligado em Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics](what-is-machine-learning.md)
