---
title: 'Tutorial: Aplicativo de escala do Maven para Spark com IntelliJ-Azure HDInsight'
description: Tutorial-criar um aplicativo Spark escrito em escala com o Apache Maven como o sistema de compilação e um arquétipo Maven existente para escalabilidade fornecida pela IntelliJ IDEA.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.openlocfilehash: 667590120bba4676cbedf3d3bb2ff08e31656e1d
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147024"
---
# <a name="tutorial-create-a-scala-maven-application-for-apache-spark-in-hdinsight-using-intellij"></a>Tutorial: Criar um aplicativo de escala de aplicativos do Maven para Apache Spark no HDInsight usando IntelliJ

Neste tutorial, você aprenderá a criar um aplicativo [Apache Spark](https://spark.apache.org/) escrito em [escalabilidade](https://www.scala-lang.org/) usando o [Apache Maven](https://maven.apache.org/) com IntelliJ IDEA. O artigo utiliza o Apache Maven como o sistema de compilação e começa com um arquétipo Maven existente para Scala fornecido pelo IntelliJ IDEA.  Criar uma aplicação Scala no IntelliJ IDEA envolve os seguintes passos:

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

* [Kit de desenvolvimento Oracle Java](https://www.azul.com/downloads/azure-only/zulu/).  Este tutorial usa a versão do Java 8.0.202.

* Um IDE Java. Este artigo usa [a IntelliJ IDEA Community ver.  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Consulte [instalando o Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in).

## <a name="install-scala-plugin-for-intellij-idea"></a>Instalar o plug-in do Scala para o IntelliJ IDEA

Execute as seguintes etapas para instalar o plug-in escalar:

1. Abra o IntelliJ IDEA.

2. Na tela de boas-vindas, navegue até **Configurar** > **plug-ins** para abrir a janela plug- **ins** .

    ![IntelliJ ideia habilitar o plug-in escalar](./media/apache-spark-create-standalone-application/enable-scala-plugin1.png)

3. Selecione **instalar** para o plug-in escalar que está em destaque na nova janela.  

    ![Plug-in de IntelliJ ideia instalar escalabilidade](./media/apache-spark-create-standalone-application/install-scala-plugin.png)

4. Após a instalação bem-sucedida do plug-in, tem de reiniciar o IDE.

## <a name="use-intellij-to-create-application"></a>Utilize o IntelliJ para criar uma aplicação

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir a janela **novo projeto** .

2. Selecione **Azure Spark/HDInsight** no painel esquerdo.

3. Selecione **projeto do Spark (escala)** na janela principal.

4. Na lista suspensa **ferramenta de compilação** , selecione uma das seguintes opções:
      * Suporte ao assistente de criação de projeto do **Maven** para escalabilidade.
      * **SBT** para gerenciar as dependências e compilar para o projeto escalar.

   ![IntelliJ a caixa de diálogo novo projeto](./media/apache-spark-create-standalone-application/create-hdi-scala-app.png)

5. Selecione **Seguinte**.

6. Na janela **novo projeto** , forneça as seguintes informações:  

  	|  Propriedade   | Descrição   |  
  	| ----- | ----- |  
  	|Nome do projeto| Insira um nome.|  
  	|Local&nbsp;do projeto| Insira o local desejado para salvar o projeto.|
  	|SDK do projeto| Isso estará em branco no primeiro uso da ideia.  Selecione **novo...** e navegue até o JDK.|
  	|Versão do Spark|O assistente de criação integra a versão apropriada do SDK do Spark e do SDK do Scale. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo usa o **Spark 2.3.0 (escala 2.11.8)** .|

    ![IDEIA IntelliJ selecionando o SDK do Spark](./media/apache-spark-create-standalone-application/hdi-scala-new-project.png)

7. Selecione **Concluir**.

## <a name="create-a-standalone-scala-project"></a>Criar um projeto de Scala autónomo

1. Inicie o IntelliJ IDEA e selecione **criar novo projeto** para abrir a janela **novo projeto** .

2. Selecione **Maven** no painel esquerdo.

3. Especifique um **Project SDK** (SDK de Projeto). Se estiver em branco, selecione **novo...** e navegue até o diretório de instalação do Java.

4. Marque a caixa de seleção **criar de arquétipo** .  

5. Na lista de arquétipos, selecione **org.scala-tools.archetypes:scala-archetype-simple**. Esse arquétipo cria a estrutura de diretório correta e baixa as dependências padrão necessárias para gravar o programa escalar.

    ![IntelliJ IDEA criar projeto Maven](./media/apache-spark-create-standalone-application/create-maven-project.png)

6. Selecione **Seguinte**.

7. Indique valores relevantes para **GroupId** (ID de Grupo), **ArtifactId** (ID de Artefacto) e **Version** (Versão). São utilizados os seguintes valores neste tutorial:

    - **GroupId:** com. Microsoft. Spark. exemplo
    - **ArtifactId:** SparkSimpleApp

8. Selecione **Seguinte**.

9. Verifique as definições e selecione **Next** (Seguinte).

10. Confirme o nome e a localização do projeto e selecione **Finish** (Concluir).  O projeto levará alguns minutos para ser importado.

11. Depois que o projeto for importado, no painel esquerdo, navegue até **SparkSimpleApp** > **src** > **Test** > **Scale** > **com** > **Microsoft** exemplo de **Spark** > .  >   Clique com o botão direito do mouse em **myspec**e selecione **excluir...** . Não precisa deste ficheiro para a aplicação.  Selecione **OK** na caixa de diálogo.
  
12. Nas etapas subsequentes, você atualiza o **pom. xml** para definir as dependências para o aplicativo do Spark escalar. Para que essas dependências sejam transferidas e resolvidas automaticamente, tem de configurar o Maven em conformidade.

13. No menu **arquivo** , selecione **configurações** para abrir a janela **configurações** .

14. Na janela **configurações** , navegue até **compilação, execução, implantação** > **ferramentas** > de compilação**Maven** > **importando**.

15. Marque a caixa de seleção **Importar projetos Maven automaticamente** .

16. Selecione **Apply** (Aplicar) e **OK**.  Você será retornado para a janela do projeto.

    ![Configurar o Maven para transferências automáticas](./media/apache-spark-create-standalone-application/configure-maven-download.png)

17. No painel esquerdo, navegue até **src** > **Main** > **escalar** > **com. Microsoft. Spark. example**e clique duas vezes em **app** para abrir app. Scale.

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

    1. No menu **arquivo** , selecione **estrutura do projeto...** .

    2. Na janela **estrutura do projeto** , navegue até **artefatos** > **o símbolo de mais +**  > **jar** > **de módulos com dependências...** .

        ![Adicionar JAR da estrutura do projeto IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdinsight-create-jar1.png)

    3. Na janela **criar jar de módulos** , selecione o ícone de pasta na caixa de texto **classe principal** .

    4. Na janela **Selecionar classe principal** , selecione a classe que aparece por padrão e, em seguida, selecione **OK**.

        ![Classe de seleção de estrutura do projeto IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdinsight-create-jar2.png)

    5. Na janela **criar jar de módulos** , verifique se a opção **extrair para o Jar de destino** está selecionada e, em seguida, selecione **OK**.  Esta definição cria um único JAR com todas as dependências.

        ![Jar da estrutura do projeto IntelliJ IDEA do módulo](./media/apache-spark-create-standalone-application/hdinsight-create-jar3.png)

    6. A guia **layout de saída** lista todos os jars incluídos como parte do projeto Maven. Pode selecionar e eliminar aqueles em que a aplicação de Scala não tem dependências diretas. Na aplicação que está a criar aqui, pode remover todos menos o último (**SparkSimpleApp compile output**). Selecione os jars a serem excluídos e, em seguida, **-** selecione o símbolo negativo.

        ![Saída de exclusão de estrutura do projeto IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-delete-output-jars.png)

        Verifique se a caixa de seleção **incluir no Build do projeto** está selecionada, o que garante que o jar seja criado toda vez que o projeto for compilado ou atualizado. Selecione **aplicar** e **OK**.

    7. Para criar o Jar, **navegue para** > compilar compilar**artefatos** > **Build**. O projeto será compilado em cerca de 30 segundos.  O jar de saída é criado em **\out\artifacts**.

        ![Saída do artefato do projeto IntelliJ IDEA](./media/apache-spark-create-standalone-application/hdi-artifact-output-jar.png)

## <a name="run-the-application-on-the-apache-spark-cluster"></a>Executar o aplicativo no cluster de Apache Spark

Para executar a aplicação no cluster, pode utilizar as seguintes abordagens:

* **Copiar o jar da aplicação para o blob de armazenamento do Azure** associado ao cluster. Para o fazer, pode utilizar [**AzCopy**](../../storage/common/storage-use-azcopy.md), um utilitário de linha de comandos. Tem à sua disposição muitos outros clientes para carregar dados. Você pode encontrar mais informações sobre eles em [carregar dados para trabalhos de Apache Hadoop no HDInsight](../hdinsight-upload-data.md).

* **Use o Apache Livy para enviar um trabalho de aplicativo remotamente** para o cluster Spark. Os clusters do Spark no HDInsight incluem o Livy que expõe os pontos finais de REST para submeter remotamente trabalhos do Spark. Para obter mais informações, consulte [enviar trabalhos de Apache Spark remotamente usando o Apache Livy com clusters Spark no HDInsight](apache-spark-livy-rest-interface.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você não pretende usar este aplicativo, exclua o cluster que você criou com as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

1. Na caixa de **pesquisa** na parte superior, digite **HDInsight**.

1. Selecione **clusters HDInsight** em **Serviços**.

1. Na lista de clusters HDInsight que aparece, selecione o **...** ao lado do cluster que você criou para este tutorial.

1. Selecione **Eliminar**. Selecione **Sim**.

![Portal do Azure do HDInsight excluir cluster](./media/apache-spark-create-standalone-application/hdinsight-azure-portal-delete-cluster.png "Excluir cluster HDInsight")

## <a name="next-step"></a>Passo seguinte

Neste artigo, você aprendeu a criar um aplicativo Apache Spark escalabilidade. Avance para o próximo artigo para saber como executar esta aplicação num cluster do HDInsight Spark com Livy.

> [!div class="nextstepaction"]
>[Executar trabalhos remotamente em um cluster Apache Spark usando o Apache Livy](./apache-spark-livy-rest-interface.md)
