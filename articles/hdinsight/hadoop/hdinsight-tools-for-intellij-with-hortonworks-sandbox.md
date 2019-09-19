---
title: Usar Azure Toolkit for IntelliJ com a área restrita do Hortonworks
description: Saiba como usar as ferramentas do HDInsight em Azure Toolkit for IntelliJ com a área restrita do Hortonworks.
keywords: ferramentas do Hadoop, consulta de Hive, IntelliJ, hortonworks sandbox, kit de ferramentas do Azure para IntelliJ
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 783d7fc8bc26ce2c715c774e63ecf60c5b75a439
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076261"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Usar as ferramentas do HDInsight para IntelliJ com a área restrita do Hortonworks

Saiba como usar as ferramentas do HDInsight para IntelliJ para desenvolver aplicativos Apache Scale e, em seguida, testar os aplicativos na [área restrita do Hortonworks](https://hortonworks.com/products/sandbox/) em execução no computador. 

O [INTELLIJ Idea](https://www.jetbrains.com/idea/) é um ambiente de desenvolvimento integrado Java (IDE) para o desenvolvimento de software de computador. Depois de desenvolver e testar seus aplicativos na área restrita do Hortonworks, você pode mover os aplicativos para o [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter os seguintes itens:

- Hortonworks Data Platform (HDP) 2,4 na área restrita do Hortonworks em execução no computador local. Para configurar o HDP, consulte Introdução [ao ecossistema de Apache Hadoop com uma área restrita do Hadoop em uma máquina virtual](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > As ferramentas do HDInsight para IntelliJ foram testadas somente com o HDP 2,4. Para obter o HDP 2,4, expanda **Hortonworks sandbox arquivo morto** no [site de downloads do Hortonworks sandbox](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) versão 1,8 ou posterior](https://aka.ms/azure-jdks). Azure Toolkit for IntelliJ requer JDK.

- [INTELLIJ Idea Community Edition](https://www.jetbrains.com/idea/download) com o plug-in [escalar](https://plugins.jetbrains.com/idea/plugin/1347-scala) e o plug-in [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) . As ferramentas do HDInsight para IntelliJ estão disponíveis como parte do Azure Toolkit for IntelliJ. 

Para instalar os plug-ins:

  1. Abra o IntelliJ IDEA.
  2. Na página de **boas-vindas** , selecione **Configurar**e, em seguida, selecione **plug-ins**.
  3. No canto inferior esquerdo, selecione **instalar plug-in JetBrains**.
  4. Use a função de pesquisa para pesquisar **escalares**e, em seguida, selecione **instalar**.
  5. Para concluir a instalação, selecione **reiniciar INTELLIJ ideia**.
  6. Repita as etapas 4 e 5 para instalar o **Azure Toolkit for IntelliJ**. Para obter mais informações, consulte [Install Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Criar um aplicativo Apache Spark escalar

Nesta seção, você cria um exemplo de projeto escalar usando IntelliJ IDEA. Na próxima seção, você vincula IntelliJ ideia à área restrita do Hortonworks (emulador) antes de enviar o projeto.

1. Abra o IntelliJ IDEA no seu computador. Na caixa de diálogo **novo projeto** , conclua estas etapas:

   1. Selecione **HDInsight** > **Spark no HDInsight (Scala)** .
   2. Na lista de **ferramentas de compilação** , selecione uma das seguintes opções, com base em seu cenário:

      * **Maven**: Para o suporte do assistente escala de criação de projeto.
      * **SBT**: Para gerenciar dependências e compilar para o projeto escalar.

   ![IntelliJ criar novo projeto escalar](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecione **Seguinte**.
3. Na próxima caixa de diálogo **novo projeto** , conclua as seguintes etapas:

   1. Na caixa **nome do projeto** , insira um nome de projeto.
   2. Na caixa **local do projeto** , insira um local do projeto.
   3. Ao lado da lista suspensa **SDK do projeto** , selecione **novo**, selecione **JDK**e, em seguida, ESPECIFIQUE a pasta para o Java JDK versão 1,7 ou posterior. Selecione **Java 1,8** para o cluster Spark 2. x. Selecione **Java 1,7** para o cluster Spark 1. x. O local padrão é C:\Program Files\Java\jdk1.8.x_xxx.
   4. Na lista suspensa **versão do Spark** , o assistente de criação de projeto escalar integra a versão correta para o SDK do Spark e o SDK do Scale. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo usa o Spark 1.6.2 (escala 2.10.5). Verifique se você está usando o repositório marcado como **escalat 2.10. x**. Não use o repositório marcado como escala 2.11. x.
    
      ![Criar propriedades de projeto escalares IntelliJ](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecione **Concluir**.
5. Se o modo de exibição de **projeto** ainda não estiver aberto, pressione **ALT + 1** para abri-lo.
6. No **Explorador de projeto**, expanda o projeto e, em seguida, selecione **src**.
7. Clique com o botão direito do mouse em **src**, aponte para **novo**e selecione **classe escalar**.
8. Na caixa **nome** , insira um nome. Na caixa **tipo** , selecione **objeto**. Em seguida, selecione **OK**.

    ![A caixa de diálogo Criar nova classe escala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. No arquivo. Scale, Cole o seguinte código:

        import java.util.Random
        import org.apache.spark.{SparkConf, SparkContext}
        import org.apache.spark.SparkContext._

        /**
        * Usage: GroupByTest [numMappers] [numKVPairs] [valSize] [numReducers]
        */
        object GroupByTest {
            def main(args: Array[String]) {
                val sparkConf = new SparkConf().setAppName("GroupBy Test")
                var numMappers = 3
                var numKVPairs = 10
                var valSize = 10
                var numReducers = 2

                val sc = new SparkContext(sparkConf)

                val pairs1 = sc.parallelize(0 until numMappers, numMappers).flatMap { p =>
                val ranGen = new Random
                var arr1 = new Array[(Int, Array[Byte])](numKVPairs)
                for (i <- 0 until numKVPairs) {
                    val byteArr = new Array[Byte](valSize)
                    ranGen.nextBytes(byteArr)
                    arr1(i) = (ranGen.nextInt(Int.MaxValue), byteArr)
                }
                arr1
                }.cache
                // Enforce that everything has been calculated and in cache.
                pairs1.count

                println(pairs1.groupByKey(numReducers).count)
            }
        }

10. No menu **Compilar** , selecione **Compilar projeto**. Verifique se a compilação foi concluída com êxito.


## <a name="link-to-the-hortonworks-sandbox"></a>Link para a área restrita do Hortonworks

Antes de poder vincular a um Hortonworks sandbox (emulador), você deve ter um aplicativo IntelliJ existente.

Para vincular a um emulador:

1. Abra o projeto no IntelliJ.
2. No menu **Exibir** , selecione **janelas de ferramentas**e, em seguida, selecione **Azure Explorer**.
3. Expanda **Azure**, clique com o botão direito do mouse em **HDInsight**e selecione **vincular um emulador**.
4. Na caixa de diálogo **vincular um novo emulador** , digite a senha que você definiu para a conta raiz da área restrita do Hortonworks. Em seguida, insira valores semelhantes aos usados na captura de tela a seguir. Em seguida, selecione **OK**. 

   ![A caixa de diálogo Vincular um novo emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Para configurar o emulador, selecione **Sim**.

Quando o emulador é conectado com êxito, o emulador (Hortonworks Sandbox) é listado no nó HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Enviar o aplicativo de escala do Spark para a área restrita do Hortonworks

Depois de ter vinculado o IntelliJ IDEA ao emulador, você pode enviar seu projeto.

Para enviar um projeto a um emulador:

1. No **Gerenciador de projetos**, clique com o botão direito do mouse no projeto e selecione **Enviar aplicativo Spark para o HDInsight**.
2. Conclua os seguintes passos:

    1. Na lista suspensa **cluster do Spark (somente Linux)** , selecione a área restrita do Hortonworks local.
    2. Na caixa **nome da classe principal** , selecione ou insira o nome da classe principal. Para este artigo, o nome é **GroupByTest**.

3. Selecione **submeter**. Os logs de envio de trabalho são mostrados na janela da ferramenta de envio do Spark.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para criar aplicativos Apache Spark para um cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Para ver um vídeo sobre as ferramentas do HDInsight para IntelliJ, consulte [introduzir ferramentas do hdinsight para IntelliJ para o desenvolvimento de Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Saiba como [depurar remotamente Apache Spark aplicativos em um cluster HDInsight com Azure Toolkit for IntelliJ por meio de SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Saiba como [usar as ferramentas do HDInsight no Azure Toolkit for IntelliJ para depurar aplicativos Apache Spark remotamente em um cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Saiba como [usar as ferramentas do HDInsight no Azure Toolkit for Eclipse para criar Apache Spark aplicativos](../spark/apache-spark-eclipse-tool-plugin.md).

- Para ver um vídeo sobre as ferramentas do HDInsight para Eclipse, consulte [usar as ferramentas do hdinsight para Eclipse para criar aplicativos Spark](https://mix.office.com/watch/1rau2mopb6fha).
