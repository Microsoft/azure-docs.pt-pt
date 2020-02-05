---
title: Migrar da loja on-prem HDFS para o Armazenamento Azure com caixa de dados Azure
description: Migrar dados de uma loja HDFS no local para o Armazenamento Azure
author: normesta
ms.service: storage
ms.date: 11/19/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e82c325ad5ad91e6b4503949e6534b054023f1f2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990968"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrar da loja on-prem HDFS para o Armazenamento Azure com caixa de dados Azure

Pode migrar dados de uma loja HDFS no local do seu cluster Hadoop para o Armazenamento Azure (armazenamento de blob ou Data Lake Storage Gen2) utilizando um dispositivo Data Box. Pode escolher entre uma Caixa de Dados de 80 TB ou uma caixa de dados de 770 TB Pesada.

Este artigo ajuda-o a completar estas tarefas:

> [!div class="checklist"]
> * Prepare-se para migrar os seus dados.
> * Copie os seus dados para uma Caixa de Dados ou para um dispositivo Data Box Heavy.
> * Envie o dispositivo de volta para a Microsoft.
> * Mova os dados para Data Lake Storage Gen2.

## <a name="prerequisites"></a>Pré-requisitos

Precisas destas coisas para completar a migração.

* Duas contas de armazenamento; um que tem um espaço de nome hierárquico habilitado nele, e um que não tem.

* Um aglomerado hadoop no local que contém os seus dados de origem.

* Um dispositivo Caixa de [Dados Azure](https://azure.microsoft.com/services/storage/databox/).

  * [Encomende a sua Caixa](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered) de Dados ou [Caixa de Dados Pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-ordered). Ao encomendar o seu dispositivo, lembre-se de escolher uma conta de armazenamento que **não** tenha espaços hierárquicos habilitados nele. Isto porque os dispositivos Data Box ainda não suportam a ingestão direta no Azure Data Lake Storage Gen2. Terá de copiar para uma conta de armazenamento e, em seguida, fazer uma segunda cópia na conta ADLS Gen2. As instruções para tal são dadas nos degraus abaixo.

  * Cabo e ligue a sua Caixa de [Dados](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) ou Caixa de [Dados Pesada](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-set-up) a uma rede no local.

Se estiver pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copie os seus dados para um dispositivo Data Box

Se os seus dados se enquadrarem num único dispositivo data Box, então irá copiar os dados para o dispositivo Data Box. 

Se o tamanho dos seus dados exceder a capacidade do dispositivo Data Box, utilize o [procedimento opcional para dividir os dados em vários dispositivos da Data Box](#appendix-split-data-across-multiple-data-box-devices) e, em seguida, executar este passo. 

Para copiar os dados da sua loja HDFS no local para um dispositivo Data Box, irá configurar algumas coisas e, em seguida, utilizar a ferramenta [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Siga estes passos para copiar dados através das APIs REST do armazenamento blob/objeto para o seu dispositivo Data Box. A interface REST API fará com que o dispositivo apareça como uma loja HDFS para o seu cluster.

1. Antes de copiar os dados via REST, identifique os primitivos de segurança e ligação para se ligar à interface REST na Caixa de Dados ou Caixa de Dados Pesada. Inscreva-se na Web UI local da Data Box e vá para **connect e página de cópia.** Contra a conta de armazenamento Azure do seu dispositivo, sob **as definições**de Acesso, localize e selecione **REST**.

    ![Página "Ligar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na conta de armazenamento de acesso e no diálogo de dados de upload, copie o ponto final do **serviço Blob** e a **chave da conta de armazenamento**. Do ponto final do serviço de blob, omita o `https://` e o corte de rasto.

    Neste caso, o ponto final é: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/`. A parte hospedeira do URI que vai usar é: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com`. Por exemplo, veja como [ligar para descansar em http](/azure/databox/data-box-deploy-copy-data-via-rest). 

     ![Diálogo "conta de armazenamento de acesso e upload de dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicione o ponto final e a caixa de dados ou endereço IP do nó pesado para `/etc/hosts` em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se estiver a utilizar outro mecanismo para o DNS, deve certificar-se de que o ponto final da Caixa de Dados pode ser resolvido.

4. Deteto a variável do reservatório `azjars` à localização dos ficheiros `hadoop-azure` e `azure-storage` jarros. Pode encontrar estes ficheiros sob o diretório de instalação hadoop.

    Para determinar se estes ficheiros existem, utilize o seguinte comando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure`. Substitua o `<hadoop_install_dir>` espaço reservado pelo caminho para o diretório onde instalou Hadoop. Certifique-se de que utiliza caminhos totalmente qualificados.

    Exemplos:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Crie o recipiente de armazenamento que pretende utilizar para cópia de dados. Deve também especificar um diretório de destino como parte deste comando. Este pode ser um diretório de destino idiota neste momento.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o espaço reservado `<blob_service_endpoint>` com o nome do seu ponto final de serviço blob.

    * Substitua o espaço reservado `<account_key>` pela chave de acesso da sua conta.

    * Substitua o espaço reservado `<container-name>` com o nome do seu recipiente.

    * Substitua o espaço reservado `<destination_directory>` pelo nome do diretório para o que pretende copiar os seus dados.

6. Execute um comando de lista para garantir que o seu recipiente e diretório foram criados.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Substitua o espaço reservado `<blob_service_endpoint>` com o nome do seu ponto final de serviço blob.

   * Substitua o espaço reservado `<account_key>` pela chave de acesso da sua conta.

   * Substitua o espaço reservado `<container-name>` com o nome do seu recipiente.

7. Copie os dados do Hadoop HDFS para o armazenamento da Caixa de Dados Blob, no recipiente que criou anteriormente. Se o diretório em que está a copiar não for encontrado, o comando cria-o automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o espaço reservado `<blob_service_endpoint>` com o nome do seu ponto final de serviço blob.

    * Substitua o espaço reservado `<account_key>` pela chave de acesso da sua conta.

    * Substitua o espaço reservado `<container-name>` com o nome do seu recipiente.

    * Substitua o espaço reservado `<exlusion_filelist_file>` pelo nome do ficheiro que contém a sua lista de exclusões de ficheiros.

    * Substitua o espaço reservado `<source_directory>` pelo nome do diretório que contém os dados que pretende copiar.

    * Substitua o espaço reservado `<destination_directory>` pelo nome do diretório para o que pretende copiar os seus dados.

    A opção `-libjars` é utilizada para disponibilizar os ficheiros `hadoop-azure*.jar` e `azure-storage*.jar` dependentes para `distcp`. Isto pode já ocorrer para alguns aglomerados.

    O exemplo que se segue mostra como o comando `distcp` é usado para copiar dados.

    ```
     hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.mystorageaccount.blob.mydataboxno.microsoftdatabox.com=myaccountkey \
    -filter ./exclusions.lst -f /tmp/copylist1 -m 4 \
    /data/testfiles \
    wasb://hdfscontainer@mystorageaccount.blob.mydataboxno.microsoftdatabox.com/data
    ```
  
    Para melhorar a velocidade da cópia:

    * Tente mudar o número de mappers. (O exemplo acima usa `m` = 4 mappers.)

    * Tente correr várias `distcp` em paralelo.

    * Lembre-se que os ficheiros grandes funcionam melhor do que ficheiros pequenos.

## <a name="ship-the-data-box-to-microsoft"></a>Envie a Caixa de Dados para a Microsoft

Siga estes passos para preparar e enviar o dispositivo Data Box para a Microsoft.

1. Primeiro, [prepare-se para enviar na sua Caixa de Dados ou Caixa de Dados Pesada](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest).

2. Depois de concluída a preparação do dispositivo, faça o download dos ficheiros BOM. Utilizará estes ficheiros BOM ou manifestos mais tarde para verificar os dados enviados para o Azure.

3. Desligue o aparelho e retire os cabos.

4. Agende uma recolha com a UPS.

    * Para dispositivos Data Box, consulte [Enviar a sua Caixa de Dados](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

    * Para dispositivos pesados data Box, consulte [Enviar a sua caixa de dados heavy](https://docs.microsoft.com/azure/databox/data-box-heavy-deploy-picked-up).

5. Depois de a Microsoft receber o seu dispositivo, está ligado à rede do data center e os dados são enviados para a conta de armazenamento que especificou (com espaços hierárquicos desativados) quando fez a encomenda do dispositivo. Verifique nos ficheiros BOM que todos os seus dados são enviados para o Azure. Agora pode mover estes dados para uma conta de armazenamento gen2 de armazenamento de data Lake.

## <a name="move-the-data-into-azure-data-lake-storage-gen2"></a>Mova os dados para Azure Data Lake Storage Gen2

Já tem os dados na sua conta de Armazenamento Azure. Agora irá copiar os dados na sua conta de armazenamento do Lago de Dados Azure e aplicar permissões de acesso a ficheiros e diretórios.

> [!NOTE]
> Este passo é necessário se estiver a utilizar o Azure Data Lake Storage Gen2 como loja de dados. Se estiver a utilizar apenas uma conta de armazenamento blob sem espaço de nome hierárquico como o seu armazenamento de dados, pode saltar esta secção.

### <a name="copy-data-to-the-azure-data-lake-storage-gen-2-account"></a>Copiar dados para a conta Azure Data Lake Storage Gen 2

Pode copiar dados utilizando a Azure Data Factory ou utilizando o seu cluster Hadoop baseado em Azure.

* Para utilizar a Azure Data Factory, consulte a [Azure Data Factory para mover dados para a ADLS Gen2](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2). Certifique-se de especificar o **Armazenamento de Blob Azure** como fonte.

* Para utilizar o seu cluster Hadoop baseado em Azure, execute este comando DistCp:

    ```bash
    hadoop distcp -Dfs.azure.account.key.<source_account>.dfs.windows.net=<source_account_key> abfs://<source_container> @<source_account>.dfs.windows.net/<source_path> abfs://<dest_container>@<dest_account>.dfs.windows.net/<dest_path>
    ```

    * Substitua os espaços reservados `<source_account>` e `<dest_account>` pelos nomes das contas de armazenamento de origem e destino.

    * Substitua os `<source_container>` e `<dest_container>` espaços reservados pelos nomes dos contentores de origem e destino.

    * Substitua os `<source_path>` e `<dest_path>` espaços reservados pelas vias de direção de origem e destino.

    * Substitua o espaço reservado `<source_account_key>` pela chave de acesso da conta de armazenamento que contém os dados.

    Este comando copia dados e metadados da sua conta de armazenamento na sua conta de armazenamento Gen2 data Lake.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Crie um diretor de serviço para a sua conta Azure Data Lake Storage Gen2

Para criar um diretor de serviço, consulte [Como: Utilize o portal para criar uma aplicação e um diretor de serviço seletiva seletiva que possa aceder a recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

* Ao executar os passos na [Atribuição da aplicação a uma](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) secção de papel do artigo, certifique-se de atribuir a função de Contribuinte de Dados **blob** de armazenamento ao diretor de serviço.

* Ao executar os passos nos [valores Get para assinar na](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) secção do artigo, guarde o ID da aplicação e os valores secretos do cliente num ficheiro de texto. Você precisará delas em breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Gerar uma lista de ficheiros copiados com as suas permissões

A partir do aglomerado de Hadoop no local, executar este comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Este comando gera uma lista de ficheiros copiados com as suas permissões.

> [!NOTE]
> Dependendo do número de ficheiros no HDFS, este comando pode demorar muito tempo a ser executado.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Gere uma lista de identidades e mapeie-as para identidades do Azure Ative Directory (ADD)

1. Descarregue o guião `copy-acls.py`. Consulte os scripts de [ajuda de download e instale o seu nó de borda para executar a](#download-helper-scripts) secção deste artigo.

2. Executar este comando para gerar uma lista de identidades únicas.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Este script gera um ficheiro chamado `id_map.json` que contém as identidades que precisa de mapear para identidades baseadas em ADD.

3. Abra o ficheiro `id_map.json` num editor de texto.

4. Para cada objeto JSON que apareça no ficheiro, atualize o atributo `target` de um Nome Principal de Utilizador AAD (UPN) ou ObjectId (OID), com a identidade mapeada apropriada. Depois de terminar, guarde o ficheiro. Vai precisar deste ficheiro no próximo passo.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Aplicar permissões a ficheiros copiados e aplicar mapeamentos de identidade

Execute este comando para aplicar permissões aos dados que copiou na conta Data Lake Storage Gen2:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Substitua o espaço reservado `<storage-account-name>` com o nome da sua conta de armazenamento.

* Substitua o espaço reservado `<container-name>` com o nome do seu recipiente.

* Substitua os `<application-id>` e `<client-secret>` espaços reservados pelo ID da aplicação e pelo segredo do cliente que recolheu quando criou o diretor de serviço.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Apêndice: Divida dados em vários dispositivos da Data Box

Antes de transferir os seus dados para um dispositivo Data Box, terá de descarregar alguns scripts de ajuda, certificar-se de que os seus dados estão organizados para se encaixarem num dispositivo Data Box e excluir quaisquer ficheiros desnecessários.

<a id="download-helper-scripts" />

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Descarregue scripts de ajudante e crie o seu nó de borda para executá-los

1. A partir da sua borda ou nó de cabeça do seu aglomerado hadoop no local, executar este comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Este comando clona o repositório GitHub que contém os scripts de ajudante.

2. Certifique-se de que tem a embalagem [jq](https://stedolan.github.io/jq/) instalada no seu computador local.

   ```bash
   
   sudo apt-get install jq
   ```

3. Instale o pacote [Requests](https://2.python-requests.org/en/master/) python.

   ```bash
   
   pip install requests
   ```

4. Detete permissões de execução nos scripts necessários.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Certifique-se de que os seus dados estão organizados para se encaixarem num dispositivo Data Box

Se o tamanho dos seus dados exceder o tamanho de um único dispositivo Data Box, pode dividir ficheiros em grupos que pode armazenar em vários dispositivos data Box.

Se os seus dados não excederem o tamanho de um dispositivo caixa de dados de singe, pode passar para a secção seguinte.

1. Com permissões elevadas, execute o roteiro `generate-file-list` que descarregou seguindo a orientação na secção anterior.

   Aqui está uma descrição dos parâmetros de comando:

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

2. Copie as listas de ficheiros geradas para HDFS de modo a que estejam acessíveis ao trabalho [da DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Excluir ficheiros desnecessários

Terá de excluir alguns diretórios do trabalho da DisCp. Por exemplo, excluir diretórios que contenham informações estatais que mantenham o cluster em funcionamento.

No aglomerado de Hadoop no local onde planeia iniciar o trabalho da DistCp, crie um ficheiro que especifique a lista de diretórios que pretende excluir.

Segue-se um exemplo:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Passos seguintes

Saiba como o Data Lake Storage Gen2 funciona com clusters HDInsight. Consulte a utilização do Armazenamento de [Lagos Azure Data Gen2 com clusters Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).
