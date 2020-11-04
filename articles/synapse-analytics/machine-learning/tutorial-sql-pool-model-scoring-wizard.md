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
ms.openlocfilehash: f5c5edc067b3f7b525fd129462c48ca50fdafc8f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314042"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-dedicated-sql-pools"></a>Tutorial: Assistente de pontuação de modelo de machine learning para piscinas SQL dedicadas

Aprenda a enriquecer facilmente os seus dados em piscinas SQL dedicadas com modelos preditivos de aprendizagem automática.  Os modelos que os seus cientistas de dados criam são agora facilmente acessíveis aos profissionais de dados para análise preditiva. Um profissional de dados em Synapse pode simplesmente selecionar um modelo do registo do modelo Azure Machine Learning para implantação em piscinas SQL synapse e lançar previsões para enriquecer os dados.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> - Treine um modelo preditivo de aprendizagem automática e registe o modelo no registo de modelos Azure Machine Learning
> - Use o assistente de pontuação SQL para lançar previsões em piscina SQL dedicada

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- [Espaço de trabalho Synapse Analytics](../get-started-create-workspace.md) com uma conta de armazenamento ADLS Gen2 configurada como o armazenamento padrão. Tem de ser o colaborador de dados da **Blob** de Armazenamento do sistema de ficheiros ADLS Gen2 com o que trabalha.
- Piscina SQL dedicada no seu espaço de trabalho Synapse Analytics. Para mais detalhes, consulte [criar uma piscina SQL dedicada.](../quickstart-create-sql-pool-studio.md)
- Serviço de aprendizagem automática Azure no seu espaço de trabalho Synapse Analytics. Para mais detalhes, consulte [criar um serviço de aprendizagem automática Azure em Synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/)

## <a name="train-a-model-in-azure-machine-learning"></a>Treine um modelo em Azure Machine Learning

Antes de começar, verifique se a sua versão **de sklearn** é de 0.20.3.

Antes de executar todas as células no caderno, verifique se a instância de computação está em funcionamento.

![Verificar o cálculo da AML](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Navegue para o seu espaço de trabalho de aprendizagem de máquinas Azure.

1. Baixar [Previsivelmente Dicas de Táxi NYC.ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Lançar o espaço de trabalho Azure Machine Learning no [estúdio Azure Machine Learning.](https://ml.azure.com)

1. Vá a **Cadernos** e clique em **Carregar ficheiros,** selecione "Predict NYC Taxi Tips.ipynb" que descarregou e carreja o ficheiro.
   ![Carregar o ficheiro](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Depois de o caderno ser carregado e aberto, clique em **Executar todas as células**.

   Uma das células pode falhar e pedir-lhe para autenticar para Azure. Fique de olho nas saídas das células e autença no seu navegador seguindo o link e introduzindo o código. Em seguida, re-executar o Caderno.

1. O portátil irá treinar um modelo ONNX e registá-lo com o MLFlow. Vá a **Models** para verificar se o novo modelo está devidamente registado.
   ![Modelo no registo](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. A execução do caderno também exportará os dados do teste para um ficheiro CSV. Descarregue o ficheiro CSV para o seu sistema local. Mais tarde, importará o ficheiro CSV para um pool DE SQL dedicado e utilizará os dados para testar o modelo.

   O ficheiro CSV é criado na mesma pasta que o seu ficheiro de portátil. Clique em "Refresh" no explorador de ficheiros se não o vir imediatamente.

   ![Ficheiro CSV](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Previsões de lançamento com assistente de pontuação SQL

1. Abra o espaço de trabalho da Sinapse com o Synapse Studio.

1. Navegue **Data** para contas de armazenamento  ->  **ligadas a**  ->  **dados.** Faça o upload `test_data.csv` para a conta de armazenamento predefinido.

   ![Carregar dados](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Vá para **desenvolver**  ->  **scripts SQL**. Crie um novo script SQL para carregar `test_data.csv` na sua piscina SQL dedicada.

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

1. Ir ao **Espaço**  ->  **de Trabalho de Dados.** Abra o assistente de pontuação SQL clicando à direita na tabela de bilhar SQL dedicada. Selecione **Machine Learning**  ->  **Enrich com o modelo existente.**

   > [!NOTE]
   > A opção de machine learning não aparece a menos que tenha um serviço ligado criado para a Azure Machine Learning (ver **Pré-requisitos** no início deste tutorial).

   ![Opção de aprendizagem automática](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Selecione um espaço de trabalho de aprendizagem de máquina azure ligado na caixa de entrega. Isto carrega uma lista de modelos de machine learning a partir do registo modelo do espaço de trabalho Azure Machine Learning escolhido. Atualmente, apenas os modelos ONNX são suportados, pelo que este irá apresentar apenas os modelos ONNX.

1. Selecione o modelo que acabou de treinar e clique em **Continuar**.

   ![Selecione modelo de aprendizagem automática Azure](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Em seguida, mapear as colunas de tabela para as entradas do modelo e especificar as saídas do modelo. Se o modelo for guardado no formato MLFlow e a assinatura do modelo for povoada, o mapeamento será feito automaticamente para si utilizando uma lógica baseada na semelhança dos nomes. A interface também suporta mapeamento manual.

   Clique em **Continue** (Continuar).

   ![Tabela para modelo de mapeamento](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. O código T-SQL gerado está envolto num Procedimento Armazenado. É por isso que precisa fornecer um nome para um procedimento armazenado. O modelo binário, incluindo metadados (versão, descrição, etc.) será fisicamente copiado do Azure Machine Learning para uma mesa de bilhar SQL dedicada. Por isso, tens de especificar em que mesa para guardar o modelo. Pode escolher "Use uma mesa existente" ou "Criar uma nova tabela". Uma vez feito, clique em **Implementar o modelo + editor aberto** para implementar o modelo e gerar um script de previsão T-SQL.

   ![Criar procedimento](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Assim que o script for gerado, clique em "Executar" para executar a pontuação e obter previsões.

   ![Previsões de execução](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Criar um novo serviço de aprendizagem automática Azure em Synapse](quickstart-integrate-azure-machine-learning.md)
- [Capacidades de aprendizagem automática em Azure Synapse Analytics (pré-visualização de espaços de trabalho)](what-is-machine-learning.md)
