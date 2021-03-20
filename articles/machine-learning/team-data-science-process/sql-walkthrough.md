---
title: Construa e implemente um modelo num SQL Server VM - Processo de Ciência de Dados de Equipa
description: Construa e implemente um modelo de aprendizagem automática utilizando o SQL Server num Azure VM com um conjunto de dados disponível ao público.
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
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007414"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>O processo de ciência de dados da equipa em ação: usando o SQL Server
Neste tutorial, você anda pelo processo de construção e implementação de um modelo de machine learning usando SQL Server e um conjunto de dados publicamente disponível -- o conjunto de dados [de Viagens de Táxi de NYC.](https://www.andresmh.com/nyctaxitrips/) O procedimento segue um fluxo de trabalho padrão da ciência dos dados: ingerir e explorar os dados, criar recursos para facilitar a aprendizagem, depois construir e implementar um modelo.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Descrição do conjunto de datas de viagens de táxi de NYC
Os dados da NyC Taxi Trip são cerca de 20 GB de ficheiros CSV comprimidos (~48 GB descomprimidos), compreendendo mais de 173 milhões de viagens individuais e as tarifas pagas por cada viagem. Cada registo de viagem inclui o local e a hora de recolha e entrega, número de licença de hack anonimizado (motorista) e número de medalhão (id único do táxi). Os dados cobrem todas as viagens do ano de 2013 e são fornecidos nos dois conjuntos de dados seguintes para cada mês:

1. O CSV 'trip_data' contém detalhes da viagem, tais como o número de passageiros, pontos de recolha e entrega, duração da viagem e duração da viagem. Aqui estão alguns registos de amostras:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. O CSV 'trip_fare' contém detalhes da tarifa paga por cada viagem, como tipo de pagamento, valor da tarifa, sobretaxa e impostos, gorjetas e portagens, e o valor total pago. Aqui estão alguns registos de amostras:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

A chave única para juntar dados de viagem \_ e tarifa de viagem é composta pelos \_ campos: medalhão, licença de hack \_ e data de \_ recolha.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exemplos de Tarefas de Previsão
Vamos formular três problemas de previsão com base no valor da *gorjeta, \_* nomeadamente:

* Classificação binária: Prever se uma gorjeta foi ou não paga por uma viagem, isto é, um *\_ valor* de gorjeta superior a $0 é um exemplo positivo, enquanto um *\_ valor* de gorjeta de $0 é um exemplo negativo.
* Classificação multiclasse: Para prever o intervalo de gorjeta paga pela viagem. Dividimos a *\_ quantidade de gorjeta* em cinco caixotes ou classes:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Tarefa de regressão: Prever o valor da gorjeta paga por uma viagem.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Criação do ambiente de ciência de dados Azure para análises avançadas
Como pode ver no guia [Plan Your Environment,](plan-your-environment.md) existem várias opções para trabalhar com o conjunto de dados de viagens de táxi de NYC em Azure:

* Trabalhe com os dados em blobs Azure e depois modele em Azure Machine Learning
* Carregue os dados numa base de dados do SQL Server e, em seguida, modele em Azure Machine Learning

Neste tutorial, demonstraremos a importação paralela a granel dos dados para um SQL Server, exploração de dados, engenharia de recursos e amostragem através do SQL Server Management Studio, bem como usando o IPython Notebook. [Os scripts de amostra e](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) [os cadernos IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) são partilhados no GitHub. Um caderno IPython de amostra para trabalhar com os dados em blobs Azure também está disponível no mesmo local.

Para configurar o seu ambiente Azure Data Science:

1. [Criar uma conta de armazenamento](../../storage/common/storage-account-create.md)
2. [Criar uma área de trabalho do Azure Machine Learning](../classic/create-workspace.md)
3. [Fornecer uma máquina virtual de ciência de dados,](../data-science-virtual-machine/overview.md)que fornece um Servidor SQL e um servidor de caderno iPython.
   
   > [!NOTE]
   > Os scripts de amostra e os cadernos IPython serão descarregados para a sua máquina virtual Data Science durante o processo de configuração. Quando o script de pós-instalação VM estiver concluído, as amostras estarão na biblioteca de Documentos VM:  
   > 
   > * Scripts de amostra: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Cadernos IPython da amostra: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   onde `<user_name>` está o nome de login do seu VM Windows. Referimo-nos às pastas da amostra como **Scripts de Amostra** e **Cadernos IPython de amostra.**
   > 
   > 

Com base no tamanho do conjunto de dados, na localização da fonte de dados e no ambiente alvo do Azure selecionado, este cenário é semelhante ao [Cenário \# 5: Grande conjunto de dados num ficheiro local, alvo sql server em Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Obtenha os dados de Fonte Pública
Para obter o conjunto de dados de [Viagens](https://www.andresmh.com/nyctaxitrips/) de Táxi de NYC a partir da sua localização pública, pode utilizar qualquer um dos métodos descritos em [Move Data de e para Azure Blob Storage](move-azure-blob.md) para copiar os dados para a sua nova máquina virtual.

Para copiar os dados utilizando a AzCopy:

1. Faça login na sua máquina virtual (VM)
2. Criar um novo diretório no disco de dados do VM (Nota: Não utilize o Disco Temporário que vem com o VM como disco de dados).
3. Numa janela de pedido de comando de comando, executar a seguinte linha de comando Azcopy, substituindo <path_to_data_folder> pela sua pasta de dados criada em (2):

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    Quando o AzCopy estiver concluído, um total de 24 ficheiros CSV com fecho (12 para dados de viagem \_ e 12 para a tarifa da \_ viagem) devem estar na pasta de dados.
4. Desaperte os ficheiros descarregados. Note a pasta onde residem os ficheiros não reprimidos. Esta pasta será referida como o caminho <\_ para \_ os \_ ficheiros de dados \> .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Dados de importação a granel na base de dados do servidor SQL
O desempenho do carregamento/transferência de grandes quantidades de dados para uma Base de Dados SQL e consultas subsequentes pode ser melhorado utilizando *tabelas e vistas partitionadas.* Nesta secção, seguiremos as instruções descritas na Importação paralela de Dados a Granel utilizando [tabelas de partição SQL](parallel-load-sql-partitioned-tables.md) para criar uma nova base de dados e carregar os dados em tabelas divididas em paralelo.

1. Enquanto inicia o seu VM, inicie **o SQL Server Management Studio**.
2. Conecte-se utilizando a autenticação do Windows.
   
    ![SSMS Ligam][12]
3. Se ainda não alterou o modo de autenticação do SQL Server e criou um novo utilizador de login SQL, abra o ficheiro de scripts nomeado **alterar \_ auth.sql** na pasta **Scripts de Amostra.** Altere o nome de utilizador e a palavra-passe predefinidos. Clique em **Executar** na barra de ferramentas para executar o script.
   
    ![Executar Script][13]
4. Verifique e/ou altere a base de dados padrão do SQL Server e as pastas de registo para garantir que as bases de dados recém-criadas serão armazenadas num Disco de Dados. A imagem VM do Servidor SQL que é otimizada para cargas de armazenamento de dados é pré-configurada com dados e discos de log. Se o seu VM não incluir um Disco de Dados e tiver adicionado novos discos rígidos virtuais durante o processo de configuração VM, altere as pastas predefinidos da seguinte forma:
   
   * Clique com o botão direito no nome SQL Server no painel esquerdo e clique em **Propriedades**.
     
       ![Propriedades do servidor SQL][14]
   * Selecione **Definições** de base de dados da lista **de página Seletiva** para a esquerda.
   * Verifique e/ou altere as **localizações predefinidos da Base de Dados** para as localizações do Disco de **Dados** à sua escolha. Esta localização é onde residem novas bases de dados se criadas com as definições predefinidos.
     
       ![Predefinições da base de dados SQL][15]  
5. Para criar uma nova base de dados e um conjunto de grupos de ficheiros para segurar as tabelas divididas, abra o script de amostra **criar \_ db \_ predefinido.sql**. O script criará uma nova base de dados chamada **TaxiNYC** e 12 grupos de ficheiros na localização de dados predefinidos. Cada grupo de ficheiros terá um mês de dados de viagem \_ e \_ dados de tarifas de viagem. Modifique o nome da base de dados, se desejar. Clique **em Executar** para executar o script.
6. Em seguida, crie duas mesas de partição, uma para os dados da viagem \_ e outra para a \_ viagem. Abra o roteiro da amostra **crie \_ mesa \_ dividida.sql,** que:
   
   * Crie uma função de partição para dividir os dados por mês.
   * Crie um esquema de partição para mapear os dados de cada mês para um grupo de ficheiros diferente.
   * Crie duas mesas divididas mapeadas para o esquema de partição: **a \_ viagem nyctaxi** irá conter os dados da viagem \_ e a **\_ tarifa nyctaxi** irá conter os dados da tarifa da \_ viagem.
     
     Clique em **Executar** para executar o script e criar as tabelas divididas.
7. Na pasta **Scripts sample scripts,** existem dois scripts PowerShell de amostra fornecidos para demonstrar importações paralelas a granel de dados para tabelas de servidorES SQL.
   
   * **\_generic.ps1paralelo \_ bcp** é um script genérico para dados paralelos de importação a granel em uma tabela. Modifique este script para definir as variáveis de entrada e alvo como indicado nas linhas de comentário no script.
   * **onyctaxi.ps1paralelo bcp \_ \_** é uma versão pré-configurada do script genérico e pode ser usado para carregar ambas as tabelas para os dados de Viagens de Táxi de NYC.  
8. Clique com o lado direito do **bcp \_ paralelo \_nyctaxi.ps1** nome do guião e clique em **Editar** para o abrir no PowerShell. Reveja as variáveis predefinidas e modifique de acordo com o nome da base de dados selecionada, pasta de dados de entrada, pasta de registo de destino e caminhos para os ficheiros do formato da amostra **nyctaxi_trip.xml** e **\_fare.xmlnyctaxi** (fornecidos na pasta **Scripts de Amostra).**
   
    ![Dados de importação a granel][16]
   
    Pode também selecionar o modo de autenticação, o padrão é a autenticação do Windows. Clique na seta verde na barra de ferramentas para correr. O guião lançará 24 operações de importação a granel em paralelo, 12 para cada mesa dividida. Pode monitorizar o progresso da importação de dados abrindo a pasta de dados padrão SQL Server conforme definido acima.
9. O script PowerShell relata os tempos de início e fim. Quando todas as importações a granel terminam, o tempo final é reportado. Verifique a pasta de registo de destino para verificar se as importações a granel foram bem sucedidas, ou seja, não há erros relatados na pasta de registo alvo.
10. A sua base de dados está agora pronta para exploração, engenharia de recursos e outras operações conforme desejado. Uma vez que as tabelas são divididas de acordo com o campo de **\_ data de recolha,** as consultas que incluem as condições de **\_ data de recolha** na cláusula **WHERE** beneficiarão do esquema de partição.
11. No **SQL Server Management Studio**, explore as consultas de **amostras \_ fornecidas.sql**. Para executar qualquer uma das consultas de amostra, realce as linhas de consulta e clique em **Executar** na barra de ferramentas.
12. Os dados da NYC Taxi Trips são carregados em duas tabelas separadas. Para melhorar as operações de junção, é altamente recomendado indexar as tabelas. O script da amostra **cria \_ índices \_ divididos.sql** cria índices divididos no medalhão de chave de **adesão, licença de hack \_ e \_ hora de recolha**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Exploração de dados e Engenharia de Recursos no SqL Server
Nesta secção, realizaremos a exploração de dados e geração de recursos executando consultas SQL diretamente no **SQL Server Management Studio** usando a base de dados sql Server criada anteriormente. Um guião de amostra chamado **\_ consultas de amostra.sql** é fornecido na pasta **Scripts de Amostra.** Modifique o script para alterar o nome da base de dados, se for diferente do padrão: **TaxiNYC**.

Neste exercício, iremos:

* Conecte-se ao **SQL Server Management Studio** utilizando a autenticação do Windows ou utilizando a autenticação SQL e o nome de login e senha sql.
* Explore a distribuição de dados de alguns campos em janelas de tempo variadas.
* Investigue a qualidade dos dados dos campos de longitude e latitude.
* Gere etiquetas de classificação binárias e multiclasses com base na **\_ quantidade de gorjeta**.
* Gere características e computação/compare distâncias de viagem.
* Junte-se às duas tabelas e extraa uma amostra aleatória que será usada para construir modelos.

Quando estiver pronto para prosseguir para a Azure Machine Learning, pode:  

1. Guarde a consulta final do SQL para extrair e recolher os dados e copiar a consulta diretamente num módulo [de dados de importação][import-data] em Azure Machine Learning, ou
2. Persista os dados recolhidos e projetados que pretende utilizar para a construção de modelos numa nova tabela de bases de dados e utilize a nova tabela no módulo [de Dados de Importação][import-data] em Azure Machine Learning.

Nesta secção, guardaremos a consulta final para extrair e recolher os dados. O segundo método é demonstrado na [secção de Exploração de Dados e Engenharia de Recursos na secção IPython Notebook.](#ipnb)

Para uma rápida verificação do número de linhas e colunas nas tabelas povoadas anteriormente, utilizando a importação paralela a granel,

- Reportar o número de linhas na tabela nyctaxi_trip sem digitalização de mesa: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Número de colunas na tabela nyctaxi_trip: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Exploração: Distribuição de viagem por medalhão
Este exemplo identifica o medalhão (números de táxi) com mais de 100 viagens num determinado período de tempo. A consulta beneficiaria do acesso à mesa dividido, uma vez que está condicionada pelo esquema de partição da **\_ data** de recolha . A consulta do conjunto de dados completo também utilizará a tabela dividida e/ou a varredura de índice.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Exploração: Distribuição de viagem por medalhão e hack_license

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Avaliação da Qualidade dos Dados: Verificar registos com longitude e/ou latitude incorretas
Este exemplo investiga se algum dos campos de longitude e/ou latitude contém um valor inválido (os graus radian devem estar entre -90 e 90), ou têm (0,0) coordenadas.

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploração: Distribuição de viagens com gorjeta vs. não gorjeta
Este exemplo encontra o número de viagens que foram inclinadas vs. não inclinadas num determinado período de tempo (ou em conjunto de dados completos se cobrir o ano inteiro). Esta distribuição reflete a distribuição da etiqueta binária para ser posteriormente utilizada para modelação de classificação binária.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Exploração: Classe de ponta/distribuição de alcance
Este exemplo calcula a distribuição das gamas de ponta num determinado período de tempo (ou no conjunto de dados completo se cobrir o ano inteiro). Esta distribuição das classes de etiquetas será usada mais tarde para modelação de classificação multiclasse.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Exploração: Calcular e Comparar distância de viagem
Este exemplo converte a longitude e a latitude de pickup e dropoff para pontos de geografia SQL, calcula a distância de viagem usando a diferença de pontos de geografia SQL, e devolve uma amostra aleatória dos resultados para comparação. O exemplo limita os resultados a coordenadas válidas apenas utilizando a consulta de avaliação da qualidade dos dados abrangida anteriormente.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Engenharia de Recursos em Consultas SQL
As consultas de exploração de geração de etiquetas e de conversão de geografia também podem ser usadas para gerar rótulos/características removendo a parte de contagem. Exemplos adicionais de engenharia de recursos SQL são fornecidos na [secção de Exploração de Dados e Engenharia de Recursos na secção IPython Notebook.](#ipnb) É mais eficiente executar as consultas de geração de funcionalidades no conjunto de dados completo ou um grande subconjunto do mesmo usando consultas SQL que funcionam diretamente na caixa de dados do SQL Server. As consultas podem ser executadas no **SQL Server Management Studio**, IPython Notebook, ou em qualquer ferramenta ou ambiente de desenvolvimento que possa aceder à base de dados local ou remotamente.

#### <a name="preparing-data-for-model-building"></a>Preparação de dados para a construção de modelos
A seguinte consulta junta-se às tabelas **de tarifas nyctaxi \_ e** **nyctaxi, \_** gera uma etiqueta de classificação binária **inclinada**, uma **\_ classe** de ponta de etiqueta de classificação multi-classe , e extrai uma amostra aleatória de 1% do conjunto de dados completo. Esta consulta pode ser copiada e colada diretamente no módulo [Azure Machine Learning Studio](https://studio.azureml.net) [Import Data][import-data] para ingestão direta de dados a partir da caixa de dados do SQL Server em Azure. A consulta exclui registos com coordenadas incorretas (0,0).

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Exploração de dados e engenharia de recursos em bloco de notas de IPython
Nesta secção, realizaremos a exploração de dados e geração de recursos utilizando consultas python e SQL com a base de dados sql Server criada anteriormente. Um caderno IPython de amostra chamado **machine-Learning-data-science-process-sql-story.ipynb** é fornecido na pasta **sample IPython Notebooks.** Este caderno também está disponível no [GitHub.](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks)

Ao trabalhar com grandes dados, siga esta sequência recomendada:

* Leia numa pequena amostra dos dados num quadro de dados na memória.
* Execute algumas visualizações e explorações utilizando os dados amostrados.
* Experimente a engenharia de recursos utilizando os dados amostrados.
* Para uma maior exploração de dados, manipulação de dados e engenharia de recursos, utilize python para emitir consultas SQL diretamente contra a base de dados do SQL Server no Azure VM.
* Decida o tamanho da amostra a utilizar para o edifício do modelo Azure Machine Learning.

Quando estiver pronto para prosseguir para a Azure Machine Learning, pode:  

1. Guarde a consulta final de SQL para extrair e recolher os dados e copiar a consulta diretamente num módulo [de Dados de Importação][import-data] em Azure Machine Learning. Este método é demonstrado nos [Modelos de Construção na secção Azure Machine Learning.](#mlmodel)    
2. Persistir os dados recolhidos e projetados que pretende utilizar para a construção de modelos numa nova tabela de bases de dados e, em seguida, utilizar a nova tabela no módulo [De Dados de Importação.][import-data]

Seguem-se alguns exemplos de exploração de dados, visualização de dados e exemplos de engenharia de recursos. Para mais exemplos, consulte a amostra do caderno SQL IPython na pasta **Sample IPython Notebooks.**

#### <a name="initialize-database-credentials"></a>Inicializar credenciais de base de dados
Inicialize as definições de ligação da sua base de dados nas seguintes variáveis:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Criar Ligação de Bases de Dados

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Reportar o número de linhas e colunas na tabela nyctaxi_trip

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Número total de linhas = 173179759  
* Número total de colunas = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Read-in uma pequena amostra de dados da Base de Dados do Servidor SQL

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

A hora de ler a tabela de amostras é de 6.49200 segundos  
Número de linhas e colunas recuperadas = (84952, 21)

#### <a name="descriptive-statistics"></a>Estatísticas Descritivas
Agora estão prontos para explorar os dados amostrados. Começamos por olhar para estatísticas descritivas para o campo de distância de **viagem \_** (ou qualquer outro) campo):

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Visualização: Exemplo do enredo da caixa
Em seguida, olhamos para o enredo da caixa para a distância de viagem para visualizar os quantiles

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![#1 do enredo][1]

#### <a name="visualization-distribution-plot-example"></a>Visualização: Exemplo do enredo de distribuição

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![#2 do enredo][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualização: Parcelas de barras e linhas
Neste exemplo, colocamos a distância de viagem em cinco caixotes e visualizamos os resultados de vinculação.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Podemos traçar a distribuição do caixote acima em um bar ou lote de linha como abaixo

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![#3 do enredo][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![#4 de enredo][4]

#### <a name="visualization-scatterplot-example"></a>Visualização: Scatterplot Exemplo
Mostramos o enredo de dispersão entre **o tempo de viagem em \_ \_ \_ segundos** e a distância da **viagem \_** para ver se há alguma correlação

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![#6 do enredo][6]

Da mesma forma, podemos verificar a relação entre o **\_ código de taxa** e **a distância \_ de viagem.**

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![#8 do enredo][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Sampling os Dados em SQL
Ao preparar dados para a construção de modelos no [Azure Machine Learning Studio,](https://studio.azureml.net)pode decidir sobre **a consulta SQL para utilizar diretamente no módulo de Dados de Importação** ou persistir os dados projetados e amostrados numa nova tabela, que poderá utilizar no módulo De [Dados de Importação][import-data] com um simples SELECT * FROM <o seu novo nome de mesa **\_ \_ \_>**.

Nesta secção, criaremos uma nova tabela para conter os dados amostrados e projetados. Um exemplo de uma consulta direta de SQL para a construção de modelos é fornecido na [secção de Exploração de Dados e Engenharia de Recursos na secção SQL Server.](#dbexplore)

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Crie uma tabela de amostras e povoe com 1% das tabelas unidas. Mesa de lançamento primeiro se existir.
Nesta secção, juntamo-nos às tabelas **nyctaxi \_ trip** e **tarifa nyctaxi, \_** extrair uma amostra aleatória de 1% e persistir os dados amostrados em um novo nome de mesa **nyctaxi \_ um por \_ cento**:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Exploração de dados usando consultas SQL em bloco de notas IPython
Nesta secção, exploramos as distribuições de dados utilizando os dados amostrados de 1% que persistem na nova tabela que criamos acima. Explorações semelhantes podem ser realizadas usando as tabelas originais, utilizando opcionalmente **tablesample** para limitar a amostra de exploração ou limitando os resultados a um determinado período de tempo usando as divisórias de **\_ data de recolha,** como ilustrado na [secção de Exploração de Dados e Engenharia de Recursos na secção SQL Server.](#dbexplore)

#### <a name="exploration-daily-distribution-of-trips"></a>Exploração: Distribuição diária de viagens

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploração: Distribuição de viagem por medalhão

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Geração de recursos usando consultas SQL em bloco de notas IPython
Nesta secção, iremos gerar novas etiquetas e funcionalidades utilizando diretamente consultas SQL, operando na tabela de amostras de 1% que criamos na secção anterior.

#### <a name="label-generation-generate-class-labels"></a>Geração de rótulos: Gerar rótulos de classe
No exemplo seguinte, geramos dois conjuntos de rótulos para utilizar para modelação:

1. Etiquetas de classe **binárias inclinadas** (prevendo se uma gorjeta será dada)
2. Classe de **ponta \_** de rótulos multiclasses (previsão do caixote da ponta ou alcance)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Engenharia de Recursos: Características do Conde para Colunas Catebláblias
Este exemplo transforma um campo categórico num campo numérico, substituindo cada categoria pela contagem das suas ocorrências nos dados.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Engenharia de Recursos: Características do caixote do lixo para colunas numéricas
Este exemplo transforma um campo numérico contínuo em gamas de categorias predefinidas, isto é, transformar o campo numérico num campo categórico.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Engenharia de Recursos: Características de localização de extração de Latitude/Longitude Decimal
Este exemplo decompõe a representação decimal de um campo de latitude e/ou longitude em múltiplos campos de granularidade, tais como, país/região, cidade, cidade, bloco, etc. Os novos geo-campos não estão mapeados para locais reais. Para obter informações sobre as localizações do geocódigo de mapeamento, consulte [os Serviços Bing Maps REST](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifique a forma final da tabela apresentando

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Estamos agora prontos para avançar para a construção de modelos e implantação de modelos em [Azure Machine Learning.](https://studio.azureml.net) Os dados estão prontos para qualquer um dos problemas de previsão identificados anteriormente, nomeadamente:

1. Classificação binária: Para prever se uma gorjeta foi ou não paga por uma viagem.
2. Classificação multiclasse: Para prever o intervalo de gorjeta paga, de acordo com as classes previamente definidas.
3. Tarefa de regressão: Prever o valor da gorjeta paga por uma viagem.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Modelos de construção em aprendizagem automática Azure
Para iniciar o exercício de modelação, inicie sessão no seu espaço de trabalho de Aprendizagem automática Azure. Se ainda não criou um espaço de trabalho de aprendizagem automática, consulte Criar um espaço de trabalho de [aprendizagem automática Azure.](../classic/create-workspace.md)

1. Para começar com a Azure Machine Learning, veja [o que é o Azure Machine Learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Faça login no [Azure Machine Learning Studio](https://studio.azureml.net).
3. A página Studio Home fornece uma grande quantidade de informações, vídeos, tutoriais, links para a Referência de Módulos, e outros recursos. Para obter mais informações sobre a Azure Machine Learning, consulte o [Centro de Documentação de Aprendizagem automática Azure.](https://azure.microsoft.com/documentation/services/machine-learning/)

Uma experiência de treino típica consiste nos seguintes passos:

1. Crie uma experiência **+NEW.**
2. Obtenha os dados para a Azure Machine Learning.
3. Pré-processar, transformar e manipular os dados conforme necessário.
4. Gerar características conforme necessário.
5. Divida os dados em conjuntos de dados de formação/validação/teste (ou tenha conjuntos de dados separados para cada um).
6. Selecione um ou mais algoritmos de aprendizagem automática dependendo do problema de aprendizagem para resolver. Por exemplo, classificação binária, classificação multiclasse, regressão.
7. Treine um ou mais modelos utilizando o conjunto de dados de treino.
8. Marque o conjunto de dados de validação utilizando o(s) modelo(s) treinado.
9. Avaliar os modelos para calcular as métricas relevantes para o problema de aprendizagem.
10. Sintonize os modelos e selecione o melhor modelo a ser implantado.

Neste exercício, já exploramos e concebemos os dados no SQL Server, e decidimos sobre o tamanho da amostra para ingerir em Azure Machine Learning. Para construir um ou mais dos modelos de previsão, decidimos:

1. Obtenha os dados para Azure Machine Learning utilizando o módulo [de dados de importação,][import-data] disponível na secção entrada e saída de **dados.** Para obter mais informações, consulte a página de referência do módulo [de dados de importação.][import-data]
   
    ![Dados de importação de aprendizagem de máquinas Azure][17]
2. Selecione **Azure SQL Database** como **fonte de dados** no painel **Propriedades.**
3. Introduza o nome DNS da base de dados no campo **de nome do servidor Database.** Formato: `tcp:<your_virtual_machine_DNS_name>,1433`
4. Introduza o **nome base de dados** no campo correspondente.
5. Introduza o **nome de utilizador SQL** no nome da **conta de utilizador** do Servidor e na **palavra-passe** da **conta de utilizador** do Servidor .
7. Na **área de consulta de base de dados** editar a área de texto, cole a consulta que extrai os campos de base de dados necessários (incluindo quaisquer campos calculados, como os rótulos) e diminui as amostras dos dados para o tamanho da amostra pretendido.

Um exemplo de uma experiência de classificação binária que lê dados diretamente da base de dados do SQL Server está na figura abaixo. Experiências semelhantes podem ser construídas para problemas de classificação e regressão multiclasse.

![Azure Machine Learning Train][10]

> [!IMPORTANT]
> Nos exemplos de extração de dados de modelação e de amostragem fornecidos nas secções anteriores, **todas as etiquetas para os três exercícios de modelação estão incluídas na consulta**. Um passo importante (necessário) em cada um dos exercícios de modelação consiste em **excluir** os rótulos desnecessários para os outros dois **problemas,** e quaisquer outras fugas de alvo . Por exemplo, quando utilizar a classificação binária, utilize a etiqueta **inclinada** e exclua a **classe \_ de ponta** dos campos, **a quantidade \_ de gorjeta** e **a \_ quantidade total**. Estes últimos são fugas de alvo, uma vez que implicam a gorjeta paga.
> 
> Para excluir colunas desnecessárias e/ou fugas de alvo, pode utilizar as [Colunas Selecionadas no][select-columns] módulo Dataset ou nos [Metadados de Edição.][edit-metadata] Para obter mais informações, consulte [Colunas selecionadas em páginas de][select-columns] referência de datas e [edite metadados.][edit-metadata]
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Implantação de modelos em aprendizagem automática Azure
Quando o seu modelo estiver pronto, pode facilmente implantá-lo como um serviço web diretamente da experiência. Para obter mais informações sobre a implementação de serviços web Azure Machine Learning, consulte [implementar um serviço web Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).

Para implementar um novo serviço web, precisa:

1. Criar uma experiência de pontuação.
2. Implemente o serviço web.

Para criar uma experiência de pontuação a partir de uma experiência de treino **acabada,** clique em **CREATE SCORING EXPERIMENT** na barra de ação inferior.

![Azure Marcação][18]

A Azure Machine Learning tentará criar uma experiência de pontuação baseada nos componentes da experiência de treino. Em particular, irá:

1. Guarde o modelo treinado e remova os módulos de treino do modelo.
2. Identifique uma porta de **entrada** lógica para representar o esquema de dados de entrada esperado.
3. Identifique uma **porta de saída** lógica para representar o esquema de saída esperado do serviço web.

Quando a experiência de pontuação for criada, reveja-a e ajuste-a conforme necessário. Um ajuste típico é substituir o conjunto de dados de entrada e/ou consulta por um que exclui os campos de etiquetas, uma vez que estas etiquetas não estarão disponíveis no esquema quando o serviço é chamado. É também uma boa prática reduzir o tamanho do conjunto de dados de entrada e/ou consulta para alguns registos, o suficiente para indicar o esquema de entrada. Para a porta de saída, é comum excluir todos os campos de entrada e incluir apenas as **etiquetas pontuadas** e **as probabilidades pontuadas** na saída utilizando as [Colunas Selecionadas no módulo Dataset.][select-columns]

Uma experiência de pontuação de amostra está na figura abaixo. Quando estiver pronto para ser implantado, clique no botão **PUBLISH WEB SERVICE** na barra de ação inferior.

![Publicação de Aprendizagem de Máquinas Azure][11]

Para recapitular, neste tutorial walkthrough, criou um ambiente de ciência de dados Azure, trabalhou com um grande conjunto de dados públicos desde a aquisição de dados até à formação de modelos e implementação de um serviço web Azure Machine Learning.

### <a name="license-information"></a>Informação sobre Licenças
Esta amostra e os seus scripts que acompanham e os portátils IPython são partilhados pela Microsoft sob a licença do MIT. Verifique o ficheiro LICENSE.txt no diretório do código de amostra no GitHub para obter mais detalhes.

### <a name="references"></a>Referências
•    [Página de descarregamento de viagens de táxi de Andrés Monroy NYC](https://www.andresmh.com/nyctaxitrips/)  
•    [FOILing NYC's Taxi Trip Data by Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• Investigação e Estatística da [Comissão de Táxis e Limusines](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page) de NYC

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data