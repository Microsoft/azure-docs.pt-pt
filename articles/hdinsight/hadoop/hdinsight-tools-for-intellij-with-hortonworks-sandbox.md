---
title: Use o Kit de Ferramentas Azure para IntelliJ com a Hortonworks Sandbox
description: Aprenda a utilizar ferramentas HDInsight em Kit de Ferramentas Azure para IntelliJ com A Sandbox Hortonworks.
keywords: ferramentas hadoop,consulta de colmeia,intellij,hortonworks sandbox,azure toolkit para intellij
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/16/2018
ms.openlocfilehash: 65a15a8506b88e95e14af8c87bcbe33087301519
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647837"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Utilize ferramentas HDInsight para IntelliJ com Caixa de Areia Hortonworks

Aprenda a usar ferramentas HDInsight para o IntelliJ para desenvolver aplicações Apache Scala e, em seguida, testar as aplicações na [Hortonworks Sandbox](https://hortonworks.com/products/sandbox/) em execução no seu computador. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) é um ambiente de desenvolvimento integrado java (IDE) para o desenvolvimento de software informático. Depois de desenvolver e testar as suas aplicações na Hortonworks Sandbox, pode mover as aplicações para [Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, deve ter os seguintes itens:

- Plataforma de Dados Hortonworks (HDP) 2.4 na Hortonworks Sandbox em execução no seu computador local. Para configurar o HDP, veja [O Get começou no ecossistema Apache Hadoop com uma caixa de areia Hadoop numa máquina virtual.](apache-hadoop-emulator-get-started.md) 
    > [!NOTE]
    > As ferramentas HDInsight para IntelliJ foram testadas apenas com ODD 2.4. Para obter O HDP 2.4, expanda o **Hortonworks Sandbox Archive** no site de [downloads hortonworks Sandbox](https://hortonworks.com/downloads/#sandbox).

- [Java Developer Kit (JDK) versão 1.8 ou posterior](https://aka.ms/azure-jdks). O Kit de Ferramentas Azure para IntelliJ requer JDK.

- [Edição comunitária IntelliJ IDEA](https://www.jetbrains.com/idea/download) com o [plug-in Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) e o [Azure Toolkit para o plug-in in IntelliJ.](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) As ferramentas HDInsight para IntelliJ estão disponíveis como parte do Kit de Ferramentas Azure para o IntelliJ. 

Para instalar os plug-ins:

  1. Abra o IntelliJ IDEA.
  2. Na página **Welcome,** **selecione Configure**, e, em seguida, selecione **Plugins**.
  3. No canto inferior esquerdo, selecione **Instale plugin JetBrains**.
  4. Utilize a função de pesquisa para procurar **scala,** e, em seguida, **selecione Instalar**.
  5. Para completar a instalação, selecione **Reiniciar IntelliJ IDEA**.
  6. Repita os passos 4 e 5 para instalar **o Kit de Ferramentas Azure para o IntelliJ**. Para mais informações, consulte [Instale O Kit de Ferramentas Azure para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Criar uma aplicação Apache Spark Scala

Nesta secção, cria-se um projeto Scala de amostra utilizando o IntelliJ IDEA. Na secção seguinte, liga o IntelliJ IDEA à Caixa de Areia Hortonworks (emulador) antes de submeter o projeto.

1. Abra o IntelliJ IDEA no seu computador. Na caixa de diálogo **New Project,** complete estes passos:

   1. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.
   2. Na lista de **ferramentas Build,** selecione um dos seguintes, com base no seu cenário:

      * **Maven**: Para suporte a assistente de criação de projeto scala.
      * **SBT**: Para gerir dependências e construir para o projeto Scala.

   ![Intellij cria novo projeto de scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecione **Next**.
3. Na próxima caixa de diálogo **new project,** complete os seguintes passos:

   1. Na caixa de nome do **Projeto,** insira um nome de projeto.
   2. Na caixa de localização do **Projeto,** insira um local de projeto.
   3. Ao lado da lista de drop-down do **Project SDK,** selecione **New**, selecione **JDK,** e, em seguida, especifique a pasta para a versão 1.7 de Java JDK ou posterior. Selecione **Java 1.8** para o cluster Spark 2.x. Selecione **Java 1.7** para o cluster Spark 1.x. A localização padrão é C:\Program Files\Java\jdk1.8.x_xxx.
   4. Na lista de drop-down da **versão Spark,** o assistente de criação do projeto Scala integra a versão correta para o Spark SDK e Scala SDK. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza a Faísca 1.6.2 (Scala 2.10.5). Certifique-se de que está a utilizar o repositório marcado **Scala 2.10.x**. Não utilize o repositório marcado Scala 2.11.x.
    
      ![Criar propriedades do projeto IntelliJ Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecione **Concluir**.
5. Se a vista do **Projeto** ainda não estiver aberta, prima **alt+1** para a abrir.
6. No **Project Explorer,** expanda o projeto e, em seguida, selecione **src**.
7. Clique à direita **src,** aponte para **Novo**e, em seguida, selecione **classe Scala**.
8. Na caixa **Nome**, introduza um nome. Na caixa **Kind,** selecione **Object**. Em seguida, selecione **OK**.

    ![A nova caixa de diálogo da Classe Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. No ficheiro .scala, colhe o seguinte código:

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

10. No menu **Build,** selecione **Build project**. Certifique-se de que a compilação termina com sucesso.


## <a name="link-to-the-hortonworks-sandbox"></a>Link para a Caixa de Areia Hortonworks

Antes de poder ligar a uma Caixa de Areia Hortonworks (emulador), deve ter uma aplicação IntelliJ existente.

Para ligar a um emulador:

1. Abra o projeto em IntelliJ.
2. No menu **'Ver',** selecione **Tools Windows**, e depois selecione **Azure Explorer**.
3. Expanda **o Azure,** clique à direita **HDInsight,** e, em seguida, selecione **Link um Emulador**.
4. Na caixa de diálogo Link Uma Nova Caixa de Diálogo **Emulador,** introduza a palavra-passe que definiu para a conta raiz da Caixa de Areia Hortonworks. Em seguida, introduza valores semelhantes aos utilizados na seguinte imagem. Em seguida, selecione **OK**. 

   ![A link uma nova caixa de diálogo do emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Para configurar o emulador, selecione **Sim**.

Quando o emulador está ligado com sucesso, o emulador (Hortonworks Sandbox) está listado no nó HDInsight.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Envie a aplicação Spark Scala para a Caixa de Areia Hortonworks

Depois de ter ligado o IntelliJ IDEA ao emulador, pode submeter o seu projeto.

Submeter um projeto a um emulador:

1. No **Project Explorer,** clique no projeto e, em seguida, selecione Enviar a **aplicação Spark para HDInsight**.
2. Conclua os seguintes passos:

    1. Na lista de drop-down do **cluster Spark (apenas Linux),** selecione a sua Hortonworks Sandbox local.
    2. Na caixa de **nomes da classe Principal,** selecione ou introduza o nome da classe principal. Para este artigo, o nome é **GroupByTest**.

3. Selecione **Submeter**. Os registos de submissão de trabalho são mostrados na janela da ferramenta de submissão Spark.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [utilizar ferramentas HDInsight no Kit de Ferramentas Azure para o IntelliJ para criar aplicações Apache Spark para um cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Para um vídeo sobre as Ferramentas HDInsight para IntelliJ, consulte [Introduza ferramentas HDInsight para intelliJ para desenvolvimento de Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Aprenda a [depurar remotamente as aplicações apache Spark num cluster HDInsight com O Kit de Ferramentas Azure para IntelliJ através de SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Saiba como [utilizar ferramentas HDInsight no Kit de Ferramentas Azure para intelliJ para depurar aplicações Apache Spark remotamente num cluster HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Saiba como [utilizar ferramentas HDInsight no Kit de Ferramentas Azure para o Eclipse para criar aplicações Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

