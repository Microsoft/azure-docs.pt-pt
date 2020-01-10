---
title: Importar dados de formação
titleSuffix: ML Studio (classic) - Azure
description: Como importar seus dados para Azure Machine Learning Studio (clássico) de várias fontes de dados. Saiba quais tipos de dados e formatos de dados têm suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 95938b979a90766c7e50f2560cf72266e287bfb5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454697"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importe seus dados de treinamento para Azure Machine Learning Studio (clássico) de várias fontes de dados

Para usar seus próprios dados no Machine Learning Studio (clássico) para desenvolver e treinar uma solução de análise preditiva, você pode usar dados de: 

* **Arquivo local** -carregue dados locais antecipadamente a partir do disco rígido para criar um módulo de DataSet em seu espaço de trabalho
* **Fontes de dados online** – use o módulo [importar dados][import-data] para acessar dados de uma das várias fontes online enquanto o experimento está em execução
* **Teste de Machine Learning Studio (clássico)** -usar dados que foram salvos como um conjunto em Machine Learning Studio (clássico)
* [**Banco de dados SQL Server local**](use-data-from-an-on-premises-sql-server.md) – use os dados de um banco de SQL Server local sem precisar copiar os dados manualmente

> [!NOTE]
> Há vários conjuntos de dados de exemplo disponíveis no Machine Learning Studio (clássico) que você pode usar para dados de treinamento. Para obter informações sobre eles, consulte [usar os conjuntos de dados de exemplo em Azure Machine Learning Studio (clássico)](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparar dados

O Machine Learning Studio (clássico) foi projetado para trabalhar com dados retangulares ou tabulares, como dados de texto delimitados ou dados estruturados de um banco, embora em algumas circunstâncias os dados não retangulares possam ser usados.

É melhor se os dados forem relativamente limpos antes de você importá-los para o Studio (clássico). Por exemplo, você desejará cuidar de problemas como cadeias de caracteres sem aspas.

No entanto, há módulos disponíveis no estúdio (clássico) que habilitam alguma manipulação de dados em seu experimento após a importação dos dados. Dependendo dos algoritmos de aprendizado de máquina que você usará, talvez seja necessário decidir como você tratará os problemas estruturais de dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Procure na seção **transformação de dados** da paleta de módulo os módulos que executam essas funções.

Em qualquer ponto do experimento, você pode exibir ou baixar os dados produzidos por um módulo clicando na porta de saída. Dependendo do módulo, pode haver diferentes opções de download disponíveis, ou você poderá visualizar os dados no navegador da Web no Studio (clássico).

## <a name="supported-data-formats-and-data-types"></a>Tipos de dados e formatos de dados com suporte

Você pode importar vários tipos de dados para seu experimento, dependendo do mecanismo usado para importar dados e de onde eles estão vindo:

* Texto sem formatação (. txt)
* CSV (valores separados por vírgula) com um cabeçalho (. csv) ou sem (. NH. csv)
* Valores separados por tabulação (TSV) com um cabeçalho (. tsv) ou sem (. NH. tsv)
* Arquivo do Excel
* Tabela do Azure
* Tabela do hive
* Tabela do banco de dados SQL
* Valores de OData
* Dados de SVMLight (. SVMLight) (consulte a [definição de SVMLight](http://svmlight.joachims.org/) para obter informações sobre o formato)
* Dados de formato de arquivo de relação de atributo (ARFF) (. ARFF) (consulte a [definição de ARFF](https://weka.wikispaces.com/ARFF) para obter informações sobre o formato)
* Arquivo zip (. zip)
* Objeto R ou arquivo de espaço de trabalho (. RData

Se você importar dados em um formato como ARFF que inclui metadados, o Studio (clássico) usará esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se você importar dados como o formato TSV ou CSV que não inclua esses metadados, o Studio (clássico) infere o tipo de dados para cada coluna por meio da amostragem dos dados. Se os dados também não tiverem títulos de coluna, o estúdio (clássico) fornecerá nomes padrão.

Você pode especificar ou alterar explicitamente os títulos e tipos de dados para colunas usando o módulo [Editar metadados][edit-metadata] .

Os seguintes tipos de dados são reconhecidos pelo estúdio (clássico):

* String
* Número inteiro
* Double
* Booleano
* DateTime
* TimeSpan

O estúdio usa um tipo de dados interno chamado ***tabela de dados*** para passar dados entre módulos. Você pode converter explicitamente seus dados em formato de tabela de dados usando o módulo [converter para conjunto de][convert-to-dataset] dados.

Qualquer módulo que aceite formatos diferentes da tabela de dados converterá os dados em uma tabela de dados silenciosamente antes de passá-los para o próximo módulo.

Se necessário, você pode converter o formato de tabela de dados de volta no formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão.
Examine a seção **conversões de formato de dados** da paleta de módulo para obter os módulos que executam essas funções.

## <a name="data-capacities"></a>Capacidades de dados

Os módulos no Machine Learning Studio (clássico) dão suporte a conjuntos de dados de até 10 GB de data numérico denso para casos de uso comuns. Se um módulo precisar de mais do que uma entrada, 10 GB é o valor do tamanho total de todas as entradas de dados. Você pode obter amostras de conjuntos de dados maiores usando consultas do hive ou do banco de dado SQL do Azure, ou pode usar o aprendizado por conta de pré-processamento antes de importar os dados.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados de maiores dimensões durante a normalização da funcionalidade, estando limitados a menos de 10 GB:

* Disperso
* Categórico
* Cadeias
* Dados binários

Os seguintes módulos estão limitados a conjuntos de dados inferiores a 10 GB:

* Módulos de recomendador
* Módulo Synthetic Minority Oversampling Technique (SMOTE)
* Módulos de script: R, Python, SQL
* Módulos onde o tamanho dos dados de saída pode ser superior ao tamanho dos dados de entrada, tais como Associação ou Hashing de Funcionalidade
* Validação Cruzada, Hiperparâmetros do Modelo de Otimização, Regressão Ordinal e Multicasse “One-vs-All”, quando o número de iterações é muito grande

Para conjuntos de dados maiores que alguns GBs, carregue os dados no armazenamento do Azure ou no banco de dados SQL do Azure ou use o Azure HDInsight, em vez de carregar diretamente de um arquivo local.

Você pode encontrar informações sobre dados de imagem na referência do módulo [importar imagens](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) .

## <a name="import-from-a-local-file"></a>Importar de um arquivo local

Você pode carregar um arquivo de dados do disco rígido para usar como dados de treinamento no Studio (clássico). Quando você importa um arquivo de dados, você cria um módulo de DataSet pronto para uso em experimentos em seu espaço de trabalho.

Para importar dados de uma unidade de disco rígida local, faça o seguinte:

1. Clique em **+ novo** na parte inferior da janela do Studio (clássico).
2. Selecione **conjunto de dados** e **do ficheiro LOCAL**.
3. Na caixa de diálogo **carregar um novo conjunto de um** , navegue até o arquivo que você deseja carregar.
4. Introduza um nome, identificar o tipo de dados e, opcionalmente, introduza uma descrição. Recomenda-se uma descrição, ele permite que Registre quaisquer características sobre os dados que deseja lembrar ao utilizar os dados no futuro.
5. A caixa de verificação **esta é a nova versão de um conjunto de dados existente** permite-lhe atualizar um conjunto de dados existente com novos dados. Para fazer isso, clique nessa caixa de seleção e digite o nome de um conjunto de um existente.

![Carregar um novo conjunto de dados](./media/import-data/upload-dataset-from-local-file.png)

Carregar tempo depende do tamanho dos seus dados e a velocidade da sua ligação ao serviço. Se você souber que o arquivo levará muito tempo, você poderá fazer outras coisas dentro do Studio (clássico) enquanto espera. No entanto, fechar o navegador antes que o carregamento de dados seja concluído faz com que o carregamento falhe.

Depois de seus dados são carregados, ele é armazenado num módulo de conjunto de dados e está disponível para qualquer experimentação na sua área de trabalho.

Quando estiver editando um experimento, você poderá encontrar os conjuntos de os que você carregou na lista **meus conjuntos** de valores na lista **DataSets salvos** na paleta de módulos. Pode arrastar e largar o conjunto de dados para a tela de experimentação, quando desejar usar o conjunto de dados para análise adicional e o machine learning.

## <a name="import-from-online-data-sources"></a>Importar de fontes de dados online

Usando o módulo [importar dados][import-data] , seu experimento pode importar dados de várias fontes de dados online enquanto o experimento está em execução.

> [!NOTE]
> Este artigo fornece informações gerais sobre o módulo [importar dados][import-data] . Para obter informações mais detalhadas sobre os tipos de dados que você pode acessar, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência do módulo para o módulo [importar dados][import-data] .

Usando o módulo [importar dados][import-data] , você pode acessar dados de uma das várias fontes de dados online enquanto o experimento está em execução:

* Um URL de Web através de HTTP
* Hadoop com o HiveQL
* Armazenamento de blobs do Azure
* Tabela do Azure
* Base de dados SQL ou SQL Server numa VM do Azure do Azure
* Base de dados do SQL Server no local
* Um provedor de OData atualmente de feed de dados
* Azure Cosmos DB

Como esses dados de treinamento são acessados enquanto o experimento está em execução, ele só está disponível nesse experimento. Por comparação, os dados que foram armazenados em um módulo DataSet estão disponíveis para qualquer experimento em seu espaço de trabalho.

Para acessar fontes de dados online em seu experimento do estúdio (clássico), adicione o módulo [importar dados][import-data] ao seu experimento. Em seguida, selecione **iniciar o assistente de dados de importação** em **Propriedades** para obter instruções guiadas passo a passo para selecionar e configurar a fonte de dados. Como alternativa, você pode selecionar manualmente a **fonte de dados** em **Propriedades** e fornecer os parâmetros necessários para acessar os dados.

As origens de dados online, que são suportadas estão descritas na tabela abaixo. Esta tabela também resume os formatos de ficheiro suportados e os parâmetros que são utilizados para aceder aos dados.

> [!IMPORTANT]
> Atualmente, os módulos [importar dados][import-data] e [exportar dados][export-data] podem ler e gravar dados somente do armazenamento do Azure criado usando o modelo de implantação clássico. Em outras palavras, o novo tipo de conta de armazenamento de Blobs do Azure que oferece uma camada de acesso de armazenamento frequente ou de uma camada de acesso de armazenamento esporádico não ainda é suportado.
>
> Em geral, as contas de armazenamento do Azure que pode ter criado antes desta opção de serviço se tornar disponível não deve ser afetado.
> Se precisar de criar uma nova conta, selecione **clássica** para a implementação de modelo, ou utilizar o Resource manager e selecione **fins gerais** vez **armazenamento de BLOBs** para  **Tipo de conta**.
>
> Para obter mais informações, consulte [armazenamento de Blobs do Azure: camadas de armazenamento frequente e esporádico](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Origens de dados online suportadas
O módulo de dados de **importação** Azure Machine Learning Studio (clássico) dá suporte às seguintes fontes de dados:

| Origem de Dados | Descrição | Parâmetros |
| --- | --- | --- |
| URL de Web por meio de HTTP |Leia os dados em valores separados por vírgulas (CSV), valores separados por tabulações (TSV), o formato de ficheiro de relação de atributo (ARFF) e formatos de máquinas de Vetores de suporte (SVM luz), de qualquer URL da web que utiliza HTTP |<b>URL</b>: Especifica o nome completo do ficheiro, incluindo o URL do site e o nome de ficheiro, com qualquer extensão. <br/><br/><b>Formato de dados</b>: Especifica um os dados suportados formatos: CSV, TSV, ARFF ou SVM-light. Se os dados têm uma linha de cabeçalho, ele é usado para atribuir nomes de coluna. |
| Hadoop/HDFS |Leia os dados de armazenamento distribuído no Hadoop. Especifique os dados que pretende ao utilizar o HiveQL, uma linguagem de consulta do tipo SQL. O HiveQL também pode ser usado para agregar dados e executar a filtragem de dados antes de adicionar os dados ao Studio (clássico). |<b>Consulta de base de dados do Hive</b>: Especifica a consulta do Hive utilizada para gerar os dados.<br/><br/><b>URI do servidor de HCatalog </b> : especificado o nome do seu cluster com o formato  *&lt;o nome do cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome de conta de utilizador do Hadoop</b>: Especifica o nome de conta de utilizador do Hadoop utilizado para aprovisionar o cluster.<br/><br/><b>Palavra-passe de conta de utilizador Hadoop</b> : Especifica as credenciais utilizadas quando o aprovisionamento do cluster. Para obter mais informações, consulte [criar clusters Hadoop no HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Localização de dados de saída</b>: Especifica se os dados são armazenados num sistema de ficheiros distribuído Hadoop (HDFS) ou no Azure. <br/><ul>Se armazenar dados de saída no HDFS, especifique o URI do servidor HDFS. (Certifique-se de que utiliza o nome de cluster do HDInsight sem o prefixo HTTPS://). <br/><br/>Se armazenar os dados de saída no Azure, tem de especificar o nome da conta de armazenamento do Azure, a chave de acesso de armazenamento e o nome do contentor de armazenamento.</ul> |
| Base de dados SQL |Lê os dados armazenados numa base de dados SQL do Azure ou numa base de dados do SQL Server em execução numa máquina virtual do Azure. |<b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está em execução.<br/><ul>Em caso de base de dados do Azure SQL, introduza o nome do servidor que é gerado. Normalmente, ela tem o formato  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>No caso de um SQL Server hospedado em uma máquina virtual do Azure *, insira TCP:&lt;nome DNS da máquina virtual&gt;, 1433*</ul><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso para a base de dados. <br/><br/><b>Palavra-passe de conta de utilizador servidor</b>: Especifica a palavra-passe da conta de utilizador.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução SQL que descreve os dados que pretende ler. |
| Base de dados SQL no local |Lê os dados armazenados numa base de dados SQL no local. |<b>Gateway de dados</b>: Especifica o nome do Gateway de gestão de dados instalado num computador em que ele pode acessar seu banco de dados do SQL Server. Para obter informações sobre como configurar o gateway, consulte [executar análises avançadas com Azure Machine Learning Studio (clássico) usando dados de um SQL Server local](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está em execução.<br/><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso para a base de dados. <br/><br/><b>Nome de utilizador e palavra-passe</b>: clique em <b>introduza valores</b> que introduza as credenciais da base de dados. Pode usar a autenticação integrada do Windows ou a autenticação do SQL Server, dependendo de como o SQL Server no local está configurado.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução SQL que descreve os dados que pretende ler. |
| Tabela do Azure |Lê dados a partir do serviço tabela no armazenamento do Azure.<br/><br/>Se leu grandes quantidades de dados com pouca frequência, utilize o serviço de tabela do Azure. Ele fornece flexível, não relacionais (NoSQL), a solução de armazenamento de elevada disponibilidade, económica e dimensionável em massa. |As opções de **importar dados** alterar dependendo se aceder a informações públicas ou uma conta de armazenamento privado que necessite de credenciais de início de sessão. Tal é determinado através da <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou "Conta", cada um com seu próprio conjunto de parâmetros. <br/><br/><b>Público ou assinatura partilhado de acesso (SAS) URI</b>: os parâmetros são:<br/><br/><ul><b>URI de tabela</b>: Especifica o público ou o URL de SAS para a tabela.<br/><br/><b>Especifica as linhas para verificar a existência de nomes de propriedade</b>: os valores são <i>TopN</i> para analisar o número especificado de linhas, ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados são homogêneos e previsível, recomenda-se que seleciona *TopN* e introduza um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam com base na profundidade e a posição da tabela, escolha o *ScanAll* opção para verificar todas as linhas. Isto garante a integridade da sua propriedade resultante e a conversão de metadados.<br/><br/></ul><b>Conta de armazenamento privado</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a tabela de ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : Especifica o nome da tabela que contém os dados para ler.<br/><br/><b>Linhas para verificar a existência de nomes de propriedade</b>: os valores são <i>TopN</i> para analisar o número especificado de linhas, ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados forem homogêneos e previsível, recomendamos que seleciona *TopN* e introduza um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam com base na profundidade e a posição da tabela, escolha o *ScanAll* opção para verificar todas as linhas. Isto garante a integridade da sua propriedade resultante e a conversão de metadados.<br/><br/> |
| Armazenamento de Blobs do Azure |Lê os dados armazenados no serviço de BLOBs no armazenamento do Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Pode utilizar o serviço de BLOBs para expor publicamente os dados ou para armazenar os dados da aplicação em privado. Pode aceder aos dados a partir de qualquer lugar usando conexões HTTP ou HTTPS. |As opções de **importar dados** módulo alteração dependendo se aceder a informações públicas ou uma conta de armazenamento privado que necessite de credenciais de início de sessão. Tal é determinado através da <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou "Conta".<br/><br/><b>Público ou assinatura partilhado de acesso (SAS) URI</b>: os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica o público ou o URL de SAS para o blob de armazenamento.<br/><br/><b>Formato de arquivo</b>: Especifica o formato dos dados no serviço Blob. Os formatos suportados são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento privado</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém o blob que pretende ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contentor, diretório ou blob </b> : Especifica o nome do blob que contém os dados para ler.<br/><br/><b>Formato de ficheiro blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados suportados são CSV, TSV, ARFF, CSV com uma codificação especificada e o Excel. <br/><br/><ul>Se o formato CSV ou TSV, certifique-se de que indicam se o ficheiro contém uma linha de cabeçalho.<br/><br/>Pode utilizar a opção do Excel para ler dados de livros do Excel. Na <i>formato de dados do Excel</i> opção, indique se os dados estão num intervalo de folha de cálculo do Excel ou numa tabela do Excel. Na <i>folha do Excel ou tabela embedded </i>opção, especifique o nome da folha de cálculo ou tabela que pretende ler a partir de.</ul><br/> |
| Fornecedor de Feeds de dados |Leia os dados de um provedor de feed de suporte. Atualmente apenas o formato de Open Data Protocol (OData) é suportado. |<b>Tipo de conteúdo de dados</b>: Especifica o formato OData.<br/><br/><b>URL de origem</b>: Especifica o URL completo para o feed de dados. <br/>Por exemplo, o seguinte URL lê a partir de dados Northwind de exemplo: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importar de outro experimento

Haverá ocasiões em que você desejará pegar um resultado intermediário de um experimento e usá-lo como parte de outro experimento. Para fazer isso, salve o módulo como um conjunto de uma:

1. Clique na saída do módulo que você deseja salvar como um conjunto de um DataSet.
2. Clique em **salvar como conjunto de conjuntos**.
3. Quando solicitado, insira um nome e uma descrição que permitiriam que você identificasse o conjunto de os facilmente.
4. Clique na marca de seleção **OK** .

Quando o salvamento for concluído, o conjunto de um estará disponível para uso em qualquer experimento no espaço de trabalho. Você pode encontrá-lo na lista **DataSets salvos** na paleta de módulos.

## <a name="next-steps"></a>Passos seguintes

[Implantando Azure Machine Learning Studio Web Services que usam módulos de importação de dados e exportação de dados](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
