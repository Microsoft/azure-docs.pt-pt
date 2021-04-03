---
title: Migrar da loja ON-Prem HDFS para o Azure Storage com Azure Data Box
description: Migrar os dados de uma loja HDFS no local para o Azure Storage (armazenamento de bolhas ou Data Lake Storage Gen2) utilizando um dispositivo Data Box.
author: normesta
ms.service: storage
ms.date: 02/14/2019
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: e58137dd680ff9a2be2bd657f0969304b526873f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95913118"
---
# <a name="migrate-from-on-prem-hdfs-store-to-azure-storage-with-azure-data-box"></a>Migrar da loja ON-Prem HDFS para o Azure Storage com Azure Data Box

Pode migrar dados de uma loja HDFS no local do seu cluster Hadoop para o Azure Storage (armazenamento de bolhas ou Data Lake Storage Gen2) utilizando um dispositivo Data Box. Pode escolher entre o Disco caixa de dados, uma Caixa de Dados de 80 TB ou uma caixa de dados de 770-TB Pesada.

Este artigo ajuda-o a completar estas tarefas:

> [!div class="checklist"]
> * Preparem-se para migrar os vossos dados.
> * Copie os seus dados para um disco de caixa de dados, caixa de dados ou um dispositivo pesado caixa de dados.
> * Envie o dispositivo de volta para a Microsoft.
> * Aplicar permissões de acesso a ficheiros e diretórios (apenas data lake storage gen2)

## <a name="prerequisites"></a>Pré-requisitos

Precisas destas coisas para completar a migração.

* Uma conta do Armazenamento do Azure.

* Um cluster Hadoop no local que contém os seus dados de origem.

* Um [dispositivo Azure Data Box](https://azure.microsoft.com/services/storage/databox/).

  * [Encomende a sua Caixa de Dados](../../databox/data-box-deploy-ordered.md) ou [Caixa de Dados Pesada](../../databox/data-box-heavy-deploy-ordered.md). 

  * Por cabo e ligue a sua [Caixa de Dados](../../databox/data-box-deploy-set-up.md) ou Caixa de [Dados Pesada](../../databox/data-box-heavy-deploy-set-up.md) a uma rede no local.

Se está pronto, vamos começar.

## <a name="copy-your-data-to-a-data-box-device"></a>Copie os seus dados para um dispositivo Data Box

Se os seus dados se encaixarem num único dispositivo Data Box, copiará os dados para o dispositivo Data Box. 

Se o seu tamanho de dados exceder a capacidade do dispositivo Data Box, use o [procedimento opcional para dividir os dados em vários dispositivos data box](#appendix-split-data-across-multiple-data-box-devices) e, em seguida, executar este passo. 

Para copiar os dados da sua loja HDFS no local para um dispositivo Data Box, irá configurar algumas coisas e, em seguida, utilizar a ferramenta [DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

Siga estes passos para copiar dados através das APIs REST do armazenamento blob/objeto para o seu dispositivo Data Box. A interface REST API fará com que o dispositivo apareça como uma loja HDFS para o seu cluster.

1. Antes de copiar os dados através do REST, identifique os primitivos de segurança e ligação para ligar à interface REST na Caixa de Dados ou Caixa de Dados Pesada. Inscreva-se na UI web local da Data Box e vá para **a página De Ligar e copiar.** Contra a conta de armazenamento Azure para o seu dispositivo, nas **definições de Acesso,** localizar e selecionar **REST**.

    ![Página "Ligar e copiar"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connect-rest.png)

2. Na conta de armazenamento Access e no diálogo de dados de upload, copie o ponto final do **serviço Blob** e a chave da **conta de Armazenamento**. Do ponto final do serviço blob, omita `https://` o e o corte de fuga.

    Neste caso, o ponto final é: `https://mystorageaccount.blob.mydataboxno.microsoftdatabox.com/` . A parte hospedeira do URI que vais usar é: `mystorageaccount.blob.mydataboxno.microsoftdatabox.com` . Por exemplo, consulte como [ligar para ficar em repouso em http](../../databox/data-box-deploy-copy-data-via-rest.md). 

     ![Diálogo de "conta de armazenamento de acesso e upload de dados"](media/data-lake-storage-migrate-on-premises-HDFS-cluster/data-box-connection-string-http.png)

3. Adicione o ponto final e a Caixa de Dados ou caixa de dados Endereço IP pesado `/etc/hosts` em cada nó.

    ```    
    10.128.5.42  mystorageaccount.blob.mydataboxno.microsoftdatabox.com
    ```

    Se estiver a utilizar algum outro mecanismo para DNS, deve certificar-se de que o ponto final da Caixa de Dados pode ser resolvido.

4. Desa estaca a variável da concha `azjars` para a localização dos `hadoop-azure` ficheiros e do `azure-storage` frasco. Pode encontrar estes ficheiros no diretório de instalações do Hadoop.

    Para determinar se estes ficheiros existem, utilize o seguinte comando: `ls -l $<hadoop_install_dir>/share/hadoop/tools/lib/ | grep azure` . Substitua o `<hadoop_install_dir>` espaço reservado pelo caminho para o diretório onde instalou o Hadoop. Certifique-se de que utiliza caminhos totalmente qualificados.

    Exemplos:

    `azjars=$hadoop_install_dir/share/hadoop/tools/lib/hadoop-azure-2.6.0-cdh5.14.0.jar` `azjars=$azjars,$hadoop_install_dir/share/hadoop/tools/lib/microsoft-windowsazure-storage-sdk-0.6.0.jar`

5. Crie o recipiente de armazenamento que pretende utilizar para a cópia de dados. Você também deve especificar um diretório de destino como parte deste comando. Este pode ser um diretório de destino falso neste momento.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -mkdir -p  wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` espaço reservado pelo nome do seu ponto de terminação de serviço blob.

    * Substitua o `<account_key>` espaço reservado pela chave de acesso da sua conta.

    * Substitua o `<container-name>` espaço reservado pelo nome do seu recipiente.

    * Substitua o `<destination_directory>` espaço reservado pelo nome do diretório para o quais pretende copiar os seus dados.

6. Executar um comando de lista para garantir que o seu contentor e diretório foram criados.

    ```
    hadoop fs -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint>=<account_key> \
    -ls -R  wasb://<container_name>@<blob_service_endpoint>/
    ```

   * Substitua o `<blob_service_endpoint>` espaço reservado pelo nome do seu ponto de terminação de serviço blob.

   * Substitua o `<account_key>` espaço reservado pela chave de acesso da sua conta.

   * Substitua o `<container-name>` espaço reservado pelo nome do seu recipiente.

7. Copie os dados do armazenamento Hadoop HDFS para o depósito da Caixa de Dados blob, no recipiente que criou anteriormente. Se o diretório que está a copiar não for encontrado, o comando cria-o automaticamente.

    ```
    hadoop distcp \
    -libjars $azjars \
    -D fs.AbstractFileSystem.wasb.Impl=org.apache.hadoop.fs.azure.Wasb \
    -D fs.azure.account.key.<blob_service_endpoint<>=<account_key> \
    -filters <exclusion_filelist_file> \
    [-f filelist_file | /<source_directory> \
           wasb://<container_name>@<blob_service_endpoint>/<destination_directory>
    ```

    * Substitua o `<blob_service_endpoint>` espaço reservado pelo nome do seu ponto de terminação de serviço blob.

    * Substitua o `<account_key>` espaço reservado pela chave de acesso da sua conta.

    * Substitua o `<container-name>` espaço reservado pelo nome do seu recipiente.

    * Substitua o `<exlusion_filelist_file>` espaço reservado pelo nome do ficheiro que contém a sua lista de exclusões de ficheiros.

    * Substitua o `<source_directory>` espaço reservado pelo nome do diretório que contém os dados que pretende copiar.

    * Substitua o `<destination_directory>` espaço reservado pelo nome do diretório para o quais pretende copiar os seus dados.

    A `-libjars` opção é usada para disponibilizar os `hadoop-azure*.jar` ficheiros e os `azure-storage*.jar` ficheiros dependentes para `distcp` . Isto pode já ocorrer para alguns aglomerados.

    O exemplo a seguir mostra como o `distcp` comando é usado para copiar dados.

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

    * Tente alterar o número de mappers. (O exemplo acima usa `m` = 4 mappers.)

    * Tente correr vários `distcp` em paralelo.

    * Lembre-se que os ficheiros grandes funcionam melhor do que os ficheiros pequenos.

## <a name="ship-the-data-box-to-microsoft"></a>Envie a Caixa de Dados para a Microsoft

Siga estes passos para preparar e enviar o dispositivo Data Box para a Microsoft.

1. Primeiro, [Prepare-se para enviar na sua Caixa de Dados ou Caixa de Dados Pesada.](../../databox/data-box-deploy-copy-data-via-rest.md)

2. Depois de concluída a preparação do dispositivo, descarregue os ficheiros BOM. Utilizará estes ficheiros BOM ou manifesto mais tarde para verificar os dados enviados para o Azure.

3. Desligue o aparelho e retire os cabos.

4. Agende uma recolha com a UPS.

    * Para dispositivos data box, consulte [enviar a sua Caixa de Dados.](../../databox/data-box-deploy-picked-up.md)

    * Para dispositivos pesados data box, consulte [enviar a sua caixa de dados pesada.](../../databox/data-box-heavy-deploy-picked-up.md)

5. Depois de a Microsoft receber o seu dispositivo, este está ligado à rede de data centers e os dados são enviados para a conta de armazenamento que especificou quando fez a encomenda do dispositivo. Verifique nos ficheiros BOM que todos os seus dados são enviados para o Azure. 

## <a name="apply-access-permissions-to-files-and-directories-data-lake-storage-gen2-only"></a>Aplicar permissões de acesso a ficheiros e diretórios (apenas data lake storage gen2)

Já tem os dados na sua conta de Armazenamento Azure. Agora aplicará permissões de acesso a ficheiros e diretórios.

> [!NOTE]
> Este passo só é necessário se estiver a utilizar o Azure Data Lake Storage Gen2 como sua loja de dados. Se estiver a utilizar apenas uma conta de armazenamento de bolhas sem espaço hierárquico como loja de dados, pode saltar esta secção.

### <a name="create-a-service-principal-for-your-azure-data-lake-storage-gen2-account"></a>Crie um principal de serviço para a sua conta Azure Data Lake Storage Gen2

Para criar um principal de serviço, consulte [Como: Usar o portal para criar uma aplicação AD AZure e um diretor de serviço que possa aceder aos recursos.](../../active-directory/develop/howto-create-service-principal-portal.md)

* Ao executar os passos na [Atribuição da aplicação a uma](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) secção de função do artigo, certifique-se de atribuir a função de Contribuinte de **Dados de Armazenamento** ao titular do serviço.

* Ao executar os passos nos [valores Get para a assinatura na](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) secção do artigo, guarde o ID da aplicação e os valores secretos do cliente num ficheiro de texto. Vai precisar disso em breve.

### <a name="generate-a-list-of-copied-files-with-their-permissions"></a>Gerar uma lista de ficheiros copiados com as suas permissões

A partir do aglomerado hadoop no local, executar este comando:

```bash

sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

Este comando gera uma lista de ficheiros copiados com as suas permissões.

> [!NOTE]
> Dependendo do número de ficheiros no HDFS, este comando pode demorar muito tempo a ser executado.

### <a name="generate-a-list-of-identities-and-map-them-to-azure-active-directory-add-identities"></a>Gere uma lista de identidades e mapeá-las para identidades do Azure Ative Directory (ADD)

1. Descarregue o `copy-acls.py` guião. Consulte os [scripts do helper download e erfirou o nó de borda para executá-los](#download-helper-scripts) secção deste artigo.

2. Executar este comando para gerar uma lista de identidades únicas.

   ```bash
   
   ./copy-acls.py -s ./filelist.json -i ./id_map.json -g
   ```

   Este script gera um ficheiro nomeado `id_map.json` que contém as identidades que precisa de mapear para identidades baseadas em ADD.

3. Abra o `id_map.json` ficheiro num editor de texto.

4. Para cada objeto JSON que aparece no ficheiro, atualize o `target` atributo de um Nome Principal do Utilizador AAD (UPN) ou ObjectId (OID), com a identidade mapeada apropriada. Depois de terminar, guarde o ficheiro. Vai precisar deste ficheiro no próximo passo.

### <a name="apply-permissions-to-copied-files-and-apply-identity-mappings"></a>Aplicar permissões em ficheiros copiados e aplicar mapeamentos de identidade

Executar este comando para aplicar permissões aos dados que copiou na conta Gen2 de armazenamento de data lake:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A <storage-account-name> -C <container-name> --dest-spn-id <application-id>  --dest-spn-secret <client-secret>
```

* Substitua o `<storage-account-name>` espaço reservado pelo nome da sua conta de armazenamento.

* Substitua o `<container-name>` espaço reservado pelo nome do seu recipiente.

* Substitua os `<application-id>` espaços reservados e `<client-secret>` os espaços reservados pelo ID da aplicação e o segredo do cliente que recolheu quando criou o principal do serviço.

## <a name="appendix-split-data-across-multiple-data-box-devices"></a>Apêndice: Dados divididos em vários dispositivos da Caixa de Dados

Antes de transferir os seus dados para um dispositivo Data Box, terá de descarregar alguns scripts de ajuda, garantir que os seus dados estão organizados para se encaixar num dispositivo Data Box e excluir quaisquer ficheiros desnecessários.

<a id="download-helper-scripts"></a>

### <a name="download-helper-scripts-and-set-up-your-edge-node-to-run-them"></a>Descarregue scripts de ajudante e crie o seu nó de borda para executá-los

1. A partir da sua borda ou nó de cabeça do seu cluster Hadoop no local, executar este comando:

   ```bash
   
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   Este comando clona o repositório GitHub que contém os scripts de ajuda.

2. Certifique-se de que tem o pacote [jq](https://stedolan.github.io/jq/) instalado no seu computador local.

   ```bash
   
   sudo apt-get install jq
   ```

3. Instale o pacote [Requests](https://2.python-requests.org/en/master/) python.

   ```bash
   
   pip install requests
   ```

4. Desemote permissões de execução nos scripts necessários.

   ```bash
   
   chmod +x *.py *.sh

   ```

### <a name="ensure-that-your-data-is-organized-to-fit-onto-a-data-box-device"></a>Certifique-se de que os seus dados estão organizados para se encaixar num dispositivo Data Box

Se o tamanho dos seus dados exceder o tamanho de um único dispositivo Data Box, pode dividir ficheiros em grupos que pode armazenar em vários dispositivos Data Box.

Se os seus dados não excederem o tamanho de um dispositivo Singe Data Box, pode proceder à secção seguinte.

1. Com permissões elevadas, execute o `generate-file-list` script que descarregou seguindo as orientações na secção anterior.

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

2. Copie as listas de ficheiros geradas para o HDFS de modo a que estejam acessíveis ao trabalho [do DistCp.](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html)

   ```
   hadoop fs -copyFromLocal {filelist_pattern} /[hdfs directory]
   ```

### <a name="exclude-unnecessary-files"></a>Excluir ficheiros desnecessários

Terá de excluir alguns diretórios do trabalho da DisCp. Por exemplo, exclua diretórios que contenham informações estatais que mantenham o cluster em funcionamento.

No cluster Hadoop, onde planeia iniciar o trabalho do DistCp, crie um ficheiro que especifique a lista de diretórios que pretende excluir.

Eis um exemplo:

```
.*ranger/audit.*
.*/hbase/data/WALs.*
```

## <a name="next-steps"></a>Passos seguintes

Saiba como funciona o Data Lake Storage Gen2 com clusters HDInsight. Consulte [a Utilização Azure Data Lake Storage Gen2 com clusters Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md).