---
title: Identificar cenários do Azure Machine Learning - Team Data Science Process
description: Selecione os cenários adequados para fazer avançadas de Análise Preditiva com o processo de ciência de dados de equipa.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76710489"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários de análises avançadas no Azure Machine Learning
Este artigo descreve a variedade de origens de dados de exemplo e cenários de destino que podem ser processados pela [Team Data Science Process (TDSP)](overview.md). O TDSP fornece uma abordagem sistemática para as equipas colaborarem na criação de aplicações inteligentes. Os cenários apresentados aqui ilustram as opções disponíveis no fluxo de trabalho de processamento de dados que dependem as características dos dados, localizações de origem e repositórios de destino no Azure.

A árvore de **decisão** para selecionar os cenários de amostra que são adequados para os seus dados e objetivo é apresentada na última secção.

Cada uma das seções a seguir apresenta um cenário de exemplo. Para cada cenário, uma ciência de dados ou análise avançada de fluxo e dar suporte a recursos do Azure estão listados.

> [!NOTE]
> **Para todos os cenários seguintes, tem de:**
> <br/>
> 
> * [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
>   <br/>
> * [Criar uma área de trabalho do Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Cenário \#1: Conjunto de dados tabular pequeno a médio nos ficheiros locais
![Pequenas e médias ficheiros locais][1]

#### <a name="additional-azure-resources-none"></a>Outros recursos do Azure: nenhum
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Carregar um conjunto de dados.
1. Crie um fluxo de experimentação do Azure Machine Learning, começando com conjuntos de dados carregados.

## <a name="smalllocalprocess"></a>Cenário \#2: pequenas a médio conjunto de dados de ficheiros locais que necessitem de processamento
![Pequenas e médias ficheiros locais com o processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Outros recursos do Azure: máquinas virtuais do Azure (servidor IPython Notebook)
1. Crie uma Máquina Virtual em execução IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no IPython Notebook, acedendo a dados do contentor de armazenamento Azure.
1. Transforme os dados num formato tabular limpo.
1. Guarde os dados transformados em blobs do Azure.
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados de BLOBs do Azure usando o módulo [importar dados][import-data] .
1. Crie um fluxo de experimentação do Azure Machine Learning, começando com conjuntos de dados ingeridos.

## <a name="largelocal"></a>Cenário \#3: conjunto de dados grande de arquivos locais, a filtragem de Blobs do Azure
![Grandes ficheiros locais][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Outros recursos do Azure: máquinas virtuais do Azure (servidor IPython Notebook)
1. Crie uma Máquina Virtual em execução IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados em IPython Notebook, aceder aos dados de blobs do Azure.
1. Transforme os dados num formato tabular limpo, se necessário.
1. Explorar dados e criar recursos conforme necessário.
1. Extrair uma amostra de dados de pequeno a médio.
1. Salve os dados de amostras em blobs do Azure.
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados de BLOBs do Azure usando o módulo [importar dados][import-data] .
1. Crie fluxo de experimentação do Azure Machine Learning a partir do conjuntos de dados ingeridos.

## <a name="smalllocaltodb"></a>Cenário \#4: pequeno a médio conjunto de dados de ficheiros locais, visando o SQL Server na máquina Virtual do Azure
![Pequenas e médias ficheiros locais para a BD do SQL no Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Outros recursos do Azure: máquinas virtuais do Azure (SQL Server / servidor IPython Notebook)
1. Crie uma Máquina Virtual a executar o SQL Server + IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no recipiente de armazenamento Azure utilizando o IPython Notebook.
1. Transforme os dados num formato tabular limpo, se necessário.
1. Guardar os dados para ficheiros de VM-local (IPython Notebook está em execução na VM, unidades locais, consulte a unidades VM).
1. Carregar dados para o banco de dados do SQL Server em execução numa VM do Azure.
   
   Opção \#1: com o SQL Server Management Studio.
   
   * Iniciar sessão no SQL Server VM
   * Execute o SQL Server Management Studio.
   * Crie tabelas de base de dados e de destino.
   * Utilize um da maior parte importar métodos para carregar os dados a partir dos ficheiros de VM-local.
   
   Opção \#2: utilizar IPython Notebook – não é aconselhável para conjuntos de dados maiores e médios
   
   <!-- -->    
   * Utilize cadeia de ligação de ODBC para acessar o SQL Server na VM.
   * Crie tabelas de base de dados e de destino.
   * Utilize um da maior parte importar métodos para carregar os dados a partir dos ficheiros de VM-local.
1. Explorar dados, criar recursos conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas tenha em atenção a consulta necessária para criá-los.
1. Escolher um tamanho de amostra de dados, se necessário e/ou assim o desejar.
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [importar dados][import-data] . Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Crie fluxo de experimentação do Azure Machine Learning a partir do conjuntos de dados ingeridos.

## <a name="largelocaltodb"></a>Cenário \#5: Grande conjunto de dados em ficheiros locais, alvo SQL Server em Azure VM
![Grandes ficheiros locais para a BD do SQL no Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Outros recursos do Azure: máquinas virtuais do Azure (SQL Server / servidor IPython Notebook)
1. Crie uma Máquina Virtual a executar o SQL Server e servidor IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. (Opcional) Pré-processar e limpar dados.
   
    a.  Pré-processar e limpar dados em IPython Notebook, aceder aos dados de blobs do Azure.
   
    b.  Transforme os dados num formato tabular limpo, se necessário.
   
    c.  Guardar os dados para ficheiros de VM-local (IPython Notebook está em execução na VM, unidades locais, consulte a unidades VM).
1. Carregar dados para o banco de dados do SQL Server em execução numa VM do Azure.
   
    a.  Inicie sessão no SQL Server VM.
   
    b.  Se os dados ainda não forem guardados, descarregue ficheiros de dados do contentor de armazenamento Azure para a pasta VM local.
   
    c.  Execute o SQL Server Management Studio.
   
    d.  Crie tabelas de base de dados e de destino.
   
    e.  Utilize um da maior parte importar métodos para carregar os dados.
   
    f.  Se associações de tabelas forem necessárias, crie índices para agilizar as associações.
   
   > [!NOTE]
   > Para carregamento mais rápido de tamanhos de dados de grandes dimensões, recomenda-se que criar tabelas particionadas e em massa importar os dados em paralelo. Para obter mais informações, consulte [paralela de importação de dados para tabelas Particionadas do SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explorar dados, criar recursos conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas tenha em atenção a consulta necessária para criá-los.
1. Escolher um tamanho de amostra de dados, se necessário e/ou assim o desejar.
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [importar dados][import-data] . Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Fluxo de experimentação do Azure Machine Learning simples a partir do conjunto de dados carregado

## <a name="largedbtodb"></a>Cenário \#6: conjunto de dados grande em um SQL Server local, direcionando SQL Server em uma máquina virtual do Azure
![Grandes SQL DB local para a BD do SQL no Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Outros recursos do Azure: máquinas virtuais do Azure (SQL Server / servidor IPython Notebook)
1. Crie uma Máquina Virtual a executar o SQL Server e servidor IPython Notebook.
1. Utilize um dos dados exportar métodos para exportar os dados do SQL Server para ficheiros de informação.
   
   > [!NOTE]
   > Se você decidir mover todos os dados do banco de dado local, um método alternativo (mais rápido) para mover o banco de dados completo para a instância de SQL Server no Azure. Ignore os passos para exportar dados, criar a base de dados e carga/importar dados para a base de dados de destino e siga o método alternativo.
   > 
   > 
1. Faça upload de ficheiros de despejo para o contentor de armazenamento Azure.
1. Carregar os dados para uma base de dados do SQL Server em execução numa máquina Virtual do Azure.
   
   a.  Inicie sessão no VM do Servidor SQL.
   
   b.  Descarregue ficheiros de dados de um contentor de Armazenamento Azure para a pasta VM local.
   
   c.  Execute o SQL Server Management Studio.
   
   d.  Crie tabelas de base de dados e de destino.
   
   e.  Utilize um da maior parte importar métodos para carregar os dados.
   
   f.  Se associações de tabelas forem necessárias, crie índices para agilizar as associações.
   
   > [!NOTE]
   > Para carregamento mais rápido de tamanhos de dados de grande dimensão, crie as tabelas particionadas e em massa importe os dados em paralelo. Para obter mais informações, consulte [paralela de importação de dados para tabelas Particionadas do SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explorar dados, criar recursos conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas tenha em atenção a consulta necessária para criá-los.
1. Escolher um tamanho de amostra de dados, se necessário e/ou assim o desejar.
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do SQL Server usando o módulo [importar dados][import-data] . Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Fluxo de experimentação do simples do Azure Machine Learning a partir do conjunto de dados carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar uma base de dados completa de um servidor de SQL no local para a base de dados do Azure SQL
![Desanexar local DB e anexar a BD do SQL no Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Outros recursos do Azure: máquinas virtuais do Azure (SQL Server / servidor IPython Notebook)
Para replicar a base de dados completa do SQL Server na sua VM do SQL Server, deve copiar uma base de dados de um/servidor de localização para outra, partindo do princípio de que a base de dados pode ser colocado offline temporariamente. Pode utilizar o SQL Server Management Studio Object Explorer ou utilizar os comandos transact-SQL equivalentes.

1. Anular a exposição da base de dados a localização de origem. Para obter mais informações, consulte [anular a exposição de uma base de dados](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. Na janela do Explorador do Windows ou da linha de comandos do Windows, copie o ficheiro de base de dados desanexada ou ficheiros e ficheiro de registo ou ficheiros para a localização de destino na VM do SQL Server no Azure.
1. Anexe os arquivos copiados para a instância do SQL Server de destino. Para obter mais informações, consulte [anexar uma base de dados](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Mover uma base de dados com desanexar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Cenário \#7: grandes quantidades de dados em ficheiros locais, base de dados do Hive em clusters do Hadoop de HDInsight do Azure de destino
![Grandes volumes de dados no destino local do Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Outros recursos do Azure: Cluster de Hadoop do HDInsight do Azure e a Máquina Virtual do Azure (servidor IPython Notebook)
1. Crie uma Máquina Virtual a executar o servidor IPython Notebook.
1. Crie um cluster do Azure HDInsight Hadoop.
1. (Opcional) Pré-processar e limpar dados.
   
   a.  Pré-processar e limpar dados em IPython Notebook, aceder aos dados do Azure
   
       blobs.
   
   b.  Transforme os dados num formato tabular limpo, se necessário.
   
   c.  Guardar os dados para ficheiros de VM-local (IPython Notebook está em execução na VM, unidades locais, consulte a unidades VM).
1. Carregar dados para o contentor predefinido do cluster de Hadoop selecionado no passo 2.
1. Carregar dados para o banco de dados de Hive num cluster do Azure HDInsight Hadoop.
   
   a.  Inicie sessão no nó principal do cluster de Hadoop
   
   b.  Abra a linha de comandos do Hadoop.
   
   c.  Introduza o diretório de raiz do ramo de registo pelo comando `cd %hive_home%\bin` na linha de comandos do Hadoop.
   
   d.  Execute as consultas do Hive para criar a base de dados e tabelas e carregar dados do armazenamento de BLOBs para tabelas do Hive.
   
   > [!NOTE]
   > Se os dados forem grandes, os utilizadores podem criar tabela do Hive com partições. Em seguida, os utilizadores podem utilizar um `for` loop no Hadoop linha de comandos no nó principal para carregar dados para a tabela de Hive particionada por partição.
   > 
   > 
1. Explorar dados e criar recursos, conforme necessário na linha de comandos do Hadoop. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas tenha em atenção a consulta necessária para criá-los.
   
   a.  Inicie sessão no nó principal do cluster de Hadoop
   
   b.  Abra a linha de comandos do Hadoop.
   
   c.  Introduza o diretório de raiz do ramo de registo pelo comando `cd %hive_home%\bin` na linha de comandos do Hadoop.
   
   d.  Execute as consultas do Hive na linha de comandos do Hadoop no nó principal do cluster Hadoop para explorar os dados e criar recursos conforme necessário.
1. Se for necessário e/ou assim o desejar, amostra os dados para caber no Azure Machine Learning Studio.
1. Inicie sessão para o [do Azure Machine Learning Studio](https://studio.azureml.net/).
1. Leia os dados diretamente do `Hive Queries` usando o módulo [importar dados][import-data] . Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Fluxo de experimentação do simples do Azure Machine Learning a partir do conjunto de dados carregado.

## <a name="decisiontree"></a>Árvore de decisão para a seleção do cenário
---
O diagrama a seguir resume os cenários descritos acima e o Advanced Analytics Process e opções de tecnologia feitas que leva-o para cada um dos cenários itemized. O processamento de dados, exploração, engenharia de recursos e amostragem podem ocorrer num ou mais método/ambiente -- nos ambientes de origem, intermédios e/ou alvo - e podem proceder iterativamente conforme necessário. O diagrama apenas serve para ilustrar alguns dos possíveis fluxos e não fornece uma enumeração exaustiva.

![Cenários de instruções de processo de DS de exemplo][8]

### <a name="advanced-analytics-in-action-examples"></a>Análises avançadas em ação exemplos
Para o ponto a ponto do Azure Machine Learning orientações passo a passo que empregam o Advanced Analytics Process e utilizar conjuntos de dados públicos de tecnologia, consulte:

* [O processo de ciência de dados de equipa em ação: utilizar o SQL Server](sql-walkthrough.md).
* [O processo de ciência de dados de equipa em ação: com clusters do HDInsight Hadoop](hive-walkthrough.md).

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
