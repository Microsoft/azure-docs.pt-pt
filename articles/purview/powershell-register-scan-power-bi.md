---
title: Utilize o PowerShell para registar e digitalizar o Power BI (pré-visualização)
description: Saiba como usar o PowerShell para registar e digitalizar um inquilino power BI em Azure Purview.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553506"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Use PowerShell para registar e digitalizar Power BI em Azure Purview (pré-visualização) 

Este artigo mostra como usar o PowerShell para configurar uma digitalização de um inquilino do Power BI num catálogo da Azure Purview.

## <a name="power-bi-authentication-background"></a>Fundo de autenticação do Power BI

O catálogo De Purview deve ligar-se à Power BI Admin API para digitalizar artefactos num inquilino do Power BI. A Power BI ADMIN API suporta atualmente dois tipos de autenticação:

- Identidade gerida (MSI).
- Autenticação delegada do utilizador.

> [!Note]
> Recomenda-se a MSI, a menos que seja necessária a autenticação delegada do utilizador.

## <a name="create-a-security-group"></a>Criar um grupo de segurança

Cada catálogo de Purview tem a sua própria identidade gerida atribuída ao sistema que deve ter acesso ao inquilino power BI para permitir a digitalização. O nome do catálogo pode ser usado para encontrar a sua identidade no portal Azure.

1. No [portal Azure,](https://portal.azure.com)procure o Azure Ative Directory.
1. Crie um novo grupo de segurança no seu Azure Ative Directory, seguindo [criar um grupo básico e adicionar membros usando o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Pode saltar este passo se já tiver um grupo de segurança para usar.

1. Certifique-se de selecionar a Segurança como **o Tipo de Grupo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo de grupo de segurança":::

1. Adicione a identidade gerida do seu catálogo a este grupo de segurança selecionando membros e, em seguida, **+ Adicionar os membros**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adicione a instância gerida do catálogo ao grupo":::

1. Procure o seu catálogo e selecione-o.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Adicione catálogo procurando-o":::

1. Deve ver uma notificação de sucesso mostrando que foi adicionada.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Adicione o sucesso do CATÁLOGO MSI":::

## <a name="associate-the-security-group-with-power-bi"></a>Associe o grupo de segurança ao Power BI

1. Inicie sessão no [portal de administração Power BI](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Apecimos esta bandeira de característica:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Esta bandeira permite a funcionalidade que lhe permite associar o seu grupo de segurança. Por exemplo,

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Você precisa ser um Power BI Admin para ver a página de configurações do inquilino.

1. **Selecione as definições do Desenvolvedor** Permita que os  >  **principais de serviço utilizem APIs de POWER APENAS DE LEITURA (Pré-visualização)**.
1. Selecione **grupos de segurança específicos.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Imagem mostrando como permitir que os diretores de serviço obtenham permissões de API de administração de power bi apenas de leitura ":::

    > [!Caution]
    > Quando permite que o grupo de segurança que criou (que tem o seu catálogo de dados gerido identidade como membro) utilize apis de administração de power bi apenas de leitura, também lhe permite aceder aos metadados (por exemplo, dashboard e reportar nomes, proprietários, descrições, etc.) para todos os seus artefactos Power BI neste inquilino. Uma vez que os metadados foram puxados para o Azure Purview, as permissões do Purview, não permissões power BI, determinam quem pode ver esses metadados.

    > [!Note]
    > Pode remover o grupo de segurança das definições do seu programador, mas os metadados anteriormente extraídos não serão removidos da conta ''' '' '' Pode eliminá-lo separadamente.

## <a name="register-power-bi-and-set-up-a-scan"></a>Registar Power BI e configurar uma varredura

Agora que deu permissão ao catálogo para se ligar à ADMIN API do seu inquilino Power BI, precisa de configurar a sua digitalização no catálogo. Para isso, configura-se e executou um guião PowerShell.

1. Faça o download e extrate os cmdlets ADC PowerShell.
1. Configure o seu script fornecendo valores para as atribuições no topo do script.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > Deve ser um contribuinte ou proprietário na subscrição sob a qual dirige os comandos.

1. Inicie a sua digitalização executando o seguinte script:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Registar e digitalizar Power BI

O método de autenticação recomendado é MSI. No entanto, para digitalizar um inquilino do Power BI que está num inquilino Azure diferente do seu catálogo, você usa a autenticação delegada.

Para fazer a autenticação delegada, tem de ter credenciais de administração do utilizador, bem como credenciais de administração Power BI. Você também deve criar uma app Azure e conceder-lhe power BI Tenant.ReadAll permissões.

1. Navegue [para o portal Azure](https://portal.azure.com) e procure **registos de aplicações.**

1. A partir de **inscrições de Aplicações,** selecione **+ Novo registo.**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Imagem mostrando como criar um novo registo de aplicações Azure":::

1. Insira um nome para a sua aplicação.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="registar nova app":::

1. Assim que a sua aplicação for criada, selecione **permissões API**, e depois **+ Adicionar uma permissão**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Imagem mostrando como adicionar permissão à app":::

1. Selecione **Power BI Service** on Request **API permissões**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Imagem mostrando como selecionar o serviço PBI":::

1. Selecione **permissões delegadas** e **Inquilino.Read.All**. Em seguida, **selecione Adicionar permissões**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Imagem mostrando como solicitar permissões de API":::

1. Selecione **o consentimento de administração grant**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Imagem mostrando como conceder consentimento administrativo":::

1. Copie o ID do **Application (cliente)** e os valores de ID do **Diretório (inquilino).**  Usará estes valores quando configurar a sua tomografia.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Imagem mostrando a cópia do cliente e iDs do inquilino":::

1. Configure a sua digitalização no PowerShell. O guião vai pedir credenciais. Deve ter pelo menos função de administrador de função e fonte de dados de purview na subscrição Azure que pretende utilizar.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Verifique a sua tomografia.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Passos seguintes

Para começar com o Azure Purview, consulte [Quickstart: Criar uma conta Azure Purview](create-catalog-portal.md).
