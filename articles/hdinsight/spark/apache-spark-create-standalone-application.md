---
title: 'Tutorial: Aplicação Scala Maven para Spark & IntelliJ - Azure HDInsight'
description: Tutorial - Crie uma aplicação Spark escrita em Scala com Apache Maven como o sistema de construção. E um arquétipo Maven existente para Scala fornecido pela IntelliJ IDEA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc,seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 4da8fd4c2b5d736f0b384db306c62304384b2766
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82194028"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Criar uma aplicação Scala Maven para Apache Spark em HDInsight usando intelliJ

Neste tutorial, aprende-se a criar uma aplicação Apache Spark escrita em Scala usando Apache Maven com IntelliJ IDEA. O artigo usa Apache Maven como o sistema de construção. E começa com um arquétipo Maven existente para Scala fornecido pela IntelliJ IDEA.  Criar uma aplicação Scala no IntelliJ IDEA envolve os seguintes passos:

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

* [Kit de Desenvolvimento Oracle Java.](https://www.azul.com/downloads/azure-only/zulu/)  Este tutorial utiliza a versão Java 8.0.202.

* Um IDE Java. Este artigo utiliza [intelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Kit de ferramentas Azure para IntelliJ.  Consulte [a instalação do Kit de Ferramentas Azure para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Faça os seguintes passos para instalar o plugin Scala:

1. Abra o IntelliJ IDEA.

2. No ecrã de boas-vindas, navegue para **Configurar** > **plugins** para abrir a janela **Plugins.**

    !['IntelliJ IDEA permite plugin scala'](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Selecione **Instalar** para o plugin Scala que está em destaque na nova janela.  

    !['IntelliJ IDEA instala scala plugin'](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="use-intellij-to-create-application"></a>Utilize o IntelliJ para criar uma aplicação

1. Inicie o IntelliJ IDEA e selecione **Create New Project** para abrir a janela new **project.**

2. Selecione **Apache Spark/HDInsight** a partir do painel esquerdo.

3. Selecione **Spark Project (Scala)** a partir da janela principal.

4. A partir da lista de drop-down da **ferramenta Build,** selecione um dos seguintes valores:
      * **Maven** para o apoio ao assistente de criação de projetos scala.
      * **SBT** para gerir as dependências e construção para o projeto Scala.

   ![IntelliJ A caixa de diálogo do novo projeto](./media/apache-spark-create-standalone-application/intellij-project-apache-spark.png)

5. Selecione **Seguinte**.

6. Na janela **New Project,** forneça as seguintes informações:  

  	|  Propriedade   | Descrição   |  
  	| ----- | ----- |  
  	|Nome do projeto| Insira um nome.|  
  	|Localização&nbsp;do projeto| Insira o local para salvar o seu projeto.|
  	|Projeto SDK| Este campo ficará em branco na sua primeira utilização do IDEA.  Selecione **New...** e navegue para o seu JDK.|
  	|Versão Spark|O assistente de criação integra a versão adequada para Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **o Spark 2.3.0 (Scala 2.11.8)**.|

    ![IntelliJ IDEA Selecionando o Spark SDK](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Selecione **Concluir**.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autónomo

1. Inicie o IntelliJ IDEA e selecione **Create New Project** para abrir a janela new **project.**

2. Selecione **Maven** do painel esquerdo.

3. Especifique um **Project SDK** (SDK de Projeto). Se estiver em branco, selecione **New...** e navegue para o diretório de instalação de Java.

4. Selecione o Create a partir de caixa de verificação **arquétipo.**  

5. A partir da lista de **`org.scala-tools.archetypes:scala-archetype-simple`** arquétipos, selecione . Este arquétipo cria a estrutura de diretório certa e descarrega as dependências padrão necessárias para escrever o programa Scala.

    !['IntelliJ IDEA criar projeto Maven'](./media/apache-spark-create-standalone-application/intellij-project-create-maven.png)

6. Selecione **Seguinte**.

7. Expandir as coordenadas de **artefactos.** Forneça valores relevantes para **o GroupId**e **o ArtifactId.** **Nome**e **Localização** irão autopovoar. São utilizados os seguintes valores neste tutorial:

    - **GroupId:** com.microsoft.spark.example
    - **Artefacto:** SparkSimpleApp

    !['IntelliJ IDEA criar projeto Maven'](./media/apache-spark-create-standalone-application/intellij-artifact-coordinates.png)

8. Selecione **Seguinte**.

9. Verifique as definições e selecione **Next** (Seguinte).

10. Confirme o nome e a localização do projeto e selecione **Finish** (Concluir).  O projeto levará alguns minutos para importar.

11. Uma vez importado o projeto, desde o painel esquerdo navegar até **sparkSimpleApp** > **test** > **teste scala** > **src** > **com** > **microsoft** > **spark** > **exemplo**.  Clique no **MySpec,** clique na direita, e depois selecione **Delete...**. Não precisa deste ficheiro para a inscrição.  Selecione **OK** na caixa de diálogo.
  
12. Nos passos posteriores, atualiza o **pom.xml** para definir as dependências da aplicação Spark Scala. Para que essas dependências sejam descarregadas e resolvidas automaticamente, tem de configurar o Maven.

13. A partir do menu **'Ficheiro',** selecione **Definições** para abrir a janela **Definições.**

14. A partir da janela **Definições,** navegue para **construir, executar, ferramentas** > **Build Tools** > de construção de implementação**Maven** > **Importing**.

15. Selecione os **projetos Import Maven automaticamente** checkbox.

16. Selecione **Apply** (Aplicar) e **OK**.  Em seguida, será devolvido à janela do projeto.

    ![Configurar o Maven para transferências automáticas](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. A partir do painel esquerdo, navegue para **src** > **main** > **scala** > **com.microsoft.spark.exemplo**, e, em seguida, clique duas vezes na **App** para abrir app.scala.

18. Substitua o código de exemplo existente pelo seguinte código e guarde as alterações. Este código lê os dados do HVAC.csv (disponível em todos os clusters HDInsight Spark). Recupera as linhas que só têm um dígito na sexta coluna. E escreve a saída para **/HVACOut** sob o recipiente de armazenamento predefinido para o cluster.

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
19. No painel do lado esquerdo, faça duplo clique em **pom.xml**.  

20. Em `<project>\<properties>`, adicione os seguintes segmentos:

          <scala.version>2.11.8</scala.version>
          <scala.compat.version>2.11.8</scala.compat.version>
          <scala.binary.version>2.11</scala.binary.version>

21. Em `<project>\<dependencies>`, adicione os seguintes segmentos:

           <dependency>
             <groupId>org.apache.spark</groupId>
             <artifactId>spark-core_${scala.binary.version}</artifactId>
             <version>2.3.0</version>
           </dependency>

    Guarde as alterações ao pom.xml.

22. Crie o ficheiro .jar. O IntelliJ IDEA permite a criação do JAR como um artefacto de um projeto. Faça os seguintes passos.

    1. A partir do menu **Arquivo,** selecione Estrutura de **Projeto...**.

    2. A partir da janela Estrutura do **Projeto,** navegue até **Artefactos** > **o símbolo mais +** > **JAR** > **De módulos com dependências...**.

        !['Estrutura do projeto IntelliJ IDEA adicionar jarro'](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. Na janela **Create JAR from Modules,** selecione o ícone da pasta na caixa de texto **classe principal.**

    4. Na janela **Select Main Class,** selecione a classe que aparece por padrão e, em seguida, selecione **OK**.

        !['IntelliJ IDEA estrutura de projeto selecionar classe'](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. Na janela **Create JAR from Modules,** certifique-se de que o **extrato para a** opção TARGET JAR é selecionado e, em seguida, selecione **OK**.  Esta definição cria um único JAR com todas as dependências.

        ![Frasco de estrutura de projeto IntelliJ IDEA do módulo](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. O separador **'Layout de saída'** lista todos os frascos que estão incluídos como parte do projeto Maven. Pode selecionar e eliminar aqueles em que a aplicação de Scala não tem dependências diretas. Para a aplicação, está a criar aqui, pode remover tudo menos o último (**sparkSimpleApp compila saída).** Selecione os frascos para apagar **-** e, em seguida, selecione o símbolo negativo .

        !['Estrutura do projeto IntelliJ IDEA apaga saída'](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Certifique-se de que o Incluir na caixa de verificação de construção de **projetos** está selecionado. Esta opção garante que o jarro é criado sempre que o projeto é construído ou atualizado. Selecione **Aplicar** **e,** em seguida, OK .

    7. Para criar o jarro, navegue para **construir** > **artefactos** > **de construção.** O projeto vai compilar em cerca de 30 segundos.  O jar de saída é criado em **\out\artifacts**.

        ![Saída de artefacto do projeto IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Executar a aplicação no cluster Apache Spark

Para executar a aplicação no cluster, pode utilizar as seguintes abordagens:

* **Copie o frasco de aplicação para a bolha** de armazenamento Azure associada ao cluster. Para o fazer, pode utilizar **AzCopy**, um utilitário de linha de comandos. Tem à sua disposição muitos outros clientes para carregar dados. Pode descobrir mais sobre eles em [dados de Upload para trabalhos Apache Hadoop em HDInsight](../hdinsight-upload-data.md).

* **Use apache Livy para submeter um trabalho** de candidatura remotamente ao cluster Spark. Os clusters do Spark no HDInsight incluem o Livy que expõe os pontos finais de REST para submeter remotamente trabalhos do Spark. Para mais informações, consulte [Submeta os trabalhos da Apache Spark remotamente utilizando apache Livy com clusters De faísca no HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se não vai continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa **de pesquisa** na parte superior, digite **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparecem, selecione o **...** ao lado do cluster que criou para este tutorial.

1. Selecione **Eliminar**. Selecione **Sim**.

!['HDInsight azure portal eliminar cluster'](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Eliminar o cluster HDInsight")

## <a name="next-step"></a>Passo seguinte

Neste artigo, aprendeu a criar uma aplicação apache Spark scala. Avance para o próximo artigo para saber como executar esta aplicação num cluster do HDInsight Spark com Livy.

> [!div class="nextstepaction"]
>[Executar trabalhos remotamente em um cluster Apache Spark usando Apache Livy](./apache-spark-livy-rest-interface.md)
