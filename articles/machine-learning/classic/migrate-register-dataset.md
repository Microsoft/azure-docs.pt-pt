---
title: 'Estúdio ML (clássico): Migrar para Azure Machine Learning - Reconstruir conjunto de dados'
description: Conjuntos de dados do Estúdio De Reconstrução (clássico) em Azure Machine Learning designer
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565299"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrar um conjunto de dados (clássico) para Azure Machine Learning

Neste artigo, aprende-se a migrar um conjunto de dados studio (clássico) para a Azure Machine Learning. Para obter mais informações sobre a migração do Studio (clássico), consulte [o artigo de visão geral da migração.](migrate-overview.md)

Tem três opções para migrar um conjunto de dados para a Azure Machine Learning. Leia cada secção para determinar qual a melhor opção para o seu cenário.


|Onde estão os dados? | Opção de migração  |
|---------|---------|
|Em Studio (clássico)     |  Opção 1: [Descarregue o conjunto de dados do Studio (clássico) e faça o upload para Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Armazenamento na cloud     | Opção 2: [Registar um conjunto de dados a partir de uma fonte de nuvem](#import-data-from-cloud-sources). <br><br>  Opção 3: [Utilize o módulo de Dados de Importação para obter dados de uma fonte de nuvem](#import-data-from-cloud-sources).        |

> [!NOTE]
> O Azure Machine Learning também suporta [fluxos de trabalho de código](../how-to-create-register-datasets.md) para criar e gerir conjuntos de dados. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma área de trabalho do Azure Machine Learning. Criar um espaço de trabalho para [aprendizagem automática Azure.](../how-to-manage-workspace.md#create-a-workspace)
- Um conjunto de dados do Estúdio (clássico) para migrar.


## <a name="download-the-dataset-from-studio-classic"></a>Descarregue o conjunto de dados do Studio (clássico)

A forma mais simples de migrar um conjunto de dados studio (clássico) para a Azure Machine Learning é descarregar o seu conjunto de dados e registá-lo no Azure Machine Learning. Isto cria uma nova cópia do seu conjunto de dados e envia-o para uma loja de dados Azure Machine Learning.

Pode baixar diretamente os seguintes tipos de conjunto de dados Studio (clássicos).

* Texto simples (.txt)
* Valores separados por vírgula (CSV) com um cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por separados por separados (TSV) com um cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo Excel
* Arquivo zip (.zip)

Para descarregar conjuntos de dados diretamente:
1. Vá ao seu espaço de trabalho (clássico) (). [https://studio.azureml.net](https://studio.azureml.net)
1. Na barra de navegação esquerda, selecione o **separador Datasets.**
1. Selecione o(s) conjunto de dados(s) que pretende descarregar.
1. Na barra de ação inferior, selecione **Baixar**.

    ![Screenshot mostrando como descarregar um conjunto de dados em Studio (clássico)](./media/migrate-register-dataset/download-dataset.png)

Para os seguintes tipos de dados, deve utilizar o módulo **Converte para CSV** para descarregar conjuntos de dados.

* Dados SVMLight (.svmlight) 
* Atributo Dados do Formato de Ficheiro de Relação (ARFF) (.arff) 
* Ficheiro de objeto r ou espaço de trabalho (. RData)
* Tipo de conjunto de dados (.dados). O tipo de conjunto de dados é o tipo de dados interno studio (clássico) para a saída do módulo.

Para converter o seu conjunto de dados num CSV e fazer o download dos resultados:

1. Vá ao seu espaço de trabalho (clássico) (). [https://studio.azureml.net](https://studio.azureml.net)
1. Criar uma nova experiência.
1. Arraste e deixe cair o conjunto de dados que pretende transferir para a tela.
1. Adicione um converse ao módulo **CSV.**
1. Ligue a porta de entrada **Converte-CSV** à porta de saída do seu conjunto de dados.
1. Execute a experimentação.
1. Clique com o botão direito no módulo **Converte para CSV.**
1. Selecione **o conjunto de dados de**  >  **resultados Descarregar**.

    ![Screenshot mostrando como configurar um convertido para o pipeline CSV](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Faça o upload do seu conjunto de dados para Azure Machine Learning

Depois de descarregar o ficheiro de dados, pode registar o conjunto de dados no Azure Machine Learning:

1. Vá ao estúdio Azure Machine Learning[(ml.azure.com).](https://ml.azure.com)
1. No painel de navegação esquerdo, selecione o **separador Datasets.**
1. Selecione Criar conjunto de **dados**  >  **A partir de ficheiros locais**.
    ![Screenshot mostrando o separador conjuntos de dados e o botão para criar um ficheiro local](./media/migrate-register-dataset/register-dataset.png)
1. Insira um nome e descrição.
1. Para **o tipo de conjunto de dados,** selecione **Tabular**.

    > [!NOTE]
    > Também pode carregar ficheiros ZIP como conjuntos de dados. Para fazer o upload de um ficheiro ZIP, selecione **Ficheiro** para **o tipo dataset**.

1. **Para a datastore e seleção de ficheiros,** selecione a datastore para onde pretende fazer o upload do seu ficheiro dataset para.

    Por predefinição, o Azure Machine Learning armazena o conjunto de dados na blobstore do espaço de trabalho predefinido. Para obter mais informações sobre as datastores, consulte [o Connect to storage services](../how-to-access-data.md).

1. Defina as definições e esquema de análise de dados para o seu conjunto de dados. Em seguida, confirme as suas definições.

## <a name="import-data-from-cloud-sources"></a>Dados de importação de fontes de nuvem

Se os seus dados já estiverem num serviço de armazenamento em nuvem, e pretende manter os seus dados na sua localização nativa. Pode utilizar qualquer uma das seguintes opções:

|Método de ingestão|Description|
|---| --- |
|Registar um conjunto de dados de aprendizagem de máquinas Azure|Ingerir dados de fontes de dados locais e online (Blob, ADLS Gen1, ADLS Gen2, File share, SQL DB). <br><br>Cria uma referência à fonte de dados, que é preguiçosamente avaliada em tempo de execução. Utilize esta opção se aceder repetidamente a este conjunto de dados e pretender ativar funcionalidades avançadas de dados como a versão e monitorização de dados.
|Módulo de Dados de Importação|Ingerir dados de fontes de dados online (Blob, ADLS Gen1, ADLS Gen2, File share, SQL DB). <br><br> O conjunto de dados é importado apenas para o atual pipeline de design.


>[!Note]
> Os utilizadores do estúdio (clássico) devem notar que as seguintes fontes de nuvem não são suportadas de forma nativa na Azure Machine Learning:
> - Consulta de Colmeia
> - Tabela do Azure
> - Azure Cosmos DB
> - Base de Dados SQL no local
>
> Recomendamos que os utilizadores migram os seus dados para um serviço de armazenamento suportado utilizando a Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Registar um conjunto de dados de aprendizagem de máquinas Azure

Utilize os seguintes passos para registar um conjunto de dados para Azure Machine Learning a partir de um serviço de nuvem: 

1. [Crie uma loja de dados,](../how-to-connect-data-ui.md#create-datastores)que ligue o serviço de armazenamento em nuvem ao seu espaço de trabalho Azure Machine Learning. 

1. [Registar um conjunto de dados](../how-to-connect-data-ui.md#create-datasets). Se estiver a migrar um conjunto de dados Studio (clássico), selecione a definição de conjunto de dados **Tabular.**

Depois de registar um conjunto de dados no Azure Machine Learning, pode usá-lo no designer:
 
1. Crie um novo projeto de pipeline de design.
1. Na paleta do módulo à esquerda, expanda a secção **Datasets.**
1. Arraste o conjunto de dados registado para a tela. 

### <a name="use-the-import-data-module"></a>Utilize o módulo de dados de importação

Utilize os seguintes passos para importar dados diretamente para o seu pipeline de designer:

1. [Crie uma loja de dados,](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)que ligue o serviço de armazenamento em nuvem ao seu espaço de trabalho Azure Machine Learning. 

Depois de criar a datastore, pode utilizar o módulo [**de Dados de Importação**](../algorithm-module-reference/import-data.md) no designer para ingerir dados a partir dele:

1. Crie um novo projeto de pipeline de design.
1. Na paleta de módulos à esquerda, encontre o módulo **de Dados de Importação** e arraste-o para a tela.
1. Selecione o módulo **Dados de Importação** e utilize as definições no painel direito para configurar a sua fonte de dados.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a migrar um conjunto de dados studio (clássico) para a Azure Machine Learning. O próximo passo é [reconstruir um pipeline de treino studio (clássico).](migrate-rebuild-experiment.md)


Veja os outros artigos na série de migração Studio (clássica):

1. [Visão geral da migração](migrate-overview.md).
1. **Migrar conjuntos de dados**.
1. [Reconstruir um pipeline de treino studio (clássico).](migrate-rebuild-experiment.md)
1. [Reconstruir um serviço web Studio (clássico).](migrate-rebuild-web-service.md)
1. [Integre um serviço web Azure Machine Learning com aplicações para clientes.](migrate-rebuild-integrate-with-client-app.md)
1. [Migrar Executar O Guião R](migrate-execute-r-script.md).
