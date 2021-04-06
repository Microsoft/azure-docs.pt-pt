---
title: Identificar cenários para Azure Machine Learning - Processo de Ciência de Dados de Equipa
description: Selecione os cenários apropriados para fazer análises preditivas avançadas com o Processo de Ciência de Dados de Equipa.
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
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93319299"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários de análises avançadas no Azure Machine Learning
Este artigo descreve a variedade de fontes de dados de amostra e cenários-alvo que podem ser tratados pelo Processo de Ciência de [Dados de Equipa (TDSP)](overview.md). O TDSP fornece uma abordagem sistemática para que as equipas colaborem na construção de aplicações inteligentes. Os cenários aqui apresentados ilustram as opções disponíveis no fluxo de trabalho de processamento de dados que dependem das características dos dados, das localizações de origem e dos repositórios-alvo em Azure.

A **árvore de decisão** para selecionar os cenários de amostra adequados aos seus dados e objetivo é apresentada na última secção.

Cada uma das secções seguintes apresenta um cenário de amostra. Para cada cenário, uma possível ciência de dados ou fluxo de análise avançado e recursos de suporte Azure são listados.

> [!NOTE]
> **Para todos os seguintes cenários, você precisa:**
> <br/>
> 
> * [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
>   <br/>
> * [Criar uma área de trabalho do Azure Machine Learning](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Cenário \# 1: Conjunto de dados tabulares pequenos a médios em ficheiros locais
![Pequenos e médios ficheiros locais][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionais do Azure: Nenhum
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Faça o upload de um conjunto de dados.
1. Construa um fluxo de experiências Azure Machine Learning começando com conjuntos de dados carregados.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Cenário \# 2: Conjunto de dados pequeno a médio de ficheiros locais que requerem processamento
![Pequenos e médios ficheiros locais com processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos Azure adicionais: Azure Virtual Machine (servidor de caderno IPython)
1. Crie uma máquina virtual Azure executando o bloco de notas IPython.
1. Faça o upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no IPython Notebook, acedendo a dados do contentor de armazenamento Azure.
1. Transforme os dados num formulário tabular limpo.
1. Guarde dados transformados em bolhas Azure.
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Leia os dados das bolhas Azure utilizando o módulo [de Dados de Importação.][import-data]
1. Construa um fluxo de experiências Azure Machine Learning começando com conjuntos de dados ingeridos.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Cenário \# 3: Grande conjunto de dados de ficheiros locais, direcionados para a Azure Blobs
![Grandes arquivos locais][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos Azure adicionais: Azure Virtual Machine (servidor de caderno IPython)
1. Crie uma máquina virtual Azure executando o bloco de notas IPython.
1. Faça o upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no IPython Notebook, acedendo a dados de blobs Azure.
1. Transforme os dados num formulário tabular limpo, se necessário.
1. Explore os dados e crie funcionalidades conforme necessário.
1. Extrair uma amostra de dados pequena a média.
1. Guarde os dados recolhidos em bolhas Azure.
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Leia os dados das bolhas Azure utilizando o módulo [de Dados de Importação.][import-data]
1. Construa o fluxo de experiências de aprendizagem automática Azure começando com conjuntos de dados ingeridos.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Cenário \# 4: Conjunto de dados pequeno a médio de ficheiros locais, direcionando o SQL Server numa Máquina Virtual Azure
![Pequenos e médios ficheiros locais para SQL DB em Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos Azure adicionais: Azure Virtual Machine (SQL Server / IPython Notebook server)
1. Crie uma máquina virtual Azure executando o SQL Server + IPython Notebook.
1. Faça o upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no recipiente de armazenamento Azure usando o Bloco de Notas IPython.
1. Transforme os dados num formulário tabular limpo, se necessário.
1. Guarde os dados para ficheiros VM-locais (o IPython Notebook está a funcionar em VM, as unidades locais referem-se às unidades VM).
1. Carregue os dados para a base de dados do SQL Server em execução num VM Azure.
   
   Opção \# 1: Utilização do SQL Server Management Studio.
   
   * Faça login no SQL Server VM
   * Executar o SQL Server Management Studio.
   * Criar bases de dados e tabelas-alvo.
   * Utilize um dos métodos de importação a granel para carregar os dados a partir de ficheiros VM-locais.
   
   Opção \# 2: Utilização do Caderno IPython – não aconselhável para conjuntos de dados médios e maiores
   
   <!-- -->    
   * Utilize o fio de ligação ODBC para aceder ao SqL Server em VM.
   * Criar bases de dados e tabelas-alvo.
   * Utilize um dos métodos de importação a granel para carregar os dados a partir de ficheiros VM-locais.
1. Explore os dados, crie funcionalidades conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas note a consulta necessária para criá-los.
1. Decida o tamanho da amostra de dados, se necessário e/ou desejado.
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Leia os dados diretamente do SqL Server utilizando o módulo [de dados de importação.][import-data] Cole a consulta necessária que extrai campos, cria características e amostra dados se necessário diretamente na consulta [de Dados de Importação.][import-data]
1. Construa o fluxo de experiências de aprendizagem automática Azure começando com conjuntos de dados ingeridos.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Cenário \# 5: Conjunto de dados grande em ficheiros locais, target SQL Server em Azure VM
![Grandes ficheiros locais para SQL DB em Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos Azure adicionais: Azure Virtual Machine (SQL Server / IPython Notebook server)
1. Crie uma máquina virtual Azure que executa o servidor SQL e o servidor de cadernos IPython.
1. Faça o upload de dados para um recipiente de armazenamento Azure.
1. (Opcional) Pré-processar e limpar dados.
   
    a.  Pré-processar e limpar dados no IPython Notebook, acedendo a dados de blobs Azure.
   
    b.  Transforme os dados num formulário tabular limpo, se necessário.
   
    c.  Guarde os dados para ficheiros VM-locais (o IPython Notebook está a funcionar em VM, as unidades locais referem-se às unidades VM).
1. Carregue os dados para a base de dados do SQL Server em execução num VM Azure.
   
    a.  Faça login no SQL Server VM.
   
    b.  Se os dados ainda não forem guardados, descarregue ficheiros de dados do contentor de armazenamento Azure para a pasta local-VM.
   
    c.  Executar o SQL Server Management Studio.
   
    d.  Criar bases de dados e tabelas-alvo.
   
    e.  Utilize um dos métodos de importação a granel para carregar os dados.
   
    f.  Se forem necessárias juntas de mesa, crie índices para acelerar as juntas.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes tamanhos de dados, recomenda-se que crie tabelas divididas e importe a granel os dados em paralelo. Para obter mais informações, consulte [a Importe De Dados Paralelos para as Tabelas Partitioned SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore os dados, crie funcionalidades conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas note a consulta necessária para criá-los.
1. Decida o tamanho da amostra de dados, se necessário e/ou desejado.
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Leia os dados diretamente do SqL Server utilizando o módulo [de dados de importação.][import-data] Cole a consulta necessária que extrai campos, cria características e amostra dados se necessário diretamente na consulta [de Dados de Importação.][import-data]
1. Fluxo de experiência de aprendizagem de máquina simples Azure começando com conjunto de dados carregado

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Cenário \# 6: Conjunto de dados grande numa base de dados do SQL Server nas instalações, direcionando o SQL Server numa Máquina Virtual Azure
![Grande SQL DB on-prem para SQL DB em Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos Azure adicionais: Azure Virtual Machine (SQL Server / IPython Notebook server)
1. Crie uma máquina virtual Azure que executa o servidor SQL e o servidor de cadernos IPython.
1. Utilize um dos métodos de exportação de dados para exportar os dados do SQL Server para despejar ficheiros.
   
   > [!NOTE]
   > Se decidir mover todos os dados da base de dados de instalações, um método alternativo (mais rápido) para mover a base de dados completa para a instância do SQL Server em Azure. Ignore as etapas para exportar dados, crie base de dados e carregue/importe dados para a base de dados-alvo e siga o método alternativo.
   > 
   > 
1. Faça o upload de ficheiros de despejo para o contentor de armazenamento Azure.
1. Carregue os dados numa base de dados do SQL Server em execução numa Máquina Virtual Azure.
   
   a.  Faça login no SQL Server VM.
   
   b.  Descarregue ficheiros de dados de um recipiente de Armazenamento Azure para a pasta local-VM.
   
   c.  Executar o SQL Server Management Studio.
   
   d.  Criar bases de dados e tabelas-alvo.
   
   e.  Utilize um dos métodos de importação a granel para carregar os dados.
   
   f.  Se forem necessárias juntas de mesa, crie índices para acelerar as juntas.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes tamanhos de dados, crie tabelas divididas e importe em massa os dados em paralelo. Para obter mais informações, consulte [a Importe De Dados Paralelos para as Tabelas Partitioned SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore os dados, crie funcionalidades conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas note a consulta necessária para criá-los.
1. Decida o tamanho da amostra de dados, se necessário e/ou desejado.
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Leia os dados diretamente do SqL Server utilizando o módulo [de dados de importação.][import-data] Cole a consulta necessária que extrai campos, cria características e amostra dados se necessário diretamente na consulta [de Dados de Importação.][import-data]
1. Fluxo de experiência de aprendizagem automática simples Azure começando com conjunto de dados carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar uma base de dados completa de um servidor SQL no local para Azure SQL Database
![Retire a DB local e anexe-se ao SQL DB em Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos Azure adicionais: Azure Virtual Machine (SQL Server / IPython Notebook server)
Para replicar toda a base de dados do SQL Server no seu SQL Server VM, deverá copiar uma base de dados de um local/servidor para outro, assumindo que a base de dados pode ser retirada temporariamente offline. Pode utilizar o SQL Server Management Studio Object Explorer ou utilizar os comandos Transt-SQL equivalentes.

1. Retire a base de dados no local da origem. Para obter mais informações, consulte [a desvinculação de uma base de dados.](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx)
1. Na janela de pedido do Windows Explorer ou do Windows Command, copie o ficheiro de base de dados ou ficheiros de base de dados separados e faça login ou ficheiros para a localização do alvo no SQL Server VM em Azure.
1. Fixe os ficheiros copiados à instância do servidor SQL alvo. Para mais informações, consulte [anexar uma base de dados.](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx)

[Mover uma base de dados utilizando o desacoplar e anexar (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Cenário \# 7: Big data em ficheiros locais, base de dados de Hive alvo em clusters Azure HDInsight Hadoop
![Big data na colmeia alvo local][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos Azure adicionais: Azure HDInsight Hadoop Cluster e Azure Virtual Machine (servidor de caderno IPython)
1. Crie uma máquina virtual Azure executando o servidor IPython Notebook.
1. Crie um cluster Azure HDInsight Hadoop.
1. (Opcional) Pré-processar e limpar dados.
   
   a.  Pré-processar e limpar dados no IPython Notebook, acedendo a dados de blobs Azure.
   
   b.  Transforme os dados num formulário tabular limpo, se necessário.
   
   c.  Guarde os dados para ficheiros VM-locais (o IPython Notebook está a funcionar em VM, as unidades locais referem-se às unidades VM).
1. Faça o upload dos dados para o recipiente predefinido do cluster Hadoop selecionado no passo 2.
1. Carregue os dados da base de dados da Hive no cluster Azure HDInsight Hadoop.
   
   a.  Faça login no nó de cabeça do cluster Hadoop
   
   b.  Abra a Linha de Comando Hadoop.
   
   c.  Insira o diretório de raiz da Colmeia por comando `cd %hive_home%\bin` na Linha de Comando Hadoop.
   
   d.  Execute as consultas da Hive para criar bases de dados e tabelas, e carregue dados do armazenamento de bolhas para as tabelas de Colmeia.
   
   > [!NOTE]
   > Se os dados forem grandes, os utilizadores podem criar a tabela Hive com divisórias. Em seguida, os utilizadores podem usar um `for` loop na Linha de Comando Hadoop no nó da cabeça para carregar dados na tabela hive dividida por partição.
   > 
   > 
1. Explore os dados e crie funcionalidades conforme necessário na Linha de Comando Hadoop. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Apenas note a consulta necessária para criá-los.
   
   a.  Faça login no nó de cabeça do cluster Hadoop
   
   b.  Abra a Linha de Comando Hadoop.
   
   c.  Insira o diretório de raiz da Colmeia por comando `cd %hive_home%\bin` na Linha de Comando Hadoop.
   
   d.  Executar as consultas de Colmeia na Linha de Comando Hadoop no nó de cabeça do cluster Hadoop para explorar os dados e criar funcionalidades conforme necessário.
1. Se necessário e/ou desejar, experimente os dados para caber no Azure Machine Learning Studio.
1. Inscreva-se no [Azure Machine Learning Studio.](https://studio.azureml.net/)
1. Leia os dados diretamente a partir do `Hive Queries` módulo [de dados de importação.][import-data] Cole a consulta necessária que extrai campos, cria características e amostra dados se necessário diretamente na consulta [de Dados de Importação.][import-data]
1. Fluxo de experiência de aprendizagem automática simples Azure começando com conjunto de dados carregado.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Árvore de decisão para a seleção de cenários
---
O diagrama seguinte resume os cenários acima descritos e as escolhas avançadas de Processo e Tecnologia de Análise feitas que o levam a cada um dos cenários itemados. O processamento de dados, a exploração, a engenharia de recursos e a amostragem podem ocorrer num ou mais método/ambiente -- nos ambientes de origem, intermédios e/ou alvos - e podem proceder iterativamente, se necessário. O diagrama serve apenas como ilustração de alguns dos fluxos possíveis e não fornece uma enumeração exaustiva.

![Exemplo de DS processa cenários de passagem][8]

### <a name="advanced-analytics-in-action-examples"></a>Análise Avançada em Exemplos de Ação
Para as análises de aprendizagem automática de azure que utilizam o Processo e Tecnologia De Análise Avançada utilizando conjuntos de dados públicos, consulte:

* [Processo de ciência de dados da equipa em ação: utilização do SQL Server](sql-walkthrough.md).
* [Processo de Ciência de Dados de Equipa em ação: utilizando clusters HDInsight Hadoop](hive-walkthrough.md).

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
[import-data]: /azure/machine-learning/studio-module-reference/import-data