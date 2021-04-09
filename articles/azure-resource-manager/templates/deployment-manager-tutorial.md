---
title: Use o Gestor de Implementação Azure para implementar modelos
description: Saiba como usar os modelos do Gestor de Recursos com o Gestor de Implementação Azure para implementar recursos Azure.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627587"
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

* [Referência Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Tutorial: Use o controlo de saúde no Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
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

Existem dois serviços atribuídos nos eua ocidentais e nas localizações do Leste dos EUA. Cada serviço tem duas unidades de serviço: uma aplicação web frontal e uma conta de armazenamento back-end. As definições da unidade de serviço contêm ligações ao modelo e aos ficheiros de parâmetros que criam as aplicações web e as contas de armazenamento.

## <a name="download-the-tutorial-files"></a>Transferir os ficheiros do tutorial

1. Transfira [os modelos e os artefactos](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) utilizados neste tutorial.
1. Deszipe os ficheiros na localização no seu computador.

Existem duas pastas na pasta raiz:

* _ADMTemplates_: contém os modelos do Gestor de Implementações, que incluem:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore_: contém os artefactos do modelo e os artefactos binários. Veja [Preparar os artefactos](#prepare-the-artifacts).

Há dois conjuntos de modelos. Um conjunto são os modelos do Gestor de Implementação que são usados para implementar a topologia de serviço e o lançamento. O outro conjunto é chamado das unidades de serviço para criar serviços web e contas de armazenamento.

## <a name="prepare-the-artifacts"></a>Preparar os artefactos

A pasta ArtifactStore da transferência tem duas pastas:

![Diagrama da origem de artefactos do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* A pasta _templates_ contém os artefactos do modelo. As pastas _1.0.0.0_ e _1.0.0.1_ representam as duas versões dos artefactos binários. Em cada versão, existe uma pasta para cada serviço: _ServiceEUS (ServiceEUS_ (Service EAST US) e _ServiceWUS_ (Service West US). Cada serviço tem um par de ficheiros de modelos e parâmetros para criar uma conta de armazenamento e outro par para criar a aplicação Web. O modelo de aplicação Web chama um pacote comprimido, que contém os ficheiros da aplicação Web. O ficheiro comprimido é um artefacto binário armazenado na pasta “binaries”.
* A pasta _binaries_ contém os artefactos binários. As pastas _1.0.0.0_ e _1.0.0.1_ representam as duas versões dos artefactos binários. Dentro de cada versão, existe um ficheiro zip para criar a aplicação web na localização dos EUA Ocidental, e o outro ficheiro zip para criar a aplicação web na localização dos EUA Orientais.

Ambas as versões (1.0.0.0 e 1.0.0.1) destinam-se à [implementação de revisões](#deploy-the-revision). Embora, tanto os artefactos do modelo, como os artefactos binários, tenham duas versões, só estes últimos são diferentes entre as duas versões. Na prática, os artefactos binários são atualizados com mais frequência em comparação com os do modelo.

1. Abra _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_ num editor de texto. É um modelo básico para criar uma conta de armazenamento.
1. Abra _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Modelo para criar aplicação Web do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    O modelo chama um pacote de implementação, que contém os ficheiros da aplicação Web. Neste tutorial, o pacote comprimido contém apenas um _ficheiroindex.html._
1. Abra  _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Parâmetros containerRoot do modelo para criar aplicação Web do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    O valor `deployPackageUri` é o caminho para o pacote de implantação. O parâmetro contém uma `$containerRoot` variável. O valor é fornecido no modelo de `$containerRoot` [lançamento,](#create-the-rollout-template) concatenando a localização sas de origem do artefacto, raiz de artefacto, e `deployPackageUri` .
1. Abra _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

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

    O HTML mostra a localização e as informações da versão. O ficheiro binário na pasta _1.0.0.1_ mostra a _versão 1.0.0.1_. Depois de implementar o serviço, pode navegar para essas páginas.
1. Veja outros ficheiros de artefactos. Isso ajuda-o a compreender melhor o cenário.

Os artefactos do modelo são utilizados pelo modelo de topologia de serviço e os artefactos binários são utilizados pelo modelo de lançamento. Tanto o modelo de topologia, como o modelo de lançamento, definem um recurso do Azure de origem de artefactos, que é um recurso utilizado para apontar o Resource Manager para os artefactos de modelo e binários utilizados na implementação. Para simplificar o tutorial, é utilizada uma conta de armazenamento para armazenar os dois tipos de artefactos. Ambas as origens dos artefactos apontam para a mesma conta de armazenamento.

Executar o seguinte script PowerShell para criar um grupo de recursos, criar um recipiente de armazenamento, criar um recipiente blob, carregar os ficheiros descarregados e, em seguida, criar um token SAS.

> [!IMPORTANT]
> `projectName` no script PowerShell é usado para gerar nomes para os serviços Azure que são implantados neste tutorial. Diferentes serviços Azure têm diferentes requisitos sobre os nomes. Para garantir que a implementação é bem sucedida, escolha um nome com menos de 12 caracteres com letras e números minúsculas.
> Guarde uma cópia do nome do projeto. Usas o mesmo `projectName` durante todo o tutorial.

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

Faça uma cópia do URL com o token SAS. Este URL é necessário para povoar um campo nos dois ficheiros de parâmetros: ficheiro de parâmetros de topologia e ficheiro de parâmetros de lançamento.

Abra o recipiente a partir do portal Azure e verifique se tanto as _binárias_ como as _pastas de modelos_ e os ficheiros são carregados.

## <a name="create-the-user-assigned-managed-identity"></a>Criar a identidade gerida atribuída pelo utilizador

Mais à frente no tutorial, vai implementar um lançamento. Para implementar ações, é necessária uma identidade gerida atribuída pelo utilizador (por exemplo, para implementar as aplicações Web e a conta de armazenamento). Tem de ser concedido a essa identidade acesso à subscrição do Azure na qual vai implementar o serviço, sendo que aquela também tem de ter permissão suficiente para concluir a implementação do artefacto.

Tem de criar uma identidade gerida atribuída pelo utilizador e configurar o controlo de acesso para a sua subscrição.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Crie uma [identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. No portal, selecione **Subscrições**, no menu do lado esquerdo, e selecione a sua subscrição.
1. Selecione **o controlo de acesso (IAM)** e, em seguida, selecione Adicionar a atribuição de **função**.
1. Introduza ou selecione os seguintes valores:

    ![Controlo de acesso da identidade gerida atribuída pelo utilizador do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Função**: dê permissão suficiente para concluir a implementação dos artefactos (as aplicações Web e as contas de armazenamento). Para este tutorial, selecione **Contribuidor**. Na prática, o que se quer é restringir as permissões ao mínimo.
    * **Atribuir acesso a**: **selecione Identidade Gerida atribuída pelo Utilizador.**
    * Selecione a identidade gerida atribuída pelo utilizador que criou anteriormente no tutorial.
1. Selecione **Guardar**.

## <a name="create-the-service-topology-template"></a>Criar o modelo de topologia de serviço

Abra _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os parâmetros seguintes:

* `projectName`: Este nome é usado para criar os nomes para os recursos do Gestor de Implantação. Por exemplo, usando **a demo,** o nome de topologia de serviço é **demo** ServiceTopology. Os nomes dos recursos são definidos na secção do `variables` modelo.
* `azureResourcelocation`: Para simplificar o tutorial, todos os recursos partilham este local, a menos que seja especificado o contrário.
* `artifactSourceSASLocation`: O SAS URI para o recipiente Blob onde o modelo da unidade de serviço e os ficheiros de parâmetros são armazenados para implantação. Veja [Preparar os artefactos](#prepare-the-artifacts).
* `templateArtifactRoot`: O caminho de compensação do recipiente Blob onde os modelos e parâmetros são armazenados. O valor predefinido é _templates/1.0.0.0_. Não altere este valor, a menos que pretenda alterar a estrutura de pastas explicada em [Preparar os artefactos](#prepare-the-artifacts). Neste tutorial, são utilizados caminhos relativos. O caminho completo é construído através da concatenação, `artifactSourceSASLocation` `templateArtifactRoot` e `templateArtifactSourceRelativePath` (ou `parametersArtifactSourceRelativePath` .
* `targetSubscriptionID`: O ID de subscrição para o qual os recursos do Gestor de Implantação serão implantados e faturados. Utilize o ID da sua subscrição neste tutorial.

### <a name="the-variables"></a>As variáveis

A secção de variáveis define os nomes dos recursos, as localizações do Azure para os dois `ServiceWUS` `ServiceEUS` serviços: e , e os caminhos do artefacto:

![Variáveis do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Compare os caminhos dos artefactos com a estrutura de pastas que carregou para a conta de armazenamento. Tenha em conta que os caminhos dos artefactos são relativos. O caminho completo é construído através da concatenação, `artifactSourceSASLocation` `templateArtifactRoot` e `templateArtifactSourceRelativePath` (ou `parametersArtifactSourceRelativePath` .

### <a name="the-resources"></a>Os recursos

No nível de raiz, são definidos dois recursos: *uma fonte de artefactos,* e *uma topologia de serviço.*

A definição da origem do artefacto é:

![Origem do artefacto de recursos do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

A captura de ecrã seguinte só mostra algumas partes dos serviços de topologia de serviço, os serviços e as definições das unidades de serviço:

![Topologia de serviço de recursos do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Utilizado para associar o recurso de fonte de artefacto ao recurso de topologia de serviço.
* `dependsOn`: Todos os recursos de topologia de serviço dependem do recurso de origem do artefacto.
* `artifacts`: Aponte para os artefactos do modelo. Utilizam-se caminhos relativos aqui. O percurso completo é construído através da concatenação `artifactSourceSASLocation` (definida na fonte do artefacto), `artifactRoot` (definida na fonte do artefacto) e `templateArtifactSourceRelativePath` (ou `parametersArtifactSourceRelativePath` .

### <a name="topology-parameters-file"></a>Ficheiro de parâmetros da topologia

Vai criar um ficheiro de parâmetros que é utilizado com o modelo de topologia.

1. Abra _\ADMTemplates\CreateADMServiceTopology.Parameters.jsno_ Código do Estúdio Visual ou em qualquer editor de texto.
1. Introduza os valores dos parâmetros:

    * `projectName`: Introduza uma corda com 4-5 caracteres. Este nome é usado para criar nomes únicos de recursos Azure.
    * `azureResourceLocation`: Se não estiver familiarizado com as localizações do Azure, use **central neste** tutorial.
    * `artifactSourceSASLocation`: Introduza o SAS URI no diretório de raiz (o recipiente Blob) onde os ficheiros de modelo e parâmetros da unidade de serviço são armazenados para implantação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
    * `templateArtifactRoot`: A menos que altere a estrutura da pasta dos artefactos, utilize _modelos/1.0.0.0 neste_ tutorial.

> [!IMPORTANT]
> O modelo de topologia e o modelo de lançamento partilham alguns parâmetros. Esses parâmetros têm de ter os mesmos valores. Estes parâmetros são: `projectName` `azureResourceLocation` , e `artifactSourceSASLocation` (ambas as fontes de artefactos partilham a mesma conta de armazenamento neste tutorial).

## <a name="create-the-rollout-template"></a>Criar o modelo de lançamento

Abra _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os parâmetros seguintes:

![Parâmetros do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Este nome é usado para criar os nomes para os recursos do Gestor de Implantação. Por exemplo, usando **a demonstração,** o nome de lançamento é **demo** Rollout. Os nomes são definidos na secção do `variables` modelo.
* `azureResourcelocation`: Para simplificar o tutorial, todos os recursos do Gestor de Implantação partilham este local, a menos que seja especificado o contrário.
* `artifactSourceSASLocation`: O SAS URI ao diretório de raiz (o recipiente Blob) onde o modelo da unidade de serviço e os ficheiros de parâmetros são armazenados para implantação. Veja [Preparar os artefactos](#prepare-the-artifacts).
* `binaryArtifactRoot`: O valor predefinido é _binário/1.0.0.0_. Não altere este valor, a menos que pretenda alterar a estrutura de pastas explicada em [Preparar os artefactos](#prepare-the-artifacts). Neste tutorial, são utilizados caminhos relativos. O caminho completo é construído através da concatenação, `artifactSourceSASLocation` e o especificado emCreateWebApplicationParameters.js`binaryArtifactRoot` `deployPackageUri` _em_. Veja [Preparar os artefactos](#prepare-the-artifacts).
* `managedIdentityID`: A identidade gerida atribuída pelo utilizador que executa as ações de implantação. Veja [Criar a identidade gerida atribuída pelo utilizador](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>As variáveis

A `variables` secção define os nomes dos recursos. Confirme que o nome da topologia de serviço, os nomes do serviço e os nomes das unidades de serviço correspondem aos nomes definidos no [modelo de topologia](#create-the-service-topology-template).

![Variáveis do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Os recursos

Ao nível da raiz, há três recursos definidos, uma origem do artefacto, um passo e um lançamento.

A definição da origem do artefacto é idêntica à que foi definida no modelo de topologia. Veja [Criar o modelo de topologia de serviço](#create-the-service-topology-template) para obter mais informações.

A imagem que se segue mostra a definição do `wait` passo:

![Passo de espera dos recursos do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

A duração utiliza a [norma ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (tem de estar em letras maiúsculas) é um exemplo de uma espera de 1 minuto.

A captura de ecrã seguinte só mostra algumas partes da definição de lançamento:

![Lançamento de recursos do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: O recurso de lançamento depende do recurso de origem do artefacto e de qualquer dos passos definidos.
* `artifactSourceId`: Utilizado para associar o recurso de origem do artefacto ao recurso de lançamento.
* `targetServiceTopologyId`: Utilizado para associar o recurso de topologia de serviço ao recurso de lançamento.
* `deploymentTargetId`: É o ID de recursos da unidade de serviço do recurso de topologia de serviço.
* `preDeploymentSteps` e `postDeploymentSteps` : Contém os passos de lançamento. No modelo, um `wait` passo é chamado.
* `dependsOnStepGroups`: Configurar as dependências entre os grupos de passo.

### <a name="rollout-parameters-file"></a>Ficheiro de parâmetros do lançamento

Vai criar um ficheiro de parâmetros que é utilizado com o modelo de lançamento.

1. Abra _\ADMTemplates\CreateADMRollout.Parameters.jsno_ Código do Estúdio Visual ou em qualquer editor de texto.
1. Introduza os valores dos parâmetros:

    * `projectName`: Introduza uma corda com 4-5 caracteres. Este nome é usado para criar nomes únicos de recursos Azure.
    * `azureResourceLocation`: Especificar uma localização Azure.
    * `artifactSourceSASLocation`: Introduza o SAS URI no diretório de raiz (o recipiente Blob) onde os ficheiros de modelo e parâmetros da unidade de serviço são armazenados para implantação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
    * `binaryArtifactRoot`: A menos que altere a estrutura da pasta dos artefactos, utilize _binários/1.0.0.0 neste_ tutorial.
    * `managedIdentityID`: Introduza a identidade gerida atribuída pelo utilizador. Veja [Criar a identidade gerida atribuída pelo utilizador](#create-the-user-assigned-managed-identity). A sintaxe é:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> O modelo de topologia e o modelo de lançamento partilham alguns parâmetros. Esses parâmetros têm de ter os mesmos valores. Estes parâmetros são: `projectName` `azureResourceLocation` , e `artifactSourceSASLocation` (ambas as fontes de artefactos partilham a mesma conta de armazenamento neste tutorial).

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

    Se executar este script a partir de uma sessão de PowerShell diferente daquela que executou o roteiro [Prepare os artefactos,](#prepare-the-artifacts) precisa de repovoar as variáveis primeiro, que incluem `$resourceGroupName` e `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` é uma chamada assíncronea. A mensagem de sucesso significa apenas que a implantação começou com sucesso. Para verificar a colocação, consulte o passo 2 e o passo 4 deste procedimento.

1. Utilize o portal do Azure para confirmar que a topologia de serviço e os recursos sublinhados foram criados com êxito:

    ![Recursos de topologia de serviço implementados do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Para ver os recursos, **Mostrar tipos ocultos** tem de estar selecionado.

1. <a id="deploy-the-rollout-template"></a>Implementar o modelo de lançamento

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Utilize o seguinte script do PowerShell para verificar o progresso do lançamento:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Ante de poder executar este cmdlet, têm de ser instalados os cmdlets do PowerShell do Gestor de Implementações. Veja [Pré-requisitos](#prerequisites). O `-Verbose` parâmetro pode ser usado para ver toda a saída.

    O exemplo seguinte mostra o estado da execução:

    ```Output
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

    Depois de o lançamento ser implementado com sucesso, verá mais dois grupos de recursos criados, um para cada serviço.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Abra o [portal do Azure](https://portal.azure.com).
1. Navegue para as aplicações web recém-criadas sob os novos grupos de recursos criados pela implementação do lançamento.
1. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro _index.html._

## <a name="deploy-the-revision"></a>Implementar a revisão

Quando tiver uma versão nova da aplicação Web (1.0.0.1), pode utilizar o procedimento seguinte para a reimplementar.

1. Abra _CreateADMRollout.Parameters.js._
1. Atualização `binaryArtifactRoot` para _binários/1.0.0.1_.
1. Reimplemente o lançamento, conforme as instruções em [Implementar os modelos](#deploy-the-rollout-template).
1. Verifique a implementação, conforme as instruções em [Verificar a implementação](#verify-the-deployment). A página Web deverá mostrar a versão 1.0.0.1.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
1. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial.

    * **&lt; projectName>rg**: contém os recursos do Gestor de Implantação.
    * **&lt; projectName>ServiceWUSrg:** contém os recursos definidos pelo ServiceWUS.
    * **&lt; projectName>ServiceEUSrg:** contém os recursos definidos pelo ServiceEUS.
    * O grupo de recursos da identidade gerida atribuída pelo utilizador.
1. Selecione o nome do grupo de recursos.
1. **Selecione Eliminar o grupo** de recursos do menu superior.
1. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a utilizar o Gestor de Implementações do Azure. Para integrar a monitorização de saúde no Azure Deployment Manager, consulte [Tutorial: Use o exame de saúde no Gestor de Implementação do Azure](./deployment-manager-tutorial-health-check.md).
