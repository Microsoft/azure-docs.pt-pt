---
title: Implementar políticas personalizadas com pipelines Azure
titleSuffix: Azure AD B2C
description: Aprenda a implementar políticas personalizadas Azure AD B2C num oleoduto CI/CD utilizando pipelines Azure em Serviços Azure DevOps.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b23b60ae49a4973fa04e6fa5f795f99536e32e7f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78188754"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Implementar políticas personalizadas com pipelines Azure

Utilizando um pipeline de integração e entrega contínua (CI/CD) que instalou em [Pipelines Azure,][devops-pipelines]pode incluir as suas políticas personalizadas Azure AD B2C na sua entrega de software e automatização de controlo de códigos. À medida que se desdobra em diferentes ambientes Azure AD B2C, por exemplo de v, teste e produção, recomendamos que remova os processos manuais e realize testes automatizados utilizando pipelines Azure.

Existem três etapas primárias necessárias para permitir que os oleodutos Azure gerem políticas personalizadas dentro do Azure AD B2C:

1. Crie um registo de candidatura web no seu inquilino Azure AD B2C
1. Configure um Azure Repo
1. Configure um oleoduto Azure

> [!IMPORTANT]
> Gerir as políticas personalizadas do Azure AD B2C com um Pipeline `/beta` Azure utiliza atualmente operações de **pré-visualização** disponíveis no ponto final da Microsoft Graph API. A utilização destas APIs em aplicações de produção não é suportada. Para mais informações, consulte a referência beta do [Microsoft Graph REST API](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Pré-requisitos

* [Inquilino Azure AD B2C,](tutorial-create-tenant.md)e credenciais para um utilizador no diretório com o papel de Administrador de [Política B2C IEF](../active-directory/users-groups-roles/directory-assign-admin-roles.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](custom-policy-get-started.md) enviadas para o seu inquilino
* [App de gestão](microsoft-graph-get-started.md) registada no seu inquilino com a Política de Permissão API do Microsoft *Graph.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/), e acesso a um [projeto azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Fluxo de concessão de credenciais de cliente

O cenário aqui descrito utiliza chamadas de serviço-a-serviço entre a Azure Pipelines e a Azure AD B2C utilizando o fluxo de concessão de [credenciais](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)de clientes OAuth 2.0. Este fluxo de subvenção permite que um serviço web como o Azure Pipelines (o cliente confidencial) utilize as suas próprias credenciais em vez de se fazer passar por um utilizador para autenticar quando chama outro serviço web (a Microsoft Graph API, neste caso). A Azure Pipelines obtém um token não interactivamente e, em seguida, faz pedidos para a API do Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Registar um pedido de tarefas de gestão

Como mencionado nos [Pré-requisitos,](#prerequisites)precisa de um registo de inscrição que os seus scripts PowerShell - executados pela Azure Pipelines -- podem usar para aceder aos recursos do seu inquilino.

Se já tem um registo de aplicação que utiliza para tarefas de automação, certifique-se de que lhe foi concedida a política de**política** > do **gráfico** > da**Microsoft.ReadWrite.TrustFramework** no âmbito das **permissões** da API do registo da aplicação.

Para obter instruções sobre o registo de uma aplicação de gestão, consulte [Manage Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Configure um Azure Repo

Com uma aplicação de gestão registada, está pronto para configurar um repositório para os seus ficheiros de política.

1. Inscreva-se na sua organização Azure DevOps Services.
1. [Crie um novo projeto][devops-create-project] ou selecione um projeto existente.
1. No seu projeto, navegue para **Repos** e selecione a página **Ficheiros.** Selecione um repositório existente ou crie um para este exercício.
1. Crie uma pasta chamada *B2CAssets*. Nomeie o ficheiro reservado necessário *README.md* e **comprometa** o ficheiro. Pode remover este ficheiro mais tarde, se quiser.
1. Adicione os seus ficheiros de política Azure AD B2C à pasta *B2CAssets.* Isto inclui o *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*, e quaisquer outras políticas que tenha criado. Grave o nome de ficheiro de cada ficheiro de política Azure AD B2C para uso num passo posterior (são usados como argumentos de script PowerShell).
1. Crie uma pasta denominada *Scripts* no diretório raiz do repositório, nomeie o ficheiro de espaço reservado *DeployToB2c.ps1*. Não cometa o ficheiro neste momento, vai fazê-lo num passo posterior.
1. Colhe o seguinte script PowerShell em *DeployToB2c.ps1 e,* em seguida, **comprometa** o ficheiro. O script adquire um símbolo da Azure AD e chama a API do Microsoft Graph para fazer o upload das políticas dentro da pasta *B2CAssets* para o seu inquilino Azure AD B2C.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Configure o seu oleoduto Azure

Com o seu repositório inicializado e povoado com os seus ficheiros de política personalizadas, está pronto para configurar o pipeline de libertação.

### <a name="create-pipeline"></a>Criar pipeline

1. Inscreva-se na sua organização DevOps Azure E navegue para o seu projeto.
1. No seu projeto, selecione **Pipelines** > **Releases** > **Novo oleoduto**.
1. Em **selecione um modelo,** selecione **Trabalho vazio**.
1. Introduza um **nome de palco**, por *exemplo, implemente Políticas Personalizadas,* em seguida, feche o painel.
1. **Selecione Adicionar um artefacto,** e sob o **tipo fonte,** selecione **Repositório Azure**.
    1. Escolha o repositório de origem que contém a pasta *Scripts* que povoou com o script PowerShell.
    1. Escolha um **ramo Predefinido**. Se criou um novo repositório na secção anterior, o ramo padrão é *mestre*.
    1. Deixe a definição de **versão Predefinida** *do Mais Recente a partir do ramo predefinido*.
    1. Insira um **pseudónimo fonte** para o repositório. Por exemplo, *políticaRepo*. Não inclua nenhum espaço no nome do pseudónimo.
1. Selecione **Adicionar**
1. Mude o nome do oleoduto para refletir a sua intenção. Por exemplo, *implementar pipeline de política personalizada*.
1. Selecione **Guardar** para salvar a configuração do gasoduto.

### <a name="configure-pipeline-variables"></a>Configurar variáveis de gasoduto

1. Selecione o separador **Variáveis.**
1. Adicione as seguintes variáveis sob **variáveis pipeline** e estabeleça os seus valores conforme especificado:

    | Nome | Valor |
    | ---- | ----- |
    | `clientId` | **Identificação de pedido (cliente)** do pedido que registou anteriormente. |
    | `clientSecret` | O valor do segredo do **cliente** que criou anteriormente. <br /> Mude o tipo variável para **segredo** (selecione o ícone de bloqueio). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, onde *o seu inquilino b2c* é o nome do seu inquilino Azure AD B2C. |

1. Selecione **Guardar** para salvar as variáveis.

### <a name="add-pipeline-tasks"></a>Adicionar tarefas de pipeline

Em seguida, adicione uma tarefa para implementar um ficheiro de política.

1. Selecione o separador **Tarefas.**
1. Selecione **a função de****+** Agente , e, em seguida, selecione o sinal de adição ( ) para adicionar uma tarefa ao trabalho do Agente.
1. Procure e selecione **PowerShell**. Não selecione "Azure PowerShell", "PowerShell on target machines", ou outra entrada powerShell.
1. Selecione a nova tarefa **do Script PowerShell.**
1. Introduza os seguintes valores para a tarefa do Script PowerShell:
    * **Versão de tarefa**: 2.*
    * Nome do **mostrador**: O nome da política que esta tarefa deve carregar. Por exemplo, *B2C_1A_TrustFrameworkBase.*
    * **Tipo**: Caminho de Arquivo
    * **Script Path**: Selecione a elipsis ***(...),*** navegue para a pasta *Scripts* e, em seguida, selecione o ficheiro *DeployToB2C.ps1.*
    * **Argumentos:**

        Introduza os seguintes **valores**para argumentos . Substitua-a `{alias-name}` pelo pseudónimo especificado na secção anterior.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Por exemplo, se o pseudónimo que especificou for *policyRepo,* a linha de argumento deve ser:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Selecione **Guardar** para salvar o trabalho do Agente.

A tarefa que acaba de adicionar envia *um* ficheiro de política para o Azure AD B2C. Antes de prosseguir, desencadeie manualmente o trabalho **(Criar libertação**) para garantir que completa com sucesso antes de criar tarefas adicionais.

Se a tarefa completar com sucesso, adicione tarefas de implementação executando os passos anteriores para cada um dos ficheiros de política personalizados. Modificar `-PolicyId` os `-PathToFile` valores e argumentos de cada política.

Trata-se de `PolicyId` um valor encontrado no início de um ficheiro político XML no nó TrustFrameworkPolicy. Por exemplo, `PolicyId` a seguinte política XML é *B2C_1A_TrustFrameworkBase:*

```XML
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Ao executar os agentes e carregar os ficheiros de política, certifique-se de que são carregados nesta ordem:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

O Quadro de Experiência de Identidade aplica esta ordem à medida que a estrutura de ficheiros é construída sobre uma cadeia hierárquica.

## <a name="test-your-pipeline"></a>Testar o pipeline

Para testar o seu gasoduto de libertação:

1. Selecione **Pipelines** e, em seguida, **lançamentos**.
1. Selecione o pipeline que criou anteriormente, por exemplo *Implemente Políticas Personalizadas*.
1. Selecione **Criar a libertação**e, em seguida, selecione **Criar** para fazer fila a versão.

Deve ver um banner de notificação que diz que um lançamento foi feito na fila. Para ver o seu estado, selecione o link no banner de notificação ou selecione-o na lista no separador **'Lançamentos'.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

* [Chamadas de serviço a serviço usando credenciais de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Serviços de DevOps do Azure](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
