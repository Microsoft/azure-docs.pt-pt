---
title: Identificar cenários para Azure Machine Learning - Team Data Science Process
description: Selecione os cenários apropriados para fazer análises preditivas avançadas com o Processo de Ciência de Dados da Equipa.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251625"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Cenários de análises avançadas no Azure Machine Learning
Este artigo descreve a variedade de fontes de dados de amostra e cenários-alvo que podem ser tratados pelo Processo de Ciência de Dados da [Equipa (TDSP)](overview.md). A TDSP fornece uma abordagem sistemática para as equipas colaborarem na construção de aplicações inteligentes. Os cenários aqui apresentados ilustram opções disponíveis no fluxo de trabalho de processamento de dados que dependem das características dos dados, das localizações de origem e dos repositórios-alvo em Azure.

A árvore de **decisão** para selecionar os cenários de amostra que são adequados para os seus dados e objetivo é apresentada na última secção.

Cada uma das seguintes secções apresenta um cenário de amostragem. Para cada cenário, estão listados um possível fluxo de análise de dados ou análise avançada e recursos de apoio do Azure.

> [!NOTE]
> **Para todos os seguintes cenários, é necessário:**
> <br/>
> 
> * [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
>   <br/>
> * [Criar um espaço de trabalho azure machine learning](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Cenário \#1: Conjunto de dados tabular pequeno a médio nos ficheiros locais
![Pequenos a médios ficheiros locais][1]

#### <a name="additional-azure-resources-none"></a>Recursos adicionais azure: Nenhum
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Faça upload de um conjunto de dados.
1. Construa um fluxo de experiência de aprendizagem de máquinas azure começando com conjuntos de dados carregados.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Cenário \#2: Conjunto de dados pequeno a médio dos ficheiros locais que requerem processamento
![Pequenos a médios ficheiros locais com processamento][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos azure adicionais: Máquina Virtual Azure (servidor IPython Notebook)
1. Crie uma máquina virtual Azure com o IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no IPython Notebook, acedendo a dados do contentor de armazenamento Azure.
1. Transforme os dados num formato tabular limpo.
1. Guarde dados transformados em bolhas Azure.
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Leia os dados das bolhas Azure utilizando o módulo [dados de importação.][import-data]
1. Construa um fluxo de experiência de aprendizagem automática Azure a partir de conjuntos de dados ingeridos.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Cenário \#3: Grande conjunto de dados de ficheiros locais, direcionados para as Bolhas Azure
![Grandes arquivos locais][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Recursos azure adicionais: Máquina Virtual Azure (servidor IPython Notebook)
1. Crie uma máquina virtual Azure com o IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no IPython Notebook, acedendo a dados de blobs Azure.
1. Transforme os dados num formato tabular limpo, se necessário.
1. Explore os dados e crie funcionalidades conforme necessário.
1. Extrair uma amostra de dados de pequeno a médio.
1. Guarde os dados amostrados em bolhas Azure.
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Leia os dados das bolhas Azure utilizando o módulo [dados de importação.][import-data]
1. Construir fluxo de experiência de machine learning Azure começando com conjuntos de dados ingeridos.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Cenário \#4: Conjunto de dados pequeno a médio de ficheiros locais, direcionado para o Servidor SQL numa Máquina Virtual Azure
![Pequenos a médios ficheiros locais para SQL DB em Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos azure adicionais: Máquina Virtual Azure (Servidor SQL / servidor Deporpor IPython)
1. Crie uma máquina virtual Azure executando O Servidor SQL + IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. Pré-processar e limpar dados no recipiente de armazenamento Azure utilizando o IPython Notebook.
1. Transforme os dados num formato tabular limpo, se necessário.
1. Guarde dados para ficheiros vm-locais (O IPython Notebook está em execução em VM, as unidades locais referem-se a unidades VM).
1. Carregue os dados para a base de dados do SQL Server em execução num VM Azure.
   
   Opção \#1: Utilização do Estúdio de Gestão de Servidores SQL.
   
   * Iniciar sessão no SQL Server VM
   * Executar o estúdio de gestão de servidores SQL.
   * Criar bases de dados e tabelas de alvos.
   * Utilize um dos métodos de importação a granel para carregar os dados a partir de ficheiros VM-local.
   
   Opção \#2: Utilização do Notebook IPython – não aconselhável para conjuntos de dados médios e maiores
   
   <!-- -->    
   * Utilize a cadeia de ligação ODBC para aceder ao Servidor SQL em VM.
   * Criar bases de dados e tabelas de alvos.
   * Utilize um dos métodos de importação a granel para carregar os dados a partir de ficheiros VM-local.
1. Explore dados, crie funcionalidades conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Note a consulta necessária para criá-los.
1. Decida sobre o tamanho da amostra de dados, se necessário e/ou desejado.
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Leia os dados diretamente do Servidor SQL utilizando o módulo [de Dados de Importação.][import-data] Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Construir fluxo de experiência de machine learning Azure começando com conjuntos de dados ingeridos.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Cenário \#5: Grande conjunto de dados em ficheiros locais, alvo SQL Server em Azure VM
![Grandes ficheiros locais para SQL DB em Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos azure adicionais: Máquina Virtual Azure (Servidor SQL / servidor Deporpor IPython)
1. Crie uma máquina virtual Azure que execute o Servidor SQL e o servidor IPython Notebook.
1. Faça upload de dados para um recipiente de armazenamento Azure.
1. (Opcional) Pré-processo e dados limpos.
   
    a.  Pré-processar e limpar dados no IPython Notebook, acedendo a dados de blobs Azure.
   
    b.  Transforme os dados num formato tabular limpo, se necessário.
   
    c.  Guarde dados para ficheiros vm-locais (O IPython Notebook está em execução em VM, as unidades locais referem-se a unidades VM).
1. Carregue os dados para a base de dados do SQL Server em execução num VM Azure.
   
    a.  Inicie sessão no SQL Server VM.
   
    b.  Se os dados ainda não forem guardados, descarregue ficheiros de dados do contentor de armazenamento Azure para a pasta VM local.
   
    c.  Executar o estúdio de gestão de servidores SQL.
   
    d.  Criar bases de dados e tabelas de alvos.
   
    e.  Utilize um dos métodos de importação a granel para carregar os dados.
   
    f.  Se forem necessárias juntas de mesa, crie índices para acelerar as juntas.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes tamanhos de dados, recomenda-se que crie tabelas divididas e importe os dados em paralelo. Para mais informações, consulte a [Importação de Dados Paralelos para tabelas partidas SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore dados, crie funcionalidades conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Note a consulta necessária para criá-los.
1. Decida sobre o tamanho da amostra de dados, se necessário e/ou desejado.
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Leia os dados diretamente do Servidor SQL utilizando o módulo [de Dados de Importação.][import-data] Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Fluxo de experiência de aprendizagem de máquina saquires simples azure começando com conjunto de dados carregado

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Cenário \#6: Grande conjunto de dados numa base de dados do Servidor SQL nas instalações, direcionando o Servidor SQL numa Máquina Virtual Azure
![Grande SQL DB on-prem para SQL DB em Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos azure adicionais: Máquina Virtual Azure (Servidor SQL / servidor Deporpor IPython)
1. Crie uma máquina virtual Azure que execute o Servidor SQL e o servidor IPython Notebook.
1. Utilize um dos métodos de exportação de dados para exportar os dados do SQL Server para despejar ficheiros.
   
   > [!NOTE]
   > Se decidir mover todos os dados da base de dados no local, um método alternativo (mais rápido) para mover a base de dados completa para a instância Do Servidor SQL em Azure. Ignore as medidas de exportação de dados, crie base de dados e carregue/importe dados para a base de dados alvo e siga o método alternativo.
   > 
   > 
1. Faça upload de ficheiros de despejo para o contentor de armazenamento Azure.
1. Carregue os dados para uma base de dados do SQL Server em execução numa Máquina Virtual Azure.
   
   a.  Inicie sessão no VM do Servidor SQL.
   
   b.  Descarregue ficheiros de dados de um contentor de Armazenamento Azure para a pasta VM local.
   
   c.  Executar o estúdio de gestão de servidores SQL.
   
   d.  Criar bases de dados e tabelas de alvos.
   
   e.  Utilize um dos métodos de importação a granel para carregar os dados.
   
   f.  Se forem necessárias juntas de mesa, crie índices para acelerar as juntas.
   
   > [!NOTE]
   > Para um carregamento mais rápido de grandes tamanhos de dados, crie tabelas divididas e importe os dados em paralelo. Para mais informações, consulte a [Importação de Dados Paralelos para tabelas partidas SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Explore dados, crie funcionalidades conforme necessário. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Note a consulta necessária para criá-los.
1. Decida sobre o tamanho da amostra de dados, se necessário e/ou desejado.
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Leia os dados diretamente do Servidor SQL utilizando o módulo [de Dados de Importação.][import-data] Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Fluxo de experiência de aprendizagem automática azure simples começando com conjunto de dados carregado.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Método alternativo para copiar uma base de dados completa de um Servidor SQL no local para Base de Dados SQL Azure
![Desacate db local e anexe-se ao SQL DB em Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Recursos azure adicionais: Máquina Virtual Azure (Servidor SQL / servidor Deporpor IPython)
Para replicar toda a base de dados do SQL Server no seu VM do Servidor SQL, deve copiar uma base de dados de uma localização/servidor para outra, assumindo que a base de dados pode ser retirada temporariamente offline. Pode utilizar o SQL Server Management Studio Object Explorer ou utilizar os comandos transact-SQL equivalentes.

1. Desaque a base de dados no local de origem. Para mais informações, consulte [Desvincular uma base de dados](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. No Windows Explorer ou windows Command Prompt janela, copie o ficheiro ou ficheiros de base de dados separados e o ficheiro ou ficheiros de registo para a localização do alvo no VM do Servidor SQL em Azure.
1. Fixe os ficheiros copiados à instância do Servidor SQL alvo. Para mais informações, consulte [anexar uma Base de Dados](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Mova uma base de dados utilizando o desprendimento e o anexo (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Cenário \#7: Big data em ficheiros locais, base de dados de hive-alvo em clusters Hadoop Azure HDInsight
![Big data na colmeia alvo local][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Recursos azure adicionais: Azure HDInsight Hadoop Cluster e Máquina Virtual Azure (servidor IPython Notebook)
1. Crie uma máquina virtual Azure que execute o servidor IPython Notebook.
1. Crie um cluster Hadoop Azure HDInsight.
1. (Opcional) Pré-processo e dados limpos.
   
   a.  Pré-processo e dados limpos no IPython Notebook, acedendo a dados do Azure
   
       blobs.
   
   b.  Transforme os dados num formato tabular limpo, se necessário.
   
   c.  Guarde dados para ficheiros vm-locais (O IPython Notebook está em execução em VM, as unidades locais referem-se a unidades VM).
1. Faça upload de dados para o recipiente predefinido do cluster Hadoop selecionado no passo 2.
1. Carregue dados para a base de dados da Hive no cluster Hadoop Azure HDInsight.
   
   a.  Inicie sessão no nó da cabeça do aglomerado hadoop
   
   b.  Abra a Linha de Comando Hadoop.
   
   c.  Entre no diretório raiz `cd %hive_home%\bin` da Colmeia por comando na Linha de Comando Hadoop.
   
   d.  Execute as consultas da Hive para criar bases de dados e tabelas, e carregue dados do armazenamento de blob para as mesas da Colmeia.
   
   > [!NOTE]
   > Se os dados forem grandes, os utilizadores podem criar a tabela Hive com divisórias. Em seguida, os `for` utilizadores podem usar um loop na Linha de Comando Hadoop no nó da cabeça para carregar dados na tabela Hive dividida por divisória.
   > 
   > 
1. Explore os dados e crie funcionalidades conforme necessário na Linha de Comando Hadoop. As funcionalidades não precisam de ser materializadas nas tabelas de bases de dados. Note a consulta necessária para criá-los.
   
   a.  Inicie sessão no nó da cabeça do aglomerado hadoop
   
   b.  Abra a Linha de Comando Hadoop.
   
   c.  Entre no diretório raiz `cd %hive_home%\bin` da Colmeia por comando na Linha de Comando Hadoop.
   
   d.  Execute as consultas da Colmeia na Linha de Comando hadoop no nó de cabeça do cluster Hadoop para explorar os dados e criar funcionalidades conforme necessário.
1. Se necessário e/ou desejado, experimente os dados para caber no Estúdio de Aprendizagem automática Azure.
1. Inscreva-se no Estúdio de [Aprendizagem automática Azure.](https://studio.azureml.net/)
1. Leia os dados diretamente a `Hive Queries` partir do módulo de dados de [importação.][import-data] Colar a consulta necessária que extrai campos, cria funcionalidades e amostra os dados se necessário diretamente na consulta de Dados de [Importação.][import-data]
1. Fluxo de experiência de aprendizagem automática azure simples começando com conjunto de dados carregado.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Árvore de decisão para seleção de cenários
---
O diagrama seguinte resume os cenários acima descritos e as escolhas avançadas de processo e tecnologia de Análise feitas que o levam a cada um dos cenários item. O processamento de dados, exploração, engenharia de recursos e amostragem podem ocorrer num ou mais método/ambiente -- nos ambientes de origem, intermédios e/ou alvo - e podem proceder iterativamente conforme necessário. O diagrama serve apenas como uma ilustração de alguns dos possíveis fluxos e não fornece uma enumeração exaustiva.

![Exemplo De Processo DS cenários de walkthrough][8]

### <a name="advanced-analytics-in-action-examples"></a>Análise Avançada em Exemplos de ação
Para caminhadas de aprendizagem automática azure de ponta a ponta que empregam o Processo e Tecnologia Avançada de Analytics utilizando conjuntos de dados públicos, consulte:

* Processo de Ciência de [Dados da equipa em ação: utilizando o Servidor SQL](sql-walkthrough.md).
* Processo de Ciência de [Dados da equipa em ação: utilizando aglomerados Hadoop HDInsight](hive-walkthrough.md).

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
