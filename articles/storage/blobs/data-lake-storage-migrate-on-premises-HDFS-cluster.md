---
title: Utilização do Azure Data Box para migrar dados a partir de locais HDFS armazenar no armazenamento do Azure
description: Migrar dados a partir de uma loja HDFS no local para o armazenamento do Azure
services: storage
author: normesta
ms.service: storage
ms.date: 06/11/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4445a8566c04d30cfb8743cbd33623f2e23f0dde
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595405"
---
# <a name="use-azure-data-box-to-migrate-data-from-an-on-premises-hdfs-store-to-azure-storage"></a>Utilizar o Azure Data Box para migrar dados a partir de uma loja HDFS no local para o armazenamento do Azure

Pode migrar dados de um arquivo HDFS no local do seu cluster do Hadoop para o armazenamento do Azure (armazenamento de BLOBs ou geração 2 de armazenamento do Data Lake) com um dispositivo do Data Box. Pode escolher entre uma caixa de dados de 80 TB ou uma sobrecarga de caixa de dados de 770 TB.

Este artigo ajuda-o a concluir estas tarefas:

> [!div class="checklist"]
> * Prepare para migrar os seus dados.
> * Copie os dados para uma caixa de dados ou um dispositivo pesadas de caixa de dados.
> * Envie o dispositivo à Microsoft.
> * Mova os dados para a geração 2 de armazenamento do Data Lake.

## <a name="prerequisites"></a>Pré-requisitos

Precisa essas coisas para concluir a migração.

* Duas contas de armazenamento; que tem um espaço de nomes hierárquico habilitado nela e que não.

* Um cluster de Hadoop no local que contém os dados de origem.

* Uma [dispositivo do Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Encomendar o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) ou [dados de caixa pesados](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Enquanto o dispositivo de ordenação, lembre-se de escolher um armazenamento de conta que **não** ter espaços de nomes hierárquicos ativados no mesmo. Isso é porque dispositivos do Data Box ainda não suportam ingestão direto no Azure Data Lake Storage Gen2. Terá de copiar para uma conta de armazenamento e, em seguida, fazer uma segunda cópia para a conta do ADLS Gen2. Instruções para isto são indicadas nos passos abaixo.

  * Instalar os cabos e ligue-se sua [Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) ou [dados caixa pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a uma rede no local.

Se estiver pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copiar os dados num dispositivo do Data Box

Se seus dados se encaixa num único dispositivo do Data Box, em seguida, irá copiar os dados no dispositivo do Data Box. 

Se o tamanho dos dados exceder a capacidade do dispositivo do Data Box, em seguida, utilize o [procedimento opcional para dividir os dados em vários dispositivos de Data Box](#appendix-split-data-across-multiple-data-box-devices) e, em seguida, efetue este passo. 

Para copiar os dados da sua loja HDFS no local para um dispositivo do Data Box, irá configurar algumas coisas e, em seguida, utilize o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) ferramenta.

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

4. Defina a variável de shell `azjars` para a localização do `hadoop-azure` e `azure-storage` jar ficheiros. Pode encontrar estes ficheiros no diretório de instalação do Hadoop.

    Para determinar se estes ficheiros de existir, utilize o seguinte comando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Substitua o `<hadoop_install_dir>` marcador de posição pelo caminho para o diretório onde instalou o Hadoop. Certifique-se de que use caminhos totalmente qualificados.

    Exemplos:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Crie o contentor de armazenamento que pretende utilizar para a cópia de dados. Também deve especificar um diretório de destino como parte deste comando. Isto pode ser um diretório de destino fictício neste momento.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` marcador de posição pelo nome do seu ponto final de serviço de Blobs.

    * Substitua o `<account_key>` marcador de posição pela chave de acesso da sua conta.

    * Substitua o `<container-name>` marcador de posição pelo nome do seu contentor.

    * Substitua o `<destination_directory>` com o nome do diretório que pretende copiar os dados para o marcador de posição.

6. Execute um comando de lista para garantir que o contentor e directory foram criados.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Substitua o `<blob_service_endpoint>` marcador de posição pelo nome do seu ponto final de serviço de Blobs.

   * Substitua o `<account_key>` marcador de posição pela chave de acesso da sua conta.

   * Substitua o `<container-name>` marcador de posição pelo nome do seu contentor.

7. Copie dados do Hadoop HDFS para armazenamento de BLOBs de caixa de dados, para o contentor que criou anteriormente. Se o diretório que está a copiar para não for encontrado, o comando cria-a automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` marcador de posição pelo nome do seu ponto final de serviço de Blobs.

    * Substitua o `<account_key>` marcador de posição pela chave de acesso da sua conta.

    * Substitua o `<container-name>` marcador de posição pelo nome do seu contentor.

    * Substitua o `<exlusion_filelist_file>` marcador de posição pelo nome do ficheiro que contém a sua lista de exclusões de ficheiros.

    * Substitua o `<source_directory>` marcador de posição pelo nome do diretório que contém os dados que pretende copiar.

    * Substitua o `<destination_directory>` com o nome do diretório que pretende copiar os dados para o marcador de posição.

    O `-libjars` opção é utilizada para efetuar a `hadoop-azure*.jar` e dependent `azure-storage*.jar` disponíveis para os ficheiros `distcp`. Já Isto pode ocorrer para alguns clusters.

    A exemplo a seguir mostra como o `distcp` comando é utilizado para copiar dados.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Para melhorar a velocidade de cópia:

    * Tente alterar o número de mapeadores. (O exemplo acima utiliza `m` = 4 mapeadores.)

    * Tente executar vários `distcp` em paralelo.

    * Lembre-se de que arquivos grandes desempenho melhor do que arquivos pequenos.

## <a name="ship-the-data-box-to-microsoft"></a>Envie o Data Box para a Microsoft

Siga estes passos para preparar e enviar o dispositivo do Data Box para a Microsoft.

1. Primeiro, [preparação para envio em sua caixa de dados ou dados caixa pesadas](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Depois de concluída a preparação do dispositivo, transferir os ficheiros BOM. Irá utilizar estes BOM ou mais tarde para verificar os dados carregados para o Azure, os arquivos de manifesto.

3. Encerre o dispositivo e remova os cabos.

4. Agende uma recolha no-BREAK.

    * Para dispositivos do Data Box, consulte [envie o Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Para dispositivos pesadas de caixa de dados, consulte [envie sua pesada dados de caixa](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Após a Microsoft receber o seu dispositivo, está ligado à rede do Centro de dados e os dados são carregados para a conta de armazenamento especificada (com espaços de nomes hierárquicos desativados) quando colocou a ordem de dispositivo. Certifique-se contra os ficheiros BOM que todos os seus dados é carregado para o Azure. Agora pode aceder estes dados para uma conta de armazenamento de geração 2 de armazenamento do Data Lake.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Mover os dados para a geração 2 de armazenamento do Azure Data Lake

Já tem os dados na sua conta de armazenamento do Azure. Agora irá copiar os dados para a sua conta de armazenamento do Azure Data Lake e aplicar as permissões de acesso a arquivos e diretórios.

> [!NOTE]
> Este passo é necessário se estiver a utilizar o Azure Data Lake Storage Gen2 como arquivo de dados. Se estiver a utilizar apenas uma conta de armazenamento de BLOBs sem espaço de nomes hierárquico como arquivo de dados, pode ignorar esta secção.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Copiar dados para a conta de armazenamento Gen 2 do Azure Data Lake

Pode copiar dados com o Azure Data Factory, ou utilizando o seu cluster do Hadoop de baseados no Azure.

* Para utilizar o Azure Data Factory, veja [do Azure Data Factory para mover dados para ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Confirme que especifica **armazenamento de Blobs do Azure** como origem.

* Para utilizar o seu cluster do Hadoop de baseados no Azure, execute este comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Substitua a `<source_account>` e `<dest_account>` marcadores de posição com os nomes das contas de armazenamento de origem e de destino.

    * Substitua a `<source_container>` e `<dest_container>` marcadores de posição com os nomes dos contentores de origem e de destino.

    * Substitua a `<source_path>` e `<dest_path>` marcadores de posição com os caminhos de diretório de origem e de destino.

    * Substitua o `<source_account_key>` marcador de posição pela chave de acesso da conta de armazenamento que contém os dados.

    Este comando copia dados e metadados da conta de armazenamento para a sua conta de armazenamento de geração 2 de armazenamento do Data Lake.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Criar um principal de serviço para a sua conta de geração 2 de armazenamento do Azure Data Lake

Para criar um principal de serviço, consulte [como: Utilizar o portal para criar um Azure AD principal de aplicações e serviço que pode aceder a recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

* Quando realizar os passos no [atribuir a aplicação a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) secção do artigo, lembre-se de que atribuir a **contribuinte de dados de Blob de armazenamento** função ao principal de serviço.

* Ao realizar os passos a [obter os valores para iniciar sessão](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, ID da aplicação e valores secretos do cliente num arquivo de texto. Precisará aqueles em breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Gerar uma lista de ficheiros copiados com as suas permissões

A partir do cluster do Hadoop no local, execute este comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Este comando gera uma lista de ficheiros copiados com as suas permissões.

> [!NOTE]
> Dependendo do número de ficheiros do hdfs, este comando pode demorar muito tempo a executar.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Gerar uma lista de identidades e mapeá-los para as identidades do Azure Active Directory (adicionar)

1. Transferir o `copy-acls.py` script. Consulte a [transfira scripts auxiliares e configure o seu nó de extremidade para executá-los](#download-helper-scripts) seção deste artigo.

2. Execute este comando para gerar uma lista de identidades únicas.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Este script cria um arquivo chamado `id_map.json` que contém as identidades que tem de mapear para identidades baseadas em Adicionar.

3. Abra o `id_map.json` ficheiro num editor de texto.

4. Para cada objeto JSON que é apresentado no ficheiro, atualize o `target` atributo de um nome de Principal utilizador do AAD (UPN) ou o ObjectId (OID), com o adequado mapeado a identidade. Depois de terminar, guarde o ficheiro. Precisará este ficheiro no próximo passo.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Aplicar permissões ao arquivos copiados e aplicar os mapeamentos de identidade

Execute este comando para aplicar permissões para os dados que copiou para a conta de geração 2 de armazenamento do Data Lake:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Substitua o `<storage-account-name>` marcador de posição pelo nome da sua conta de armazenamento.

* Substitua o `<container-name>` marcador de posição pelo nome do seu contentor.

* Substitua a `<application-id>` e `<client-secret>` marcadores de posição com o segredo de cliente e o ID de aplicação que recolheu quando criou o principal de serviço.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Apêndice: Dividir dados em vários dispositivos de Data Box

Antes de mover os dados para um dispositivo do Data Box, terá de transferir alguns scripts de programa auxiliar, certifique-se de que os seus dados são organizados para se encaixa num dispositivo do Data Box e excluir arquivos desnecessários.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Transfira scripts auxiliares e configure o seu nó de extremidade para executá-los

1. A partir de seu edge ou o nó principal do seu cluster do Hadoop no local, execute este comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Este comando clona o repositório do GitHub que contém os scripts auxiliares.

2. Certifique-se de que tem o [jq](https://stedolan.github.io/jq/) pacote instalado no seu computador local.

   ```bash
   
   sudo apt-get install jq
   ```

3. Instalar o [pedidos](http://docs.python-requests.org/en/master/) pacote do python.

   ```bash
   
   pip install requests
   ```

4. Conjunto de permissões de execução sobre os scripts necessários.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Certifique-se de que os seus dados são organizados de acordo com as num dispositivo do Data Box

Se o tamanho dos seus dados excede o tamanho de um único dispositivo do Data Box, pode dividir ficheiros em grupos que pode armazenar em vários dispositivos de Data Box.

Se seus dados não excederem o tamanho de único dispositivo do Data Box, pode avançar para a secção seguinte.

1. Com permissões elevadas, execute o `generate-file-list` script que transferiu, ao seguir as orientações na secção anterior.

   Eis uma descrição dos parâmetros de comando:

   ```
   sudo -u hdfs ./generate-file-list.py [-h] [-s DATABOX_SIZE] [-b FILELIST_BASENAME]
                    [-f LOG_CONFIG] [-l LOG_FILE]
                    [-v {DEBUG,INFO,WARNING,ERROR}]
                    path

   where:
   positional arguments:
   path                  The base HDFS path to process.

   optional arguments:
   -h, --help            show this help message and exit
   -s DATABOX_SIZE, --databox-size DATABOX_SIZE
                        The size of each Data Box in bytes.
   -b FILELIST_BASENAME, --filelist-basename FILELIST_BASENAME
                        The base name for the output filelists. Lists will be
                        named basename1, basename2, ... .
   -f LOG_CONFIG, --log-config LOG_CONFIG
                        The name of a configuration file for logging.
   -l LOG_FILE, --log-file LOG_FILE
                        Name of file to have log output written to (default is
                        stdout/stderr)
   -v {DEBUG,INFO,WARNING,ERROR}, --log-level {DEBUG,INFO,WARNING,ERROR}
                        Level of log information to output. Default is 'INFO'.
   ```

2. Copiar o arquivo gerado apresenta uma lista de HDFS, para que fiquem acessíveis para o [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) tarefa.

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Excluir arquivos desnecessários

Terá de excluir alguns diretórios da tarefa DisCp. Por exemplo, exclua diretórios que contêm informações de estado que manter o cluster em execução.

No cluster de Hadoop no local em que pretende iniciar a tarefa de DistCp, crie um ficheiro que especifica a lista de diretórios que pretende excluir.

Segue-se um exemplo:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Passos Seguintes

Saiba como a geração 2 de armazenamento do Data Lake funciona com clusters do HDInsight. Ver [Use Azure Data Lake armazenamento Gen2 com o Azure HDInsight clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
