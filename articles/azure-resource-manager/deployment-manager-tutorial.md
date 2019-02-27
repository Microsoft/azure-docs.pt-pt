---
title: Utilizar o Gestor de Implementações do Azure com modelos do Resource Manager | Microsoft Docs
description: Utilize modelos do Resource Manager com o Gestor de Implementações do Azure para implementar recursos do Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9f548fbb9611b6d4b16efe5c4d26db73d85c9654
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882302"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-private-preview"></a>Tutorial: Utilizar o Gestor de implementação do Azure com modelos do Resource Manager (pré-visualização privada)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Saiba como utilizar o [Gestor de Implementações do Azure](./deployment-manager-overview.md) para implementar as suas aplicações em várias regiões. Para utilizar o Gestor de implementação, terá de criar dois modelos:

* **Um modelo de topologia**: descreve os recursos do Azure que compõem as suas aplicações e onde os implementar.
* **Um modelo de lançamento**: descreve os passos a seguir durante a implementação das aplicações.

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
> * Limpar recursos

A referência de API de REST do Gestor de implementação do Azure pode ser encontrada [aqui](https://docs.microsoft.com/rest/api/deploymentmanager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Alguma experiência no desenvolvimento dos [modelos do Azure Resource Manager](./resource-group-overview.md).
* O Gestor de Implementação do Azure está em pré-visualização privada. Para se inscrever com o Gestor de Implementações do Azure, preencha a [ficha de inscrição](https://aka.ms/admsignup). 
* Azure PowerShell. Para obter mais informações, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Cmdlets do Gestor de Implementações. Para instalar estes cmdlets de pré-lançamento, precisa da versão mais recente do PowerShellGet. Para obter a versão mais recente, veja [Installing PowerShellGet](/powershell/gallery/installing-psget) (Instalar o PowerShellGet). Depois de instalar o PowerShellGet, feche a janela do PowerShell. Abra uma janela nova elevada do PowerShell e utilize o seguinte comando:

    ```powershell
    Install-Module -Name AzureRM.DeploymentManager -AllowPrerelease
    ```
* [Explorador de Armazenamento do Microsoft Azure](https://azure.microsoft.com/features/storage-explorer/). O Explorador de Armazenamento do Azure não é necessário, mas facilita.

## <a name="understand-the-scenario"></a>Compreender o cenário

O modelo de topologia de serviço descreve os recursos do Azure que compõem o seu serviço e onde os implementar. A definição da topologia de serviço tem a hierarquia seguinte:

* Topologia de serviço
    * Serviços
        * Unidades de serviço

O diagrama abaixo ilustra a topologia de serviço utilizada neste tutorial:

![Diagrama do cenário do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Há dois serviços alocados nas localizações E.U.A. Oeste e E.U.A. Este.  Cada serviço tem duas unidades de serviço - um front-end da aplicação Web e uma conta de armazenamento para o back-end. As definições da unidade de serviço contêm ligações para os ficheiros de modelo e de parâmetros para criar as aplicações Web e as contas de armazenamento.

## <a name="download-the-tutorial-files"></a>Transferir os ficheiros do tutorial

1. Transfira [os modelos e os artefactos](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) utilizados neste tutorial.
2. Deszipe os ficheiros na localização no seu computador.

Existem duas pastas na pasta raiz:

- **ADMTemplates**: contém os modelos do Gestor de Implementações, que incluem:
    - CreateADMServiceTopology.json
    - CreateADMServiceTopology.Parameters.json
    - CreateADMRollout.json
    - CreateADMRollout.Parameters.json
- **ArtifactStore**: contém os artefactos do modelo e os artefactos binários. Veja [Preparar os artefactos](#prepare-the-artifacts).

Tenha em conta que há dois conjuntos de modelos.  Um conjunto são os modelos do Gestor de Implementações que são utilizados para implementar a topologia de serviço e o lançamento; o outro conjunto é chamado a partir das unidades de serviço para criar serviços Web e contas de armazenamento.

## <a name="prepare-the-artifacts"></a>Preparar os artefactos

A pasta ArtifactStore da transferência tem duas pastas:

![Diagrama da origem de artefactos do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

- A pasta **templates** contém os artefactos do modelo. **1.0.0.0** e **1.0.0.1** representam as duas versões dos artefactos binários. Cada versão tem uma pasta para cada serviço (Service East U.S. e Service West U.S.). Cada serviço tem um par de ficheiros de modelos e parâmetros para criar uma conta de armazenamento e outro par para criar a aplicação Web. O modelo de aplicação Web chama um pacote comprimido, que contém os ficheiros da aplicação Web. O ficheiro comprimido é um artefacto binário armazenado na pasta “binaries”.
- A pasta **binaries** contém os artefactos binários. **1.0.0.0** e **1.0.0.1** representam as duas versões dos artefactos binários. Em cada versão, há um ficheiro zip para criar a aplicação Web na localização E.U.A. Oeste e outro para a criar na localização E.U.A. Este.

Ambas as versões (1.0.0.0 e 1.0.0.1) destinam-se à [implementação de revisões](#deploy-the-revision). Embora, tanto os artefactos do modelo, como os artefactos binários, tenham duas versões, só estes últimos são diferentes entre as duas versões. Na prática, os artefactos binários são atualizados com mais frequência em comparação com os do modelo.

1. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** num editor de texto. É um modelo básico para criar uma conta de armazenamento.  
2. Abra **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**. 

    ![Modelo para criar aplicação Web do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    O modelo chama um pacote de implementação, que contém os ficheiros da aplicação Web. Neste tutorial, o pacote comprimido só contém o ficheiro index.html.
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

1. Crie uma conta do Storage do Azure. Para obter instruções, consulte [início rápido: Carregar, transferir e listar blobs através do portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Crie um contentor de blobs na conta de armazenamento.
3. Copie as duas pastas (“binaries” e “templates”) e os conteúdos das mesmas para o contentor de blobs. O [Explorador de Armazenamento do Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) suporta a funcionalidade de arrastar e largar.
4. Obtenha a localização SAS do contentor com as instruções seguintes:

    1. No Explorador de Armazenamento do Azure, navegue para o contentor de blobs.
    2. Clique com o botão direito do rato no contentor de blobs, no painel do lado esquerdo, e selecione **Get Shared Access Signature** (Obter Assinatura de Acesso Partilhado).
    3. Configure **Start time** (Hora de início) e **Expiry time** (Hora de fim).
    4. Selecione **Criar**.
    5. Faça uma cópia do URL. Este URL é necessário para preencher um campo nos dois ficheiros de parâmetros, os ficheiros [parâmetros da topologia](#topology-parameters-file) e [parâmetros do lançamento](#rollout-parameters-file).

## <a name="create-the-user-assigned-managed-identity"></a>Criar a identidade gerida atribuída pelo utilizador

Mais à frente no tutorial, vai implementar um lançamento. Para implementar ações, é necessária uma identidade gerida atribuída pelo utilizador (por exemplo, para implementar as aplicações Web e a conta de armazenamento). Tem de ser concedido a essa identidade acesso à subscrição do Azure na qual vai implementar o serviço, sendo que aquela também tem de ter permissão suficiente para concluir a implementação do artefacto.

Tem de criar uma identidade gerida atribuída pelo utilizador e configurar o controlo de acesso para a sua subscrição.

> [!IMPORTANT]
> A identidade gerida atribuída pelo utilizador tem de estar na mesma localização do [lançamento](#create-the-rollout-template). Atualmente, os recursos do Gestor de Implementações, incluindo o lançamento, só podem ser criados nos E.U.A. Central ou nos E.U.A. Leste 2. No entanto, isso só é verdadeiro para os recursos do Gestor de implementação (por exemplo, a topologia de serviço, serviços, unidades de serviço, implementação e passos). Os recursos de destino podem ser implementados para qualquer região do Azure suportada. Neste tutorial, por exemplo, o implementação do Gestor de recursos é implementado nos E.U.A. Central, mas os serviços são implementados nos E.U.A. leste e E.U.A. oeste. Esta restrição será elevada no futuro.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Crie uma [identidade gerida atribuída pelo utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. No portal, selecione **Subscrições**, no menu do lado esquerdo, e selecione a sua subscrição.
4. Selecione **controlo de acesso (IAM)** e, em seguida, selecione **adicionar atribuição de função**.
5. Introduza ou selecione os seguintes valores:

    ![Controlo de acesso da identidade gerida atribuída pelo utilizador do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    - **Função**: dê permissão suficiente para concluir a implementação dos artefactos (as aplicações Web e as contas de armazenamento). Para este tutorial, selecione **Contribuidor**. Na prática, o que se quer é restringir as permissões ao mínimo.
    - **Acesso atribuído a**: selecione **Identidade Gerida Atribuída pelo Utilizador**.
    - Selecione a identidade gerida atribuída pelo utilizador que criou anteriormente no tutorial.
6. Selecione **Guardar**.

## <a name="create-the-service-topology-template"></a>Criar o modelo de topologia de serviço

Abra **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os parâmetros seguintes:

![Parâmetros do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

- **namePrefix**: Este prefixo é utilizado para criar os nomes dos recursos do Gestor de implementação. Por exemplo, se for utilizado o prefixo “jdoe”, o nome da topologia de serviço será **jdoe**ServiceTopology.  Os nomes dos recursos são definidos na secção de variáveis deste modelo.
- **azureResourcelocation**: Para simplificar o tutorial, todos os recursos partilham esta localização, a menos que é especificado de outra forma. Atualmente, os recursos do Gestor de Implementações o Azure só podem ser criados nos **E.U.A. Central** ou nos **E.U.A. Leste 2**.
- **artifactSourceSASLocation**: O URI de SAS para o contentor de BLOBs onde os arquivos de modelo e parâmetros de unidade de serviço são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
- **templateArtifactRoot**: O caminho de deslocamento do contentor de BLOBs onde estão armazenados os parâmetros e modelos. O valor predefinido é **templates/1.0.0.0**. Não altere este valor, a menos que pretenda alterar a estrutura de pastas explicada em [Preparar os artefactos](#prepare-the-artifacts). Neste tutorial, são utilizados caminhos relativos.  O caminho completo é construído mediante a concatenação de **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (ou **parametersArtifactSourceRelativePath**).
- **targetSubscriptionID**: O ID de subscrição ao qual o implementação do Gestor de recursos vão ser implementado e cobrados. Utilize o ID da sua subscrição neste tutorial.

### <a name="the-variables"></a>As variáveis

A secção de variáveis define os nomes dos recursos, as localizações do Azure para os dois serviços: **Serviço WUS** e **serviço EUS**e os caminhos de artefacto:

![Variáveis do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Compare os caminhos dos artefactos com a estrutura de pastas que carregou para a conta de armazenamento. Tenha em conta que os caminhos dos artefactos são relativos. O caminho completo é construído mediante a concatenação de **artifactSourceSASLocation**, **templateArtifactRoot** e **templateArtifactSourceRelativePath** (ou **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Os recursos

Ao nível da raiz, há dois recursos definidos, *uma origem do artefacto* e uma *topologia de serviço*.

A definição da origem do artefacto é:

![Origem do artefacto de recursos do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

A captura de ecrã seguinte só mostra algumas partes dos serviços de topologia de serviço, os serviços e as definições das unidades de serviço:

![Topologia de serviço de recursos do modelo de topologia do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

- **artifactSourceId** é utilizado para associar o recurso da origem do artefacto ao recurso da topologia de serviço.
- **dependsOn**: Todos os recursos de topologia de serviço dependem do recurso de origem do artefacto.
- **artifacts** aponta para os artefactos do modelo.  Utilizam-se caminhos relativos aqui. O caminho completo é construído mediante a concatenação de artifactSourceSASLocation (definido na origem do artefacto), artifactRoot (definido na origem do artefacto) e templateArtifactSourceRelativePath (ou parametersArtifactSourceRelativePath).

> [!NOTE]
> Os nomes de unidade de serviço tem de conter a 31 carateres ou menos. 

### <a name="topology-parameters-file"></a>Ficheiro de parâmetros da topologia

Vai criar um ficheiro de parâmetros que é utilizado com o modelo de topologia.

1. Abra **\ADMTemplates\CreateADMServiceTopology.Parameters** no Visual Studio Code ou noutro editor de texto.
2. Preencha os valores dos parâmetros:

    - **namePrefix**: Introduza uma cadeia de caracteres com caracteres de 4 a 5. Este prefixo é utilizado para criar nomes de recursos do Azure exclusivos.
    - **azureResourceLocation**: Se não estiver familiarizado com localizações do Azure, utilize **centralus** neste tutorial.
    - **artifactSourceSASLocation**: Introduza o URI de SAS para o diretório de raiz (o contentor de BLOBs) onde os arquivos de modelo e parâmetros de unidade de serviço são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
    - **templateArtifactRoot**: A menos que altere a estrutura de pastas dos artefatos, utilize **templates/1.0.0.0** neste tutorial.
    - **targetScriptionID**: Introduza o ID de subscrição do Azure.

> [!IMPORTANT]
> O modelo de topologia e o modelo de lançamento partilham alguns parâmetros. Esses parâmetros têm de ter os mesmos valores. Os parâmetros são **namePrefix**, **azureResourceLocation** e **artifactSourceSASLocation** (ambas as origens dos artefactos partilham a mesma conta de armazenamento neste tutorial).

## <a name="create-the-rollout-template"></a>Criar o modelo de lançamento

Abra **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Os parâmetros

O modelo contém os parâmetros seguintes:

![Parâmetros do modelo de lançamento do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

- **namePrefix**: Este prefixo é utilizado para criar os nomes dos recursos do Gestor de implementação. Por exemplo, se utilizar o prefixo "jdoe", o nome do lançamento seria **jdoe**Rollout.  Os nomes são definidos na secção de variáveis do modelo.
- **azureResourcelocation**: Para simplificar o tutorial, todos os recursos do Gestor de implementação partilham esta localização, a menos que é especificado de outra forma. Atualmente, os recursos do Gestor de Implementações o Azure só podem ser criados nos **E.U.A. Central** ou nos **E.U.A. Leste 2**.
- **artifactSourceSASLocation**: O URI de SAS para o diretório de raiz (o contentor de BLOBs) onde os arquivos de modelo e parâmetros de unidade de serviço são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
- **binaryArtifactRoot**:  O valor predefinido é **binaries/1.0.0.0**. Não altere este valor, a menos que pretenda alterar a estrutura de pastas explicada em [Preparar os artefactos](#prepare-the-artifacts). Neste tutorial, são utilizados caminhos relativos.  O caminho completo é construído mediante a concatenação de **artifactSourceSASLocation**, **binaryArtifactRoot** e **deployPackageUri**, este último especificado em CreateWebApplicationParameters.json.  Veja [Preparar os artefactos](#prepare-the-artifacts).
- **managedIdentityID**: Identidade de gerida atribuído ao utilizador que executa as ações de implementação. Veja [Criar a identidade gerida atribuída pelo utilizador](#create-the-user-assigned-managed-identity).

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

- **dependsOn**: O recurso de implementação depende do recurso de origem de artefacto e qualquer um dos passos definidos.
- **artifactSourceId**: utilizado para associar o recurso de origem do artefacto ao recurso de lançamento.
- **targetServiceTopologyId**: utilizado para associar o recurso de topologia de serviço ao recurso de lançamento.
- **deploymentTargetId**: É o ID de recurso de unidade de serviço do recurso de topologia de serviço.
- **preDeploymentSteps** e **postDeploymentSteps**: contêm os passos do lançamento. No modelo, é chamado um passo de espera.
- **dependsOnStepGroups**: configure as dependências entre os grupos de passos.

### <a name="rollout-parameters-file"></a>Ficheiro de parâmetros do lançamento

Vai criar um ficheiro de parâmetros que é utilizado com o modelo de lançamento.

1. Abra **\ADMTemplates\CreateADMRollout.Parameters** no Visual Studio Code ou em qualquer editor de texto.
2. Preencha os valores dos parâmetros:

    - **namePrefix**: Introduza uma cadeia de caracteres com caracteres de 4 a 5. Este prefixo é utilizado para criar nomes de recursos do Azure exclusivos.
    - **azureResourceLocation**: Atualmente, os recursos do Gestor de Implementações o Azure só podem ser criados nos E.U.A. Central ou nos **E.U.A. Leste 2**.
    - **artifactSourceSASLocation**: Introduza o URI de SAS para o diretório de raiz (o contentor de BLOBs) onde os arquivos de modelo e parâmetros de unidade de serviço são armazenados para implementação.  Veja [Preparar os artefactos](#prepare-the-artifacts).
    - **binaryArtifactRoot**: A menos que altere a estrutura de pastas dos artefatos, utilize **binaries/1.0.0.0** neste tutorial.
    - **managedIdentityID**: Introduza a identidade gerida atribuído ao utilizador. Veja [Criar a identidade gerida atribuída pelo utilizador](#create-the-user-assigned-managed-identity). A sintaxe é:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> O modelo de topologia e o modelo de lançamento partilham alguns parâmetros. Esses parâmetros têm de ter os mesmos valores. Os parâmetros são **namePrefix**, **azureResourceLocation** e **artifactSourceSASLocation** (ambas as origens dos artefactos partilham a mesma conta de armazenamento neste tutorial).

## <a name="deploy-the-templates"></a>Implementar os modelos

Para implementar os modelos, pode ser utilizado o Azure PowerShell. 

1. Execute o script para implementar a topologia de serviço.

    ```azurepowershell-interactive
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"  
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"
    
    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create the service topology
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

2. Utilize o portal do Azure para confirmar que a topologia de serviço e os recursos sublinhados foram criados com êxito:

    ![Recursos de topologia de serviço implementados do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Para ver os recursos, **Mostrar tipos ocultos** tem de estar selecionado.

3. <a id="deploy-the-rollout-template"></a>Implemente o modelo de implementação:

    ```azurepowershell-interactive
    # Create the rollout
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Utilize o seguinte script do PowerShell para verificar o progresso do lançamento:

    ```azurepowershell-interactive
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzureRmDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName
    ```

    Ante de poder executar este cmdlet, têm de ser instalados os cmdlets do PowerShell do Gestor de Implementações. Consulte os pré-requisitos.

    O exemplo seguinte mostra o estado da execução:
    
    ```
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

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Navegue para as aplicações Web criadas recentemente, nos grupos de recursos novos que foram criados com a implementação do lançamento.
3. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro index.html.

## <a name="deploy-the-revision"></a>Implementar a revisão

Quando tiver uma versão nova da aplicação Web (1.0.0.1), pode utilizar o procedimento seguinte para a reimplementar.

1. Abra CreateADMRollout.Parameters.json.
2. Atualize **binaryArtifactRoot** para **binaries/1.0.0.1**.
3. Reimplemente o lançamento, conforme as instruções em [Implementar os modelos](#deploy-the-rollout-template).
4. Verifique a implementação, conforme as instruções em [Verificar a implementação](#verify-the-deployment). A página Web deverá mostrar a versão 1.0.0.1.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial. Deverá haver entre 3 a 4.

    - **&lt;namePrefix>rg**: contém os recursos do Gestor de Implementações.
    - **&lt;namePrefix>ServiceWUSrg**: contém os recursos definidos por ServiceWUS.
    - **&lt;namePrefix>ServiceEUSrg**: contém os recursos definidos por ServiceEUS.
    - O grupo de recursos da identidade gerida atribuída pelo utilizador.
3. Selecione o nome do grupo de recursos.  
4. Selecione **Eliminar grupo de recursos** no menu superior.
5. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar o Gestor de Implementações do Azure. Para saber mais, veja a [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
