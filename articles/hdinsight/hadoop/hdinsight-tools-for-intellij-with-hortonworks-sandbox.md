---
title: Utilizar o Azure Toolkit para IntelliJ com a Sandbox da Hortonworks
description: Saiba como utilizar ferramentas do HDInsight no Azure Toolkit para IntelliJ com a Sandbox da Hortonworks.
keywords: ferramentas do hadoop, do hive consulta, o intellij, o sandbox da hortonworks, o azure toolkit para intellij
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 98f22f531ca15bf88cd7d0a9add2851651e2eec7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118328"
---
# <a name="use-hdinsight-tools-for-intellij-with-hortonworks-sandbox"></a>Utilizar ferramentas do HDInsight para IntelliJ com a Sandbox da Hortonworks

Saiba como utilizar ferramentas do HDInsight para o IntelliJ para desenvolver aplicativos de Apache Scala e, em seguida, testar os aplicativos no [Sandbox da Hortonworks](https://hortonworks.com/products/sandbox/) em execução no seu computador. 

[IntelliJ IDEA](https://www.jetbrains.com/idea/) é um ambiente de desenvolvimento integrado (IDE) de Java para o desenvolvimento de software do computador. Depois de desenvolver e testar seus aplicativos na Sandbox da Hortonworks, pode mover os aplicativos a serem [do Azure HDInsight](apache-hadoop-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

- Hortonworks Data Platform (HDP) 2.4 na Sandbox da Hortonworks em execução no seu computador local. Para configurar a HDP, consulte [começar no ecossistema do Apache Hadoop com um sandbox do Hadoop numa máquina virtual](apache-hadoop-emulator-get-started.md). 
    > [!NOTE]
    > Ferramentas do HDInsight para o IntelliJ foi testada apenas com HDP 2.4. Para obter HDP 2.4, expanda **Hortonworks Sandbox arquivo** sobre o [site de downloads de Sandbox da Hortonworks](https://hortonworks.com/downloads/#sandbox).

- [Kit de desenvolvimento Java (JDK) versão 1.8 ou posterior](https://aka.ms/azure-jdks). Azure Toolkit para IntelliJ requer o JDK.

- [Edição de Comunidade do IntelliJ IDEA](https://www.jetbrains.com/idea/download) com o [Scala](https://plugins.jetbrains.com/idea/plugin/1347-scala) Plug-in e a [Azure Toolkit para IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij) Plug-in. Ferramentas do HDInsight para o IntelliJ está disponível como parte do Azure Toolkit para IntelliJ. 

Para instalar os plug-ins:

  1. Abra o IntelliJ IDEA.
  2. Sobre o **bem-vindo** página, selecione **configurar**e, em seguida, selecione **plug-ins**.
  3. No canto inferior esquerdo, selecione **Plug-in do instalar JetBrains**.
  4. Utilize a função de pesquisa para procurar **Scala**e, em seguida, selecione **instalar**.
  5. Para concluir a instalação, selecione **reiniciar o IntelliJ IDEA**.
  6. Repita os passos 4 e 5 para instalar **Azure Toolkit para IntelliJ**. Para obter mais informações, consulte [instalar o Azure Toolkit para IntelliJ](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation).

## <a name="create-an-apache-spark-scala-application"></a>Criar uma aplicação do Apache Spark Scala

Nesta secção, vai criar um projeto de Scala de exemplo com o IntelliJ IDEA. Na secção seguinte, vai ligar IntelliJ IDEA para a Sandbox da Hortonworks (emulador) antes de submeter o projeto.

1. Abra o IntelliJ IDEA no seu computador. Na **novo projeto** diálogo caixa, conclua estes passos:

   1. Selecione **HDInsight** > **Spark no HDInsight (Scala)**.
   2. Na **ferramenta de compilação** lista, selecione um dos seguintes, com base no seu cenário:

      * **Maven**: Para obter suporte do Assistente de criação do projeto de Scala.
      * **SBT**: Para gerir as dependências e a criação do projeto Scala.

   ![Caixa de diálogo O Novo Projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project.png)

2. Selecione **Seguinte**.
3. Nos próximos **novo projeto** diálogo caixa, conclua os seguintes passos:

   1. Na **nome do projeto** , introduza um nome de projeto.
   2. Na **localização do projeto** , introduza uma localização de projeto.
   3. Junto a **SDK do Project** na lista pendente, selecione **New**, selecione **JDK**, e, em seguida, especifique a pasta para o JDK do Java versão 1.7 ou posterior. Selecione **Java 1.8** para o cluster do Spark 2.x. Selecione **Java 1.7** para o cluster do Spark 1.x. A localização predefinida é c:\Programas\Microsoft Files\Java\jdk1.8.x_xxx.
   4. Na **versão do Spark** na lista pendente, o Assistente de criação de projeto Scala integra-se a versão correta para o SDK do Spark e do Scala. Se a versão do cluster do Spark for anterior à 2.0, selecione **Spark 1.x**. Caso contrário, selecione **Spark2.x**. Este exemplo utiliza o Spark 1.6.2 (Scala 2.10.5). Certifique-se de que está a utilizar o repositório marcado **Scala 2.10.x**. Não utilize o repositório marcado Scala 2.11.x.
    
      ![Criar IntelliJ Scala propriedades do projeto](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-scala-project-properties.png)


4. Selecione **Concluir**.
5. Se o **Project** vista não esteja ainda aberta, prima **Alt + 1** para abri-lo.
6. Na **Explorador de projeto**, expanda o projeto e, em seguida, selecione **src**.
7. Com o botão direito **src**, aponte para **New**e, em seguida, selecione **Scala classe**.
8. Na **nome** , introduza um nome. Na **tipo** caixa, selecione **objeto**. Em seguida, selecione **OK**.

    ![A caixa de diálogo Criar nova classe Scala](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-create-new-scala-class.png)

9. No ficheiro .scala, cole o seguinte código:

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

10. Sobre o **crie** menu, selecione **projeto de compilação**. Certifique-se de que a compilação é concluída com êxito.


## <a name="link-to-the-hortonworks-sandbox"></a>Ligação para a Sandbox da Hortonworks

Pode ligar a uma Sandbox da Hortonworks (emulador), tem de ter uma aplicação existente do IntelliJ.

Para ligar a um emulador:

1. Abra o projeto no IntelliJ.
2. Sobre o **View** menu, selecione **ferramentas Windows**e, em seguida, selecione **Explorador do Azure**.
3. Expanda **Azure**, clique com botão direito **HDInsight**e, em seguida, selecione **ligar um emulador**.
4. Na **ligação de um novo emulador** caixa de diálogo, introduza a palavra-passe que definiu para a conta de raiz da Sandbox da Hortonworks. Em seguida, introduza os valores semelhantes às usadas na captura de ecrã seguinte. Em seguida, selecione **OK**. 

   ![A ligação uma caixa de diálogo novo emulador](./media/hdinsight-tools-for-intellij-with-hortonworks-sandbox/intellij-link-an-emulator.png)

5. Para configurar o emulador, selecione **Sim**.

Quando o emulador é ligado com êxito, o emulador (Sandbox da Hortonworks) está listado no HDInsight nó.

## <a name="submit-the-spark-scala-application-to-the-hortonworks-sandbox"></a>Submeter a aplicação Spark Scala para o Sandbox da Hortonworks

Depois de ter ligado o IntelliJ IDEA no emulador, pode enviar o seu projeto.

Para enviar um projeto para um emulador:

1. Na **Explorador de projeto**, clique com o botão direito no projeto e, em seguida, selecione **aplicação de submeter Spark para HDInsight**.
2. Conclua os seguintes passos:

    1. Na **cluster do Spark (apenas Linux)** pendente, selecione seu local Sandbox da Hortonworks.
    2. Na **nome da classe principal** caixa, selecione ou introduza o nome da classe principal. Para este tutorial, o nome é **GroupByTest**.

3. Selecione **submeter**. Os registos de submissão da tarefa são apresentados na janela de ferramenta de submissão do Spark.

## <a name="next-steps"></a>Passos Seguintes

- Saiba como [utilizar ferramentas do HDInsight no Azure Toolkit para IntelliJ para criar aplicativos de Apache Spark para um cluster do HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin.md).

- Para ver um vídeo sobre as ferramentas do HDInsight para IntelliJ, veja [introduz ferramentas do HDInsight para o IntelliJ para desenvolvimento do Apache Spark](https://www.youtube.com/watch?v=YTZzYVgut6c).

- Saiba como [depurar remotamente aplicações Apache Spark num cluster do HDInsight com o Azure Toolkit para IntelliJ através de SSH](../spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md).

- Saiba como [utilizar ferramentas do HDInsight no Azure Toolkit para IntelliJ para depurar aplicações do Apache Spark remotamente num cluster do HDInsight Spark Linux](../spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md).

- Saiba como [usar ferramentas do HDInsight no Azure Toolkit para Eclipse criar aplicações do Apache Spark](../spark/apache-spark-eclipse-tool-plugin.md).

- Para ver um vídeo sobre as ferramentas do HDInsight para o Eclipse, consulte [Use as ferramentas HDInsight para o Eclipse criar Spark aplicações](https://mix.office.com/watch/1rau2mopb6fha).
