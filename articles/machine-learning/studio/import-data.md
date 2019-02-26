---
title: Importar dados de várias origens de dados
titleSuffix: Azure Machine Learning Studio
description: Como importar os dados para o Azure Machine Learning Studio a partir de várias origens de dados. Saiba quais tipos de dados e formatos de dados são suportados.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: 0e2073efc0611e4e36c673f05aea0523a57d3aa7
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823264"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importar os seus dados de preparação para o Azure Machine Learning Studio a partir de várias origens de dados

Para utilizar os seus dados no Machine Learning Studio para desenvolver e formar uma solução de Análise Preditiva, pode utilizar dados a partir de: 

* **Ficheiro local** -carregar os dados locais antes do tempo do disco rígido para criar um módulo de conjunto de dados na sua área de trabalho
* **Origens de dados online** -utilize o [importar dados] [ import-data] módulo para aceder a dados a partir de uma das várias origens online durante a execução de sua experiência
* **Experimentação do Studio de aprendizagem da máquina** -usar dados que foi guardados como um conjunto de dados no Machine Learning Studio
* [**Base de dados do SQL Server no local** ](use-data-from-an-on-premises-sql-server.md) -utilizar dados a partir de uma base de dados do SQL Server no local sem ter de copiar manualmente os dados

> [!NOTE]
> Um número de conjuntos de dados de exemplo estão disponíveis no Machine Learning Studio, que pode utilizar para os dados de treinamento. Para obter informações sobre elas, consulte [utilizar os conjuntos de dados de exemplo no Azure Machine Learning Studio](use-sample-datasets.md).

## <a name="prepare-data"></a>Preparar dados

O Machine Learning Studio foi projetado para trabalhar com dados retangulares ou em tabela, como dados de texto delimitados ou estruturado de dados a partir de uma base de dados, embora em algumas circunstâncias os dados não retangulares podem ser usados.

É melhor se seus dados forem relativamente limpos antes de importá-lo no Studio. Por exemplo, desejará lidar com problemas como unquoted cadeias de caracteres.

No entanto, existem módulos disponíveis no Studio que permitem alguma manipulação de dados dentro de sua experiência, depois de importar os dados. Consoante os algoritmos de machine learning que irá utilizar, poderá ter de decidir como irá lidar com problemas de estrutural de dados, tais como valores em falta e dados dispersos, e existem módulos que podem ajudar com isso. Consulte a **transformação de dados** secção da paleta do módulo para módulos que executar estas funções.

Em qualquer ponto na sua experimentação, pode ver ou transferir os dados que são produzidos por um módulo ao clicar na porta de saída. Dependendo do módulo, pode haver opções diferentes de download disponível ou poderá visualizar os dados dentro de seu navegador da web no Studio.

## <a name="supported-data-formats-and-data-types"></a>Formatos de dados suportados e tipos de dados

Pode importar um número de tipos de dados na sua experimentação, dependendo de qual mecanismo é utilizado para importar dados e de onde vem de:

* Texto sem formatação (. txt)
* Valores separados por vírgulas (CSV) com um cabeçalho (. csv) ou sem (. nh.csv)
* Separador valores separados (TSV) com um cabeçalho. (tsv) ou sem (. nh.tsv)
* Ficheiro do Excel
* Tabela do Azure
* Tabela do Hive
* Tabela de base de dados SQL
* Valores de OData
* Dados de SVMLight (.svmlight) (consulte a [SVMLight definição](http://svmlight.joachims.org/) para informações de formato)
* Atributo de dados do formato de ficheiro de relação (ARFF) (.arff) (consulte a [definição ARFF](http://weka.wikispaces.com/ARFF) para informações de formato)
* Ficheiro zip (. zip)
* Ficheiro de objeto ou área de trabalho de R (. RData)

Se importar dados num formato como ARFF que inclui metadados, o Studio usa esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se importar dados, tais como o formato TSV ou CSV, que não inclua estes metadados, o Studio infere o tipo de dados para cada coluna, os dados de amostragem. Se os dados também não tem cabeçalhos de coluna, o Studio fornece nomes predefinidos.

É possível especificar explicitamente ou alterar os cabeçalhos e tipos de dados para colunas com o [Editar metadados] [ edit-metadata] módulo.

Os seguintes tipos de dados são reconhecidos pelo Studio:

* String
* Número inteiro
* Double
* Booleano
* DateTime
* TimeSpan

Studio utiliza um tipo de dados interno chamado ***tabela de dados*** para passar dados entre os módulos. Explicitamente pode converter os dados em formato de tabela do dados com o [converter para o conjunto de dados] [ convert-to-dataset] módulo.

Qualquer módulo que formatos que não seja a tabela de dados irá converter a tabela de dados para dados silenciosamente, antes de passá-lo ao módulo seguinte.

Se necessário, pode converter o formato de tabela de dados no CSV, TSV, ARFF ou formato de SVMLight com outros módulos de conversão.
Consulte a **conversões de formato de dados** secção da paleta do módulo para módulos que executar estas funções.

## <a name="data-capacities"></a>Capacidades de dados

Os módulos no Machine Learning Studio suportam conjuntos de dados até 10 GB de dados numéricos em casos de utilização comum. Se um módulo precisar de mais do que uma entrada, 10 GB é o valor do tamanho total de todas as entradas de dados. Pode apresentar exemplos dos conjuntos de dados maiores através de consultas do Hive ou base de dados do Azure SQL ou pode utilizar a aprendizagem por contagens pré-processamentos antes de importar os dados.  

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

Para conjuntos de dados superiores a alguns GB, carregue os dados para o armazenamento do Azure ou a base de dados do Azure SQL ou utilizar o Azure HDInsight, em vez de os carregar diretamente a partir de um ficheiro local.

Pode encontrar informações sobre dados de imagem na [importar imagens](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) referência do módulo.

## <a name="import-from-a-local-file"></a>Importar a partir de um ficheiro local

Pode carregar um ficheiro de dados do disco rígido para utilizar como dados de treinamento no Studio. Quando importa um ficheiro de dados, vai criar um módulo de conjunto de dados pronta a utilizar experimentações em sua área de trabalho.

Para importar dados de uma unidade de disco rígida local, faça o seguinte:

1. Clique em **+ novo** na parte inferior da janela do Studio.
2. Selecione **conjunto de dados** e **do ficheiro LOCAL**.
3. Na **carregar um novo conjunto de dados** caixa de diálogo, procure o ficheiro que pretende carregar.
4. Introduza um nome, identificar o tipo de dados e, opcionalmente, introduza uma descrição. Recomenda-se uma descrição, ele permite que Registre quaisquer características sobre os dados que deseja lembrar ao utilizar os dados no futuro.
5. A caixa de verificação **esta é a nova versão de um conjunto de dados existente** permite-lhe atualizar um conjunto de dados existente com novos dados. Para tal, clique nesta caixa de verificação e, em seguida, introduza o nome de um conjunto de dados existente.

![Carregar um novo conjunto de dados](./media/import-data/upload-dataset-from-local-file.png)

Carregar tempo depende do tamanho dos seus dados e a velocidade da sua ligação ao serviço. Se souber que o ficheiro irá demorar muito tempo, pode fazer outras coisas dentro do Studio enquanto espera. No entanto, fecho do browser antes do carregamento de dados está concluído faz com que o carregamento falhe.

Depois de seus dados são carregados, ele é armazenado num módulo de conjunto de dados e está disponível para qualquer experimentação na sua área de trabalho.

Quando estiver a editar uma experimentação, pode encontrar os conjuntos de dados que carregou o **conjuntos de dados de meu** lista sob a **conjuntos de dados guardado** lista na paleta do módulo. Pode arrastar e largar o conjunto de dados para a tela de experimentação, quando desejar usar o conjunto de dados para análise adicional e o machine learning.

## <a name="import-from-online-data-sources"></a>Importar a partir de origens de dados online

Utilizar o [importar dados] [ import-data] módulo, sua experimentação pode importar dados de várias origens de dados online ao mesmo tempo a experimentação em execução.

> [!NOTE]
> Este artigo fornece informações gerais sobre o [importar dados] [ import-data] módulo. Para obter mais informações sobre os tipos de dados pode aceder, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência de módulo para o [importar dados] [ import-data] módulo.

Ao utilizar o [importar dados] [ import-data] módulo, pode acessar dados de uma das várias origens de dados online durante a execução de sua experiência:

* Um URL de Web através de HTTP
* Hadoop com o HiveQL
* Armazenamento de blobs do Azure
* Tabela do Azure
* Base de dados SQL ou SQL Server numa VM do Azure do Azure
* Base de dados do SQL Server no local
* Um provedor de OData atualmente de feed de dados
* Azure Cosmos DB

Uma vez que estes dados de treinamento são acedidos durante a execução de sua experiência, só está disponível desse experimento. Por comparação, os dados que tenham sido armazenados num módulo de conjunto de dados estão disponíveis para qualquer experimentação na sua área de trabalho.

Para aceder a origens de dados online na sua experimentação do Studio, adicione a [importar dados] [ import-data] módulo à sua experimentação. Em seguida, selecione **iniciar o Assistente para importar dados** sob **propriedades** para obter instruções passo a passo de guiado selecionar e configurar a origem de dados. Em alternativa, pode selecionar manualmente **origem de dados** sob **propriedades** e fornecer os parâmetros necessários para acessar os dados.

As origens de dados online, que são suportadas estão descritas na tabela abaixo. Esta tabela também resume os formatos de ficheiro suportados e os parâmetros que são utilizados para aceder aos dados.

> [!IMPORTANT]
> Atualmente, o [importar dados] [ import-data] e [exportar dados] [ export-data] módulos podem ler e escrever dados apenas a partir de armazenamento do Azure criado com clássica modelo de implementação. Em outras palavras, o novo tipo de conta de armazenamento de Blobs do Azure que oferece uma camada de acesso de armazenamento frequente ou de uma camada de acesso de armazenamento esporádico não ainda é suportado.
>
> Em geral, as contas de armazenamento do Azure que pode ter criado antes desta opção de serviço se tornar disponível não deve ser afetado.
> Se precisar de criar uma nova conta, selecione **clássica** para a implementação de modelo, ou utilizar o Resource manager e selecione **fins gerais** vez **armazenamento de BLOBs** para  **Tipo de conta**.
>
> Para obter mais informações, consulte [armazenamento de Blobs do Azure: Acesso frequente e esporádico camadas de armazenamento](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Origens de dados online suportadas
O Azure Machine Learning Studio **importar dados** módulo suporta as seguintes origens de dados:

| Origem de Dados | Descrição | Parâmetros |
| --- | --- | --- |
| URL de Web por meio de HTTP |Leia os dados em valores separados por vírgulas (CSV), valores separados por tabulações (TSV), o formato de ficheiro de relação de atributo (ARFF) e formatos de máquinas de Vetores de suporte (SVM luz), de qualquer URL da web que utiliza HTTP |<b>URL</b>: Especifica o nome completo do ficheiro, incluindo o URL do site e o nome de ficheiro, com qualquer extensão. <br/><br/><b>Formato de dados</b>: Especifica um dos formatos de dados suportados: CSV, TSV, ARFF ou SVM-light. Se os dados têm uma linha de cabeçalho, ele é usado para atribuir nomes de coluna. |
| Hadoop/HDFS |Leia os dados de armazenamento distribuído no Hadoop. Especifique os dados que pretende ao utilizar o HiveQL, uma linguagem de consulta do tipo SQL. Também pode ser utilizado o HiveQL para agregar dados e realizar antes de adicionar os dados para o Studio de filtragem de dados. |<b>Consulta de base de dados do Hive</b>: Especifica a consulta do Hive utilizada para gerar os dados.<br/><br/><b>URI do servidor de HCatalog </b> : Especificar o nome do seu cluster com o formato  *&lt;o nome do cluster&gt;. azurehdinsight.net.*<br/><br/><b>Nome de conta de utilizador do Hadoop</b>: Especifica o nome de conta de utilizador do Hadoop utilizado para aprovisionar o cluster.<br/><br/><b>Palavra-passe de conta de utilizador Hadoop</b> : Especifica as credenciais utilizadas quando o aprovisionamento do cluster. Para obter mais informações, consulte [criar clusters Hadoop no HDInsight](/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).<br/><br/><b>Localização de dados de saída</b>: Especifica se os dados são armazenados num sistema de ficheiros distribuído Hadoop (HDFS) ou no Azure. <br/><ul>Se armazenar dados de saída no HDFS, especifique o URI do servidor HDFS. (Certifique-se de que utiliza o nome de cluster do HDInsight sem o prefixo HTTPS://). <br/><br/>Se armazenar os dados de saída no Azure, tem de especificar o nome da conta de armazenamento do Azure, a chave de acesso de armazenamento e o nome do contentor de armazenamento.</ul> |
| Base de dados SQL |Lê os dados armazenados numa base de dados SQL do Azure ou numa base de dados do SQL Server em execução numa máquina virtual do Azure. |<b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está em execução.<br/><ul>Em caso de base de dados do Azure SQL, introduza o nome do servidor que é gerado. Normalmente, ela tem o formato  *&lt;generated_identifier&gt;. database.windows.net.* <br/><br/>Em caso de um SQL server alojado numa máquina Virtual do Azure, introduza *tcp:&lt;nome de DNS da Máquina Virtual&gt;, 1433*</ul><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso para a base de dados. <br/><br/><b>Palavra-passe de conta de utilizador servidor</b>: Especifica a palavra-passe da conta de utilizador.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução SQL que descreve os dados que pretende ler. |
| Base de dados SQL no local |Lê os dados armazenados numa base de dados SQL no local. |<b>Gateway de dados</b>: Especifica o nome do Gateway de gestão de dados instalado num computador em que ele pode acessar seu banco de dados do SQL Server. Para obter informações sobre como configurar o gateway, consulte [executar análises avançadas com o Azure Machine Learning Studio usando dados de um servidor SQL no local](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de base de dados</b>: Especifica o nome do servidor no qual a base de dados está em execução.<br/><br/><b>Nome da base de dados </b>: Especifica o nome da base de dados no servidor. <br/><br/><b>Nome de conta de utilizador do servidor</b>: Especifica um nome de utilizador para uma conta que tenha permissões de acesso para a base de dados. <br/><br/><b>Nome de utilizador e palavra-passe</b>: Clique em <b>introduza valores</b> que introduza as credenciais da base de dados. Pode usar a autenticação integrada do Windows ou a autenticação do SQL Server, dependendo de como o SQL Server no local está configurado.<br/><br/><b>Consulta de base de dados</b>: introduza uma instrução SQL que descreve os dados que pretende ler. |
| Tabela do Azure |Lê dados a partir do serviço tabela no armazenamento do Azure.<br/><br/>Se leu grandes quantidades de dados com pouca frequência, utilize o serviço de tabela do Azure. Ele fornece flexível, não relacionais (NoSQL), a solução de armazenamento de elevada disponibilidade, económica e dimensionável em massa. |As opções de **importar dados** alterar dependendo se aceder a informações públicas ou uma conta de armazenamento privado que necessite de credenciais de início de sessão. Tal é determinado através da <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou "Conta", cada um com seu próprio conjunto de parâmetros. <br/><br/><b>Assinatura de acesso público ou partilhado (SAS) URI</b>: Os parâmetros são:<br/><br/><ul><b>URI de tabela</b>: Especifica o público ou o URL de SAS para a tabela.<br/><br/><b>Especifica as linhas para verificar a existência de nomes de propriedade</b>: Os valores são <i>TopN</i> para analisar o número especificado de linhas, ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados são homogêneos e previsível, recomenda-se que seleciona *TopN* e introduza um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam com base na profundidade e a posição da tabela, escolha o *ScanAll* opção para verificar todas as linhas. Isto garante a integridade da sua propriedade resultante e a conversão de metadados.<br/><br/></ul><b>Conta de armazenamento privado</b>: Os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém a tabela de ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : Especifica o nome da tabela que contém os dados para ler.<br/><br/><b>Linhas para verificar a existência de nomes de propriedade</b>: Os valores são <i>TopN</i> para analisar o número especificado de linhas, ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados forem homogêneos e previsível, recomendamos que seleciona *TopN* e introduza um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados estão estruturados com conjuntos de propriedades que variam com base na profundidade e a posição da tabela, escolha o *ScanAll* opção para verificar todas as linhas. Isto garante a integridade da sua propriedade resultante e a conversão de metadados.<br/><br/> |
| Armazenamento de Blobs do Azure |Lê os dados armazenados no serviço de BLOBs no armazenamento do Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Pode utilizar o serviço de BLOBs para expor publicamente os dados ou para armazenar os dados da aplicação em privado. Pode aceder aos dados a partir de qualquer lugar usando conexões HTTP ou HTTPS. |As opções de **importar dados** módulo alteração dependendo se aceder a informações públicas ou uma conta de armazenamento privado que necessite de credenciais de início de sessão. Tal é determinado através da <b>tipo de autenticação</b> que pode ter um valor de "PublicOrSAS" ou "Conta".<br/><br/><b>Assinatura de acesso público ou partilhado (SAS) URI</b>: Os parâmetros são:<br/><br/><ul><b>URI</b>: Especifica o público ou o URL de SAS para o blob de armazenamento.<br/><br/><b>Formato de arquivo</b>: Especifica o formato dos dados no serviço Blob. Os formatos suportados são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de armazenamento privado</b>: Os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: Especifica o nome da conta que contém o blob que pretende ler.<br/><br/><b>Chave da conta</b>: Especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contentor, diretório ou blob </b> : Especifica o nome do blob que contém os dados para ler.<br/><br/><b>Formato de ficheiro blob</b>: Especifica o formato dos dados no serviço blob. Os formatos de dados suportados são CSV, TSV, ARFF, CSV com uma codificação especificada e o Excel. <br/><br/><ul>Se o formato CSV ou TSV, certifique-se de que indicam se o ficheiro contém uma linha de cabeçalho.<br/><br/>Pode utilizar a opção do Excel para ler dados de livros do Excel. Na <i>formato de dados do Excel</i> opção, indique se os dados estão num intervalo de folha de cálculo do Excel ou numa tabela do Excel. Na <i>folha do Excel ou tabela embedded </i>opção, especifique o nome da folha de cálculo ou tabela que pretende ler a partir de.</ul><br/> |
| Fornecedor de Feeds de dados |Leia os dados de um provedor de feed de suporte. Atualmente apenas o formato de Open Data Protocol (OData) é suportado. |<b>Tipo de conteúdo de dados</b>: Especifica o formato OData.<br/><br/><b>URL de origem</b>: Especifica o URL completo para o feed de dados. <br/>Por exemplo, o seguinte URL lê a partir de dados Northwind de exemplo: http://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importar a partir de outra experimentação

Haverá situações quando vai querer tirar um resultado intermediário a partir de uma experiência e utilizá-lo como parte de outra experimentação. Para fazer isso, salvar o módulo como um conjunto de dados:

1. Clique a saída do módulo que pretende guardar como um conjunto de dados.
2. Clique em **guardar como conjunto de dados**.
3. Quando lhe for pedido, introduza um nome e uma descrição que permitiria que identifique o conjunto de dados facilmente.
4. Clique nas **OK** marca de verificação.

Quando o salvamento for concluído, o conjunto de dados estarão disponível para uso dentro de qualquer experimentação na sua área de trabalho. Pode encontrá-lo na **conjuntos de dados guardado** lista na paleta do módulo.

## <a name="next-steps"></a>Passos Seguintes

[Implementar serviços web do Azure Machine Learning studio que utilizam a importação de dados e módulos de exportação de dados](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
