---
title: Configure BYOS (Traga o seu próprio armazenamento) para profiler & Snapshot Debugger
description: Configure BYOS (Traga o seu próprio armazenamento) para profiler & Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589675"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Configure Traga o seu próprio armazenamento (BYOS) para perfis de aplicações e debugger snapshot

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>O que é Bring Your Own Storage (BYOS) e por que posso precisar? 
Quando utiliza o Profiler Application Insights ou o Snapshot Debugger, os artefactos gerados pela sua aplicação são enviados para contas de armazenamento Azure através da Internet pública. Estas contas são pagas e controladas pela Microsoft para processamento e análise. A Microsoft controla as políticas de encriptação em repouso e gestão vitalícia para esses artefactos.

Com o Bring Your Own Storage, estes artefactos são enviados para uma conta de armazenamento que controla. Isso significa que controla a política de encriptação em repouso, a política de gestão vitalícia e o acesso à rede. No entanto, será responsável pelos custos associados a essa conta de armazenamento.

> [!NOTE]
> Se estiver a ativar o Link Privado, o Bring Your Own Storage é um requisito. Para obter mais informações sobre o Link Privado para Insights de Aplicações, [consulte a documentação.](../logs/private-link-security.md)
>
> Se estiver a ativar Customer-Managed Chaves, o Bring Your Own Storage é um requisito. Para obter mais informações sobre Customer-Managed Chaves para Insights de Aplicações, [consulte a documentação.](../logs/customer-managed-keys.md)

## <a name="how-will-my-storage-account-be-accessed"></a>Como é que a minha conta de armazenamento vai ser acedida?
1. Os agentes que executam as suas Máquinas Virtuais ou Serviço de Aplicações irão enviar artefactos (perfis, instantâneos e símbolos) para contentores de bolhas na sua conta. Este processo envolve contactar o profiler de insights de aplicações ou o serviço Snapshot Debugger para obter um token SAS (Shared Access Signature) para uma nova bolha na sua conta de armazenamento.
1. O perfil de insights de aplicação ou o serviço Snapshot Debugger analisarão a bolha de entrada e escreverão os resultados da análise e registarão ficheiros no armazenamento de bolhas. Dependendo da capacidade de computação disponível, este processo pode ocorrer a qualquer momento após o upload.
1. Quando visualizar os vestígios do perfil, ou a análise do depurador instantâneo, o serviço irá buscar os resultados da análise do armazenamento de bolhas.

## <a name="prerequisites"></a>Pré-requisitos
* Certifique-se de criar a sua Conta de Armazenamento no mesmo local que o seu Recurso de Insights de Aplicação. Ex. Se o seu recurso Application Insights estiver no West US 2, a sua Conta de Armazenamento também deve estar no West US 2. 
* Conceda a função "Storage Blob Data Contributor" à aplicação AAD "Serviços de Diagnóstico Acesso de Armazenamento Fidedignos" na sua conta de armazenamento através do UI do Controlo de Acesso (IAM).
* Se o Private Link estiver ativado, configufique a definição adicional para permitir a ligação ao nosso Serviço Microsoft Fidedigno a partir da sua Rede Virtual. 

## <a name="how-to-enable-byos"></a>Como ativar o BYOS

### <a name="create-storage-account"></a>Criar a Conta de Armazenamento
Crie uma conta de armazenamento novinha em folha (se não a tiver) no mesmo local que o seu recurso Application Insights.
Se o seu recurso 'Insights' de Aplicação estiver `West US 2` ligado, então a sua Conta de Armazenamento deve estar em `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Conceder acesso a serviços de diagnóstico à sua conta de armazenamento
Uma conta de armazenamento BYOS será ligada a um recurso Application Insights. Pode haver apenas uma conta de armazenamento por recurso Application Insights e ambos devem estar no mesmo local. Pode utilizar a mesma conta de armazenamento com mais de um recurso Application Insights.

Em primeiro lugar, o Profiler Application Insights e o serviço Snapshot Debugger têm de ter acesso à conta de armazenamento. Para conceder acesso, adicione a função `Storage Blob Data Contributor` à aplicação AAD nomeada `Diagnostic Services Trusted Storage Access` através da página Access Control (IAM) na sua conta de armazenamento, como mostra a Figura 1.0. 

Passos: 
1. Clique no botão "Adicionar" na secção "Adicionar uma atribuição de funções" 
1. Selecione a função "Storage Blob Data Contributor" 
1. Selecione "Azure AD user, group or service principal" na secção "Atribuir acesso a" 
1. Procure & selecione a aplicação "Diagnostic Services Trusted Storage Access" 
1. Guardar alterações

_![ Figura 1.0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _Figura 1.0_ 

Depois de ter adicionado o papel, aparecerá na secção "Atribuições de Funções", como a figura 1.1 abaixo. 
_![ Figura 1.1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _Figura 1.1_ 

Se também estiver a utilizar o Private Link, é necessária uma configuração adicional para permitir a ligação ao nosso Serviço Microsoft Fidedigno a partir da sua Rede Virtual. Consulte a [documentação de segurança da rede de armazenamento.](../../storage/common/storage-network-security.md#trusted-microsoft-services)

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Ligue a sua Conta de Armazenamento com o seu recurso Application Insights
Para configurar o BYOS para diagnósticos de nível de código (Profiler/Debugger), existem três opções:

* Usando cmdlets Azure PowerShell
* Utilizando interface de linha de comando Azure (CLI)
* Com o modelo do Azure Resource Manager

#### <a name="configure-using-azure-powershell-cmdlets"></a>Configurar usando cmdlets Azure PowerShell

1. Certifique-se de que instalou aZ PowerShell 4.2.0 ou superior.

    Para instalar a Azure PowerShell, consulte a [documentação Oficial Azure PowerShell](/powershell/azure/install-az-ps).

1. Instale a extensão PowerShell do Insights de Aplicação.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Inscreva-se com a sua Conta Azure
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Para obter mais informações sobre como iniciar scontabilidade, consulte a [documentação Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

1. Remova a Conta de Armazenamento anterior ligada ao seu recurso Application Insights.

    Padrão:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Exemplo:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Conecte a sua Conta de Armazenamento com o seu recurso Application Insights.
    
    Padrão:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Exemplo:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Configure usando Azure CLI

1. Certifique-se de que instalou o Azure CLI.

    Para instalar o Azure CLI, consulte a documentação oficial do [Azure CLI](/cli/azure/install-azure-cli).

1. Instale a extensão CLI do Application Insights.
    ```powershell
    az extension add -n application-insights
    ```

1. Conecte a sua Conta de Armazenamento com o seu recurso Application Insights.

    Padrão:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Exemplo:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Resultado esperado:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Para realizar atualizações nas contas de armazenamento ligadas ao seu recurso Application Insights, consulte a [documentação CLI](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)do Application Insights .

#### <a name="configure-using-azure-resource-manager-template"></a>Configurar usando o modelo do Gestor de Recursos Azure

1. Crie um ficheiro de modelo do Azure Resource Manager com o seguinte conteúdo (byos.template.jsligado).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Executar o seguinte comando PowerShell para implementar o modelo anterior (criar conta de armazenamento ligado).

    Padrão:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Exemplo:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Forneça os seguintes parâmetros quando solicitados na consola PowerShell:
    
    |           Parâmetro           |                                Descrição                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | O nome do recurso Application Insights para ativar a BYOS.            |
    | storage_account_name          | O nome do recurso 'Conta de Armazenamento' que utilizará como BYOS. |
    
    Resultado esperado:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Ativar diagnósticos de nível de código (Profiler/Debugger) sobre a carga de trabalho de interesse através do portal Azure. (Serviço de aplicações > Insights de Aplicação) _![ Figura 2.0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _Figura 2.0_

## <a name="troubleshooting"></a>Resolução de problemas
### <a name="template-schema-schema_uri-isnt-supported"></a>O esquema do modelo '{schema_uri}' não é suportado.
* Certifique-se de que a `$schema` propriedade do modelo é válida. Deve seguir o seguinte padrão: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Certifique-se de que `schema_version` o modelo está dentro de valores válidos: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Mensagem de erro:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Nenhum fornecedor de recursos registado foi encontrado para a localização '{location}'.
* Certifique-se de que `apiVersion` o recurso é `microsoft.insights/components` `2015-05-01` .
* Certifique-se de que `apiVersion` o recurso é `linkedStorageAccount` `2020-03-01-preview` .
    Mensagem de erro:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>A localização da conta de armazenamento deve coincidir com a localização do componente de IA.
* Certifique-se de que a localização do recurso Application Insights é a mesma que a Conta de Armazenamento.
    Mensagem de erro:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Para a resolução geral de problemas do Profiler, consulte a documentação de [resolução de problemas](profiler-troubleshooting.md)do Profiler .

Para a resolução geral de problemas do Snapshot Debugger, consulte a [documentação de resolução de problemas do Snapshot Debugger](snapshot-debugger-troubleshoot.md). 

## <a name="faqs"></a>FAQs
* Se eu tiver profiler ou Snapshot ativado, e então eu habilifiquei BYOS, os meus dados serão migrados para a minha Conta de Armazenamento?
    _Não, não vai._

* A BYOS trabalhará com a Encriptação em Repouso e Customer-Managed Chave?
    _Sim, para ser mais preciso, a BYOS é um requisito para ter o profiler/debugger habilitado com Customer-Manager Keys._

* A BYOS funcionará num ambiente isolado da Internet?
    _Sim. De facto, a BYOS é um requisito para cenários isolados de rede._

* A BYOS funcionará quando, ambos, Customer-Managed Keys e Private Link foram ativados? 
    _Sim, pode ser possível._

* Se eu tiver ativado BYOS, posso voltar a usar contas de armazenamento de Serviços de Diagnóstico para armazenar os meus dados recolhidos? 
    _Sim, pode, mas, neste momento, não apoiamos a migração de dados do seu BYOS._

* Depois de permitir a BYOS, assumirei todos os custos conexos do mesmo, que são armazenamento e networking? 
    _Sim_
