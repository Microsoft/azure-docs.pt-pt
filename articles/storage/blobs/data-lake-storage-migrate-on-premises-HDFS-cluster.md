---
title: Utilização do Azure Data Box para migrar dados a partir de locais HDFS armazenar no armazenamento do Azure
description: Migrar dados a partir de uma loja HDFS no local para o armazenamento do Azure
services: storage
author: normesta
ms.service: storage
ms.date: 03/01/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0a658d47f850537f18c976ab0933105b57692673
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344856"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Utilizar o Azure Data Box para migrar dados a partir de uma loja HDFS no local para o armazenamento do Azure

Pode migrar dados de um arquivo HDFS no local do seu cluster do Hadoop para o armazenamento do Azure (armazenamento de BLOBs ou geração 2 de armazenamento do Data Lake) com um dispositivo do Data Box.

Este artigo ajuda-o a concluir estas tarefas:

:heavy_check_mark: Copie os dados para um dispositivo do Data Box.

:heavy_check_mark: Enviar o dispositivo do Data Box para a Microsoft.

:heavy_check_mark: Mova os dados na sua conta de armazenamento de geração 2 de armazenamento do Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

Precisa essas coisas para concluir a migração.

* Conta de armazenamento do Azure que **não** ter espaços de nomes hierárquicos ativados no mesmo.

* Se pretender utilizar a conta de geração 2 de armazenamento do Azure Data Lake (um armazenamento de conta que **faz** ter espaços de nomes hierárquicos ativados no mesmo), em seguida, pode querer criá-lo neste momento.

* Um cluster de Hadoop no local que contém os dados de origem.

* Uma [dispositivo do Azure Data Box](https://azure.microsoft.com/services/storage/databox/). 

    - [Encomendar o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered). Durante a ordenação de sua caixa, lembre-se de escolher um armazenamento de conta que **não** ter espaços de nomes hierárquicos ativados no mesmo. Isto acontece porque o Data Box ainda não suporta ingestão direto no Azure Data Lake Storage Gen2. Terá de copiar para uma conta de armazenamento e, em seguida, fazer uma segunda cópia para a conta do ADLS Gen2. Instruções para isto são indicadas nos passos abaixo.
    - [Instalar os cabos e ligue-se o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) a uma rede no local.

Se estiver pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiar os dados num dispositivo do Data Box

Para copiar os dados da sua loja HDFS no local para um dispositivo do Data Box, irá configurar algumas coisas e, em seguida, utilize o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ferramenta.

Se a quantidade de dados que está a copiar é maior do que a capacidade de uma única caixa de dados, terá de dividir seu conjunto de dados em tamanhos de que se encaixam em suas caixas de dados.

Siga estes passos para copiar dados através do armazenamento de REST APIs de Blob/objeto para o Data Box. A interface REST API fará com que o Data Box aparecem como um arquivo de HDFS ao seu cluster. 


1. Antes de copiar os dados através de REST, identifique os primitivos de segurança e a ligação para ligar à interface REST no Data Box. Inicie sessão no web local da interface do Usuário do Data Box e aceda a **Connect e a cópia** página. Com o armazenamento do Azure da conta para o Data Box, em **definições de acesso**, localize e selecione **REST(Preview)**.

    ![Página "Ligar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na conta de armazenamento de acesso e o diálogo de dados de carregamento, copie o **ponto final de serviço Blob** e o **chave de conta de armazenamento**. Do ponto de extremidade do serviço de BLOBs, omita o `https://` e a barra.

    Neste caso, o ponto final é: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. É a parte de anfitrião do URI que pretende utilizar: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Por exemplo, veja como [ligue-se ao REST através de http](/azure/databox/data-box-deploy-copy-data-via-rest.md). 

     ![Caixa de diálogo "Aceder à conta de armazenamento e carregar dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicionar o ponto final e o endereço IP da caixa de dados para `/etc/hosts` em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```
    Se estiver a utilizar algum outro mecanismo para DNS, deve garantir que o Data Box ponto final pode ser resolvido.
    
3. Definir uma variável de shell `azjars` para apontar para o `hadoop-azure` e o `microsoft-windowsazure-storage-sdk` jar ficheiros. Estes ficheiros estão sob o diretório de instalação do Hadoop (pode verificar se estes ficheiros existem ao utilizar este comando `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` onde `<hadoop_install_dir>` é o diretório onde instalou o Hadoop) utiliza os caminhos completos. 
    
    ```
    # azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar
    # azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar
    ```

4. Copie dados do Hadoop HDFS para armazenamento de BLOBs de caixa de dados.

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
- Tente executar múltiplas `distcp` em paralelo.
- Lembre-se de que arquivos grandes desempenho melhor do que arquivos pequenos.       
    
## <a name="ship-the-data-box-to-microsoft"></a>Envie o Data Box para a Microsoft

Siga estes passos para preparar e enviar o dispositivo do Data Box para a Microsoft.

1. Depois de concluída a cópia de dados, execute [preparação para envio](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#prepare-to-ship) em sua caixa de dados. Depois de concluída a preparação do dispositivo, transferir os ficheiros BOM. Irá utilizar estes BOM ou mais tarde para verificar os dados carregados para o Azure, os arquivos de manifesto. Encerre o dispositivo e remova os cabos. 
2.  Agendar uma recolha com Backups sejam [envie o Data Box para o Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up). 
3.  Após a Microsoft receber o seu dispositivo, está ligado ao centro de dados de rede e dados são carregados para a conta de armazenamento especificada (com espaços de nomes hierárquicos desativados) quando pediu o Data Box. Certifique-se contra os ficheiros BOM que todos os seus dados é carregado para o Azure. Agora pode aceder estes dados para uma conta de armazenamento de geração 2 de armazenamento do Data Lake.

## <a name="move-the-data-onto-your-data-lake-storage-gen2-storage-account"></a>Mover os dados na sua conta de armazenamento de geração 2 de armazenamento do Data Lake

Este passo é necessário se estiver a utilizar o Azure Data Lake Storage Gen2 como arquivo de dados. Se estiver a utilizar apenas uma conta de armazenamento de BLOBs sem espaço de nomes hierárquico como arquivo de dados, não é necessário executar este passo.

Pode fazê-lo de maneiras de 2. 

- Uso [do Azure Data Factory para mover dados para ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Terá de especificar **armazenamento de Blobs do Azure** como origem.

- Utilize o seu cluster do Hadoop de baseados no Azure. Pode executar este comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container} @{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
    ```

Este comando copia dados e metadados da conta de armazenamento para a sua conta de armazenamento de geração 2 de armazenamento do Data Lake.

## <a name="next-steps"></a>Passos Seguintes

Saiba como a geração 2 de armazenamento do Data Lake funciona com clusters do HDInsight. Consulte [clusters de utilização do Azure Data Lake Storage Gen2 com o Azure HDInsight](https://docs.microsoft.com/Azure/storage/blobs/data-lake-storage-use-hdi-cluster?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
