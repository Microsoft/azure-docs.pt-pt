---
title: Utilização do Azure Data Box para migrar dados a partir de locais HDFS armazenar no armazenamento do Azure
description: Migrar dados a partir de uma loja HDFS no local para o armazenamento do Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/05/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 9a42135df38cde91cc6626a3f7d0328334af0a5d
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729037"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Utilizar o Azure Data Box para migrar dados a partir de uma loja HDFS no local para o armazenamento do Azure

Pode migrar dados de um arquivo HDFS no local do seu cluster do Hadoop para o armazenamento do Azure (armazenamento de BLOBs ou geração 2 de armazenamento do Data Lake) com um dispositivo do Data Box. Pode escolher entre uma caixa de dados de 80 TB ou uma sobrecarga de caixa de dados de 770 TB.

Este artigo ajuda-o a concluir estas tarefas:

:heavy_check_mark: Copie os dados para uma caixa de dados ou um dispositivo pesadas de caixa de dados.

:heavy_check_mark: Envie o dispositivo à Microsoft.

:heavy_check_mark: Mova os dados na sua conta de armazenamento de geração 2 de armazenamento do Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

Precisa essas coisas para concluir a migração.

* Conta de armazenamento do Azure que **não** ter espaços de nomes hierárquicos ativados no mesmo.

* Se pretender utilizar a conta de geração 2 de armazenamento do Azure Data Lake (um armazenamento de conta que **faz** ter espaços de nomes hierárquicos ativados no mesmo), em seguida, pode querer criá-lo neste momento.

* Um cluster de Hadoop no local que contém os dados de origem.

* Uma [dispositivo do Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

    - [Encomendar o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) ou [dados de caixa pesados](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Enquanto o dispositivo de ordenação, lembre-se de escolher um armazenamento de conta que **não** ter espaços de nomes hierárquicos ativados no mesmo. Isso é porque dispositivos do Data Box ainda não suportam ingestão direto no Azure Data Lake Storage Gen2. Terá de copiar para uma conta de armazenamento e, em seguida, fazer uma segunda cópia para a conta do ADLS Gen2. Instruções para isto são indicadas nos passos abaixo.
    - Instalar os cabos e ligue-se sua [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) ou [dados caixa pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a uma rede no local.

Se estiver pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiar os dados num dispositivo do Data Box

Para copiar os dados da sua loja HDFS no local para um dispositivo do Data Box, irá configurar algumas coisas e, em seguida, utilize o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ferramenta.

Se a quantidade de dados que está a copiar é maior do que a capacidade de uma única caixa de dados ou de nó único em dados de caixa pesada, divida o seu conjunto de dados em tamanhos de que se encaixam em seus dispositivos.

Siga estes passos para copiar dados através do armazenamento de REST APIs de Blob/objeto para o seu dispositivo do Data Box. A interface REST API fará com que o dispositivo aparecer como um armazenamento de HDFS ao seu cluster. 


1. Antes de copiar os dados através de REST, identifique os primitivos de segurança e a ligação para ligar à interface REST do Data Box ou pesadas de caixa de dados. Inicie sessão no web local da interface do Usuário do Data Box e aceda a **Connect e a cópia** página. Com o armazenamento do Azure da conta para o seu dispositivo, em **definições de acesso**, localize e selecione **REST**.

    ![Página "Ligar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na conta de armazenamento de acesso e o diálogo de dados de carregamento, copie o **ponto final de serviço Blob** e o **chave de conta de armazenamento**. Do ponto de extremidade do serviço de BLOBs, omita o `https://` e a barra.

    Neste caso, o ponto final é: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. É a parte de anfitrião do URI que pretende utilizar: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Por exemplo, veja como [ligue-se ao REST através de http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Caixa de diálogo "Aceder à conta de armazenamento e carregar dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicionar o ponto final e o endereço IP de nó de caixa de dados ou dados de caixa pesada a `/etc/hosts` em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Se estiver a utilizar algum outro mecanismo para DNS, deve garantir que o Data Box ponto final pode ser resolvido.
    
4. Definir uma variável de shell `azjars` para apontar para o `hadoop-azure` e o `microsoft-windowsazure-storage-sdk` jar ficheiros. Estes ficheiros estão sob o diretório de instalação do Hadoop (pode verificar se estes ficheiros existem ao utilizar este comando `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` onde `<hadoop_install_dir>` é o diretório onde instalou o Hadoop) utiliza os caminhos completos. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

5. Crie o contentor de armazenamento que pretende utilizar para a cópia de dados. Também deve especificar uma pasta de destino como parte deste comando. Isto pode ser uma pasta de destino fictício neste momento.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -mkdir -p  wasb://[container_name]@[blob_service_endpoint]/[destination_folder]
    ```

6. Execute um comando de lista para garantir que o seu contentor e a pasta foram criados.

    ```
    # hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -ls -R  wasb://[container_name]@[blob_service_endpoint]/
    ```

7. Copie dados do Hadoop HDFS para armazenamento de BLOBs de caixa de dados, para o contentor que criou anteriormente. Se a pasta que está a copiar para não for encontrada, o comando cria-a automaticamente.

    ```
    # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.[blob_service_endpoint]=[account_key] \
    -strategy dynamic -m 4 -update \
    [source_directory] \
           wasb://[container_name]@[blob_service_endpoint]/[destination_folder]       
    ```
   O `-libjars` opção é utilizada para efetuar a `hadoop-azure*.jar` e dependent `azure-storage*.jar` disponíveis para os ficheiros `distcp`. Já Isto pode ocorrer para alguns clusters.
   
   A exemplo a seguir mostra como o `distcp` comando é utilizado para copiar dados.
   
   ```
   # hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -strategy dynamic -m 4 -update \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/testfiles
   ```
  
Para melhorar a velocidade de cópia:
- Tente alterar o número de mapeadores. (O exemplo acima utiliza `m` = 4 mapeadores.)
- Tente executar vários `distcp` em paralelo.
- Lembre-se de que arquivos grandes desempenho melhor do que arquivos pequenos.
    
## <a name="ship-the-data-box-to-microsoft"></a>Envie o Data Box para a Microsoft

Siga estes passos para preparar e enviar o dispositivo do Data Box para a Microsoft.

1. Depois de concluída a cópia de dados, execute:
    
    - [Preparação para envio em sua caixa de dados ou dados caixa pesadas](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).
    - Depois de concluída a preparação do dispositivo, transferir os ficheiros BOM. Irá utilizar estes BOM ou mais tarde para verificar os dados carregados para o Azure, os arquivos de manifesto. 
    - Encerre o dispositivo e remova os cabos.
2.  Agende uma recolha no-BREAK. Siga as instruções para:

    - [Envie o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up) 
    - [Envie sua pesado de caixa de dados](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).
3.  Após a Microsoft receber o seu dispositivo, está ligado à rede do Centro de dados e os dados são carregados para a conta de armazenamento especificada (com espaços de nomes hierárquicos desativados) quando colocou a ordem de dispositivo. Certifique-se contra os ficheiros BOM que todos os seus dados é carregado para o Azure. Agora pode aceder estes dados para uma conta de armazenamento de geração 2 de armazenamento do Data Lake.


## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Mover os dados na sua conta de armazenamento de geração 2 de armazenamento do Data Lake

Este passo é necessário se estiver a utilizar o Azure Data Lake Storage Gen2 como arquivo de dados. Se estiver a utilizar apenas uma conta de armazenamento de BLOBs sem espaço de nomes hierárquico como arquivo de dados, não é necessário executar este passo.

Pode fazê-lo de duas formas.

- Uso [do Azure Data Factory para mover dados para ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Terá de especificar **armazenamento de Blobs do Azure** como origem.

- Utilize o seu cluster do Hadoop de baseados no Azure. Pode executar este comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Este comando copia dados e metadados da conta de armazenamento para a sua conta de armazenamento de geração 2 de armazenamento do Data Lake.

## <a name="next-steps"></a>Passos Seguintes

Saiba como a geração 2 de armazenamento do Data Lake funciona com clusters do HDInsight. Ver [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
