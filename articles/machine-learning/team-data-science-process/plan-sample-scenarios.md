---
title: Identificar cenários para o processo de ciência de dados de Azure Machine Learning equipe
description: Selecione os cenários apropriados para fazer análises preditivas avançadas com o processo de ciência de dados de equipe.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: de02e94014fa2a28e364c4f85bcb31a1a4827b8c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495048"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários de análises avançadas no Azure Machine Learning
Este artigo descreve a variedade de fontes de dados de exemplo e os cenários de destino que podem ser tratados pelo [TDSP (processo de ciência de dados de equipe)](overview.md). O TDSP fornece uma abordagem sistemática para que as equipes colaborem na criação de aplicativos inteligentes. Os cenários apresentados aqui ilustram as opções disponíveis no fluxo de trabalho de processamento de dados que dependem das características de dados, locais de origem e repositórios de destino no Azure.

A **árvore de decisão** para selecionar os cenários de exemplo apropriados para seus dados e objetivo é apresentada na última seção.

Cada uma das seções a seguir apresenta um cenário de exemplo. Para cada cenário, uma possível ciência de dados ou fluxo de análise avançada e recursos de suporte do Azure são listados.

> [!NOTE]
> **Para todos os cenários a seguir, você precisa:**
> <br/>
> 
> * [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md)
>   <br/>
> * [Criar um espaço de trabalho Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Cenário \#1: DataSet de tabela pequeno a médio em arquivos locais
![Arquivos locais pequenos a médios][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionais do Azure: nenhum
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Carregar um conjunto de uma.
1. Crie um Azure Machine Learning fluxo de experimento começando com DataSets carregados.

## <a name="smalllocalprocess"></a>Cenário \#2: conjunto de pequenos a médios de arquivos locais que exigem processamento
![Arquivos locais pequenos a médios com processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais do Azure: máquina virtual do Azure (servidor de notebook IPython)
1. Crie uma máquina virtual do Azure executando o IPython notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Pré-processe e limpe dados no IPython notebook, acessando dados do contêiner de armazenamento do Azure.
1. Transformar dados em formato de tabela limpo.
1. Salvar dados transformados em BLOBs do Azure.
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados de BLOBs do Azure usando o módulo [importar dados][import-data] .
1. Crie um Azure Machine Learning fluxo de experimento começando com conjuntos de os DataSets ingeridos.

## <a name="largelocal"></a>Cenário \#3: conjunto de grandes conjuntos de arquivos locais, direcionando BLOBs do Azure
![Arquivos locais grandes][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais do Azure: máquina virtual do Azure (servidor de notebook IPython)
1. Crie uma máquina virtual do Azure executando o IPython notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Pré-processe e limpe dados no IPython notebook, acessando dados de BLOBs do Azure.
1. Transforme dados em formato de tabela limpo, se necessário.
1. Explore os dados e crie recursos conforme necessário.
1. Extraia uma amostra de dados de pequeno a médio porte.
1. Salve os dados de amostra em BLOBs do Azure.
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados de BLOBs do Azure usando o módulo [importar dados][import-data] .
1. Compilar Azure Machine Learning fluxo de teste começando com conjunto (s) de DataSets ingeridos.

## <a name="smalllocaltodb"></a>Cenário \#4: conjunto de pequenos a médios de arquivos locais, direcionando SQL Server em uma máquina virtual do Azure
![Arquivos locais pequenos a médios para o BD SQL no Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: máquina virtual do Azure (servidor de notebook SQL Server/IPython)
1. Crie uma máquina virtual do Azure executando o SQL Server + IPython notebook.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Pré-processe e limpe dados no contêiner de armazenamento do Azure usando o notebook IPython.
1. Transforme dados em formato de tabela limpo, se necessário.
1. Salvar dados em arquivos locais de VM (o notebook IPython está em execução na VM, as unidades locais se referem a unidades de VM).
1. Carregar dados para SQL Server banco de dado em execução em uma VM do Azure.
   
   Opção \#1: usando SQL Server Management Studio.
   
   * Fazer logon na VM SQL Server
   * Execute SQL Server Management Studio.
   * Crie tabelas de banco de dados e destino.
   * Use um dos métodos de importação em massa para carregar os dados de arquivos locais da VM.
   
   Opção \#2: usando o IPython notebook – não é aconselhável para conjuntos de grandes e maiores
   
   <!-- -->    
   * Use a cadeia de conexão ODBC para acessar SQL Server na VM.
   * Crie tabelas de banco de dados e destino.
   * Use um dos métodos de importação em massa para carregar os dados de arquivos locais da VM.
1. Explore os dados, crie recursos conforme necessário. Observe que os recursos não precisam ser materializados nas tabelas do banco de dados. Observe apenas a consulta necessária para criá-las.
1. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [importar dados][import-data] . Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Compilar Azure Machine Learning fluxo de teste começando com conjunto (s) de DataSets ingeridos.

## <a name="largelocaltodb"></a>Cenário \#5: conjunto de grandes DataSet em arquivos locais, SQL Server de destino na VM do Azure
![Arquivos locais grandes para o BD SQL no Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: máquina virtual do Azure (servidor de notebook SQL Server/IPython)
1. Crie uma máquina virtual do Azure executando o SQL Server e o IPython Notebook Server.
1. Carregar dados em um contêiner de armazenamento do Azure.
1. Adicional Pré-processar e limpar dados.
   
   a.  Pré-processar e limpar dados no IPython notebook, acessando dados do Azure
   
       blobs.
   
   b.  Transforme dados em formato de tabela limpo, se necessário.
   
   c.  Salvar dados em arquivos locais de VM (o notebook IPython está em execução na VM, as unidades locais se referem a unidades de VM).
1. Carregar dados para SQL Server banco de dado em execução em uma VM do Azure.
   
   a.  Faça logon em SQL Server VM.
   
   b.  Se os dados ainda não foram salvos, baixe os arquivos de dados do Azure
   
       storage container to local-VM folder.
   
   c.  Execute SQL Server Management Studio.
   
   d.  Crie tabelas de banco de dados e destino.
   
   e.  Use um dos métodos de importação em massa para carregar os dados.
   
   f.  Se as junções de tabela forem necessárias, crie índices para agilizar as junções.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes tamanhos de dados, é recomendável que você crie tabelas particionadas e importe os dados em massa em paralelo. Para obter mais informações, veja [importação de dados paralelos para tabelas particionadas do SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore os dados, crie recursos conforme necessário. Observe que os recursos não precisam ser materializados nas tabelas do banco de dados. Observe apenas a consulta necessária para criá-las.
1. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [importar dados][import-data] . Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Fluxo de teste de Azure Machine Learning simples a partir do conjunto de testes carregado

## <a name="largedbtodb"></a>Cenário \#6: conjunto de dados grande em um SQL Server local, direcionando SQL Server em uma máquina virtual do Azure
![BD SQL grande no local para o BD SQL no Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: máquina virtual do Azure (servidor de notebook SQL Server/IPython)
1. Crie uma máquina virtual do Azure executando o SQL Server e o IPython Notebook Server.
1. Use um dos métodos de exportação de dados para exportar os dados de SQL Server para despejar arquivos.
   
   > [!NOTE]
   > Se você decidir mover todos os dados do banco de dado local, um método alternativo (mais rápido) para mover o banco de dados completo para a instância de SQL Server no Azure. Ignore as etapas para exportar dados, criar um banco de dados, carregar/importar, para o banco de dado de destino e seguir o método alternativo.
   > 
   > 
1. Carregar arquivos de despejo para o contêiner de armazenamento do Azure.
1. Carregue os dados em um banco de SQL Server em execução em uma máquina virtual do Azure.
   
   a.  Faça logon na VM SQL Server.
   
   b.  Baixe arquivos de dados de um contêiner de armazenamento do Azure para a pasta local da VM.
   
   c.  Execute SQL Server Management Studio.
   
   d.  Crie tabelas de banco de dados e destino.
   
   e.  Use um dos métodos de importação em massa para carregar os dados.
   
   f.  Se as junções de tabela forem necessárias, crie índices para agilizar as junções.
   
   > [!NOTE]
   > Para o carregamento mais rápido de grandes tamanhos de dados, crie tabelas particionadas e importe os dados em massa em paralelo. Para obter mais informações, veja [importação de dados paralelos para tabelas particionadas do SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore os dados, crie recursos conforme necessário. Observe que os recursos não precisam ser materializados nas tabelas do banco de dados. Observe apenas a consulta necessária para criá-las.
1. Escolha um tamanho de amostra de dados, se necessário e/ou desejado.
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [importar dados][import-data] . Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Fluxo de teste de Azure Machine Learning simples a partir do conjunto de testes carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar um banco de dados completo de um SQL Server local para o banco de dados SQL do Azure
![Desanexar o BD local e anexar ao banco de BD SQL no Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos adicionais do Azure: máquina virtual do Azure (servidor de notebook SQL Server/IPython)
Para replicar todo o banco de dados SQL Server em sua VM SQL Server, você deve copiar um banco de dados de um local/servidor para outro, supondo que o banco de dados pode ser colocado temporariamente offline. Você faz isso no Pesquisador de objetos SQL Server Management Studio ou usando os comandos Transact-SQL equivalentes.

1. Desanexe o banco de dados no local de origem. Para obter mais informações, consulte [desanexar um banco de dados](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. No Windows Explorer ou janela de prompt de comando do Windows, copie o arquivo ou os arquivos de banco de dados desanexados e os arquivos de log para o local de destino na VM SQL Server no Azure.
1. Anexe os arquivos copiados à instância de SQL Server de destino. Para obter mais informações, consulte [anexar um banco de dados](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Mover um banco de dados usando desanexar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Cenário \#7: Big data em arquivos locais, banco de dados do hive de destino em clusters Azure HDInsight Hadoop
![Big data no hive de destino local][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos adicionais do Azure: cluster de Azure HDInsight Hadoop e máquina virtual do Azure (servidor de notebook IPython)
1. Crie uma máquina virtual do Azure executando o servidor do IPython notebook.
1. Crie um cluster Azure HDInsight Hadoop.
1. Adicional Pré-processar e limpar dados.
   
   a.  Pré-processar e limpar dados no IPython notebook, acessando dados do Azure
   
       blobs.
   
   b.  Transforme dados em formato de tabela limpo, se necessário.
   
   c.  Salvar dados em arquivos locais de VM (o notebook IPython está em execução na VM, as unidades locais se referem a unidades de VM).
1. Carregue os dados no contêiner padrão do cluster do Hadoop selecionado na etapa 2.
1. Carregar dados no banco de dado do hive no cluster Azure HDInsight Hadoop.
   
   a.  Faça logon no nó principal do cluster do Hadoop
   
   b.  Abra a linha de comando do Hadoop.
   
   c.  Insira o diretório raiz do hive por comando `cd %hive_home%\bin` na linha de comando do Hadoop.
   
   d.  Execute as consultas do hive para criar um banco de dados e tabelas e carregue o armazenamento de BLOBs para tabelas Hive.
   
   > [!NOTE]
   > Se os dados forem grandes, os usuários poderão criar a tabela Hive com partições. Em seguida, os usuários podem usar um loop `for` na linha de comando do Hadoop no nó principal para carregar dados na tabela Hive particionada por partição.
   > 
   > 
1. Explore dados e crie recursos conforme necessário na linha de comando do Hadoop. Observe que os recursos não precisam ser materializados nas tabelas do banco de dados. Observe apenas a consulta necessária para criá-las.
   
   a.  Faça logon no nó principal do cluster do Hadoop
   
   b.  Abra a linha de comando do Hadoop.
   
   c.  Insira o diretório raiz do hive por comando `cd %hive_home%\bin` na linha de comando do Hadoop.
   
   d.  Execute as consultas de Hive na linha de comando do Hadoop no nó principal do cluster do Hadoop para explorar os dados e criar recursos conforme necessário.
1. Se necessário e/ou desejado, exemplo os dados para caber em Azure Machine Learning Studio.
1. Entre no [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do `Hive Queries` usando o módulo [importar dados][import-data] . Cole a consulta necessária que extrai campos, cria recursos e amostras de dados, se necessário, diretamente na consulta [importar dados][import-data] .
1. Fluxo de teste de Azure Machine Learning simples a partir do conjunto de testes carregado.

## <a name="decisiontree"></a>Árvore de decisão para seleção de cenário
---
O diagrama a seguir resume os cenários descritos acima e as opções de tecnologia e o processo de análise avançada feitas que levam você a cada um dos cenários discriminados. Observe que o processamento de dados, a exploração, a engenharia de recursos e a amostragem podem ocorrer em um ou mais métodos/ambiente – nos ambientes de origem, intermediário e/ou de destino – e podem continuar iterativamente conforme a necessidade. O diagrama serve apenas como uma ilustração de alguns dos fluxos possíveis e não fornece uma enumeração exaustiva.

![Cenários de exemplo do processo DS de amostra][8]

### <a name="advanced-analytics-in-action-examples"></a>Análise avançada em exemplos de ação
Para obter instruções de Azure Machine Learning de ponta a ponta que empregam o processo e a tecnologia de análise avançada usando conjuntos de valores públicos, consulte:

* [Processo de ciência de dados de equipe em ação: usando SQL Server](sql-walkthrough.md).
* [Processo de ciência de dados de equipe em ação: usando clusters Hadoop do HDInsight](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
