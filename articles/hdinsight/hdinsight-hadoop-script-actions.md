---
title: Desenvolvimento de ação de script com o HDInsight - Azure
description: Saiba como personalizar clusters do Hadoop com ação de Script. Ação de script pode ser utilizada para instalar software adicional em execução num cluster do Hadoop ou para alterar a configuração dos aplicativos instalados num cluster.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: d8f7808401b2e11a38b239a353e3b7af2ffcffb3
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361307"
---
# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Desenvolver scripts de ação de Script para clusters baseados em Windows HDInsight
Saiba como escrever scripts de ação de Script para o HDInsight. Para obter informações sobre como usar scripts de ação de Script, consulte [HDInsight personalizar clusters com ação de Script](hdinsight-hadoop-customize-cluster-linux.md). Para o mesmo artigo escrito para clusters do HDInsight baseado em Linux, consulte [scripts de desenvolver ações de Script para HDInsight](hdinsight-hadoop-script-actions-linux.md).


> [!IMPORTANT]  
> Os passos neste documento só funcionam em clusters do HDInsight baseado em Windows. HDInsight só está disponível no Windows para versões mais baixos do que o HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre como utilizar as ações de script com clusters baseados em Linux, consulte [ação de desenvolvimento com o HDInsight (Linux) de Script](hdinsight-hadoop-script-actions-linux.md).


Ação de script pode ser utilizada para instalar software adicional em execução num cluster do Apache Hadoop ou para alterar a configuração dos aplicativos instalados num cluster. Ações de script são scripts que são executados em nós do cluster quando os clusters do HDInsight são implementados e elas são executadas assim que nós do cluster conclua a configuração do HDInsight. Uma ação de script é executada com privilégios de conta de administrador de sistema e fornece direitos de acesso total para os nós do cluster. Cada cluster pode ser fornecido com uma lista de ações de script a ser executado na ordem em que são especificados.

> [!NOTE]  
> Se tiver a seguinte mensagem de erro:
>
> System.Management.Automation.CommandNotFoundException; ExceptionMessage: O termo 'Save-HDIFile' não é reconhecido como o nome de um cmdlet, a função, o ficheiro de script ou o programa operável. Verifique a ortografia do nome, ou se um caminho foi incluído, certifique-se de que o caminho está correto e tente novamente.
> 
> É porque não incluiu os métodos auxiliares.  Ver [métodos auxiliares para scripts personalizados](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sample-scripts"></a>Scripts de exemplo
Para criar clusters do HDInsight no sistema de operativo do Windows, a ação de Script é o script do Azure PowerShell. O script a seguir está um exemplo para configurar os ficheiros de configuração do site:

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

O script aceita quatro parâmetros, o nome de ficheiro de configuração, a propriedade que pretende modificar, o valor que pretende definir, e uma descrição. Por exemplo:

    hive-site.xml hive.metastore.client.socket.timeout 90

Estes parâmetros defina o valor de hive.metastore.client.socket.timeout para 90 no ficheiro de site do hive.  O valor predefinido é de 60 segundos.

Este script de exemplo também pode ser encontrado em [ https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1 ](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight fornece vários scripts para instalar componentes adicionais nos clusters do HDInsight:

| Name | Script |
| --- | --- |
| **Instalar o Spark** | `https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1`. Ver [instalar e utilizar clusters do Apache Spark no HDInsight][hdinsight-install-spark]. |
| **Instalar o R** | `https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1`. Ver [instalar e utilizar R nos clusters do HDInsight](r-server/r-server-hdinsight-manage.md#install-additional-r-packages-on-the-cluster). |
| **Instalar o Giraph** | `https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1`. Ver [instalar e utilizar o Apache Giraph no HDInsight clusters](hdinsight-hadoop-giraph-install.md). |
| **Pré-carregar bibliotecas do Hive** | `https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1`. Consulte [bibliotecas adicionar Apache Hive em clusters do HDInsight](hdinsight-hadoop-add-hive-libraries.md) |


Ação de script pode ser implementada no portal do Azure, Azure PowerShell ou com o SDK de .NET do HDInsight.  Para obter mais informações, consulte [clusters do HDInsight personalizar com ação de Script] [hdinsight-cluster-personalizar].

> [!NOTE]  
> Os scripts de exemplo funcionam apenas com clusters do HDInsight versão 3.1 ou acima. Para obter mais informações sobre versões de cluster do HDInsight, consulte [versões de cluster do HDInsight](hdinsight-component-versioning.md).
>
>

## <a name="helper-methods-for-custom-scripts"></a>Métodos auxiliares para scripts personalizados
Métodos de programa auxiliar de ação de script são utilitários que podem ser utilizados ao escrever scripts personalizados. Esses métodos são definidos no [ https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1 ](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)e pode ser incluído nos seus scripts usando o exemplo a seguir:

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Aqui estão os métodos de programa auxiliar que são fornecidos por este script:

| Método auxiliar | Descrição |
| --- | --- |
| **Save-HDIFile** |Transferir um ficheiro do especificado URI Uniform Resource Identifier () para uma localização no disco local que está associado com o nó de VM do Azure atribuído ao cluster. |
| **Expand-HDIZippedFile** |Deszipe ficheiros zipados. |
| **Invoke-HDICmdScript** |Execute um script de cmd.exe. |
| **Write-HDILog** |Escreva a saída do script personalizado usado para uma ação de script. |
| **Get-Services** |Obter uma lista de serviços em execução na máquina em que o script é executado. |
| **Get-Service** |Com o nome de serviço específico como entrada, obter informações detalhadas para um serviço específico (nome de serviço, processar o ID, estado, etc.) na máquina em que o script é executado. |
| **Get-HDIServices** |Obter uma lista de serviços do HDInsight em execução no computador em que o script é executado. |
| **Get-HDIService** |Com o nome de serviço HDInsight específico como entrada, obter informações detalhadas para um serviço específico (nome de serviço, processar o ID, estado, etc.) na máquina em que o script é executado. |
| **Get-ServicesRunning** |Obter uma lista de serviços em execução no computador em que o script é executado. |
| **Get-ServiceRunning** |Verifique se um serviço específico (por nome) está em execução no computador em que o script é executado. |
| **Get-HDIServicesRunning** |Obter uma lista de serviços do HDInsight em execução no computador em que o script é executado. |
| **Get-HDIServiceRunning** |Verifique se um serviço específico do HDInsight (por nome) está em execução no computador em que o script é executado. |
| **Get-HDIHadoopVersion** |Obtenha a versão do Hadoop instalado no computador em que o script é executado. |
| **Test-IsHDIHeadNode** |Verificar se o computador em que o script é executado é um nó principal. |
| **Test-IsActiveHDIHeadNode** |Verificar se o computador em que o script é executado é um nó principal do Active Directory. |
| **Test-IsHDIDataNode** |Verificar se o computador em que o script é executado é um nó de dados. |
| **Edit-HDIConfigFile** |Edite os ficheiros de configuração do hive-site, core-site, site hdfs, mapred-site ou yarn-site. |

## <a name="best-practices-for-script-development"></a>Melhores práticas para o desenvolvimento de script
Ao desenvolver um script personalizado para um cluster do HDInsight, existem várias melhores práticas a ter em mente:

* Verificar a versão do Hadoop.

    Apenas o HDInsight versão 3.1 (2.4 do Hadoop) e superior com a ação de Script para instalar componentes personalizados num cluster de suporte. No seu script personalizado, tem de utilizar o **Get-HDIHadoopVersion** método auxiliar para verificar a versão de Hadoop antes de continuar com a efetuar outras tarefas no script.
* Fornece estável links para recursos de script.

    Os utilizadores devem certificar-se de que todos os scripts e outros artefatos utilizados na personalização de um cluster permanecerem disponíveis durante o ciclo de vida do cluster e que as versões destes ficheiros não são alterados durante o período. Estes recursos são necessários se for necessária a recriação da imagem nós do cluster. A melhor prática é baixar e arquivar tudo numa conta de armazenamento que o utilizador controla. Esta conta pode ser a conta de armazenamento predefinida ou qualquer uma das contas de armazenamento adicionais especificadas ao momento da implantação para um cluster personalizado.
    O Spark e R personalizado exemplos de cluster fornecidos na documentação, por exemplo, há uma cópia local dos recursos nesta conta de armazenamento: https://hdiconfigactions.blob.core.windows.net/.
* Certifique-se de que o script de personalização do cluster é idempotente.

    Deve esperar que os nós de um cluster do HDInsight são recriar a imagem durante o tempo de vida do cluster. O script de personalização do cluster é executado sempre que é recriar a imagem de um cluster. Este script tem de ser projetado para ser personalizadas de idempotentes no sentido de que após a recriação da imagem, o script deve garantir que o cluster é retornado para o mesmo Estado que essa era apenas depois do script foi executado pela primeira vez quando o cluster foi criado inicialmente. Por exemplo, se um script personalizado instalado uma aplicação de cada D:\AppLocation na sua primeira execução, em seguida, em cada execução subsequente, após a recriação da imagem, o script deve verificar se o aplicativo existe na localização D:\AppLocation antes de continuar com as outras etapas das script.
* Instale componentes personalizados na localização do ideal.

    Quando são recriar a imagem de nós de cluster, a unidade de recurso C:\ e a unidade do sistema D:\ podem ser reformatados, resultando na perda de dados e aplicações que tinham sido instalados nessas unidades. Esta perda pode também ocorrer se um nó de máquina virtual do Azure (VM) que faz parte do cluster fica inativo e é substituído por um novo nó. Pode instalar os componentes na unidade D:\ ou na localização C:\apps no cluster. Todas as outras localizações na unidade C:\ estão reservadas. Especifique a localização onde ou bibliotecas de aplicativos devem ser instalados no script de personalização de cluster.
* Certifique-se de elevada disponibilidade da arquitetura do cluster.

    HDInsight tem uma arquitetura ativa-passiva para elevada disponibilidade, em que um nó principal está no modo de Active Directory (em que o HDInsight serviços estão em execução) e o outro nó principal está no modo de espera (em que HDInsight serviços não estão em execução). Os nós alternar os modos de ativos e passivos se os serviços de HDInsight são interrompidos. Se uma ação de script é utilizada para instalar os serviços em ambos os nós principais para elevada disponibilidade, tenha em atenção de que o mecanismo de ativação pós-falha do HDInsight não é capaz de failover automaticamente estes serviços instaladas por utilizadores. Então, instaladas por utilizadores serviços no HDInsight nós principais que devem ser de elevada disponibilidade tem de ter seu próprio mecanismo de ativação pós-falha em caso de modo ativo-passivo ou de estar no modo ativo-ativo.

    Um comando de ação de Script do HDInsight é executado em ambos os nós principais, quando a função de nó principal é especificada como um valor na *ClusterRoleCollection* parâmetro. Portanto, ao estruturar um script personalizado, certifique-se de que o script tem conhecimento desta configuração. Não deve depare com problemas em que os mesmos serviços são instalados e iniciados em ambos os nós principais e elas terminam competindo entre si. Além disso, tenha em atenção de que os dados são perdidos durante a recriação da imagem, para que o software instalado através de ação de Script tem de ser resiliente tais eventos. As aplicações devem ser concebidas para trabalhar com dados de elevada disponibilidade que são distribuídos por vários nós. Até 1/5 de nós num cluster pode ser recriados ao mesmo tempo.
* Configure os componentes personalizados para utilizar o armazenamento de Blobs do Azure.

    Os componentes personalizados que instalar em nós do cluster podem ter uma configuração predefinida para utilizar o armazenamento de Hadoop Distributed File System (HDFS). Deve alterar a configuração para utilizar o armazenamento de Blobs do Azure em vez disso. Numa recriação de imagem do cluster, o sistema de ficheiros HDFS obtém formatado e vai perder quaisquer dados armazenados nelas. Em vez disso, utilizar o armazenamento de Blobs do Azure garante que os seus dados são retidos.

## <a name="common-usage-patterns"></a>Padrões de utilização comuns
Esta seção fornece diretrizes sobre implementação de alguns dos padrões de utilização comuns que poderá encontrar ao escrever seu próprio script personalizado.

### <a name="configure-environment-variables"></a>Configurar as variáveis de ambiente
Muitas vezes, no desenvolvimento de ação de script, se sentir a necessidade de definir variáveis de ambiente. Por exemplo, um cenário mais provável é quando transferir um binário a partir de um site externo, instalá-la no cluster e adicione a localização de onde está instalado para a variável de ambiente de "Caminho". O fragmento seguinte mostra como definir as variáveis de ambiente no script personalizado.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Essa instrução define a variável de ambiente **MDS_RUNNER_CUSTOM_CLUSTER** para o valor 'true' e também define o escopo da variável a ser todo o computador. É importante que as variáveis de ambiente são definidas no âmbito do apropriado – computador ou usuário. Consultar [aqui] [ 1] para obter mais informações sobre como definir as variáveis de ambiente.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a locais onde são armazenados os scripts personalizados
Scripts utilizados para personalizar um cluster tem de ser a qualquer um na conta de armazenamento predefinida para o cluster ou num contentor só de leitura público em qualquer outra conta de armazenamento. Se o seu script acessa os recursos localizados em outro lugar os recursos tem de ser lido publicamente. Por exemplo, pode querer aceder a um ficheiro e guarde-o com o comando SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Neste exemplo, tem de garantir que o contentor `somecontainer` na conta de armazenamento `somestorageaccount` está acessível ao público. Caso contrário, o script lançará uma exceção de "Não encontrado" e falhar.

### <a name="pass-parameters-to-the-add-azhdinsightscriptaction-cmdlet"></a>Transmita os parâmetros para o cmdlet Add-AzHDInsightScriptAction
Para passar vários parâmetros para o cmdlet Add-AzHDInsightScriptAction, precisa de formatar o valor de cadeia de caracteres para conter todos os parâmetros para o script. Por exemplo:

    "-CertifcateUri wasb:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

ou

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Exceção para a implementação de cluster com falhas
Se quiser ser notificada com precisão o fato de que a personalização do cluster não foi possível ativar conforme esperado, é importante lançar uma exceção e realizar a ativação de criação do cluster. Por exemplo, talvez queira processar um ficheiro, se existir e administrar o caso de erro em que o ficheiro não existe. Isso garante que o script é encerrado corretamente e o estado do cluster corretamente é conhecido. O fragmento seguinte mostra um exemplo de como fazer isso:

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Neste fragmento, se o ficheiro não existia, ele poderia levar a um Estado em que o script, na verdade, é encerrado corretamente depois de imprimir a mensagem de erro e o cluster atinge estado de execução, partindo do princípio de que ele concluída "com êxito" o processo de personalização do cluster. Se quiser ser notificado com precisão o fato de que, essencialmente, a personalização de cluster não foi bem-sucedida conforme o esperado devido a um ficheiro em falta, é mais adequado lançar uma exceção e falhar o passo de personalização do cluster. Para conseguir isso tem de utilizar o seguinte trecho de código de exemplo em vez disso.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Lista de verificação para implementar uma ação de script
Aqui estão as etapas que seguimos ao se preparar para implantar esses scripts:

1. Coloque os ficheiros que contêm os scripts personalizados num local que seja acessível a nós do cluster durante a implementação. Isso pode ser qualquer um dos padrão ou contas de armazenamento adicionais especificadas no momento da implementação de cluster ou de qualquer outro contentor de armazenamento acessível publicamente.
2. Adicione verificações em scripts para garantir que elas sejam executadas idempotently, para que o script pode ser executado várias vezes no mesmo nó.
3. Utilize o `Write-Output` cmdlet do PowerShell do Azure para imprimir para STDOUT e STDERR. Não utilize `Write-Host`.
4. Utilizar uma pasta de ficheiro temporário, como `$env:TEMP`, para manter o ficheiro transferido usado pelos scripts e, em seguida, limpá-los após tem executado scripts.
5. Instale software personalizado apenas em D:\ ou C:\apps. Não devem ser utilizadas noutras localizações na unidade c:. como estão reservados. A instalar os ficheiros na unidade c:. fora da pasta C:\apps pode resultar em falhas de configuração durante a recria a imagem do nó.
6. No caso de definições de nível de sistema operacional ou ficheiros de configuração de serviço do Hadoop foram alterados, pode querer reinicie os serviços do HDInsight, para que eles possam começar quaisquer definições de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts de.

## <a name="debug-custom-scripts"></a>Depurar scripts personalizados
Os registos de erros de script são armazenados, juntamente com outra saída, na conta de armazenamento predefinida que especificou para o cluster em sua criação. Os registos são armazenados numa tabela com o nome *u < \cluster-name-fragment >< \time-stamp > setuplog*. Estes são registos agregados que possuem registros de todos os nós (nó principal e nós de trabalho) em que o script é executado no cluster.

É uma forma fácil de verificar os registos de utilizar ferramentas do HDInsight para Visual Studio. Para instalar as ferramentas, consulte [começar a utilizar as ferramentas Hadoop do Visual Studio para o HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-or-update-data-lake-tools-for-visual-studio)

**Para verificar o registo com o Visual Studio**

1. Abra o Visual Studio.
2. Clique em **View**e, em seguida, clique em **Explorador de servidores**.
3. Com o botão direito "do Azure", clique em ligar à **as assinaturas do Microsoft Azure**e, em seguida, introduza as suas credenciais.
4. Expanda **armazenamento**, expanda a conta de armazenamento do Azure utilizada como o sistema de ficheiros predefinido, expanda **tabelas**e, em seguida, faça duplo clique o nome da tabela.

Pode também remoto para os nós de cluster para ver o STDOUT e STDERR para scripts personalizados. Os registos em cada nó específicas apenas para esse nó e esteja conectados ao **C:\HDInsightLogs\DeploymentAgent.log**. Estes ficheiros de registo registam todos os resultados do script personalizado. Um trecho de código de registo de exemplo para uma ação de script do Spark tem esta aparência:

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Este registo, é claro que a ação de script do Spark foi executada na VM com o nome HEADNODE0 e que não existem exceções foram lançadas durante a execução.

No caso de ocorre uma falha de execução, a saída que ele descreve também está contida neste ficheiro de registo. As informações fornecidas nestes registos devem ser útil na depuração de problemas de script que podem surgir.

## <a name="see-also"></a>Consulte também
* [Personalizar clusters do HDInsight com ação de Script] [hdinsight-cluster-personalizar]
* [Instalar e utilizar o Apache Spark em clusters do HDInsight][hdinsight-install-spark]
* [Instalar e usar o Apache Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx
