---
title: Implementar políticas personalizadas com gasodutos Azure
titleSuffix: Azure AD B2C
description: Saiba como implementar as políticas personalizadas Azure AD B2C num oleoduto CI/CD utilizando gasodutos Azure em Serviços Azure DevOps.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca4464f40c0807e51adffca8e3e68c622314de6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256893"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Implementar políticas personalizadas com gasodutos Azure

Utilizando um pipeline de integração e entrega contínua (CI/CD) que instalou nos [Oleodutos Azure,][devops-pipelines]pode incluir as suas políticas personalizadas Azure AD B2C na sua entrega de software e automatização de controlo de código. À medida que se implanta em diferentes ambientes AD B2C Ad, por exemplo dev, teste e produção, recomendamos que remova os processos manuais e realize testes automatizados utilizando gasodutos Azure.

Existem três passos primários necessários para permitir que os gasodutos Azure consigam gerir as políticas personalizadas dentro do Azure AD B2C:

1. Crie um registo de aplicação web no seu inquilino Azure AD B2C
1. Configure um Azure Repo
1. Configure um Gasoduto Azure

> [!IMPORTANT]
> Gerir as políticas personalizadas Azure AD B2C com um Gasoduto Azure utiliza atualmente operações **de pré-visualização** disponíveis no ponto final da Microsoft Graph API. `/beta` A utilização destas APIs em aplicações de produção não é suportada. Para obter mais informações, consulte a [referência do ponto final beta API do Microsoft Graph REST](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta).

## <a name="prerequisites"></a>Pré-requisitos

* [Inquilino Azure AD B2C,](tutorial-create-tenant.md)e credenciais para um utilizador no diretório com a função [de Administrador de Política B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](tutorial-create-user-flows.md?pivots=b2c-custom-policy) enviadas para o seu inquilino
* [Aplicação de gestão](microsoft-graph-get-started.md) registada no seu inquilino com a Política de Permissão da API do Microsoft Graph.ReadWrite.TrustFramework 
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/), e acesso a um [projeto Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Fluxo de concessão de credenciais de cliente

O cenário aqui descrito faz uso de chamadas de serviço-a-serviço entre a Azure Pipelines e a Azure AD B2C utilizando o fluxo de concessão de [credenciais](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)de cliente OAuth 2.0 . Este fluxo de subvenção permite que um serviço web como a Azure Pipelines (o cliente confidencial) utilize as suas próprias credenciais em vez de se fazer passar por um utilizador para autenticar quando liga para outro serviço web (a Microsoft Graph API, neste caso). A Azure Pipelines obtém um símbolo não interativo e, em seguida, faz pedidos para a Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Registar um pedido de tarefas de gestão

Como mencionado em [Pré-Requisitos,](#prerequisites)precisa de um registo de aplicação que os seus scripts PowerShell - executados pela Azure Pipelines - podem usar para aceder aos recursos do seu inquilino.

Se já tem um registo de aplicação que utiliza para tarefas de automatização, certifique-se de que foi concedida a permissão da **Microsoft Graph**  >    >  **Policy.ReadWrite.TrustFramework** dentro das **permissões API** do registo da aplicação.

Para obter instruções sobre o registo de uma aplicação de gestão, consulte [Manage Azure AD B2C com o Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Configure um Azure Repo

Com uma aplicação de gestão registada, está pronto para configurar um repositório para os seus ficheiros políticos.

1. Inscreva-se na sua organização Azure DevOps Services.
1. [Crie um novo projeto][devops-create-project] ou selecione um projeto existente.
1. No seu projeto, navegue para **Repos** e selecione a página **Ficheiros.** Selecione um repositório existente ou crie um para este exercício.
1. Criar uma pasta chamada *B2CAssets*. Nomeie o ficheiro de espaço reservado necessário *README.md* e **comprometa** o ficheiro. Pode remover este ficheiro mais tarde, se quiser.
1. Adicione os ficheiros de política Azure AD B2C à pasta *B2CAssets.* Isto inclui o *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*, e quaisquer outras políticas que tenha criado. Grave o nome de cada ficheiro de cada ficheiro Ad B2C Azure para utilização num passo posterior (são usados como argumentos de script PowerShell).
1. Crie uma pasta chamada *Scripts* no diretório de raiz do repositório, nomeie o ficheiro de espaço reservado *DeployToB2c.ps1*. Não cometa o ficheiro neste momento, vai fazê-lo mais tarde.
1. Cole o seguinte script PowerShell em *DeployToB2c.ps1* e, em seguida, **comprometa** o ficheiro. O script adquire um símbolo da Azure AD e chama a Microsoft Graph API para carregar as políticas dentro da pasta *B2CAssets* para o seu inquilino Azure AD B2C.

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

Com o seu repositório inicializado e povoado com os seus ficheiros de política personalizados, está pronto para configurar o pipeline de lançamento.

### <a name="create-pipeline"></a>Criar pipeline

1. Inscreva-se na sua organização Azure DevOps Services e navegue para o seu projeto.
1. No seu projeto, selecione   >  **Pipelines Releases**  >  **Novo pipeline**.
1. Em **Selecione um modelo,** selecione **Trabalho Vazio**.
1. Introduza um **nome de palco**, por *exemplo, Implementar Políticas,* e feche o painel.
1. **Selecione Adicione um artefacto**, e sob o tipo **Fonte**, selecione **Azure Repository**.
    1. Escolha o repositório de origem que contém a pasta *Scripts* que povoou com o script PowerShell.
    1. Escolha um **ramo predefinido.** Se criou um novo repositório na secção anterior, o ramo predefinido é *mestre*.
    1. Deixe a definição de **versão padrão** da *versão mais recente do ramo predefinido*.
    1. Insira **um pseudónimo de fonte** para o repositório. Por exemplo, *políticaRepo*. Não inclua quaisquer espaços no nome do pseudónimo.
1. Selecione **Adicionar**
1. Mude o nome do oleoduto para refletir a sua intenção. Por exemplo, *implementar o pipeline de política personalizada*.
1. **Selecione Guardar** para guardar a configuração do pipeline.

### <a name="configure-pipeline-variables"></a>Configure variáveis de gasoduto

1. Selecione o separador **Variáveis.**
1. Adicione as seguintes variáveis sob **variáveis pipeline** e desa estalem os seus valores conforme especificado:

    | Name | Valor |
    | ---- | ----- |
    | `clientId` | **Identificação do pedido (cliente)** do pedido que registou anteriormente. |
    | `clientSecret` | O valor do segredo do **cliente** que criou anteriormente. <br /> Altere o tipo variável para **segredo** (selecione o ícone de bloqueio). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, onde *o seu inquilino b2c* é o nome do seu inquilino Azure AD B2C. |

1. **Selecione Guardar** para guardar as variáveis.

### <a name="add-pipeline-tasks"></a>Adicionar tarefas de pipeline

Em seguida, adicione uma tarefa para implementar um ficheiro de política.

1. Selecione o separador **Tarefas.**
1. Selecione **o trabalho de Agente**, e, em seguida, selecione o sinal de mais ( ) para adicionar uma tarefa ao trabalho de **+** Agente.
1. Procure e selecione **PowerShell**. Não selecione "Azure PowerShell", "PowerShell em máquinas-alvo", ou outra entrada PowerShell.
1. Selecione a tarefa **de Script PowerShell** recentemente adicionada.
1. Introduza os seguintes valores para a tarefa PowerShell Script:
    * **Versão de tarefa**: 2.*
    * **Nome do visor**: O nome da política que esta tarefa deve carregar. Por exemplo, *B2C_1A_TrustFrameworkBase.*
    * **Tipo**: Caminho de arquivo
    * **Caminho do guião**: Selecione a elipse (**_..._* _), navegue para a pasta _Scripts* e, em seguida, selecione o ficheiro *DeployToB2C.ps1.*
    * **Argumentos:**

        Introduza os seguintes **valores** para Argumentos . `{alias-name}`Substitua-o pelo pseudónimo especificado na secção anterior.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Por exemplo, se o pseudónimo especificado for *policyRepo,* a linha de argumento deve ser:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. **Selecione Guardar** para salvar o trabalho do Agente.

A tarefa que adicionou envia *um* ficheiro de política para o Azure AD B2C. Antes de continuar, desencadeie manualmente o trabalho (**Criar release**) para garantir que este se completa com sucesso antes de criar tarefas adicionais.

Se a tarefa terminar com sucesso, adicione tarefas de implementação executando os passos anteriores para cada um dos ficheiros de política personalizados. Modifique os `-PolicyId` valores e `-PathToFile` os valores de argumento para cada política.

É `PolicyId` um valor encontrado no início de um ficheiro de política XML dentro do nó TrustFrameworkPolicy. Por exemplo, `PolicyId` a seguinte política XML é *B2C_1A_TrustFrameworkBase:*

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Ao executar os agentes e carregar os ficheiros de política, certifique-se de que são carregados por esta ordem:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

O Quadro de Experiência de Identidade aplica esta ordem à medida que a estrutura de ficheiros é construída sobre uma cadeia hierárquica.

## <a name="test-your-pipeline"></a>Testar o pipeline

Para testar o seu oleoduto de libertação:

1. Selecione **Pipelines** e, em seguida, **desbloqueia**.
1. Selecione o pipeline que criou anteriormente, por *exemplo, Implementar Políticas*.
1. **Selecione Criar verção** e, em seguida, selecione **Criar** para fazer fila no lançamento.

Deve ver um banner de notificação que diz que uma versão foi em fila. Para visualizar o seu estado, selecione o link no banner de notificação ou selecione-o na lista no separador **Versões.**

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre:

* [Chamadas de serviço para serviço usando credenciais de cliente](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
