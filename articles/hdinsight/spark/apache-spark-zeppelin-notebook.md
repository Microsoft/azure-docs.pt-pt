---
title: Cadernos Zeppelin & aglomerado Apache Spark - Azure HDInsight
description: Instruções passo a passo sobre como usar cadernos Zeppelin com clusters Apache Spark em Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 969dda4b57deb35d745cf7c7d263b78e6fe577d2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867547"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Utilizar blocos de notas do Apache Zeppelin com o cluster do Apache Spark no Azure HDInsight

Os clusters HDInsight Spark incluem cadernos [Apache Zeppelin.](https://zeppelin.apache.org/) Usa os cadernos para gerir os empregos da Apache Spark. Neste artigo, você aprende a usar o caderno Zeppelin em um cluster HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* O esquema URI para o armazenamento primário dos seus clusters. O esquema seria `wasb://` para o Azure Blob Storage, `abfs://` para a Azure Data Lake Storage Gen2 ou `adl://` para a Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Armazenamento Blob, o URI será `wasbs://` .  Para mais informações, consulte [Exigir transferência segura no Azure Storage](../../storage/common/storage-require-secure-transfer.md) .

## <a name="launch-an-apache-zeppelin-notebook"></a>Lançar um caderno Apache Zeppelin

1. A partir do conjunto **spark,** selecione **o caderno Zeppelin** dos **dashboards cluster**. Introduza as credenciais de administração para o cluster.  

   > [!NOTE]  
   > Pode também chegar ao Bloco de Notas zeppelin para o seu cluster abrindo o seguinte URL no seu browser. Substitua **CLUSTERNAME** pelo nome do cluster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de notas. A partir do painel de cabeçalho, navegue para **Notebook**  >  **Crie uma nova nota.**

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png " alt-text="Criar um novo caderno Zeppelin" border="true":::

    Introduza um nome para o caderno e, em seguida, **selecione Criar Nota**.

3. Certifique-se de que o cabeçalho do caderno mostra um estado ligado. É denotado por um ponto verde no canto superior direito.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png " alt-text="Estado do caderno zeppelin" border="true":::

4. Carregue dados de exemplo para uma tabela temporária. Quando cria um cluster Spark em HDInsight, o ficheiro de dados da `hvac.csv` amostra, é copiado para a conta de armazenamento associada em `\HdiSamples\SensorSampleData\hvac` .

    No parágrafo vazio que é criado por defeito no novo caderno, cole o seguinte corte.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)

    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0),
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Prima **SHIFT + ENTER** ou selecione o botão **Reproduzir** para o parágrafo executar o corte. O estado no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, RUNNING para TERMINADO. A saída aparece na parte inferior do mesmo parágrafo. A imagem parece a seguinte imagem:

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png " alt-text="Criar uma tabela temporária a partir de dados brutos" border="true":::

    Também pode fornecer um título a cada parágrafo. A partir do canto direito do parágrafo, selecione o ícone **Definições** (sprocket) e, em seguida, selecione **o título de Show**.  

    > [!NOTE]  
    > O intérprete %spark2 não é suportado em cadernos Zeppelin em todas as versões HDInsight, e o intérprete %sh não será suportado a partir de HDInsight 4.0 em diante.

5. Agora pode executar declarações spark SQL na `hvac` mesa. Cole a seguinte consulta num novo parágrafo. A consulta recupera a identificação do edifício. Também a diferença entre o alvo e as temperaturas reais para cada edifício numa dada data. MUDANÇA DE IMPRENSA **+ ENTER**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13"
    ```  

    A **declaração de %sql** no início diz ao caderno para usar o intérprete Livy Scala.

6. Selecione o ícone **'Gráfico de barras'** para alterar o visor.  **definições**, apareça depois de ter selecionado **o Gráfico de Barras,** permite-lhe escolher **Chaves** e **Valores**.  A imagem que se segue mostra a saída.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png " alt-text="Executar uma declaração de Spark SQL usando o caderno1" border="true":::

7. Também pode executar declarações Spark SQL usando variáveis na consulta. O próximo corte mostra como definir uma variável, `Temp` na consulta com os possíveis valores com os quais pretende consultar. Quando se faz a consulta pela primeira vez, uma queda é automaticamente povoada com os valores especificados para a variável.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Cole este corte num novo parágrafo e prima **SHIFT + ENTER**. Em seguida, selecione **65** **da** lista de abandono temporário.

8. Selecione o ícone **'Gráfico de barras'** para alterar o visor.  Em seguida, selecione **as definições** e faça as seguintes alterações:

   * **Grupos:**  Adicione **o targettemp**.  
   * **Valores:** 1. Remover **a data**.  2. Adicione **temp_diff**.  3.  Mude o agregador de **SUM** para **AVG**.  

     A imagem que se segue mostra a saída.

     :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png " alt-text="Executar uma declaração de Spark SQL usando o caderno2" border="true":::

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como uso pacotes externos com o caderno?

O caderno Zeppelin no cluster Apache Spark em HDInsight pode usar pacotes externos e colaborativos que não estão incluídos no cluster. Pesque o [repositório Maven](https://search.maven.org/) para obter a lista completa de pacotes disponíveis. Também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa de pacotes com contribuições comunitárias está disponível nos [Pacotes Spark](https://spark-packages.org/).

Neste artigo, você verá como usar o pacote [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o Caderno Jupyter.

1. Abra as definições do intérprete. A partir do canto superior direito, selecione o nome de utilizador registado e, em seguida, selecione **Interpreter**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png " alt-text="Intérprete de lançamento" border="true":::

2. Percorra para **livy2** e, em seguida, **selecione editar**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png " alt-text="Alterar definições de intérprete1" border="true":::

3. Navegue para a `livy.spark.jars.packages` tecla, e desave o seu valor no formato `group:id:version` . Por isso, se quiser utilizar o pacote [spark-csv,](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) deve definir o valor da chave para `com.databricks:spark-csv_2.10:1.4.0` .

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png " alt-text="Alterar definições de intérprete2" border="true":::

    **Selecione Save** e, em seguida, **OK** para reiniciar o intérprete Livy.

4. Se quiser entender como chegar ao valor da chave que está acima, eis como.

    a. Localize o pacote no Repositório De Maven. Para este artigo, [usamos spark-csv.](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar)

    b. A partir do repositório, reúna os valores para **GroupId,** **ArtifactId** e **Versão**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png " alt-text="Use pacotes externos com Caderno Jupyter" border="true":::

    c. Concatenar os três valores, separados por um cólon **.**

    ```
    com.databricks:spark-csv_2.10:1.4.0
    ```

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde estão guardados os cadernos zeppelin?

Os cadernos zeppelin são guardados para os headnodes do cluster. Assim, se eliminar o cluster, os cadernos também serão apagados. Se quiser preservar os seus cadernos para posterior utilização noutros clusters, deve exportá-los depois de terminar de gerir os trabalhos. Para exportar um caderno, selecione o ícone **Exportação** como mostrado na imagem abaixo.

:::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png " alt-text="Baixar o caderno" border="true":::

Esta ação guarda o caderno como um ficheiro JSON na sua localização de descarregamento.

## <a name="use-shiro-to-configure-access-to-zeppelin-interpreters-in-enterprise-security-package-esp-clusters"></a>Utilizar `Shiro` para configurar o acesso a intérpretes zeppelin em clusters de pacotes de segurança empresarial (ESP)

Como referido acima, o `%sh` intérprete não é suportado a partir de HDInsight 4.0 em diante. Além disso, uma vez `%sh` que o intérprete introduz potenciais problemas de segurança, tais como os keytabs de acesso utilizando comandos de concha, foi removido também dos clusters HDInsight 3.6 ESP. Significa que `%sh` o intérprete não está disponível ao clicar em Criar nova **nota** ou na UI intérprete por padrão.

Os utilizadores privilegiados de domínio podem usar o `Shiro.ini` ficheiro para controlar o acesso à UI intérprete. Apenas estes utilizadores podem criar novos `%sh` intérpretes e definir permissões em cada novo `%sh` intérprete. Para controlar o acesso utilizando o `shiro.ini` ficheiro, utilize os seguintes passos:

1. Defina um novo papel utilizando um nome de grupo de domínio existente. No exemplo seguinte, `adminGroupName` está um grupo de utilizadores privilegiados na AAD. Não utilize caracteres especiais ou espaços brancos no nome de grupo. Os personagens depois `=` dão as permissões para este papel. `*` significa que o grupo tem permissões completas.

    ```
    [roles]
    adminGroupName = *
    ```

2. Adicione o novo papel de acesso aos intérpretes zeppelin. No exemplo seguinte, todos os utilizadores `adminGroupName` têm acesso a intérpretes Zeppelin e podem criar novos intérpretes. Pode colocar várias funções entre os `roles[]` suportes, separadas por vírgulas. Em seguida, os utilizadores que tenham as permissões necessárias, podem aceder aos intérpretes zeppelin.

    ```
    [urls]
    /api/interpreter/** = authc, roles[adminGroupName]
    ```

## <a name="livy-session-management"></a>Gestão de sessão de Livy

O primeiro parágrafo de código no seu caderno Zeppelin cria uma nova sessão livy no seu cluster. Esta sessão é partilhada em todos os cadernos zeppelin que mais tarde cria. Se a sessão da Livy for morta por alguma razão, os empregos não fugirão do caderno zeppelin.

Neste caso, deve fazer os seguintes passos antes de começar a trabalhar a partir de um caderno Zeppelin.  

1. Reinicie o intérprete Livy do caderno Zeppelin. Para tal, abra as definições do intérprete selecionando o nome de utilizador registado no canto superior direito e, em seguida, selecione **Interpreter**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png " alt-text="Intérprete de lançamento" border="true":::

2. Percorra para **livy2** e, em seguida, **selecione reiniciar**.

    :::image type="content" source="./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png " alt-text="Reiniciar o intérprete Livy" border="true":::

3. Executar uma célula de código de um caderno Zeppelin existente. Este código cria uma nova sessão livy no cluster HDInsight.

## <a name="general-information"></a>Informações gerais

### <a name="validate-service"></a>Validar serviço

Para validar o serviço a partir de Ambari, navegue `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` até onde clustername é o nome do seu cluster.

Para validar o serviço a partir de uma linha de comando, SSH para o nó de cabeça. Mude o utilizador para zeppelin utilizando o comando `sudo su zeppelin` . Comandos de estado:

|Comando |Descrição |
|---|---|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh status`|Estado de serviço.|
|`/usr/hdp/current/zeppelin-server/bin/zeppelin-daemon.sh --version`|Versão de serviço.|
|`ps -aux | grep zeppelin`|Identifique o PID.|

### <a name="log-locations"></a>Locais de registo

|Serviço |Caminho |
|---|---|
|zeppelin-servidor|/usr/hdp/current/zeppelin-server/|
|Registos do Servidor|/var/log/zeppelin|
|Intérprete de `Shiro` configuração, site.xml, log4j|/usr/hdp/current/zeppelin-server/conf ou /etc/zeppelin/conf|
|Diretório PID|/var/run/zeppelin|

### <a name="enable-debug-logging"></a>Ativar a exploração madeireira de depurar

1. Navegue para `https://CLUSTERNAME.azurehdinsight.net/#/main/services/ZEPPELIN/summary` onde clustername é o nome do seu cluster.

1. Navegue para **CONFIGS**  >  **Advanced zeppelin-log4j-properties**  >  **log4j_properties_content**.

1. Modificar `log4j.appender.dailyfile.Threshold = INFO` para `log4j.appender.dailyfile.Threshold = DEBUG` .

1. Adicione `log4j.logger.org.apache.zeppelin.realm=DEBUG` .

1. Guarde as alterações e reinicie o serviço.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral: Apache Spark no Azure HDInsight](apache-spark-overview.md)
* [Kernels disponíveis para Jupyter Notebook em aglomerado de faíscas apaches para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter no computador e ligar a um cluster do Spark do HDInsight](apache-spark-jupyter-notebook-install-locally.md)
