---
title: Restringir o acesso com assinaturas de acesso partilhado - Azure HDInsight
description: Saiba como utilizar assinaturas de acesso partilhado para restringir o acesso do HDInsight para dados armazenados em blobs de armazenamento do Azure.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 1e55552e238e16f2221b138b6e12afa5635d2ab2
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202678"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Utilizar assinaturas de acesso partilhado do Azure Storage para restringir o acesso aos dados no HDInsight

HDInsight tem acesso total aos dados nas contas de armazenamento do Azure associadas ao cluster. Pode utilizar assinaturas de acesso partilhado no contentor de BLOBs para restringir o acesso aos dados. Assinaturas de acesso partilhado (SAS) são uma funcionalidade de contas de armazenamento do Azure que permite-lhe limitar o acesso a dados. Por exemplo, fornecendo acesso só de leitura aos dados.

> [!IMPORTANT]  
> Para uma solução com o Apache Ranger, considere utilizar o HDInsight associado a um domínio. Para obter mais informações, consulte a [configurar o HDInsight associado a um domínio](./domain-joined/apache-domain-joined-configure.md) documento.

> [!WARNING]  
> HDInsight tem de ter acesso total para o armazenamento predefinido para o cluster.

## <a name="requirements"></a>Requisitos

* Uma subscrição do Azure
* C# ou Python. Código de exemplo do c# é fornecido como uma solução do Visual Studio.

  * Visual Studio tem de ser a versão 2013, 2015 ou 2017
  * Python tem de ser a versão 2.7 ou superior

* Um cluster do HDInsight baseado em Linux ou [do Azure PowerShell] [ powershell] -se tiver um cluster baseado em Linux, pode utilizar o Apache Ambari para adicionar uma assinatura de acesso partilhado para o cluster. Caso contrário, pode utilizar o Azure PowerShell para criar um cluster e adicione uma assinatura de acesso partilhado durante a criação do cluster.

    > [!IMPORTANT]  
    > O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

* O exemplo ficheiros a partir [ https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature ](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Este repositório contém os seguintes itens:

  * Um projeto do Visual Studio que pode criar um contentor de armazenamento, a política armazenadas e a SAS para utilização com o HDInsight
  * Um script de Python que pode criar um contentor de armazenamento, a política armazenadas e a SAS para utilização com o HDInsight
  * Um script do PowerShell que pode criar um cluster do HDInsight e configurá-lo para utilizar a SAS.

## <a name="shared-access-signatures"></a>Assinaturas de Acesso Partilhado

Existem duas formas de assinaturas de acesso partilhado:

* Ad hoc: A hora de início, hora de expiração e as permissões para a SAS são todos especificadas no URI de SAS.

* Política de acesso armazenado: É definida uma política de acesso armazenadas num contêiner de recursos, como um contentor de Blobs. Uma política pode ser utilizada para gerir restrições para um ou mais assinaturas de acesso partilhado. Quando associa um SAS com uma política de acesso armazenado, a SAS herda as restrições - a hora de início, hora de expiração e permissões - definidas para a política de acesso armazenado.

A diferença entre as duas formas é importante para um cenário de chave: revogação. Uma SAS é um URL, para que qualquer pessoa que obtém a SAS pode utilizá-lo, independentemente do que o pedido para começar. Se uma SAS é publicada publicamente, ele pode ser usado por qualquer pessoa no mundo. Uma SAS que é distribuída é válida até que uma das quatro coisas acontece:

1. For atingida a hora de expiração especificada no SAS.

2. A hora de expiração especificada na política de acesso armazenado referenciada pela SAS foi atingida. Os seguintes cenários de fazer com que a hora de expiração a ser atingido:

    * O intervalo de tempo decorrido.
    * A política de acesso armazenado é modificada para ter um tempo de expiração no passado. Alterar a hora de expiração é uma forma de revogar a SAS.

3. A política de acesso armazenado referenciada pela SAS é eliminada, que é outra forma de revogar a SAS. Caso recrie a política de acesso armazenado com o mesmo nome, todos os tokens SAS para a política anterior são válidos (se não tiver passado o tempo de expiração no SAS). Se pretende revogar o SAS, certifique-se de que utilize um nome diferente, caso recrie a política de acesso com um tempo de expiração no futuro.

4. A chave da conta que utilizou para criar a SAS é regenerada. Regenerar a chave faz com que todas as aplicações que utilizam a chave de anterior para falha na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]  
> Um URI de assinatura de acesso partilhado é associado a chave da conta utilizada para criar a assinatura e o associados armazenados política de acesso (se houver). Não se for especificada nenhuma política de acesso armazenado, é a única forma de revogar uma assinatura de acesso partilhado alterar a chave de conta.

Recomendamos que sempre usar políticas de acesso armazenadas. Quando utilizar políticas de armazenado, pode revogar assinaturas ou expandir a data de expiração, conforme necessário. Os passos neste documento utiliza armazenados políticas de acesso para gerar a SAS.

Para obter mais informações sobre assinaturas de acesso partilhado, consulte [compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Criar uma política armazenadas e SAS com C\#

1. Abra a solução no Visual Studio.

2. No Explorador de soluções, faça duplo clique sobre o **SASToken** do projeto e selecione **propriedades**.

3. Selecione **definições** e adicione valores para as seguintes entradas:

   * StorageConnectionString: A cadeia de ligação para a conta de armazenamento que pretende criar uma política armazenadas e a SAS para. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` em que `myaccount` é o nome da conta de armazenamento e `mykey` é a chave para a conta de armazenamento.

   * ContainerName: O contentor na conta de armazenamento que pretende restringir o acesso ao.

   * SASPolicyName: O nome a utilizar para a política armazenada para criar.

   * FileToUpload: O caminho para um ficheiro que é carregado para o contentor.

4. Execute o projeto. Assim que a SAS foi gerada, são apresentadas informações semelhantes ao seguinte texto:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Guarde o token de política SAS, o nome da conta de armazenamento e o nome do contentor. Estes valores são utilizados ao associar a conta de armazenamento com o seu cluster do HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Criar uma política armazenadas e SAS com o Python

1. Abra o ficheiro de SASToken.py e alterar os valores seguintes:

   * política\_nome: O nome a utilizar para a política armazenada para criar.

   * storage\_account\_name: O nome da conta de armazenamento.

   * armazenamento\_conta\_chave: A chave para a conta de armazenamento.

   * storage\_container\_name: O contentor na conta de armazenamento que pretende restringir o acesso ao.

   * exemplo\_ficheiro\_caminho: O caminho para um ficheiro que é carregado para o contentor.

2. Execute o script. Apresenta o token SAS semelhante ao seguinte texto quando o script tiver concluído:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Guarde o token de política SAS, o nome da conta de armazenamento e o nome do contentor. Estes valores são utilizados ao associar a conta de armazenamento com o seu cluster do HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Utilizar a SAS com o HDInsight

Ao criar um cluster do HDInsight, tem de especificar uma conta de armazenamento primário e, opcionalmente, pode especificar contas de armazenamento adicionais. Ambos os métodos de adicionar armazenamento requerem acesso total para as contas de armazenamento e os contentores que são utilizados.

Para utilizar uma assinatura de acesso partilhado para limitar o acesso a um contentor, adicione uma entrada personalizada para o **core-site** configuração do cluster.

* Para **baseado em Windows** ou **baseado em Linux** clusters do HDInsight, pode adicionar a entrada durante a criação do cluster com o PowerShell.
* Para **baseado em Linux** clusters do HDInsight, alterar a configuração após a criação de cluster com o Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que utiliza a SAS

Um exemplo de como criar um cluster do HDInsight que utiliza a SAS está incluído no `CreateCluster` diretório do repositório. Para usá-lo, utilize os seguintes passos:

1. Abra o `CreateCluster\HDInsightSAS.ps1` ficheiro num editor de texto e modificar os seguintes valores no início do documento.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Por exemplo, alterar `'mycluster'` para o nome do cluster que pretende criar. Os valores SAS devem corresponder os valores dos passos anteriores, ao criar uma conta de armazenamento e o SAS token.

    Assim que tiver alterado os valores, guarde o ficheiro.

2. Abra uma nova linha de comandos do Azure PowerShell. Se não estiver familiarizado com o Azure PowerShell, ou não o tiver instalado, veja [instalar e configurar o Azure PowerShell][powershell].

1. A partir de linha de comandos, utilize o seguinte comando para autenticar a sua subscrição do Azure:

    ```powershell
    Connect-AzureRmAccount
    ```

    Quando lhe for pedido, inicie sessão com a conta para a sua subscrição do Azure.

    Se a sua conta estiver associada a várias subscrições do Azure, poderá ter de utilizar `Select-AzureRmSubscription` para selecionar a subscrição que pretende utilizar.

4. Na linha de comandos, altere os diretórios para o `CreateCluster` diretório que contém o ficheiro de HDInsightSAS.ps1. Em seguida, utilize o seguinte comando para executar o script

    ```powershell
    .\HDInsightSAS.ps1
    ```

    Como o script é executado, ele a saída dos registos para a linha de comandos do PowerShell à medida que cria o recurso de contas de armazenamento e de grupo. São-lhe pedido que introduza o utilizador HTTP para o cluster do HDInsight. Esta conta é utilizada para proteger o acesso HTTP/s para o cluster.

    Se estiver a criar um cluster baseado em Linux, lhe for pedido um nome de conta de utilizador SSH e a palavra-passe. Esta conta é utilizada para iniciar sessão remotamente no cluster.

   > [!IMPORTANT]  
   > Quando lhe for pedido para o HTTP/s ou o nome de utilizador SSH e a palavra-passe, tem de fornecer uma palavra-passe que cumpra os seguintes critérios:
   >
   > * Tem de ser no mínimo 10 carateres de comprimento.
   > * Tem de conter, pelo menos, um dígito.
   > * Tem de conter pelo menos um caráter não alfanumérico.
   > * Tem de conter pelo menos um canto superior ou uma letra minúscula.

Demora algum tempo para que este script concluir, normalmente, cerca de 15 minutos. Quando o script for concluído sem erros, o cluster ter sido criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Utilizar a SAS com um cluster existente

Se tiver um cluster baseado em Linux, pode adicionar a SAS para o **core-site** configuração ao utilizar os seguintes passos:

1. Abra a IU web do Ambari para o seu cluster. O endereço para esta página é https://YOURCLUSTERNAME.azurehdinsight.net. Quando lhe for pedido, autenticar-se ao cluster com o nome de administrador (administrador) e a palavra-passe que utilizou quando criou o cluster.

2. Do lado esquerdo da Ambari web UI, selecione **HDFS** e, em seguida, selecione a **configurações** separador no meio da página.

3. Selecione o **avançadas** separador e, em seguida, desloque-se até encontrar o **personalizado core-site** secção.

4. Expanda a **core-site personalizado** secção, em seguida, desloque-se para o final e selecione o **Adicionar propriedade...**  ligação. Utilize os seguintes valores para o **chave** e **valor** campos:

   * **Key**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Valor**: A SAS devolvido pelo C# ou a aplicação de Python que executou anteriormente

     Substitua **CONTAINERNAME** com o nome do contentor utilizados com o aplicativo em C# ou SAS. Substitua **STORAGEACCOUNTNAME** com o nome de conta de armazenamento que utilizou.

5. Clique nas **Add** botão para guardar esta chave e valor, em seguida, clique nas **guardar** botão para guardar as alterações de configuração. Quando lhe for pedido, adicione uma descrição da alteração ("adicionar acesso do armazenamento SAS" por exemplo) e, em seguida, clique em **guardar**.

    Clique em **OK** quando as alterações foram concluídas.

   > [!IMPORTANT]  
   > Tem de reiniciar vários serviços para a alteração tem efeito.

6. Na IU web do Ambari, selecione **HDFS** da lista à esquerda e, em seguida, selecione **reiniciar todos os afetados** do **ações de serviço** lista pendente lista à direita. Quando lhe for pedido, selecione __confirmar reiniciar todos os__.

    Repita este processo para MapReduce2 e YARN.

7. Depois dos serviços de ser reiniciado, selecione cada um deles e desativar o modo de manutenção do **ações de serviço** menu pendente.

## <a name="test-restricted-access"></a>Testar o acesso restrito

Para verificar que tenham acesso restrito, utilize SSH para ligar ao cluster. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Assim que estiver ligado ao cluster, utilize os seguintes passos para verificar que o utilizador pode apenas leitura e a lista de itens na conta de armazenamento SAS:

1. Para listar o conteúdo do contentor, utilize o seguinte comando da linha de comandos: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua **SASCONTAINER** com o nome do contentor criado para a conta de armazenamento SAS. Substitua **SASACCOUNTNAME** com o nome da conta de armazenamento utilizado para a SAS.

    A lista inclui o ficheiro carregado quando o contentor e a SAS foram criada.

2. Utilize o seguinte comando para verificar o que pode ler o conteúdo do ficheiro. Substitua a **SASCONTAINER** e **SASACCOUNTNAME** como no passo anterior. Substitua **FILENAME** com o nome do ficheiro apresentado no comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Este comando lista o conteúdo do ficheiro.

3. Utilize o seguinte comando para transferir o ficheiro para o sistema de arquivos local:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Este comando transfere o ficheiro para um ficheiro local com o nome **testfile.txt**.

4. Utilize o seguinte comando para carregar o ficheiro local para um novo ficheiro designado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Receber uma mensagem semelhante ao seguinte texto:

        put: java.io.IOException

    Este erro ocorre porque a localização de armazenamento é leitura + lista apenas. Utilize o seguinte comando para colocar os dados no armazenamento padrão para o cluster, o que é gravável:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Desta vez, a operação deverá ser concluída com êxito.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="a-task-was-canceled"></a>Uma tarefa foi cancelada

**Os sintomas**: Ao criar um cluster com o script do PowerShell, poderá receber a seguinte mensagem de erro:

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Motivo**: Este erro pode ocorrer se usar uma palavra-passe para o utilizador de administrador/HTTP para o cluster, ou (para clusters baseados em Linux) o utilizador SSH.

**Resolução**: Utilize uma palavra-passe que cumpra os seguintes critérios:

* Tem de ser no mínimo 10 carateres de comprimento.
* Tem de conter, pelo menos, um dígito.
* Tem de conter pelo menos um caráter não alfanumérico.
* Tem de conter pelo menos um canto superior ou uma letra minúscula.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como adicionar armazenamento de acesso limitado ao seu cluster do HDInsight, saiba outras formas de trabalhar com dados no seu cluster:

* [Utilizar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Utilizar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Utilizar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
