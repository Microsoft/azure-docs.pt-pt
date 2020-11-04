---
title: 'Estúdio ML (clássico): Dados de formação de importação - Azure'
description: Como importar os seus dados para o Azure Machine Learning Studio (clássico) de várias fontes de dados. Saiba quais os tipos de dados e formatos de dados suportados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 00501a1e17f8e085b817cfe3dac819ba890248f5
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309840"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importe os seus dados de formação em Azure Machine Learning Studio (clássico) de várias fontes de dados

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Para utilizar os seus próprios dados no Machine Learning Studio (clássico) para desenvolver e formar uma solução de análise preditiva, pode utilizar dados a partir de: 

* **Arquivo local** - Carregue os dados locais com antecedência do seu disco rígido para criar um módulo de conjunto de dados no seu espaço de trabalho
* **Fontes de dados on-line** - Utilize o módulo [de Dados de Importação][import-data] para aceder a dados de uma de várias fontes online enquanto a sua experiência está em execução
* **Experiência machine learning studio (clássico)** - Use dados que foram guardados como um conjunto de dados no Machine Learning Studio (clássico)
* [**Base de dados do SQL Server**](use-data-from-an-on-premises-sql-server.md) - Utilize dados de uma base de dados do SQL Server sem ter de copiar dados manualmente

> [!NOTE]
> Existem uma série de conjuntos de dados de amostra disponíveis no Machine Learning Studio (clássico) que pode utilizar para dados de treino. Para obter informações sobre estes, consulte [utilize os conjuntos de dados da amostra no Azure Machine Learning Studio (clássico)](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparar dados

O Machine Learning Studio (clássico) é projetado para trabalhar com dados retangulares ou tabulares, tais como dados de texto que são dados delimitados ou estruturados a partir de uma base de dados, embora em algumas circunstâncias dados não retangulares possam ser usados.

É melhor se os seus dados estiverem relativamente limpos antes de os importar para o Studio (clássico). Por exemplo, vai querer resolver questões como cordas não citadas.

No entanto, existem módulos disponíveis no Studio (clássico) que permitem alguma manipulação de dados dentro da sua experiência depois de importar os seus dados. Dependendo dos algoritmos de aprendizagem automática que vai utilizar, poderá ter de decidir como lidará com questões estruturais de dados, como valores em falta e dados escassos, e há módulos que podem ajudar nisso. Consulte na secção **de Transformação** de Dados da paleta de módulos para os módulos que desempenham estas funções.

Em qualquer ponto da sua experiência, pode visualizar ou descarregar os dados que são produzidos por um módulo clicando na porta de saída. Dependendo do módulo, pode haver diferentes opções de descarregamento disponíveis, ou poderá visualizar os dados dentro do seu navegador web no Studio (clássico).

## <a name="supported-data-formats-and-data-types"></a>Formatos de dados suportados e tipos de dados

Pode importar vários tipos de dados para a sua experiência, dependendo do mecanismo que utiliza para importar dados e de onde vem:

* Texto simples (.txt)
* Valores separados por vírgula (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por separados por separados (TSV) com um cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo Excel
* Tabela do Azure
* Mesa de colmeia
* Tabela de base de dados SQL
* Valores OData
* Dados SVMLight (.svmlight) (ver [definição SVMLight](http://svmlight.joachims.org/) para informação de formato)
* Atributo Dados do Formato do Ficheiro de Relação (ARFF) (.arff) (ver [definição ARFF](https://weka.wikispaces.com/ARFF) para informação de formato)
* Arquivo zip (.zip)
* Ficheiro de objeto r ou espaço de trabalho (. RData)

Se importar dados num formato como o ARFF que inclui metadados, o Studio (clássico) utiliza estes metadados para definir o título e o tipo de dados de cada coluna.

Se importar dados como tSV ou formato CSV que não inclua estes metadados, Studio (clássico) infere o tipo de dados para cada coluna através da amostragem dos dados. Se os dados também não tiverem títulos de coluna, o Studio (clássico) fornece nomes predefinidos.

Pode especificar ou alterar explicitamente os títulos e tipos de dados para colunas utilizando o módulo [de metadados de edição.][edit-metadata]

Os seguintes tipos de dados são reconhecidos pelo Studio (clássico):

* String
* Número inteiro
* Double (Duplo)
* Booleano
* DateTime
* TimeSpan

O estúdio utiliza um tipo de dados interno chamado * **tabela de dados** para transmitir dados entre módulos. Pode converter explicitamente os seus dados em formato de tabela de dados utilizando o módulo [Converte-se para Conjunto de Dados.][convert-to-dataset]

Qualquer módulo que aceite formatos que não a tabela de dados converterá os dados em tabela de dados silenciosamente antes de os passar para o módulo seguinte.

Se necessário, pode converter o formato da tabela de dados de volta em formato CSV, TSV, ARFF ou SVMLight utilizando outros módulos de conversão.
Consulte a secção _ *Conversãos de Formato de Dados* * da paleta do módulo para módulos que executam estas funções.

## <a name="data-capacities"></a>Capacidades de dados

Os módulos do Machine Learning Studio (clássico) suportam conjuntos de dados de até 10 GB de dados numéricos densos para casos de uso comum. Se um módulo precisar de mais do que uma entrada, 10 GB é o valor do tamanho total de todas as entradas de dados. Pode recolher amostras de conjuntos de dados maiores utilizando consultas da Hive ou da Base de Dados Azure SQL, ou pode utilizar o pré-processamento learning by Counts antes de importar os dados.  

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

Para conjuntos de dados maiores do que um par de GBs, faça o upload dos dados para Azure Storage ou Azure SQL Database, ou utilize o Azure HDInsight, em vez de fazer upload diretamente de um ficheiro local.

Pode encontrar informações sobre dados de imagem na referência do módulo [Import Images.](/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)

## <a name="import-from-a-local-file"></a>Importação de um arquivo local

Pode fazer o upload de um ficheiro de dados a partir do seu disco rígido para utilizar como dados de treino no Studio (clássico). Quando importa um ficheiro de dados, cria um módulo de conjunto de dados pronto a ser utilizado em experiências no seu espaço de trabalho.

Para importar dados de um disco rígido local, faça o seguinte:

1. Clique **em +NEW** na parte inferior da janela Studio (clássico).
2. Selecione **DATASET** e **A PARTIR DE ARQUIVO LOCAL**.
3. No **Upload um novo** diálogo do conjunto de dados, navegue para o ficheiro que pretende carregar.
4. Introduza um nome, identifique o tipo de dados e introduza opcionalmente uma descrição. Recomenda-se uma descrição - permite registar quaisquer características sobre os dados que pretende recordar ao utilizar os dados no futuro.
5. A caixa de verificação **Esta é a nova versão de um conjunto de dados existente** que permite atualizar um conjunto de dados existente com novos dados. Para tal, clique nesta caixa de verificação e, em seguida, introduza o nome de um conjunto de dados existente.

![Faça o upload de um novo conjunto de dados](./media/import-data/upload-dataset-from-local-file.png)

O tempo de upload depende do tamanho dos seus dados e da velocidade da sua ligação ao serviço. Se souber que o ficheiro demorará muito tempo, pode fazer outras coisas dentro do Studio (clássico) enquanto espera. No entanto, fechar o navegador antes do upload de dados é completo faz com que o upload falhe.

Uma vez que os seus dados são carregados, é armazenado num módulo de conjunto de dados e está disponível para qualquer experiência no seu espaço de trabalho.

Quando estiver a editar uma experiência, pode encontrar os conjuntos de dados que carregou na lista **my Datasets** na lista de **Conjuntos de Dados Guardados** na paleta de módulos. Pode arrastar e largar o conjunto de dados na tela de experiências quando pretender utilizar o conjunto de dados para análises mais aprofundadas e aprendizagem automática.

## <a name="import-from-online-data-sources"></a>Importação de fontes de dados on-line

Utilizando o módulo [De Dados de Importação,][import-data] a sua experiência pode importar dados de várias fontes de dados online durante a experiência em execução.

> [!NOTE]
> Este artigo fornece informações gerais sobre o módulo [de Dados de Importação.][import-data] Para obter informações mais detalhadas sobre os tipos de dados a que pode aceder, formatos, parâmetros e respostas a questões comuns, consulte o tópico de referência do módulo [de dados de importação.][import-data]

Ao utilizar o módulo [De Dados de Importação,][import-data] pode aceder aos dados de uma de várias fontes de dados online enquanto a sua experiência está em execução:

* Um URL web usando HTTP
* Hadoop usando HiveQL
* Armazenamento de blobs do Azure
* Tabela do Azure
* Base de Dados SQL do Azure. Sql Managed Instance, ou SQL Server
* Um fornecedor de feed de dados, o OData atualmente
* Azure Cosmos DB

Como estes dados de treino são acedidos enquanto a sua experiência está em execução, só está disponível nessa experiência. Em comparação, os dados que foram armazenados num módulo de conjunto de dados estão disponíveis para qualquer experiência no seu espaço de trabalho.

Para aceder a fontes de dados on-line na sua experiência Studio (clássica), adicione o módulo [de Dados de Importação][import-data] à sua experiência. Em seguida, selecione **Launch Import Data Wizard** under **Properties** para obter instruções guiadas passo a passo para selecionar e configurar a fonte de dados. Em alternativa, pode selecionar manualmente **a fonte de dados** em **Propriedades** e fornecer os parâmetros necessários para aceder aos dados.

As fontes de dados online que são suportadas estão itemadas na tabela abaixo. Esta tabela também resume os formatos de ficheiros suportados e os parâmetros que são utilizados para aceder aos dados.

> [!IMPORTANT]
> Atualmente, os módulos [de Dados de Importação][import-data] e [Dados de Exportação][export-data] só podem ler e escrever dados a partir do armazenamento Azure criados usando o modelo de implementação Clássico. Por outras palavras, o novo tipo de conta Azure Blob Storage que oferece um nível de acesso de armazenamento quente ou nível de acesso de armazenamento fresco ainda não está suportado.
>
> Geralmente, quaisquer contas de armazenamento Azure que possa ter criado antes desta opção de serviço ficar disponível não devem ser afetadas.
> Se precisar de criar uma nova conta, selecione **Classic** para o modelo de Implementação ou utilize o gestor de recursos e selecione **o propósito geral** em vez do armazenamento **blob** para **o tipo De conta**.
>
> Para mais informações, consulte [o Azure Blob Storage: Hot and Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Fontes de dados online suportadas
O módulo Azure Machine Learning Studio (clássico) **Import Data** suporta as seguintes fontes de dados:

| Origem de dados | Description | Parâmetros |
| --- | --- | --- |
| Web URL via HTTP |Lê dados em valores separados por vírgula (CSV), valores separados por separados por separados de separados por separados (TSV), formato de ficheiro de relação de atributos (ARFF) e Máquinas de Vetores de Suporte (luz SVM), a partir de qualquer URL web que utilize HTTP |<b>URL</b>: Especifica o nome completo do ficheiro, incluindo o URL do site e o nome do ficheiro, com qualquer extensão. <br/><br/><b>Formato de dados</b>: Especifica um dos formatos de dados suportados: CSV, TSV, ARFF ou SVM-light. Se os dados ímis ímis agem, é utilizado para atribuir nomes de colunas. |
| Hadoop/HDFS |Lê dados de armazenamento distribuído em Hadoop. Especifica os dados que pretende utilizando o HiveQL, uma linguagem de consulta semelhante ao SQL. O HiveQL também pode ser usado para agregar dados e realizar a filtragem de dados antes de adicionar os dados ao Studio (clássico). |<b>Consulta da base de dados da colmeia</b>: Especifica a consulta de Colmeia utilizada para gerar os dados.<br/><br/><b>HCatalog servidor URI</b> : Especificou o nome do seu cluster utilizando o formato do *&lt; seu nome de cluster &gt; .azurehdinsight.net.*<br/><br/><b>Nome da conta do utilizador Hadoop</b>: Especifica o nome da conta de utilizador Hadoop utilizado para a provisionação do cluster.<br/><br/><b>Palavra-passe da conta de utilizador Hadoop</b> : Especifica as credenciais utilizadas no fornecimento do cluster. Para obter mais informações, consulte [os clusters Create Hadoop em HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).<br/><br/><b>Localização dos dados de saída</b>: Especifica se os dados são armazenados num sistema de ficheiros distribuídos por Hadoop (HDFS) ou em Azure. <br/><ul>Se armazenar dados de saída em HDFS, especifique o servidor HDFS URI. (Certifique-se de que utiliza o nome do cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se armazenar os seus dados de saída no Azure, tem de especificar o nome da conta de armazenamento Azure, a chave de acesso ao armazenamento e o nome do contentor de armazenamento.</ul> |
| Base de dados SQL |Lê dados que são armazenados na Base de Dados Azure SQL, SQL Managed Instance, ou numa base de dados do SQL Server em execução numa máquina virtual Azure. |<b>Nome do servidor da base de dados</b>: Especifica o nome do servidor no qual a base de dados está a funcionar.<br/><ul>No caso de Base de Dados Azure SQL insira o nome do servidor que é gerado. Tipicamente tem a forma *&lt; generated_identifier &gt; .database.windows.net.* <br/><br/>No caso de um servidor SQL alojado numa máquina virtual Azure entrar no *TCP: &lt; Virtual Machine DNS Name &gt; , 1433*</ul><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome da conta do utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso à base de dados. <br/><br/><b>Palavra-passe da conta de utilizador</b>do servidor : Especifica a palavra-passe para a conta de utilizador.<br/><br/><b>Consulta de base de dados</b>:Introduza uma declaração SQL que descreva os dados que pretende ler. |
| Base de dados SQL no local |Lê dados que são armazenados numa base de dados SQL. |<b>Porta de dados</b>: Especifica o nome do Gateway de Gestão de Dados instalado num computador onde pode aceder à sua base de dados SQL Server. Para obter informações sobre a configuração do gateway, consulte [Executar análises avançadas com o Azure Machine Learning Studio (clássico) utilizando dados de um servidor SQL](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor da base de dados</b>: Especifica o nome do servidor no qual a base de dados está a funcionar.<br/><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome da conta do utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso à base de dados. <br/><br/><b>Nome do utilizador e palavra-passe</b>: Clique <b>em Introduzir valores</b> para introduzir as suas credenciais de base de dados. Pode utilizar a autenticação integrada do Windows ou a autenticação do servidor SQL dependendo da configuração do seu Servidor SQL.<br/><br/><b>Consulta de base de dados</b>:Introduza uma declaração SQL que descreva os dados que pretende ler. |
| Tabela do Azure |Lê dados do serviço table no Azure Storage.<br/><br/>Se ler grandes quantidades de dados com pouca frequência, utilize o Serviço de Mesa Azure. Fornece uma solução de armazenamento flexível, não relacional (NoSQL), massivamente escalável, barata e altamente disponível. |As opções na alteração de **Dados** de Importação dependendo se você está acedendo a informação pública ou uma conta de armazenamento privado que requer credenciais de login. Isto é determinado pelo <b>Tipo de Autenticação</b> que pode ter valor de "PublicOrSAS" ou "Conta", cada um dos quais tem o seu próprio conjunto de parâmetros. <br/><br/><b>Assinatura de acesso público ou partilhado (SAS) URI</b>: Os parâmetros são:<br/><br/><ul><b>Tabela URI</b>: Especifica o URL público ou SAS para a tabela.<br/><br/><b>Especifica as linhas para procurar nomes de propriedade</b>: Os valores são <i>TopN</i> para digitalizar o número especificado de linhas, ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados forem homogéneos e previsíveis, recomenda-se que selecione *TopN* e introduza um número para N. Para mesas grandes, isto pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam em função da profundidade e posição da tabela, escolha a opção *ScanAll* para digitalizar todas as linhas. Isto garante a integridade da sua conversão de propriedade e metadados resultantes.<br/><br/></ul><b>Conta de Armazenamento Privado</b>: Os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a tabela para ler.<br/><br/><b>Tecla conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : Especifica o nome da tabela que contém os dados a ler.<br/><br/><b>Linhas para procurar nomes de propriedade</b>: Os valores são <i>TopN</i> para digitalizar o número especificado de linhas, ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados forem homogéneos e previsíveis, recomendamos que selecione *TopN* e introduza um número para N. Para mesas grandes, isto pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam em função da profundidade e posição da tabela, escolha a opção *ScanAll* para digitalizar todas as linhas. Isto garante a integridade da sua conversão de propriedade e metadados resultantes.<br/><br/> |
| Armazenamento de Blobs do Azure |Lê os dados armazenados no serviço Blob no Azure Storage, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Pode utilizar o serviço Blob para expor publicamente dados ou para armazenar dados de aplicações privadas. Pode aceder aos seus dados a partir de qualquer lugar utilizando as ligações HTTP ou HTTPS. |As opções no módulo **De Dados de Importação** mudam dependendo se você está acedendo a informação pública ou uma conta de armazenamento privado que requer credenciais de login. Isto é determinado pelo <b>Tipo de Autenticação</b> que pode ter um valor quer de "PublicOrSAS" quer de "Conta".<br/><br/><b>Assinatura de acesso público ou partilhado (SAS) URI</b>: Os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica o URL público ou SAS para a bolha de armazenamento.<br/><br/><b>Formato de ficheiro</b>: Especifica o formato dos dados no serviço Blob. Os formatos suportados são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de Armazenamento Privado</b>: Os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a bolha que pretende ler.<br/><br/><b>Tecla conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contentor, diretório ou bolha </b> : Especifica o nome da bolha que contém os dados a ler.<br/><br/><b>Formato de ficheiro Blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados suportados são CSV, TSV, ARFF, CSV com uma codificação especificada, e Excel. <br/><br/><ul>Se o formato for CSV ou TSV, certifique-se de indicar se o ficheiro contém uma linha de cabeçalho.<br/><br/>Pode utilizar a opção Excel para ler dados dos livros do Excel. Na opção <i>de formato de dados do Excel,</i> indique se os dados estão numa gama de folhas de cálculo do Excel ou numa tabela Excel. Na folha excel ou na opção de <i>tabela incorporada, </i>especifique o nome da folha ou tabela que pretende ler.</ul><br/> |
| Fornecedor de feed de dados |Lê dados de um fornecedor de alimentos apoiado. Atualmente apenas o formato Do Protocolo de Dados Abertos (OData) é suportado. |<b>Tipo de conteúdo de</b>dados : Especifica o formato OData.<br/><br/><b>URL de origem</b>: Especifica o URL completo para o feed de dados. <br/>Por exemplo, o seguinte URL lê-se na base de dados da amostra northwind: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importação de outra experiência

Haverá alturas em que vai querer tirar um resultado intermédio de uma experiência e usá-lo como parte de outra experiência. Para tal, guarde o módulo como conjunto de dados:

1. Clique na saída do módulo que pretende guardar como conjunto de dados.
2. Clique **em Guardar como conjunto de dados.**
3. Quando solicitado, insira um nome e uma descrição que lhe permita identificar facilmente o conjunto de dados.
4. Clique na marca de verificação **OK.**

Quando a poupança terminar, o conjunto de dados estará disponível para utilização em qualquer experiência no seu espaço de trabalho. Pode encontrá-lo na lista **de conjuntos de dados guardados** na paleta de módulos.

## <a name="next-steps"></a>Passos seguintes

[Implementação de serviços web Azure Machine Learning Studio que utilizam módulos de importação de dados e exportação de dados](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[export-data]: /azure/machine-learning/studio-module-reference/export-data


<!-- Module References -->
[convert-to-dataset]: /azure/machine-learning/studio-module-reference/convert-to-dataset
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[import-data]: /azure/machine-learning/studio-module-reference/import-data