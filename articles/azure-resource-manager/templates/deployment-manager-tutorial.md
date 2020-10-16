---
title: Use o Gestor de Implementação Azure para implementar modelos
description: Saiba como usar os modelos do Gestor de Recursos com o Gestor de Implementação Azure para implementar recursos Azure.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 49465f05b5484dfd358136866b67ce35f789799f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88892937"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Tutorial: Utilizar o Gestor de Implementação do Azure com modelos do Resource Manager (Pré-visualização pública)

Saiba como utilizar o [Gestor de Implementações do Azure](./deployment-manager-overview.md) para implementar as suas aplicações em várias regiões. Se preferir uma abordagem mais rápida, o [Quickstart Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart) cria as configurações necessárias na sua subscrição e personaliza os artefactos para implementar uma aplicação em várias regiões. O quickstart executa as mesmas tarefas que faz neste tutorial.

Para utilizar o Gestor de Implementação, é necessário criar dois modelos:

* **Um modelo de topologia**: descreve os recursos do Azure que compõem as suas aplicações e onde os implementar.
* **Um modelo de lançamento**: descreve os passos a seguir durante a implementação das aplicações.

> [!IMPORTANT]
> Se a sua subscrição estiver marcada para o Canário para testar novas funcionalidades do Azure, só pode utilizar o Azure Deployment Manager para implantar nas regiões canárias. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Compreender o cenário
> * Transferir os ficheiros do tutorial
> * Preparar os artefactos
> * Criar a identidade gerida definida pelo utilizador
> * Criar o modelo de topologia de serviço
> * Criar o modelo de lançamento
> * Implementar os modelos
> * Verificar a implementação
> * Implementar a versão mais recente
> * Limpar os recursos

Recursos adicionais:

* Referência [Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Tutorial: Use o controlo de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Alguma experiência no desenvolvimento dos [modelos do Azure Resource Manager](overview.md).
* Azure PowerShell. Para obter mais informações, veja [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps).
* Cmdlets do Gestor de Implementações. Para instalar estes cmdlets de pré-lançamento, precisa da versão mais recente do PowerShellGet. Para obter a versão mais recente, veja [Installing PowerShellGet](/powershell/scripting/gallery/installing-psget) (Instalar o PowerShellGet). Depois de instalar o PowerShellGet, feche a janela do PowerShell. Abra uma nova janela PowerShell elevada e use o seguinte comando:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Compreender o cenário

O modelo de topologia de serviço descreve os recursos Azure que compõem o seu serviço e onde implementá-los. A definição da topologia de serviço tem a hierarquia seguinte:

* Topologia de serviço
  * Serviços
    * Unidades de serviço

O diagrama abaixo ilustra a topologia de serviço utilizada neste tutorial:

![Diagrama do cenário do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Há dois serviços alocados nas localizações E.U.A. Oeste e E.U.A. Este.  Cada serviço tem duas unidades de serviço - um front-end da aplicação Web e uma conta de armazenamento para o back-end. As definições da unidade de serviço contêm ligações para os ficheiros de modelo e de parâmetros para criar as aplicações Web e as contas de armazenamento.

## <a name="download-the-tutorial-files"></a>Transferir os ficheiros do tutorial

1. Transfira [os modelos e os artefactos](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) utilizados neste tutorial.
2. Deszipe os ficheiros na localização no seu computador.

Existem duas pastas na pasta raiz:

* **ADMTemplates**: contém os modelos do Gestor de Implementações, que incluem:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: contém os artefactos do modelo e os artefactos binários. Veja [Preparar os artefactos](#prepare-the-artifacts).

Tenha em conta que há dois conjuntos de modelos.  Um conjunto são os modelos do Gestor de Implementações que são utilizados para implementar a topologia de serviço e o lançamento; o outro conjunto é chamado a partir das unidades de serviço para criar serviços Web e contas de armazenamento.

## <a name="prepare-the-artifacts"></a>Preparar os artefactos

A pasta ArtifactStore da transferência tem duas pastas:

![Diagrama da origem de artefactos do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* A pasta **templates** contém os artefactos do modelo. **1.0.0.0** e **1.0.0.1** representam as duas versões dos artefactos binários. Cada versão tem uma pasta para cada serviço (Service East U.S. e Service West U.S.). Cada serviço tem um par de ficheiros de modelos e parâmetros para criar uma conta de armazenamento e outro par para criar a aplicação Web. O modelo de aplicação Web chama um pacote comprimido, que contém os ficheiros da aplicação Web. O ficheiro comprimido é um artefacto binário armazenado na pasta “binaries”.
* A pasta **binaries** contém os artefactos binários. **1.0.0.0** e **1.0.0.1** representam as duas versões dos artefactos binários. Em cada versão, há um ficheiro zip para criar a aplicação Web na localização E.U.A. Oeste e outro para a criar na localização E.U.A. Este.

Ambas as versões (1.0.0.0 e 1.0.0.1) destinam-se à [implementação de revisões](#deploy-the-revision). Embora, tanto os artefactos do modelo, como os artefactos binários, tenham duas versões, só estes últimos são diferentes entre as duas versões. Na prática, os artefactos binários são atualizados com mais frequência em comparação com os do modelo.

1. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** num editor de texto. É um modelo básico para criar uma conta de armazenamento.
2. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Modelo para criar aplicação Web do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    O modelo chama um pacote de implementação, que contém os ficheiros da aplicação Web. Neste tutorial, o pacote comprimido contém apenas um ficheiro index.html.
3. Abra  **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Parâmetros containerRoot do modelo para criar aplicação Web do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    O valor de deployPackageUri é o caminho para o pacote de implementação. O parâmetro contém a variável **$containerRoot**. O valor de $containerRoot é indicado no [modelo de lançaento](#create-the-rollout-template) mediante a concatenação da localização SAS da origem do artefacto, da raiz do artefacto e de deployPackageUri.
4. Abra **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    O html mostra a localização e as informações da versão. O ficheiro binário na pasta 1.0.0.1 mostra “Version 1.0.0.1”. Depois de implementar o serviço, pode navegar para essas páginas.
5. Veja outros ficheiros de artefactos. Isso ajuda-o a compreender melhor o cenário.

Os artefactos do modelo são utilizados pelo modelo de topologia de serviço e os artefactos binários são utilizados pelo modelo de lançamento. Tanto o modelo de topologia, como o modelo de lançamento, definem um recurso do Azure de origem de artefactos, que é um recurso utilizado para apontar o Resource Manager para os artefactos de modelo e binários utilizados na implementação. Para simplificar o tutorial, é utilizada uma conta de armazenamento para armazenar os dois tipos de artefactos. Ambas as origens dos artefactos apontam para a mesma conta de armazenamento.

Executar o seguinte script PowerShell para criar um grupo de recursos, criar um recipiente de armazenamento, criar um recipiente blob, carregar os ficheiros descarregados e, em seguida, criar um token SAS.

> [!IMPORTANT]
> **projectName** no script PowerShell é usado para gerar nomes para os serviços Azure que são implantados neste tutorial. Diferentes serviços Azure têm diferentes requisitos sobre os nomes. Para garantir que a implementação é bem sucedida, escolha um nome com menos de 12 caracteres com letras e números minúsculas.
> Guarde uma cópia do nome do projeto. Usa-se o mesmo projectoName através do tutorial.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Faça uma cópia do URL com o token SAS. Este URL é necessário para preencher um campo nos dois ficheiros de parâmetros, os ficheiros parâmetros da topologia e parâmetros do lançamento.

Abra o recipiente a partir do portal Azure e verifique se tanto as **binárias** como as **pastas de modelos** e os ficheiros estão carregados.

## <a name="create-the-user-assigned-managed-identity"></a>Criar a identidade gerida atribuída pelo utilizador

Mais à frente no tutorial, vai implementar um lançamento. Para implementar ações, é necessária uma identidade gerida atribuída pelo utilizador (por exemplo, para implementar as aplicações Web e a conta de armazenamento). Tem de ser concedido a essa identidade acesso à subscrição do Azure na qual vai implementar o serviço, sendo que aquela também tem de ter permissão suficiente para concluir a implementação do artefacto.

Tem de criar uma identidade gerida atribuída pelo utilizador e configurar o controlo de acesso para a sua subscrição.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Crie uma [identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. No portal, selecione **Subscrições**, no menu do lado esquerdo, e selecione a sua subscrição.
4. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione Adicionar a atribuição de **função**.
5. Introduza ou selecione os seguintes valores:

    ![Controlo de acesso da identidade gerida atribuída pelo utilizador do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Função**: dê permissão suficiente para concluir a implementação dos artefactos (as aplicações Web e as contas de armazenamento). Para este tutorial, selecione **Contribuidor**. Na prática, o que se quer é restringir as permissões ao mínimo.
    * **Acesso atribuído a**: selecione **Identidade Gerida Atribuída pelo Utilizador**.
    * Selecione a identidade gerida atribuída pelo utilizador que criou anteriormente no tutorial.
6. Selecione **Guardar**.

## <a name="create-the-service-topology-template"></a>Criar o modelo de topologia de serviço

Abra **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os parâmetros seguintes:

* **nome do projeto**: Este nome é usado para criar os nomes para os recursos do Gestor de Implantação. Por exemplo, usando "jdoe", o nome de topologia de serviço é **jdoe**ServiceTopology.  Os nomes dos recursos são definidos na secção de variáveis deste modelo.
* **azureResourcelocation**: para simplificar o tutorial, todos os recursos partilham esta localização, salvo indicação em contrário.
* **artifactSourceSASLocation**: o URI de SAS para o contentor de blobs no qual os ficheiros de modelo da unidade de serviço e de parâmetros são armazenados para a implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
* **templateArtifactRoot**: o caminho de deslocamento do contentor de blobs no qual os modelos e os parâmetros são armazenados. O valor predefinido é **templates/1.0.0.0**. Não altere este valor, a menos que pretenda alterar a estrutura de pastas explicada em [Preparar os artefactos](#prepare-the-artifacts). Neste tutorial, são utilizados caminhos relativos.  O caminho completo é construído mediante a concatenação de **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (ou **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**: o ID de subscrição na qual os recursos do Gestor de Implementações vão ser implementados e faturados. Utilize o ID da sua subscrição neste tutorial.

### <a name="the-variables"></a>As variáveis

A secção de variáveis define os nomes dos recursos, as localizações do Azure para ambos os serviços, **Service WUS** e **Service EUS**, e os caminhos dos artefactos:

![Variáveis do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Compare os caminhos dos artefactos com a estrutura de pastas que carregou para a conta de armazenamento. Tenha em conta que os caminhos dos artefactos são relativos. O caminho completo é construído mediante a concatenação de **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (ou **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Os recursos

Ao nível da raiz, há dois recursos definidos, *uma origem do artefacto* e uma *topologia de serviço*.

A definição da origem do artefacto é:

![Origem do artefacto de recursos do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

A captura de ecrã seguinte só mostra algumas partes dos serviços de topologia de serviço, os serviços e as definições das unidades de serviço:

![Topologia de serviço de recursos do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** é utilizado para associar o recurso da origem do artefacto ao recurso da topologia de serviço.
* **dependsOn**: todos os recursos da topologia de serviço dependem do recurso de origem do artefacto.
* **artifacts** aponta para os artefactos do modelo.  Utilizam-se caminhos relativos aqui. O caminho completo é construído mediante a concatenação de artifactSourceSASLocation (definido na origem do artefacto), artifactRoot (definido na origem do artefacto) e templateArtifactSourceRelativePath (ou parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Ficheiro de parâmetros da topologia

Vai criar um ficheiro de parâmetros que é utilizado com o modelo de topologia.

1. Abra **\ADMTemplates\CreateADMServiceTopology.Parameters** no Visual Studio Code ou noutro editor de texto.
2. Preencha os valores dos parâmetros:

    * **nome do projeto**: Introduza uma corda com 4-5 caracteres. Este nome é usado para criar nomes de recursos azuis únicos.
    * **azureResourceLocation**: se não estiver familiarizado com as localizações do Azure, utilize **centralus** neste tutorial.
    * **artifactSourceSASLocation**: introduza o URI de SAS para o diretório de raiz (o contentor de blobs) no qual os ficheiros do modelo de unidade de serviço e dos parâmetros são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
    * **templateArtifactRoot**: a não ser que altere a estrutura de pastas dos artefactos, utilize **templates/1.0.0.0** neste tutorial.

> [!IMPORTANT]
> O modelo de topologia e o modelo de lançamento partilham alguns parâmetros. Esses parâmetros têm de ter os mesmos valores. Estes parâmetros são: **projectName**, **azureResourceLocation**, e **artefactoSourceSASLocation** (ambas as fontes de artefactos partilham a mesma conta de armazenamento neste tutorial).

## <a name="create-the-rollout-template"></a>Criar o modelo de lançamento

Abra **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os parâmetros seguintes:

![Parâmetros do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **nome do projeto**: Este nome é usado para criar os nomes para os recursos do Gestor de Implantação. Por exemplo, usando "jdoe", o nome de lançamento é **jdoe**Rollout.  Os nomes são definidos na secção de variáveis do modelo.
* **azureResourcelocation**: para simplificar este tutorial, todos os recursos do Gestor de Implementações partilham esta localização, salvo indicação em contrário.
* **artifactSourceSASLocation**: o URI de SAS para o diretório de raiz (o contentor de blobs) no qual os ficheiros do modelo de unidade de serviço e dos parâmetros são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
* **binaryArtifactRoot**: o valor predefinido é **binaries/1.0.0.0**. Não altere este valor, a menos que pretenda alterar a estrutura de pastas explicada em [Preparar os artefactos](#prepare-the-artifacts). Neste tutorial, são utilizados caminhos relativos.  O caminho completo é construído mediante a concatenação de **artifactSourceSASLocation**, **binaryArtifactRoot** e **deployPackageUri**, este último especificado em CreateWebApplicationParameters.json.  Veja [Preparar os artefactos](#prepare-the-artifacts).
* **managedIdentityID**: a identidade gerida atribuída pelo utilizador que realiza as ações de implementação. Veja [Criar a identidade gerida atribuída pelo utilizador](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>As variáveis

A secção de variáveis define os nomes dos recursos. Confirme que o nome da topologia de serviço, os nomes do serviço e os nomes das unidades de serviço correspondem aos nomes definidos no [modelo de topologia](#create-the-service-topology-template).

![Variáveis do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Os recursos

Ao nível da raiz, há três recursos definidos, uma origem do artefacto, um passo e um lançamento.

A definição da origem do artefacto é idêntica à que foi definida no modelo de topologia.  Veja [Criar o modelo de topologia de serviço](#create-the-service-topology-template) para obter mais informações.

A captura de ecrã seguinte mostra a definição do passo de espera:

![Passo de espera dos recursos do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

A duração utiliza a [nora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (tem de estar em letras maiúsculas) é um exemplo de uma espera de 1 minuto.

A captura de ecrã seguinte só mostra algumas partes da definição de lançamento:

![Lançamento de recursos do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: o recurso de lançamento depende no recurso de origem do artefacto e dos eventuais passos definidos.
* **artifactSourceId**: utilizado para associar o recurso de origem do artefacto ao recurso de lançamento.
* **targetServiceTopologyId**: utilizado para associar o recurso de topologia de serviço ao recurso de lançamento.
* **deploymentTargetId**: é o ID do recurso de unidade de serviço do recurso de topologia de serviço.
* **preDeploymentSteps** e **postDeploymentSteps**: contêm os passos do lançamento. No modelo, é chamado um passo de espera.
* **dependsOnStepGroups**: configure as dependências entre os grupos de passos.

### <a name="rollout-parameters-file"></a>Ficheiro de parâmetros do lançamento

Vai criar um ficheiro de parâmetros que é utilizado com o modelo de lançamento.

1. Abra **\ADMTemplates\CreateADMRollout.Parameters** no Visual Studio Code ou em qualquer editor de texto.
2. Preencha os valores dos parâmetros:

    * **nome do projeto**: Introduza uma corda com 4-5 caracteres. Este nome é usado para criar nomes de recursos azuis únicos.
    * **azureResourceLocation**: Especifique uma localização Azure.
    * **artifactSourceSASLocation**: introduza o URI de SAS para o diretório de raiz (o contentor de blobs) no qual os ficheiros do modelo de unidade de serviço e dos parâmetros são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
    * **binaryArtifactRoot**: a não ser que altere a estrutura de pastas dos artefactos, utilize **binaries/1.0.0.0** neste tutorial.
    * **managedIdentityID**: introduza a identidade gerida atribuída pelo utilizador. Veja [Criar a identidade gerida atribuída pelo utilizador](#create-the-user-assigned-managed-identity). A sintaxe é:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> O modelo de topologia e o modelo de lançamento partilham alguns parâmetros. Esses parâmetros têm de ter os mesmos valores. Estes parâmetros são: **projectName**, **azureResourceLocation**, e **artefactoSourceSASLocation** (ambas as fontes de artefactos partilham a mesma conta de armazenamento neste tutorial).

## <a name="deploy-the-templates"></a>Implementar os modelos

Para implementar os modelos, pode ser utilizado o Azure PowerShell.

1. Execute o script para implementar a topologia de serviço.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Se executar este script a partir de uma sessão de PowerShell diferente daquela que executou o roteiro [Prepare os artefactos,](#prepare-the-artifacts) precisa de repovoar as variáveis primeiro, que incluem **$resourceGroupName** e **$filePath**.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` é uma chamada assíncronea. A mensagem de sucesso significa apenas que a implantação começou com sucesso. Para verificar a colocação, consulte o passo 2 e o passo 4 deste procedimento.

2. Utilize o portal do Azure para confirmar que a topologia de serviço e os recursos sublinhados foram criados com êxito:

    ![Recursos de topologia de serviço implementados do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Para ver os recursos, **Mostrar tipos ocultos** tem de estar selecionado.

3. <a id="deploy-the-rollout-template"></a>Implementar o modelo de lançamento

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Utilize o seguinte script do PowerShell para verificar o progresso do lançamento:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Ante de poder executar este cmdlet, têm de ser instalados os cmdlets do PowerShell do Gestor de Implementações. Veja Pré-requisitos. O interruptor -Verbose pode ser usado para ver toda a saída.

    O exemplo seguinte mostra o estado da execução:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Após a implementação bem-sucedida do lançamento, deverá ver outros dois grupos de recursos criados, um para cada serviço.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Abra o [portal do Azure](https://portal.azure.com).
2. Navegue para as aplicações Web criadas recentemente, nos grupos de recursos novos que foram criados com a implementação do lançamento.
3. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro index.html.

## <a name="deploy-the-revision"></a>Implementar a revisão

Quando tiver uma versão nova da aplicação Web (1.0.0.1), pode utilizar o procedimento seguinte para a reimplementar.

1. Abra CreateADMRollout.Parameters.json.
2. Atualize **binaryArtifactRoot** para **binaries/1.0.0.1**.
3. Reimplemente o lançamento, conforme as instruções em [Implementar os modelos](#deploy-the-rollout-template).
4. Verifique a implementação, conforme as instruções em [Verificar a implementação](#verify-the-deployment). A página Web deverá mostrar a versão 1.0.0.1.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial. Deverá haver entre 3 a 4.

    * ** &lt; projectName>rg**: contém os recursos do Gestor de Implantação.
    * ** &lt; projectName>ServiceWUSrg:** contém os recursos definidos pelo ServiceWUS.
    * ** &lt; projectName>ServiceEUSrg:** contém os recursos definidos pelo ServiceEUS.
    * O grupo de recursos da identidade gerida atribuída pelo utilizador.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.
5. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar o Gestor de Implementações do Azure. Para integrar a monitorização de saúde no Azure Deployment Manager, consulte [Tutorial: Use o exame de saúde no Gestor de Implementação do Azure](./deployment-manager-tutorial-health-check.md).
