---
title: 'Tutorial: App Scala Maven para Spark & IntelliJ - Azure HDInsight'
description: Tutorial - Crie uma aplicação Spark escrita em Scala com Apache Maven como o sistema de construção. E um arquétipo maven existente para Scala fornecido pela IntelliJ IDEA.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: 54738ebe45792bea70067383aaeeca99667f753f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068324"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Criar uma aplicação Scala Maven para Apache Spark em HDInsight usando IntelliJ

Neste tutorial, você aprende a criar uma aplicação Apache Spark escrita em Scala usando Apache Maven com IntelliJ IDEA. O artigo usa Apache Maven como sistema de construção. E começa com um arquétipo Maven existente para Scala fornecido pela IntelliJ IDEA.  Criar uma aplicação Scala no IntelliJ IDEA envolve os seguintes passos:

* Utilizar o Maven como o sistema de compilação.
* Atualizar o ficheiro Project Object Model (POM) (POM) para resolver dependências do módulo do Spark.
* Escrever a aplicação no Scala.
* Gerar um ficheiro jar que pode ser submetido para clusters do Spark no HDInsight.
* Utilizar o Livy para executar a aplicação no cluster do Spark.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Instalar o plug-in do Scala para o IntelliJ IDEA
> * Utilizar o IntelliJ para desenvolver uma aplicação Scala Maven
> * Criar um projeto de Scala autónomo

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, veja [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Kit de desenvolvimento Oracle Java.](https://www.azul.com/downloads/azure-only/zulu/)  Este tutorial utiliza a versão Java 8.0.202.

* Um IDE Java. Este artigo utiliza [IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit para IntelliJ.  Consulte a instalação do conjunto de [ferramentas Azure para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Faça os seguintes passos para instalar o plugin Scala:

1. Abra o IntelliJ IDEA.

2. No ecrã de boas-vindas, navegue até **Configure**  >  **Plugins** para abrir a janela **Plugins.**

    :::image type="content" source="./media/apache-spark-create-standalone-application/enable-scala-plugin1.png" alt-text="'IntelliJ IDEA permite plugin scala'" border="true":::

3. Selecione **Instalar** para o plugin Scala que está em destaque na nova janela.  

    :::image type="content" source="./media/apache-spark-create-standalone-application/install-scala-plugin.png" alt-text="'IntelliJ IDEA instala plugin scala'" border="true":::

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="use-intellij-to-create-application"></a>Utilize o IntelliJ para criar uma aplicação

1. Inicie a Ideia IntelliJ e selecione **Criar Novo Projeto** para abrir a janela New **Project.**

2. Selecione **Apache Spark/HDInsight** a partir do painel esquerdo.

3. Selecione **Spark Project (Scala)** da janela principal.

4. A partir da lista de drop-down da **ferramenta Build,** selecione um dos seguintes valores:
      * **Maven** para o suporte de assistente de criação de projeto Scala.
      * **SBT** para gerir as dependências e construção para o projeto Scala.

   :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png" alt-text="IntelliJ A caixa de diálogo do novo projeto" border="true":::

5. Selecione **Seguinte**.

6. Na janela **New Project,** forneça as seguintes informações:  

  	|  Propriedade   | Descrição   |  
  	| ----- | ----- |  
  	|Nome do projeto| Insira um nome.|  
  	|&nbsp;Localização do projeto| Entre no local para salvar o seu projeto.|
  	|Projeto SDK| Este campo ficará em branco na sua primeira utilização do IDEA.  Selecione **New...** e navegue para o seu JDK.|
  	|Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **a Faísca 2.3.0 (Scala 2.11.8)**.|

    :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-scala-new-project.png" alt-text="Ideia intelliJ Selecionando o Spark SDK" border="true":::

7. Selecione **Concluir**.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autónomo

1. Inicie a Ideia IntelliJ e selecione **Criar Novo Projeto** para abrir a janela New **Project.**

2. Selecione **Maven** a partir do painel esquerdo.

3. Especifique um **Project SDK** (SDK de Projeto). Se estiver em branco, selecione **New...** e navegue para o diretório de instalação java.

4. Selecione a Caixa de verificação **de arquétipo.**  

5. A partir da lista de arquétipos, selecione **`org.scala-tools.archetypes:scala-archetype-simple`** . Este arquétipo cria a estrutura de diretório certo e descarrega as dependências padrão necessárias para escrever o programa Scala.

    :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-project-create-maven.png" alt-text="A screenshot mostra o arquétipo selecionado na janela New Project." border="true":::

6. Selecione **Seguinte**.

7. Expandir **as coordenadas de artefactos.** Fornecer valores relevantes para **GroupId** e **ArtifactId**. **Nome**, e **localização** será autopovoar. São utilizados os seguintes valores neste tutorial:

    - **GroupId:** com.microsoft.spark.exemplo
    - **Artefactoid:** SparkSimpleApp

    :::image type="content" source="./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png" alt-text="A screenshot mostra a opção De Coordenadas de Artefacto na janela do Novo Projeto." border="true":::

8. Selecione **Seguinte**.

9. Verifique as definições e selecione **Next** (Seguinte).

10. Confirme o nome e a localização do projeto e selecione **Finish** (Concluir).  O projeto levará alguns minutos para importar.

11. Uma vez importado o projeto, desde o painel esquerdo navegue para **SparkSimpleApp**  >    >  **src test**  >  **scala**  >  **com**  >  **microsoft**  >    >  **exemplo**.  Clique com o botão direito **MySpec** e, em seguida, **selecione Delete...**. Não precisa deste ficheiro para o pedido.  Selecione **OK** na caixa de diálogo.
  
12. Nos passos posteriores, atualiza o **pom.xml** para definir as dependências da aplicação Spark Scala. Para que essas dependências sejam descarregadas e resolvidas automaticamente, tem de configurar o Maven.

13. A partir do menu **'Ficheiro',** selecione **Definições** para abrir a janela **Definições.**

14. A partir da janela **Definições,** navegue para **construir, executar,**  >  **construir ferramentas** de  >    >  **construção Maven Importar.**

15. Selecione os **projetos Import Maven automaticamente.**

16. Selecione **Apply** (Aplicar) e **OK**.  Será devolvido à janela do projeto.

    :::image type="content" source="./media/apache-spark-create-standalone-application/configure-maven-download.png" alt-text="Configurar o Maven para transferências automáticas" border="true":::

17. A partir do painel esquerdo, navegue para **src**  >  **main**  >  **scala**  >  **com.microsoft.spark.exemplo**, e, em seguida, clique duas vezes em **App** para abrir App.scala.

18. Substitua o código de exemplo existente pelo seguinte código e guarde as alterações. Este código lê os dados do HVAC.csv (disponível em todos os clusters HDInsight Spark). Recupera as linhas que só têm um dígito na sexta coluna. E escreve a saída para **/AVACOut** sob o recipiente de armazenamento predefinido para o cluster.

    ```scala
    package com.microsoft.spark.example

    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    /**
      * Test IO to wasb
      */
    object WasbIOTest {
        def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("WASBIOTest")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows which have only one digit in the 7th column in the CSV
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACout")
        }
    }
    ```

19. No painel do lado esquerdo, faça duplo clique em **pom.xml**.  

20. Em `<project>\<properties>`, adicione os seguintes segmentos:

    ```xml
    <scala.version>2.11.8</scala.version>
    <scala.compat.version>2.11.8</scala.compat.version>
    <scala.binary.version>2.11</scala.binary.version>
    ```

21. Em `<project>\<dependencies>`, adicione os seguintes segmentos:

    ```xml
    <dependency>
        <groupId>org.apache.spark</groupId>
        <artifactId>spark-core_${scala.binary.version}</artifactId>
        <version>2.3.0</version>
    </dependency>
    ```

    Guarde as alterações ao pom.xml.

22. Crie o ficheiro .jar. O IntelliJ IDEA permite a criação do JAR como um artefacto de um projeto. Siga os passos seguintes.

    1. No menu **'Arquivo',** selecione **Project Structure...**.

    2. Da janela **Project Structure,** navegue para **artefactos**  >  **o símbolo mais +**  >  **JAR**  >  **De módulos com dependências...**.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png" alt-text="'Estrutura de projeto IntelliJ IDEA adicionar frasco'" border="true":::

    3. Na janela **'Criar JAR' a partir da janela Módulos,** selecione o ícone da pasta na caixa de texto classe **principal.**

    4. Na janela **'Selecione',** selecione a classe que aparece por predefinição e, em seguida, selecione **OK**.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png" alt-text="'IntelliJ IDEA estrutura de projeto selecionar classe'" border="true":::

    5. Na janela **'Criar JAR' a partir da janela Módulos,** certifique-se de que o **extrato para a opção JAR alvo** está selecionado e, em seguida, selecione **OK**.  Esta definição cria um único JAR com todas as dependências.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png" alt-text="Frasco de estrutura de projeto IntelliJ IDEA do módulo" border="true":::

    6. O **separador Layout de Saída** lista todos os frascos incluídos como parte do projeto Maven. Pode selecionar e eliminar aqueles em que a aplicação de Scala não tem dependências diretas. Para a aplicação, está a criar aqui, pode remover tudo menos a última **(saída compilada SparkSimpleApp).** Selecione os frascos para eliminar e, em seguida, selecione o símbolo negativo **-** .

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png" alt-text="&quot;Estrutura de projeto IntelliJ IDEA elimina saída&quot;" border="true":::

        Certifique-se de que o Incluir na caixa de verificação **de construção** de projetos está selecionado. Esta opção garante que o frasco é criado sempre que o projeto é construído ou atualizado. **Selecione Aplicar** e, em seguida, **OK**.

    7. Para criar o jarro, navegue para **construir**  >  **construção de artefactos.**  >   O projeto vai compilar em cerca de 30 segundos.  O jar de saída é criado em **\out\artifacts**.

        :::image type="content" source="./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png" alt-text="Saída de artefactos do projeto IntelliJ IDEA" border="true":::

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Executar a aplicação no cluster Apache Spark

Para executar a aplicação no cluster, pode utilizar as seguintes abordagens:

* **Copie o frasco de aplicação para a bolha de armazenamento Azure** associada ao cluster. Para o fazer, pode utilizar **AzCopy**, um utilitário de linha de comandos. Tem à sua disposição muitos outros clientes para carregar dados. Você pode encontrar mais sobre eles em [upload dados para empregos Apache Hadoop em HDInsight](../hdinsight-upload-data.md).

* **Use a Apache Livy para submeter um trabalho de candidatura remotamente** ao cluster Spark. Os clusters do Spark no HDInsight incluem o Livy que expõe os pontos finais de REST para submeter remotamente trabalhos do Spark. Para obter mais informações, consulte [os trabalhos de Submissão de Apache Spark remotamente utilizando apache Livy com clusters Spark em HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** no topo, escreva **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou para este tutorial.

1. Selecione **Eliminar**. Selecione **Yes** (Sim).

:::image type="content" source="./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png " alt-text="'HDInsight azure portal delete cluster'" border="true":::lete cluster'" border="true":::

## <a name="next-step"></a>Passo seguinte

Neste artigo, aprendeu a criar uma aplicação De Scala Apache Spark. Avance para o próximo artigo para saber como executar esta aplicação num cluster do HDInsight Spark com Livy.

> [!div class="nextstepaction"]
>[Executar trabalhos remotamente em um aglomerado Apache Spark usando Apache Livy](./apache-spark-livy-rest-interface.md)