---
title: Importar dados de formação
titleSuffix: ML Studio (classic) Azure
description: Como importar seus dados para Azure Machine Learning Studio (clássico) de várias fontes de dados. Saiba quais tipos de dados e formatos de dados têm suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 453efc1129ad9ef2a8b68a571c98e3f5620a671f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684785"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importe seus dados de treinamento para Azure Machine Learning Studio (clássico) de várias fontes de dados

Para usar seus próprios dados no Machine Learning Studio (clássico) para desenvolver e treinar uma solução de análise preditiva, você pode usar dados de: 

* **Arquivo local** -carregue dados locais antecipadamente a partir do disco rígido para criar um módulo de DataSet em seu espaço de trabalho
* **Fontes de dados online** – use o módulo [importar dados][import-data] para acessar dados de uma das várias fontes online enquanto o experimento está em execução
* **Teste de Machine Learning Studio (clássico)** – use os dados que foram salvos como um DataSet na versão clássica do Machine Learning Studio
* [**Banco de dados SQL Server local**](use-data-from-an-on-premises-sql-server.md) – use os dados de um banco de SQL Server local sem precisar copiar os dados manualmente

> [!NOTE]
> Há vários conjuntos de dados de exemplo disponíveis na versão clássica do Machine Learning Studio que você pode usar para dados de treinamento. Para obter informações sobre eles, consulte [usar os conjuntos de dados de exemplo em Azure Machine Learning Studio (clássico)](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparar dados

A versão clássica do Machine Learning Studio é projetada para trabalhar com dados retangulares ou tabulares, como dados de texto delimitados ou dados estruturados de um banco de dado, embora em algumas circunstâncias os dados não retangulares possam ser usados.

É melhor se os dados forem relativamente limpos antes de você importá-los para a versão clássica do Studio. Por exemplo, você desejará cuidar de problemas como cadeias de caracteres sem aspas.

No entanto, há módulos disponíveis na versão clássica do Studio que habilitam alguma manipulação de dados dentro de seu experimento após a importação dos dados. Dependendo dos algoritmos de aprendizado de máquina que você usará, talvez seja necessário decidir como você tratará os problemas estruturais de dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Procure na seção **transformação de dados** da paleta de módulo os módulos que executam essas funções.

Em qualquer ponto do experimento, você pode exibir ou baixar os dados produzidos por um módulo clicando na porta de saída. Dependendo do módulo, pode haver diferentes opções de download disponíveis, ou você poderá visualizar os dados no navegador da Web na versão clássica do Studio.

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

Se você importar dados em um formato como ARFF que inclui metadados, a versão clássica do Studio usará esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se você importar dados como o formato TSV ou CSV que não inclua esses metadados, a versão clássica do estúdio infere o tipo de dados para cada coluna por meio da amostragem dos dados. Se os dados também não tiverem títulos de coluna, a versão clássica do estúdio fornecerá nomes padrão.

Você pode especificar ou alterar explicitamente os títulos e tipos de dados para colunas usando o módulo [Editar metadados][edit-metadata] .

Os seguintes tipos de dados são reconhecidos pela versão clássica do Studio:

* String
* Número inteiro
* Clique
* Booleano
* DateTime
* Período

O estúdio usa um tipo de dados interno chamado ***tabela de dados*** para passar dados entre módulos. Você pode converter explicitamente seus dados em formato de tabela de dados usando o módulo [converter para conjunto de][convert-to-dataset] dados.

Qualquer módulo que aceite formatos diferentes da tabela de dados converterá os dados em uma tabela de dados silenciosamente antes de passá-los para o próximo módulo.

Se necessário, você pode converter o formato de tabela de dados de volta no formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão.
Examine a seção **conversões de formato de dados** da paleta de módulo para obter os módulos que executam essas funções.

## <a name="data-capacities"></a>Capacidades de dados

Os módulos na versão clássica do Machine Learning Studio dão suporte a conjuntos de dados de até 10 GB de data numérico denso para casos de uso comuns. Se um módulo precisar de mais do que uma entrada, 10 GB é o valor do tamanho total de todas as entradas de dados. Você pode obter amostras de conjuntos de dados maiores usando consultas do hive ou do banco de dado SQL do Azure, ou pode usar o aprendizado por conta de pré-processamento antes de importar os dados.  

Os seguintes tipos de dados podem ser expandidos para conjuntos de dados de maiores dimensões durante a normalização da funcionalidade, estando limitados a menos de 10 GB:

* Dispersos
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

Você pode carregar um arquivo de dados do disco rígido para usar como dados de treinamento na versão clássica do Studio. Quando você importa um arquivo de dados, você cria um módulo de DataSet pronto para uso em experimentos em seu espaço de trabalho.

Para importar dados de um disco rígido local, faça o seguinte:

1. Clique em **+ novo** na parte inferior da janela do Studio (clássico).
2. Selecione **DataSet** e **do arquivo local**.
3. Na caixa de diálogo **carregar um novo conjunto de um** , navegue até o arquivo que você deseja carregar.
4. Insira um nome, identifique o tipo de dados e, opcionalmente, insira uma descrição. Uma descrição é recomendada-ela permite que você registre quaisquer características sobre os dados que você deseja lembrar ao usar os dados no futuro.
5. A caixa de seleção **é que a nova versão de um conjunto de dados existente** permite que você atualize um DataSet existente com novos data. Para fazer isso, clique nessa caixa de seleção e digite o nome de um conjunto de um existente.

![Carregar um novo conjunto de um](./media/import-data/upload-dataset-from-local-file.png)

O tempo de carregamento depende do tamanho dos dados e da velocidade da conexão com o serviço. Se você souber que o arquivo levará muito tempo, poderá fazer outras coisas dentro da versão clássica do estúdio enquanto espera. No entanto, fechar o navegador antes que o carregamento de dados seja concluído faz com que o carregamento falhe.

Depois que os dados são carregados, eles são armazenados em um módulo de conjunto e estão disponíveis para qualquer experimento em seu espaço de trabalho.

Quando estiver editando um experimento, você poderá encontrar os conjuntos de os que você carregou na lista **meus conjuntos** de valores na lista **DataSets salvos** na paleta de módulos. Você pode arrastar e soltar o conjunto de informações na tela do experimento quando desejar usar o conjunto de informações para análise e aprendizado de máquina adicionais.

## <a name="import-from-online-data-sources"></a>Importar de fontes de dados online

Usando o módulo [importar dados][import-data] , seu experimento pode importar dados de várias fontes de dados online enquanto o experimento está em execução.

> [!NOTE]
> Este artigo fornece informações gerais sobre o módulo [importar dados][import-data] . Para obter informações mais detalhadas sobre os tipos de dados que você pode acessar, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência do módulo para o módulo [importar dados][import-data] .

Usando o módulo [importar dados][import-data] , você pode acessar dados de uma das várias fontes de dados online enquanto o experimento está em execução:

* Uma URL da Web usando HTTP
* Hadoop usando HiveQL
* Armazenamento de BLOBs do Azure
* Tabela do Azure
* Banco de dados SQL do Azure ou SQL Server na VM do Azure
* Banco de dados SQL Server local
* Um provedor de feed de dados, OData atualmente
* Azure Cosmos DB

Como esses dados de treinamento são acessados enquanto o experimento está em execução, ele só está disponível nesse experimento. Por comparação, os dados que foram armazenados em um módulo DataSet estão disponíveis para qualquer experimento em seu espaço de trabalho.

Para acessar fontes de dados online em seu experimento do estúdio (clássico), adicione o módulo [importar dados][import-data] ao seu experimento. Em seguida, selecione **iniciar o assistente de dados de importação** em **Propriedades** para obter instruções guiadas passo a passo para selecionar e configurar a fonte de dados. Como alternativa, você pode selecionar manualmente a **fonte de dados** em **Propriedades** e fornecer os parâmetros necessários para acessar os dados.

As fontes de dados online com suporte são discriminadas na tabela a seguir. Esta tabela também resume os formatos de arquivo com suporte e os parâmetros que são usados para acessar os dados.

> [!IMPORTANT]
> Atualmente, os módulos [importar dados][import-data] e [exportar dados][export-data] podem ler e gravar dados somente do armazenamento do Azure criado usando o modelo de implantação clássico. Em outras palavras, o novo tipo de conta de armazenamento de BLOBs do Azure que oferece uma camada de acesso de armazenamento dinâmico ou camada de acesso de armazenamento fria ainda não tem suporte.
>
> Em geral, todas as contas de armazenamento do Azure que você pode ter criado antes dessa opção de serviço se tornaram disponíveis não devem ser afetadas.
> Se você precisar criar uma nova conta, selecione **clássico** para o modelo de implantação ou use o Gerenciador de recursos e selecione uso **geral** em vez de **armazenamento de BLOBs** para **tipo de conta**.
>
> Para obter mais informações, consulte [armazenamento de BLOBs do Azure: camadas de armazenamento quentes e frias](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Fontes de dados online com suporte
A versão clássica do módulo Azure Machine Learning Studio **importar dados** dá suporte às seguintes fontes de dados:

| Fonte de dados | Descrição | Parâmetros |
| --- | --- | --- |
| URL da Web via HTTP |Lê dados em CSV (valores separados por vírgula), TSV (valores separados por tabulação), formato de arquivo de relação de atributo (ARFF) e formatos de máquinas de vetor de suporte (SVM-Light), de qualquer URL da Web que usa HTTP |<b>URL</b>: especifica o nome completo do arquivo, incluindo a URL do site e o nome do arquivo, com qualquer extensão. <br/><br/><b>Formato de dados</b>: especifica um dos formatos de dados com suporte: CSV, TSV, ARFF ou SVM-Light. Se os dados tiverem uma linha de cabeçalho, eles serão usados para atribuir nomes de coluna. |
| Hadoop/HDFS |Lê dados do armazenamento distribuído no Hadoop. Você especifica os dados desejados usando o HiveQL, uma linguagem de consulta do tipo SQL. O HiveQL também pode ser usado para agregar dados e executar a filtragem de dados antes de adicionar os dados à versão clássica do Studio. |<b>Consulta de banco</b>de dados do hive: especifica a consulta do hive usada para gerar os dados.<br/><br/><b>URI do servidor HCatalog</b> : especifica o nome do cluster usando o formato *&lt;nome do cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome da conta de usuário do Hadoop</b>: especifica o nome de conta de usuário do Hadoop usado para provisionar o cluster.<br/><br/><b>Senha da conta de usuário do Hadoop</b> : especifica as credenciais usadas ao provisionar o cluster. Para obter mais informações, consulte [Criar clusters Hadoop no HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Local dos dados de saída</b>: especifica se os dados são armazenados em um sistema de arquivos distribuído HADOOP (HDFS) ou no Azure. <br/><ul>Se você armazenar os dados de saída no HDFS, especifique o URI do servidor HDFS. (Certifique-se de usar o nome do cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se você armazenar os dados de saída no Azure, deverá especificar o nome da conta de armazenamento do Azure, a chave de acesso de armazenamento e o nome do contêiner de armazenamento.</ul> |
| Base de dados SQL |Lê os dados que são armazenados em um banco de dado SQL do Azure ou em um SQL Server um em execução em uma máquina virtual do Azure. |<b>Nome do servidor de banco de dados</b>: especifica o nome do servidor no qual o banco de dados está em execução.<br/><ul>No caso do banco de dados SQL do Azure, insira o nome do servidor que é gerado. Normalmente, ele tem o formato *&lt;generated_identifier&gt;. Database.Windows.net.* <br/><br/>No caso de um SQL Server hospedado em uma máquina virtual do Azure *, insira TCP:&lt;nome DNS da máquina virtual&gt;, 1433*</ul><br/><b>Nome do banco de dados </b>: especifica o nome do banco de dados no servidor. <br/><br/><b>Nome da conta de usuário do servidor</b>: especifica um nome de usuário para uma conta que tenha permissões de acesso para o banco de dados. <br/><br/><b>Senha da conta de usuário do servidor</b>: especifica a senha da conta de usuário.<br/><br/><b>Consulta de banco</b>de dados: Insira uma instrução SQL que descreva os dados que você deseja ler. |
| Banco de dados SQL local |Lê os dados que são armazenados em um banco de dado SQL local. |<b>Gateway de dados</b>: especifica o nome do gateway de gerenciamento de dados instalado em um computador em que ele pode acessar o banco de dados de SQL Server. Para obter informações sobre como configurar o gateway, consulte [executar análise avançada com a versão clássica do Azure Machine Learning Studio usando dados de um SQL Server local](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de banco de dados</b>: especifica o nome do servidor no qual o banco de dados está em execução.<br/><br/><b>Nome do banco de dados </b>: especifica o nome do banco de dados no servidor. <br/><br/><b>Nome da conta de usuário do servidor</b>: especifica um nome de usuário para uma conta que tenha permissões de acesso para o banco de dados. <br/><br/><b>Nome de usuário e senha</b>: clique em <b>Inserir valores</b> para inserir suas credenciais de banco de dados. Você pode usar a autenticação integrada do Windows ou a autenticação SQL Server dependendo de como sua SQL Server local está configurada.<br/><br/><b>Consulta de banco</b>de dados: Insira uma instrução SQL que descreva os dados que você deseja ler. |
| Tabela do Azure |Lê dados do serviço tabela no armazenamento do Azure.<br/><br/>Se você ler grandes quantidades de dados com pouca frequência, use o serviço tabela do Azure. Ele fornece uma solução de armazenamento flexível, não relacional (NoSQL), maciçamente escalonável, barata e altamente disponível. |As opções nos **dados de importação** mudam dependendo se você estiver acessando informações públicas ou uma conta de armazenamento particular que exija credenciais de logon. Isso é determinado pelo <b>tipo de autenticação</b> que pode ter o valor "PublicOrSAS" ou "Account", cada um deles com seu próprio conjunto de parâmetros. <br/><br/><b>URI de SAS (assinatura de acesso compartilhado) ou pública</b>: os parâmetros são:<br/><br/><ul><b>URI da tabela</b>: especifica a URL pública ou de SAS para a tabela.<br/><br/><b>Especifica as linhas para verificar nomes de propriedade</b>: os valores são <i>TopN</i> para verificar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados forem homogêneos e previsíveis, é recomendável que você selecione *TopN* e insira um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam de acordo com a profundidade e a posição da tabela, escolha a opção *ScanAll* para verificar todas as linhas. Isso garante a integridade da sua propriedade resultante e da conversão de metadados.<br/><br/></ul><b>Conta de armazenamento particular</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: especifica o nome da conta que contém a tabela a ser lida.<br/><br/><b>Chave de conta</b>: especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : especifica o nome da tabela que contém os dados a serem lidos.<br/><br/><b>Linhas para verificar nomes de propriedade</b>: os valores são <i>TopN</i> para verificar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados forem homogêneos e previsíveis, recomendamos que você selecione *TopN* e insira um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam de acordo com a profundidade e a posição da tabela, escolha a opção *ScanAll* para verificar todas as linhas. Isso garante a integridade da sua propriedade resultante e da conversão de metadados.<br/><br/> |
| Armazenamento de Blobs do Azure |Lê os dados armazenados no serviço blob no armazenamento do Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Você pode usar o serviço blob para expor dados publicamente ou para armazenar dados de aplicativo de forma privada. Você pode acessar seus dados de qualquer lugar usando conexões HTTP ou HTTPS. |As opções no módulo **importar dados** são alteradas dependendo se você está acessando informações públicas ou uma conta de armazenamento particular que requer credenciais de logon. Isso é determinado pelo <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou de "Account".<br/><br/><b>URI de SAS (assinatura de acesso compartilhado) ou pública</b>: os parâmetros são:<br/><br/><ul><b>URI</b>: especifica a URL pública ou de SAS para o blob de armazenamento.<br/><br/><b>Formato de arquivo</b>: especifica o formato dos dados no serviço BLOB. Os formatos com suporte são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento particular</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: especifica o nome da conta que contém o blob que você deseja ler.<br/><br/><b>Chave de conta</b>: especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contêiner, diretório ou BLOB</b> : especifica o nome do blob que contém os dados a serem lidos.<br/><br/><b>Formato de arquivo de blob</b>: especifica o formato dos dados no serviço BLOB. Os formatos de dados com suporte são CSV, TSV, ARFF, CSV com uma codificação especificada e Excel. <br/><br/><ul>Se o formato for CSV ou TSV, certifique-se de indicar se o arquivo contém uma linha de cabeçalho.<br/><br/>Você pode usar a opção do Excel para ler dados de pastas de trabalho do Excel. Na opção <i>formato de dados do Excel</i> , indique se os dados estão em um intervalo de planilha do Excel ou em uma tabela do Excel. Na opção <i>planilha do Excel ou tabela inserida </i>, especifique o nome da planilha ou da tabela da qual você deseja ler.</ul><br/> |
| Provedor de feed de dados |Lê dados de um provedor de feeds com suporte. Atualmente, há suporte apenas para o formato Protocolo Open Data (OData). |<b>Tipo de conteúdo de dados</b>: especifica o formato OData.<br/><br/><b>URL de origem</b>: especifica a URL completa para o feed de dados. <br/>Por exemplo, a URL a seguir lê do banco de dados de exemplo Northwind: https://services.odata.org/northwind/northwind.svc/ |

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
