---
title: Importar dados de formação
titleSuffix: ML Studio (classic) - Azure
description: Como importar os seus dados para o Azure Machine Learning Studio (clássico) de várias fontes de dados. Saiba quais os tipos de dados e formatos de dados suportados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: cee49124a7547399889e425008a8580b9b25945a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217987"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importe os seus dados de formação para o Azure Machine Learning Studio (clássico) de várias fontes de dados

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Para utilizar os seus próprios dados no Machine Learning Studio (clássico) para desenvolver e treinar uma solução de análise preditiva, pode utilizar dados a partir de: 

* **Arquivo local** - Carregue os dados locais antes do tempo a partir do seu disco rígido para criar um módulo de conjunto de dados no seu espaço de trabalho
* **Fontes de dados on-line** - Utilize o módulo [dados de importação][import-data] para aceder a dados de uma das várias fontes online enquanto a sua experiência está em execução
* **Experiência Machine Learning Studio (clássica)** - Use dados que foram guardados como um conjunto de dados no Machine Learning Studio (clássico)
* Base de dados do [**SQL Server no local**](use-data-from-an-on-premises-sql-server.md) - Utilize dados de uma base de dados do SQL Server no local sem ter de copiar dados manualmente

> [!NOTE]
> Existem vários conjuntos de dados de amostradisponíveis no Machine Learning Studio (clássico) que pode utilizar para treinar dados. Para obter informações sobre estes, consulte Utilize os conjuntos de [dados da amostra no Azure Machine Learning Studio (clássico)](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparar dados

O Machine Learning Studio (clássico) foi concebido para trabalhar com dados retangulares ou tabulares, tais como dados de texto delimitados ou estruturados a partir de uma base de dados, embora em algumas circunstâncias os dados não retangulares possam ser utilizados.

É melhor se os seus dados estiverem relativamente limpos antes de os importar em Studio (clássico). Por exemplo, você vai querer cuidar de questões como cordas não citadas.

No entanto, existem módulos disponíveis no Studio (clássico) que permitem alguma manipulação de dados dentro da sua experiência depois de importar os seus dados. Dependendo dos algoritmos de aprendizagem automática que vai utilizar, poderá ter de decidir como lidará com questões estruturais de dados, como valores em falta e dados escassos, e existem módulos que podem ajudar nisso. Consulte a secção **de Transformação** de Dados da paleta de módulos para módulos que executam estas funções.

Em qualquer ponto da sua experiência, pode ver ou descarregar os dados produzidos por um módulo clicando na porta de saída. Dependendo do módulo, poderá existir diferentes opções de descarregamento disponíveis, ou poderá visualizar os dados dentro do seu navegador web no Studio (clássico).

## <a name="supported-data-formats-and-data-types"></a>Formatos de dados suportados e tipos de dados

Pode importar vários tipos de dados para a sua experiência, dependendo do mecanismo que utiliza para importar dados e de onde vem:

* Texto simples (.txt)
* Valores separados comma (CSV) com um cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por separados (TSV) com um cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo Excel
* Tabela do Azure
* Mesa de colmeia
* Tabela de bases de dados SQL
* Valores OData
* Dados SVMLight (.svmlight) (ver a [definição SVMLight](http://svmlight.joachims.org/) para informações de formato)
* Atribuir dados do formato de ficheiro de relação (ARFF) (.arff) (ver a [definição ARFF](https://weka.wikispaces.com/ARFF) para informações de formato)
* Ficheiro zip (.zip)
* Objeto R ou ficheiro espaço de trabalho (. RData)

Se importar dados num formato como o ARFF que inclui metadados, o Studio (clássico) utiliza estes metadados para definir o tipo de rubrica e dados de cada coluna.

Se importar dados como o formato TSV ou CSV que não inclua estes metadados, o Studio (clássico) infere o tipo de dados para cada coluna, através da amostragem dos dados. Se os dados também não tiverem títulos de coluna, o Studio (clássico) fornece nomes predefinidos.

Pode especificar ou alterar explicitamente as rubricas e tipos de dados para colunas utilizando o módulo [Dedados editar.][edit-metadata]

Os seguintes tipos de dados são reconhecidos pelo Studio (clássico):

* Cadeia
* Número inteiro
* Double
* Booleano
* DateTime
* TimeSpan

O estúdio usa um tipo de dados interno chamado ***data table*** para passar dados entre módulos. Pode converter explicitamente os seus dados em formato de tabela de dados utilizando o módulo [Converte para Dataset.][convert-to-dataset]

Qualquer módulo que aceite formatos que não a tabela de dados converterá os dados em tabela de dados silenciosamente antes de passá-lo para o módulo seguinte.

Se necessário, pode converter o formato da tabela de dados de volta em formato CSV, TSV, ARFF ou SVMLight utilizando outros módulos de conversão.
Veja na secção Conversões de **Formato de Dados** da paleta de módulos para módulos que executam estas funções.

## <a name="data-capacities"></a>Capacidades de dados

Os módulos do Machine Learning Studio (clássico) suportam conjuntos de dados de até 10 GB de dados numéricos densos para casos de uso comum. Se um módulo precisar de mais do que uma entrada, 10 GB é o valor do tamanho total de todas as entradas de dados. Pode experimentar conjuntos de dados maiores utilizando consultas da Hive ou da Base de Dados SQL do Azure, ou pode utilizar o pré-processamento de Learning by Counts antes de importar os dados.  

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

Para conjuntos de dados maiores do que alguns GBs, faça o upload dos dados para O Armazenamento Azure ou base de dados Azure SQL, ou utilize o Azure HDInsight, em vez de fazer o upload diretamente de um ficheiro local.

Pode encontrar informações sobre dados de imagem na referência do módulo [Import Images.](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes)

## <a name="import-from-a-local-file"></a>Importação de um arquivo local

Pode fazer o upload de um ficheiro de dados do seu disco rígido para usar como dados de treino no Studio (clássico). Ao importar um ficheiro de dados, cria-se um módulo de conjunto de dados pronto a ser utilizado em experiências no seu espaço de trabalho.

Para importar dados de um disco rígido local, faça o seguinte:

1. Clique **+NEW** na parte inferior da janela Studio (clássica).
2. Selecione **DATASET** e **FROM LOCAL FILE**.
3. No **Upload um novo** diálogo de conjunto de dados, navegue para o ficheiro que pretende carregar.
4. Introduza um nome, identifique o tipo de dados e introduza opcionalmente uma descrição. Recomenda-se uma descrição - permite-lhe registar quaisquer características sobre os dados que pretende recordar ao utilizar os dados no futuro.
5. A caixa de verificação **Esta é a nova versão de um conjunto de dados existente** permite-lhe atualizar um conjunto de dados existente com novos dados. Para tal, clique nesta caixa de verificação e introduza o nome de um conjunto de dados existente.

![Faça upload de um novo conjunto de dados](./media/import-data/upload-dataset-from-local-file.png)

O tempo de upload depende do tamanho dos seus dados e da velocidade da sua ligação ao serviço. Se souber que o ficheiro vai demorar muito tempo, pode fazer outras coisas dentro do Studio (clássico) enquanto espera. No entanto, fechar o navegador antes de o upload de dados estar completo faz com que o upload falhe.

Uma vez que os seus dados são carregados, é armazenado num módulo de conjunto de dados e está disponível para qualquer experiência no seu espaço de trabalho.

Ao editar uma experiência, pode encontrar os conjuntos de dados que fez upload na lista **My Datasets** na lista de Dados Guardados na paleta de **módulos.** Pode arrastar e deixar cair o conjunto de dados na tela da experiência quando pretender utilizar o conjunto de dados para análises adicionais e aprendizagem automática.

## <a name="import-from-online-data-sources"></a>Importação de fontes de dados online

Utilizando o módulo [Dados de Importação,][import-data] a sua experiência pode importar dados de várias fontes de dados on-line durante a experiência em execução.

> [!NOTE]
> Este artigo fornece informações gerais sobre o módulo [de Dados de Importação.][import-data] Para obter informações mais detalhadas sobre os tipos de dados a que pode aceder, formatos, parâmetros e respostas a questões comuns, consulte o tópico de referência do módulo para o módulo [de dados de importação.][import-data]

Ao utilizar o módulo dados de [importação,][import-data] pode aceder a dados de uma das várias fontes de dados online enquanto a sua experiência está em execução:

* Um URL web usando HTTP
* Hadoop usando HiveQL
* Armazenamento de blobs do Azure
* Tabela do Azure
* Base de dados Azure SQL ou Servidor SQL no Azure VM
* Base de dados do SQL Server no local
* Um fornecedor de feed de dados, OData atualmente
* Azure Cosmos DB

Como estes dados de treino são acedidos enquanto a sua experiência está em execução, só está disponível nessa experiência. Em comparação, os dados que foram armazenados num módulo de conjunto de dados estão disponíveis para qualquer experiência no seu espaço de trabalho.

Para aceder a fontes de dados on-line na sua experiência Studio (clássica), adicione o módulo [de Dados de Importação][import-data] à sua experiência. Em seguida, selecione **Launch Import Data Wizard** em **Propriedades** para instruções guiadas passo a passo para selecionar e configurar a fonte de dados. Em alternativa, pode selecionar manualmente **a fonte de dados** em **Propriedades** e fornecer os parâmetros necessários para aceder aos dados.

As fontes de dados online que são suportadas são tidas na tabela abaixo. Esta tabela também resume os formatos de ficheiros que são suportados e os parâmetros que são utilizados para aceder aos dados.

> [!IMPORTANT]
> Atualmente, os módulos [de Dados][import-data] de Importação e Dados [de Exportação][export-data] podem ler e escrever dados apenas a partir do armazenamento Azure criado usando o modelo de implementação Clássico. Por outras palavras, o novo tipo de conta de armazenamento Azure Blob que oferece um nível de acesso ao armazenamento quente ou um nível de acesso de armazenamento fresco ainda não é suportado.
>
> Geralmente, quaisquer contas de armazenamento Azure que possa ter criado antes desta opção de serviço ficar disponível não devem ser afetadas.
> Se precisar de criar uma nova conta, selecione **Classic** para o modelo de Implementação, ou utilize o Gestor de Recursos e selecione o **armazenamento geral** em vez do **armazenamento Blob** para **o tipo conta**.
>
> Para mais informações, consulte [O Armazenamento De Blob Azure: Hot and Cool Storage Tiers](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Fontes de dados online suportadas
O módulo de **dados de importação** do Azure Machine Learning Studio (clássico) suporta as seguintes fontes de dados:

| Origem de Dados | Descrição | Parâmetros |
| --- | --- | --- |
| Web URL via HTTP |Lê os dados em valores separados pela vírcula (CSV), valores separados por separados por separado (TSV), formato de ficheiro de relação de atributo (ARFF) e Máquinas vetoriais de suporte (SVM-light), de qualquer URL web que utilize HTTP |<b>URL</b>: Especifica o nome completo do ficheiro, incluindo o URL do site e o nome do ficheiro, com qualquer extensão. <br/><br/><b>Formato de dados:</b>Especifica um dos formatos de dados suportados: CSV, TSV, ARFF ou SVM-light. Se os dados têm uma linha de cabeçalho, é utilizado para atribuir nomes de colunas. |
| Hadoop/HDFS |Lê os dados do armazenamento distribuído em Hadoop. Especifica os dados que pretende utilizando o HiveQL, uma linguagem de consulta semelhante a SQL. O HiveQL também pode ser usado para agregar dados e realizar a filtragem de dados antes de adicionar os dados ao Studio (clássico). |Consulta de base de <b>dados da Hive</b>: Especifica a consulta da Hive usada para gerar os dados.<br/><br/><b>HCatalog server URI</b> : Especificou o nome do seu cluster utilizando o formato * &lt;do seu&gt;nome de cluster .azurehdinsight.net.*<br/><br/>Nome da conta de <b>utilizador hadoop</b>: Especifica o nome da conta de utilizador Hadoop utilizado para fornecer o cluster.<br/><br/><b>Palavra-passe</b> da conta de utilizador hadoop : Especifica as credenciais utilizadas no fornecimento do cluster. Para obter mais informações, consulte [Create Hadoop clusters no HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Localização dos dados de saída</b>: Especifica se os dados são armazenados num sistema de ficheiros distribuídos hadoop (HDFS) ou em Azure. <br/><ul>Se armazenar dados de saída no HDFS, especifique o servidor HDFS URI. (Certifique-se de que utiliza o nome do cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se armazenar os seus dados de saída no Azure, deve especificar o nome da conta de armazenamento Azure, a chave de acesso ao armazenamento e o nome do recipiente de armazenamento.</ul> |
| Base de dados SQL |Lê os dados armazenados numa base de dados Azure SQL ou numa base de dados do SQL Server em execução numa máquina virtual Azure. |<b>Nome</b>do servidor da base de dados : Especifica o nome do servidor em que a base de dados está a funcionar.<br/><ul>No caso de Base de Dados Azure SQL introduzir o nome do servidor que é gerado. Tipicamente tem a forma * &lt;generated_identifier&gt;.database.windows.net.* <br/><br/>No caso de um servidor SQL hospedado numa máquina Azure Virtual entrar no *tCP:&lt;Virtual Machine DNS&gt;Name , 1433*</ul><br/><b>Nome </b>da base de dados : Especifica o nome da base de dados no servidor. <br/><br/><b>Nome</b>da conta do utilizador do servidor : Especifica um nome de utilizador para uma conta que tem permissões de acesso para a base de dados. <br/><br/><b>Palavra-passe</b>da conta do utilizador do servidor : Especifica a palavra-passe para a conta de utilizador.<br/><br/><b>Consulta de base de dados</b>:Introduza uma declaração SQL que descreva os dados que pretende ler. |
| Base de dados SQL no local |Lê os dados armazenados numa base de dados SQL no local. |<b>Gateway</b>de dados : Especifica o nome do Portal de Gestão de Dados instalado num computador onde pode aceder à sua base de dados do Servidor SQL. Para obter informações sobre a configuração do gateway, consulte [Executar análises avançadas com o Azure Machine Learning Studio (clássico) utilizando dados de um servidor SQL no local.](use-data-from-an-on-premises-sql-server.md)<br/><br/><b>Nome</b>do servidor da base de dados : Especifica o nome do servidor em que a base de dados está a funcionar.<br/><br/><b>Nome </b>da base de dados : Especifica o nome da base de dados no servidor. <br/><br/><b>Nome</b>da conta do utilizador do servidor : Especifica um nome de utilizador para uma conta que tem permissões de acesso para a base de dados. <br/><br/><b>Nome e palavra-passe</b>do utilizador : <b>Clique em introduzir valores</b> para introduzir as credenciais da sua base de dados. Pode utilizar a autenticação integrada do Windows ou a autenticação do servidor SQL, dependendo da configuração do seu Servidor SQL no local.<br/><br/><b>Consulta de base de dados</b>:Introduza uma declaração SQL que descreva os dados que pretende ler. |
| Tabela do Azure |Lê os dados do serviço Mesa no Armazenamento Azure.<br/><br/>Se ler grandes quantidades de dados com pouca frequência, utilize o Serviço de Mesa Azure. Fornece uma solução de armazenamento flexível, não relacional (NoSQL), massivamente escalável, barato e altamente disponível. |As opções nos **Dados de Importação** mudam dependendo se você está a aceder a informações públicas ou uma conta de armazenamento privada que requer credenciais de login. Isto é determinado pelo Tipo de <b>Autenticação</b> que pode ter valor de "PublicOrSAS" ou "Conta", cada um dos quais tem o seu próprio conjunto de parâmetros. <br/><br/>Assinatura de <b>acesso público ou partilhado (SAS) URI</b>: Os parâmetros são:<br/><br/><ul><b>Tabela URI</b>: Especifica o URL Público ou SAS para a tabela.<br/><br/><b>Especifica as linhas para procurar nomes</b>de propriedade : Os valores são <i>TopN</i> para digitalizar o número especificado de linhas, ou <i>ScanAll</i> para colocar todas as linhas na tabela. <br/><br/>Se os dados forem homogéneos e previsíveis, recomenda-se que selecione *TopN* e introduza um número para N. Para mesas grandes, isto pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam com base na profundidade e posição da tabela, escolha a opção *ScanAll* para digitalizar todas as linhas. Isto garante a integridade da sua propriedade resultante e conversão de metadados.<br/><br/></ul><b>Conta de Armazenamento Privado</b>: Os parâmetros são: <br/><br/><ul><b>Nome</b>da conta : Especifica o nome da conta que contém a tabela a ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome</b> da tabela : Especifica o nome da tabela que contém os dados a ler.<br/><br/><b>Filas para procurar nomes</b>de propriedade : Os valores são <i>TopN</i> para digitalizar o número especificado de linhas, ou <i>ScanAll</i> para colocar todas as linhas na tabela.<br/><br/>Se os dados forem homogéneos e previsíveis, recomendamos que selecione *TopN* e introduza um número para N. Para mesas grandes, isto pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam com base na profundidade e posição da tabela, escolha a opção *ScanAll* para digitalizar todas as linhas. Isto garante a integridade da sua propriedade resultante e conversão de metadados.<br/><br/> |
| Armazenamento de Blobs do Azure |Lê os dados armazenados no serviço Blob no Armazenamento Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Pode utilizar o serviço Blob para expor publicamente dados ou para armazenar dados de aplicação privados. Pode aceder aos seus dados a partir de qualquer lugar utilizando ligações HTTP ou HTTPS. |As opções no módulo **de Dados de Importação** mudam dependendo se você está a aceder a informações públicas ou uma conta de armazenamento privada que requer credenciais de login. Isto é determinado pelo Tipo de <b>Autenticação</b> que pode ter um valor quer de "PublicOrSAS" quer de "Conta".<br/><br/>Assinatura de <b>acesso público ou partilhado (SAS) URI</b>: Os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica o URL Público ou SAS para a bolha de armazenamento.<br/><br/><b>Formato de ficheiro</b>: Especifica o formato dos dados no serviço Blob. Os formatos suportados são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de Armazenamento Privado</b>: Os parâmetros são: <br/><br/><ul><b>Nome</b>da conta : Especifica o nome da conta que contém a bolha que pretende ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para contentor, diretório ou bolha</b> : Especifica o nome da bolha que contém os dados a ler.<br/><br/><b>Formato de ficheiro blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados suportados são CSV, TSV, ARFF, CSV com uma codificação especificada, e Excel. <br/><br/><ul>Se o formato for CSV ou TSV, certifique-se de indicar se o ficheiro contém uma linha de cabeçalho.<br/><br/>Pode utilizar a opção Excel para ler dados dos livros excel. Na opção de formato de <i>dados Excel,</i> indique se os dados estão numa gama de folha de cálculo excel ou numa tabela Excel. Na folha Excel ou na opção <i>de mesa incorporada, </i>especifique o nome da folha ou tabela que pretende ler.</ul><br/> |
| Fornecedor de feed de dados |Lê dados de um fornecedor de alimentos suportado. Atualmente apenas o formato Open Data Protocol (OData) é suportado. |Tipo de conteúdo de <b>dados:</b>Especifica o formato OData.<br/><br/><b>URL de origem</b>: Especifica o URL completo para o feed de dados. <br/>Por exemplo, o seguinte URL lê-se na base de dados da amostra northwind:https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importação de outra experiência

Haverá alturas em que vai querer obter um resultado intermédio de uma experiência e usá-lo como parte de outra experiência. Para isso, guarde o módulo como conjunto de dados:

1. Clique na saída do módulo que pretende guardar como conjunto de dados.
2. Clique em **Guardar como Dataset**.
3. Quando solicitado, introduza um nome e uma descrição que lhe permitam identificar facilmente o conjunto de dados.
4. Clique na marca de verificação **OK.**

Quando o save terminar, o conjunto de dados estará disponível para utilização dentro de qualquer experiência no seu espaço de trabalho. Pode encontrá-lo na lista de Conjuntos de **Dados Guardados** na paleta de módulos.

## <a name="next-steps"></a>Passos seguintes

[Implementação de serviços web do Azure Machine Learning Studio que utilizam módulos de importação de dados e exportação de dados](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
