---
title: Gerir as dependências da aplicação Spark no Azure HDInsight
description: Este artigo fornece uma introdução de como gerir as dependências de Spark em hdInsight Spark cluster para aplicações PySpark e Scala.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f0673523c74a0ea298e7d2d520952c3e98877e91
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930040"
---
# <a name="manage-spark-application-dependencies"></a>Gerir dependências de aplicação do Spark

Neste artigo, aprende-se a gerir dependências para as suas aplicações Spark em execução no HDInsight. Cobrimos tanto o Scala como o PySpark na aplicação Spark e no âmbito do cluster.

Utilize links rápidos para saltar para a secção com base no seu caso de utilizador:
* [Configurar dependências de frascos de trabalho spark usando o Caderno Jupyter](#use-jupyter-notebook)
* [Configurar dependências de frascos de trabalho spark usando use Azure Toolkit para IntelliJ](#use-azure-toolkit-for-intellij)
* [Configure dependências de frascos para cluster spark](#jar-libs-for-cluster)
* [Gerir dependências jar com segurança](#safely-manage-jar-dependencies)
* [Configurar pacotes de python de trabalho spark usando o Caderno Jupyter](#use-jupyter-notebook-1)
* [Gerir com segurança pacotes Python para cluster Spark](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Jar libs por um trabalho de Faísca
### <a name="use-jupyter-notebook"></a>Use o caderno Jupyter
Quando uma sessão de faíscas começar no Jupyter Notebook on Spark kernel para Scala, você pode configurar pacotes a partir de:

* [Maven Repositório](https://search.maven.org/), ou pacotes com contribuições comunitárias na [Spark Packages](https://spark-packages.org/).
* Ficheiros de frascos armazenados no armazenamento primário do seu cluster.

Vais usar a `%%configure` magia para configurar o caderno para usar um pacote externo. Em cadernos que usam pacotes externos, certifique-se de chamar a `%%configure` magia na primeira célula de código. Isto garante que o núcleo está configurado para utilizar a embalagem antes do início da sessão.

>
>[!IMPORTANT]  
>Se se esquecer de configurar o núcleo na primeira célula, pode usar o `%%configure` com o `-f` parâmetro, mas isso reiniciará a sessão e todos os progressos serão perdidos.

**Amostra para pacotes de Pacotes de Repositório de Maven ou De faísca**

Depois de localizar o pacote do Repositório Maven, colete os valores para **GroupId,** **ArtifactId** e **Versão**. Concatenar os três valores, separados por um cólon **.**

   ![Esquema de pacote concatenate](./media/apache-spark-manage-dependencies/spark-package-schema.png "Esquema de pacote concatenate")

Certifique-se de que os valores que recolhe correspondem ao seu cluster. Neste caso, estamos a usar o pacote de conector DB Spark Cosmos para Scala 2.11 e Spark 2.3 para o cluster HDInsight 3.6 Spark. Se não tiver a certeza, corra `scala.util.Properties.versionString` em célula de código no kernel Spark para obter a versão cluster Scala. Corra `sc.version` para obter a versão cluster Spark.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Amostra para frascos armazenados no armazenamento primário**

Utilize o [esquema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) para ficheiros de frascos no armazenamento primário dos seus clusters. Isto seria `wasb://` para o Azure Storage, `abfs://` para Azure Data Lake Storage Gen2 ou `adl://` para Azure Data Lake Storage Gen1. Se a transferência segura estiver ativada para o Azure Storage ou data lake storage gen2, o URI seria `wasbs://` ou `abfss://` . Consulte [a transferência segura.](../../storage/common/storage-require-secure-transfer.md)

Utilize a lista separada de vírgulas de caminhos de frascos para vários ficheiros de frascos, as Globs são permitidas. Os frascos estão incluídos nos caminhos do condutor e do executor.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

Depois de configurar pacotes externos, pode executar a importação em célula de código para verificar se as embalagens foram colocadas corretamente.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Use o kit de ferramentas Azure para IntelliJ
[O Azure Toolkit para o plug-in IntelliJ](./apache-spark-intellij-tool-plugin.md) fornece experiência de UI para submeter a aplicação Spark Scala a um cluster HDInsight. Fornece `Referenced Jars` e `Referenced Files` propriedades para configurar caminhos de libs frascos ao submeter a aplicação Spark. Veja mais detalhes sobre [como utilizar o Azure Toolkit para plug-in IntelliJ para HDInsight](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

![A caixa de diálogo de submissão de faíscas](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

## <a name="jar-libs-for-cluster"></a>Jar libs para cluster
Em alguns casos, pode querer configurar as dependências do frasco ao nível do cluster para que cada aplicação possa ser configurada com as mesmas dependências por padrão. A abordagem é adicionar os seus caminhos de jarro ao condutor de Spark e ao caminho da classe executora.

1. Executar abaixo as ações de script de amostra para copiar ficheiros de frascos do armazenamento primário `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` para o sistema de ficheiros local cluster `/usr/libs/sparklibs` . O passo é necessário à medida que o linux usa `:` para separar a lista de caminhos de classes, mas o HDInsight apenas suporta caminhos de armazenamento com esquemas como `wasb://` . O caminho de armazenamento remoto não funcionará corretamente se o adicionar diretamente ao caminho da classe.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Alterar a configuração do serviço Spark de Ambari para atualizar o caminho da classe. Vá a **Ambari > Spark > Configs > padrão de Spark2 personalizados**. **Adicione a propriedade** da seguinte forma. Use `:` para separar caminhos se tiver mais de um caminho a acrescentar. Globs são permitidos.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   ![Alterar config padrão faísca](./media/apache-spark-manage-dependencies/change-spark-default-config.png "Alterar config padrão faísca")

3. Guarde as configurações alteradas e reinicie os serviços com impacto.

   ![Reiniciar serviços com impacto](./media/apache-spark-manage-dependencies/restart-impacted-services.png "Reiniciar serviços com impacto")

Pode automatizar os passos utilizando [ações de script](../hdinsight-hadoop-customize-cluster-linux.md). A ação do script para [adicionar bibliotecas personalizadas da Hive](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) é uma boa referência. Ao alterar os configs do serviço Spark, certifique-se de que utiliza APIs Ambari em vez de modificar os ficheiros config diretamente. 

## <a name="safely-manage-jar-dependencies"></a>Gerir dependências jar com segurança
O cluster HDInsight tem dependências de frascos incorporados, e as atualizações para estas versões de frasco acontecem de vez em quando. Para evitar conflitos de versão entre frascos embutidos e os frascos que traz para referência, considere [sombrear as dependências da sua aplicação](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Pacotes python para um trabalho de faísca
### <a name="use-jupyter-notebook"></a>Use o caderno Jupyter
HDInsight Jupyter Notebook PySpark kernel não suporta a instalação de pacotes Python do repositório de pacotes PyPi ou Anaconda diretamente. Se `.zip` `.egg` tiver, ou `.py` dependências, e quiser fazê-los referenciar para uma sessão de Faísca, siga abaixo os passos:

1. Executar abaixo as ações de script de amostra para copiar `.zip` , `.egg` ou `.py` ficheiros do armazenamento primário para o `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` sistema de ficheiros local cluster `/usr/libs/pylibs` . O passo é necessário à medida que o linux usa `:` para separar a lista de caminhos de pesquisa, mas o HDInsight apenas suporta caminhos de armazenamento com esquemas como `wasb://` . O caminho de armazenamento remoto não funcionará corretamente quando utilizar `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. No seu caderno, corra abaixo do código numa célula de código com o kernel PySpark:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. Corra `import` para verificar se as suas encomendas foram incluídas com sucesso.  

## <a name="python-packages-for-cluster"></a>Pacotes python para cluster
Você pode instalar pacotes Python de Anaconda para o cluster usando o comando conda através de ações de script. As embalagens instaladas estão ao nível do cluster e aplicam-se a todas as aplicações. 

O cluster HDInsight Spark tem duas instalações python incorporadas, Anaconda Python 2.7 e Anaconda Python 3.5. Para saber mais sobre as definições padrão de Python para serviços e como instalar com segurança pacotes Python externos sem quebrar o cluster, consulte mais detalhes em [Seguros gerir as dependências python para o seu cluster.](./apache-spark-python-package-installation.md)
