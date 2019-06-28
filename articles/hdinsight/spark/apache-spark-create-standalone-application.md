---
title: 'Tutorial: Criar uma aplicação Scala Maven para o Spark no HDInsight do Azure com o IntelliJ'
description: Tutorial - criar uma aplicação de Spark escrita no Scala com o Apache Maven, como o sistema de compilação e um arquétipo do Maven existente, para Scala fornecida pelo IntelliJ IDEA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: d057aa162c5554ed6c568fb765ddb2b84ac92fd6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448916"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Criar uma aplicação Scala Maven para o Apache Spark no HDInsight com o IntelliJ

Neste tutorial, irá aprender a criar uma [Apache Spark](https://spark.apache.org/) aplicação escrita [Scala](https://www.scala-lang.org/) usando [Apache Maven](https://maven.apache.org/) com o IntelliJ IDEA. O artigo utiliza o Apache Maven como o sistema de compilação e começa com um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.  Criar uma aplicação Scala no IntelliJ IDEA envolve os seguintes passos:

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

* [Kit de desenvolvimento Java de Oracle](https://www.azul.com/downloads/azure-only/zulu/).  Este tutorial utiliza o Java versão 8.0.202.

* Um IDE Java. Este artigo utiliza [ver. IntelliJ IDEIA Comunidade  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Ver [instalar o Azure Toolkit para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Execute os seguintes passos para instalar o plug-in de Scala:

1. Abra o IntelliJ IDEA.

2. No ecrã de boas-vindos, navegue até **configurar** > **plug-ins** para abrir o **plug-ins** janela.

    ![Ativar o plug-in do Scala](./media/apache-spark-create-standalone-application/enable-scala-plugin.png)

3. Selecione **instalar** para o plug-in de Scala que estiver em destaque na nova janela.  

    ![Instalar o plug-in do Scala](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="use-intellij-to-create-application"></a>Utilize o IntelliJ para criar uma aplicação

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir o **novo projeto** janela.

2. Selecione **do Azure Spark/HDInsight** no painel à esquerda.

3. Selecione **Spark projeto (Scala)** da janela principal.

4. Partir do **ferramenta de compilação** na lista pendente, selecione uma das seguintes ações:
      * **Maven** para suporte ao Assistente de criação do projeto Scala.
      * **SBT** para as dependências de gestão e a criação do projeto Scala.

   ![Caixa de diálogo O Novo Projeto](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Selecione **Seguinte**.

6. Na **novo projeto** janela, forneça as seguintes informações:  

  	|  Propriedade   | Descrição   |  
  	| ----- | ----- |  
  	|Nome do projeto| Introduza um nome.|  
  	|Projeto&nbsp;localização| Introduza a localização pretendida para guardar o seu projeto.|
  	|SDK do projeto| Isso ficará em branco na primeira utilização IDÉIA.  Selecione **novo...**  e navegue para o JDK.|
  	|Versão do Spark|O Assistente de criação integra-se a versão apropriada para o SDK do Spark e do Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Selecionar o SDK do Spark](./media/apache-spark-create-standalone-application/hdi-new-project.png)

7. Selecione **Concluir**.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autónomo

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir o **novo projeto** janela.

2. Selecione **Maven** no painel à esquerda.

3. Especifique um **Project SDK** (SDK de Projeto). Se deixado em branco, selecione **New...**  e navegue para o diretório de instalação de Java.

4. Selecione o **criar do mvn** caixa de verificação.  

5. Na lista de arquétipos, selecione **org.scala-tools.archetypes:scala-archetype-simple**. Este mvn cria a estrutura do diretório correto e transfere as dependências de padrão necessário escrever Scala programa.

    ![Criar projeto do Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Selecione **Seguinte**.

7. Indique valores relevantes para **GroupId** (ID de Grupo), **ArtifactId** (ID de Artefacto) e **Version** (Versão). São utilizados os seguintes valores neste tutorial:

    - **GroupId:** com.microsoft.spark.example
    - **ArtifactId:** SparkSimpleApp

8. Selecione **Seguinte**.

9. Verifique as definições e selecione **Next** (Seguinte).

10. Confirme o nome e a localização do projeto e selecione **Finish** (Concluir).  O projeto irá demorar alguns minutos para importar.

11. Assim que tiver importado o projeto, no painel à esquerda navegue até **SparkSimpleApp** > **src** > **testar**  >  **scala** > **com** > **microsoft** > **spark**  >  **exemplo**.  Com o botão direito **MySpec**e, em seguida, selecione **eliminar...** . Não precisa deste ficheiro para a aplicação.  Selecione **OK** na caixa de diálogo.
  
12. Nos passos subsequentes, atualize o **pom** para definir as dependências para a aplicação Spark Scala. Para que essas dependências sejam transferidas e resolvidas automaticamente, tem de configurar o Maven em conformidade.

13. Do **arquivo** menu, selecione **definições** para abrir o **definições** janela.

14. Do **definições** janela, navegue até à **compilação, execução, implantação** > **as ferramentas de compilação** > **Maven**  >  **Importar**.

15. Selecione o **automaticamente projetos do Maven importação** caixa de verificação.

16. Selecione **Apply** (Aplicar) e **OK**.  , Em seguida, será retornado para a janela de projeto.

    ![Configurar o Maven para transferências automáticas](./media/apache-spark-create-standalone-application/configure-maven.png)


17. No painel à esquerda, navegue até **src** > **principal** > **scala** > **com.microsoft.spark.example**e, em seguida, faça duplo clique **aplicação** para abrir App.scala.

18. Substitua o código de exemplo existente pelo seguinte código e guarde as alterações. Este código lê os dados do HVAC.csv (disponível em todos os clusters do Spark no HDInsight), obtém as linhas que só têm um dígito na sexta coluna e escreve a saída para **/HVACOut**, no contentor de armazenamento predefinido do cluster.

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

22. Crie o ficheiro .jar. O IntelliJ IDEA permite a criação do JAR como um artefacto de um projeto. Realize os seguintes passos.

    1. Partir do **arquivo** menu, selecione **estrutura do projeto...** .

    2. Do **estrutura do projeto** janela, navegue até à **artefactos** > **o símbolo de adição +**  > **JAR**  >  **De módulos com dependências...** .

        ![Criar o JAR](./media/apache-spark-create-standalone-application/create-jar-1.png)

    3. Na **criar JAR a partir de módulos** janela, selecione o ícone de pasta no **Main classe** caixa de texto.

    4. Na **selecionar classe de Main** janela, selecione a classe que é apresentado por predefinição e, em seguida, selecione **OK**.

        ![Criar o JAR](./media/apache-spark-create-standalone-application/create-jar-2.png)

    5. Na **criar JAR a partir de módulos** janela, certifique-se a **extrair para o destino JAR** opção está selecionada e, em seguida, selecione **OK**.  Esta definição cria um único JAR com todas as dependências.

        ![Criar o JAR](./media/apache-spark-create-standalone-application/create-jar-3.png)

    6. O **esquema de saída** separador lista todas as jars que são incluídos como parte do projeto Maven. Pode selecionar e eliminar aqueles em que a aplicação de Scala não tem dependências diretas. Na aplicação que está a criar aqui, pode remover todos menos o último (**SparkSimpleApp compile output**). Selecione os jars a eliminar e, em seguida, selecione o símbolo de negativo **-** .

        ![Criar o JAR](./media/apache-spark-create-standalone-application/delete-output-jars.png)

        Certifique-se-se de que o **incluir na compilação do projeto** caixa de verificação está selecionada, o que garante que o jar é criada sempre que o projeto é criado ou atualizado. Selecione **aplicar** e, em seguida **OK**.

    7. Para criar o jar, navegue para **crie** > **criar artefactos** > **criar**. O projeto será compilado em cerca de 30 segundos.  O jar de saída é criado em **\out\artifacts**.

        ![Criar o JAR](./media/apache-spark-create-standalone-application/output.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Executar a aplicação no cluster do Apache Spark

Para executar a aplicação no cluster, pode utilizar as seguintes abordagens:

* **Copiar o jar da aplicação para o blob de armazenamento do Azure** associado ao cluster. Para o fazer, pode utilizar [**AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comandos. Tem à sua disposição muitos outros clientes para carregar dados. Pode saber mais sobre elas, em [carregar dados para as tarefas do Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

* **Utilizar Apache Livy para submeter um trabalho de aplicação remotamente** para o cluster do Spark. Os clusters do Spark no HDInsight incluem o Livy que expõe os pontos finais de REST para submeter remotamente trabalhos do Spark. Para obter mais informações, consulte [tarefas de submeter o Apache Spark remotamente com o Livy de Apache spark clusters no HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se não pretender continuar a utilizar esta aplicação, elimine o cluster que criou com os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na **pesquisa** caixa na parte superior, tipo **HDInsight**.

1. Selecione **clusters do HDInsight** sob **serviços**.

1. Na lista de clusters do HDInsight que aparece, selecione o **...**  junto do cluster que criou para este tutorial.

1. Selecione **Eliminar**. Selecione **Sim**.

![Eliminar um cluster do HDInsight](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Eliminar um cluster do HDInsight")

## <a name="next-step"></a>Passo seguinte

Neste artigo, aprendeu a criar uma aplicação scala do Apache Spark. Avance para o próximo artigo para saber como executar esta aplicação num cluster do HDInsight Spark com Livy.

> [!div class="nextstepaction"]
>[Executar tarefas remotamente num cluster do Apache Spark com o Apache Livy](./apache-spark-livy-rest-interface.md)
