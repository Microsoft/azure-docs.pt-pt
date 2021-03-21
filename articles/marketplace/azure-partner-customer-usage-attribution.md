---
title: Atribuição de utilização do cliente Azure
description: Obtenha uma visão geral do rastreio do uso do cliente para aplicações Azure no mercado comercial e outros IP implantáveis desenvolvidos por parceiros.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: cpercy737
ms.author: camper
ms.date: 03/09/2021
ms.custom: devx-track-terraform
ms.openlocfilehash: 8f84d77b5a424d5f7273c7e748c35c52882819c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102608398"
---
# <a name="azure-customer-usage-attribution"></a>Atribuição de utilização do cliente Azure

A atribuição de uso do cliente associa o uso dos recursos Azure em subscrições de clientes criadas enquanto implementa o seu IP consigo como parceiro. A formação destas associações em sistemas internos da Microsoft traz uma maior visibilidade à pegada Azure que executa o seu software. Para [as ofertas da Azure Application no mercado comercial,](#commercial-marketplace-azure-apps)esta capacidade de rastreio ajuda-o a alinhar-se com as equipas de vendas da Microsoft e a ganhar crédito para os programas parceiros da Microsoft.

A atribuição de utilização do cliente suporta três opções de implementação:

1. Modelos de Gestor de Recursos Azure (os fundamentos comuns das aplicações Azure, também referidos no mercado comercial como "modelos de solução" ou "apps geridas"): os parceiros criam modelos de Gestor de Recursos para definir a infraestrutura e configuração das suas soluções Azure. Um modelo de Gestor de Recursos permite que os seus clientes implementem os recursos da sua solução num estado consistente e repetível.
1. APIs do Gestor de Recursos Azure: os parceiros podem ligar para as APIs do Gestor de Recursos para implementar um modelo de Gestor de Recursos ou providenciar serviços Azure diretamente.
1. Terraform: os parceiros podem usar o Terraform para implementar um modelo de Gestor de Recursos ou implementar diretamente serviços Azure.

Existem casos de utilização secundária para atribuição de uso do cliente fora do mercado comercial [descritos mais tarde neste artigo](#other-use-cases).

>[!IMPORTANT]
>- A atribuição de utilização do cliente não se destina a acompanhar o trabalho de integradores de sistemas, prestadores de serviços geridos ou ferramentas concebidas principalmente para implantar e gerir recursos Azure.
>- A atribuição de utilização do cliente é para novas implementações e não suporta recursos de rastreio que já foram implementados.
>- Nem todos os serviços da Azure são compatíveis com a atribuição de utilização do cliente. Os Azure Kubernetes Services (AKS) e vm Scale Sets têm problemas conhecidos que causam sub-reportagem de utilização.

## <a name="commercial-marketplace-azure-apps"></a>Aplicativos Azure de marketplace comercial

O rastreio do uso do Azure a partir de aplicações Azure publicadas no mercado comercial é em grande parte automático. Quando carregar um modelo de Gestor de Recursos como parte da [configuração técnica do plano da sua aplicação marketplace Azure,](https://docs.microsoft.com/azure/marketplace/create-new-azure-apps-offer-solution#define-the-technical-configuration)o Partner Center adicionará um ID de rastreio legível pelo Azure Resource Manager.

Se utilizar APIs do Gestor de Recursos Azure, terá de adicionar o seu ID de rastreio pelas [instruções abaixo](#use-resource-manager-apis) para o passar ao Azure Resource Manager à medida que o seu código implementa recursos. Este ID é visível no Partner Center na página de Configuração Técnica do seu plano. 

> [!NOTE]
> Para as aplicações Azure existentes, foi realizada uma migração única em março de 2021 para atualizar os IDs de rastreio na configuração técnica de cada plano. O uso de implementações passadas dessas ofertas continuará a ser rastreado nos sistemas da Microsoft.

## <a name="other-use-cases"></a>Outros casos de uso 

Pode utilizar a atribuição de utilização do cliente para acompanhar a utilização do Azure de soluções não disponíveis no mercado comercial. Estas soluções geralmente residem no repositório Quickstart, repositórios privados do GitHub, ou provêm de 1:1 compromissos com clientes que criam IP durável (como uma app implantável e escalável).

São necessários vários passos manuais:

1. Crie um ou mais GUIDs para usar como iDs de rastreio.
1. Registe os GUIDs no Partner Center.
1. Adicione os seus GUIDs registados à sua aplicação Azure e/ou cordas de agente de utilizador.

### <a name="create-guids"></a>Criar GUIDs

Ao contrário dos IDs de rastreio que o Partner Center cria em seu nome para aplicações Azure no mercado comercial, outras utilizações da CUA exigem que você crie um GUID para usar como iD de rastreamento. Um GUID é um identificador de referência único que tem 32 dígitos hexadémicos. Para criar GUIDs para rastreio, deve utilizar um gerador GUID, por exemplo, via PowerShell:

```powershell
[guid]::NewGuid()
```

Deve criar um GUID único para cada canal de distribuição e produto. Pode utilizar um único GUID para os múltiplos canais de distribuição de um produto se não quiser que o relatório seja dividido. O relatório ocorre por Microsoft Partner Network ID e GUID.

### <a name="register-guids"></a>Registar GUIDs

Os GUIDs devem ser registados em seguida no Partner Center para que possam ser associados a si como parceiro:

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard).

1. Inscreva-se como [editor de mercado comercial.](https://aka.ms/JoinMarketplace)

1. Selecione **Definições** (ícone de engrenagem) no canto superior direito e, em seguida, **as definições de Conta**.

1. **Selecione**  >  **os identificadores de** perfil da Organização Adicionar Tracking  >  **GUID**.

1. Na caixa **GUID,** insira o seu tracking GUID. Introduza apenas o GUID sem o `pid-` prefixo. Na caixa **Descrição,** insira o nome ou descrição da sua solução.

1. Para registar mais do que um GUID, **selecione Add Tracking GUID** novamente. Caixas adicionais aparecem na página.

1. Selecione **Guardar**.

### <a name="add-a-guid-to-a-resource-manager-template"></a>Adicione um GUID a um modelo de Gestor de Recursos

Para adicionar o seu GUID registado a um modelo de Gestor de Recursos, faça uma única modificação no ficheiro do modelo principal:

1. Abra o modelo de Gestor de Recursos.

1. Adicione um novo recurso do tipo [Microsoft.Resources/implementações](/azure/templates/microsoft.resources/deployments) no ficheiro de modelo principal. O recurso tem de estar no **mainTemplate.jsapenas em** ficheiro ou **azuredeploy.js,** não em nenhum modelo aninhado ou ligado.

1. Introduza o valor GUID após o `pid-` prefixo como o nome do recurso. Por exemplo, se o GUID for eb7927c8-dd66-43e1-b0cf-c346a422063, o nome de recurso será **pid-eb7927c8-dd66-43e1-b0cf-c346a422063**. Exemplo:
 
```json
{ // add this resource to the resources section in the mainTemplate.json
    "apiVersion": "2020-06-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```
4. Verifique se o modelo tem erros.

1. Republicar o modelo nos repositórios apropriados.

1. [Verifique o sucesso do GUID na implementação do modelo](#verify-deployments-tracked-with-a-guid).

> [!TIP]
> Para obter mais informações sobre a criação e publicação de modelos de Gestor de Recursos, consulte: [crie e implemente o seu primeiro modelo de Gestor de Recursos](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="verify-deployments-tracked-with-a-guid"></a>Verifique as implementações rastreadas com um GUID

Depois de modificar o seu modelo e executar uma implementação de teste, use o seguinte script PowerShell para recuperar os recursos que implementou e marcou.

Pode utilizar o script para verificar se o GUID é adicionado com sucesso ao seu modelo de Gestor de Recursos. O script não se aplica às implementações de API ou Terraform do Gestor de Recursos.

Inicie sessão no Azure. Selecione a subscrição com a implementação que pretende verificar antes de executar o script. Execute o script no contexto de subscrição da implementação.

São  necessários parâmetros orientados (abaixo chamados "nome de implantação") e o nome **do nome do Nome do Grupo** de Recursos da implantação.

Podes obter [o guião original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1) no GitHub.

```powershell
Param(
    [string][Parameter(Mandatory=$true)]$deploymentName, # the full name of the deployment, e.g. pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the named deployment
$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name "$deploymentName").correlationId

# Find all deployments with that correlationId
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in all deployments with that correlationId as PowerShell doesn't surface outputResources on the deployment or correlationId on the deploymentOperation

foreach ($deployment in $deployments){
    # Get deploymentOperations by deploymentName
    # then the resourceIds for each resource
    ($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.targetResource -notlike "*Microsoft.Resources/deployments*"}).TargetResource
}
```

### <a name="notify-your-customers"></a>Notifique os seus clientes

Os parceiros devem informar os seus clientes sobre as implementações que utilizam a atribuição de uso do cliente. A Microsoft reporta o uso do Azure associado a estas implementações ao parceiro. Os exemplos que se seguem incluem conteúdo que pode utilizar para notificar os seus clientes sobre estas implementações. Nos exemplos, \<PARTNER> substitua-o pelo nome da empresa. Os parceiros devem garantir que a notificação está alinhada com as suas políticas de privacidade e recolha de dados, incluindo opções para que os clientes sejam excluídos do rastreio.

#### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implementações de modelos do Gestor de Recursos

Quando implementar este modelo, a Microsoft pode identificar a instalação de \<PARTNER> software com os recursos Azure implantados. A Microsoft pode correlacionar estes recursos utilizados para suportar o software. A Microsoft recolhe estas informações para fornecer as melhores experiências com os seus produtos e operar os seus negócios. Os dados são recolhidos e regidos pelas políticas de privacidade da Microsoft, localizadas em [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

#### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implementações de SDK ou API

Quando implementa \<PARTNER> software, a Microsoft pode identificar a instalação de \<PARTNER> software com os recursos Azure implantados. A Microsoft pode correlacionar estes recursos utilizados para suportar o software. A Microsoft recolhe estas informações para fornecer as melhores experiências com os seus produtos e operar os seus negócios. Os dados são recolhidos e regidos pelas políticas de privacidade da Microsoft, localizadas em [https://www.microsoft.com/trustcenter](https://www.microsoft.com/trustcenter) .

## <a name="use-resource-manager-apis"></a>Use Recursos Gestor APIs

Em alguns casos, pode escrutinar diretamente as APIs do Gestor de Recursos PARA implantar os serviços Azure. [O Azure suporta vários SDKs](../index.yml?pivot=sdkstools) para ativar estas chamadas. Pode utilizar um dos SDKs ou ligar diretamente para as APIs REST para mobilizar recursos.

Para ativar a atribuição de utilização do cliente, quando conceber as suas chamadas API, inclua o seu ID de rastreio no cabeçalho do agente do utilizador no pedido. Formatar a cadeia com o `pid-` prefixo. Exemplos:

```xml
//Commercial Marketplace Azure app
pid-contoso-myoffer-partnercenter //copy the tracking ID exactly as it appears in Partner Center

//Other use cases
pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4 //enter your GUID after "pid-"
```
> [!IMPORTANT]
> Se estiver a utilizar APIs do Gestor de Recursos com uma aplicação Azure no mercado comercial, utilize o ID de rastreio fornecido no Partner Center. NÃO utilize um GUID.

Vários SDKs interagem com as APIs do Gestor de Recursos de forma diferente e exigirão algumas diferenças no seu código. Os exemplos abaixo apresentam a abordagem de mercado não comercial usando um GUID e abrangem uma variedade dos SDKs Azure mais populares.

#### <a name="example-python-sdk"></a>Exemplo: Python SDK

Para Python, use o atributo **config.** Só pode adicionar o atributo a um UserAgent. Exemplo:

```python
client = azure.mgmt.servicebus.ServiceBusManagementClient(**parameters)
client.config.add_user_agent("pid-b6addd8f-5ff4-4fc0-a2b5-0ec7861106c4")
```
> [!IMPORTANT]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Pode marcar uma fábrica de clientes para garantir que todos os clientes estão a seguir o rasto. Para mais informações, consulte esta [amostra de fábrica de clientes no GitHub.](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

#### <a name="example-net-sdk"></a>Exemplo: .NET SDK

Para .NET, certifique-se de definir o agente do utilizador. Utilize a biblioteca [Microsoft.Azure.Management.Fluent](/dotnet/api/microsoft.azure.management.fluent) para definir o agente do utilizador com o seguinte código (exemplo em C#):

```csharp
var azure = Microsoft.Azure.Management.Fluent.Azure
    .Configure()
    // Add your pid in the user agent header
    .WithUserAgent("pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", String.Empty) 
    .Authenticate(/* Credentials created via Microsoft.Azure.Management.ResourceManager.Fluent.SdkContext.AzureCredentialsFactory */)
    .WithSubscription("<subscription ID>");
```

#### <a name="example-azure-powershell"></a>Exemplo: Azure PowerShell

Se utilizar recursos através do Azure PowerShell, apedte o seu GUID utilizando este método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="example-azure-cli"></a>Exemplo: Azure CLI

Quando utilizar o CLI Azure para anexar o seu GUID, desaprote a variável ambiente **AZURE_HTTP_USER_AGENT** no âmbito de um script. Também pode definir a variável globalmente para o âmbito da concha:

```powershell
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```

Para mais informações, consulte [Azure SDK for Go](/azure/developer/go/).

## <a name="use-terraform"></a>Utilizar Terraform

O suporte para a Terraform está disponível através da versão 1.21.0 do Azure Provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) . Isto aplica-se a todos os parceiros que implementem a sua solução através da Terraform e todos os recursos utilizados e medidos pelo Azure Provider (versão 1.21.0 ou posterior).

O fornecedor Azure para a Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) para especificar o guiado de rastreio utilizado para a sua solução. O valor deste campo também pode ser obtido a partir da *Variável ambiente ARM_PARTNER_ID.*

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Descreva o valor da *partner_id* para um GUID registado. NÃO prefixe o GUID com "pid-", basta fixá-lo para o verdadeiro GUID.

> [!IMPORTANT]
> Se estiver a utilizar a Terraform com uma aplicação Azure no mercado comercial, utilize todo o ID de rastreio fornecido no Partner Center. NÃO utilize um GUID.

## <a name="get-support"></a>Obter suporte

Conheça as opções de suporte no mercado comercial [no Support for the commercial marketplace program in Partner Center.](support.md)

### <a name="how-to-submit-a-technical-consultation-request"></a>Como apresentar um pedido de consulta técnica

1. Visite [os Serviços Técnicos do Parceiro.](https://aka.ms/TechnicalJourney)
1. Selecione **infraestrutura e gestão cloud** para ver a viagem técnica.
1. Selecione **Serviços de Implantação**  >  **Enviar um pedido**.
1. Inscreva-se utilizando a sua MSA (conta MPN) ou a sua AAD (conta Partner Dashboard).
1. Complete/reveja as informações de contacto no formulário que abre. Os detalhes da consulta podem ser pré-povoados ou você pode ter opções de abandono.
1. Introduza um título e uma descrição detalhada do problema.
1. Selecione **Submeter**.

Consulte instruções passo a passo com imagens na [Utilização de Pré-Venda Técnica e Serviços de Implantação](https://aka.ms/TechConsultInstructions).

Será contactado por um Consultor Técnico de Parceiros da Microsoft para configurar uma chamada para a scope das suas necessidades.

## <a name="report"></a>Relatório
A reportagem para o uso do Azure rastreada através da atribuição de utilização do cliente não está disponível hoje para os parceiros ISV. A adição de reporte ao Programa de Marketplace Comercial no Partner Center para cobrir a atribuição de utilização do cliente é direcionada para o segundo semestre de 2021.

## <a name="faq"></a>FAQ

#### <a name="after-a-tracking-id-is-added-can-it-be-changed"></a>Depois de ser adicionado um ID de rastreio, pode ser alterado?

Os IDs de rastreio para aplicações Azure no mercado comercial são geridos automaticamente pelo Partner Center. No entanto, um cliente pode descarregar um modelo e alterar ou remover o ID de rastreio. Os parceiros devem descrever proactivamente o papel do ID de rastreio aos seus clientes para impedir a remoção ou edições. A alteração do ID de rastreio afeta apenas novas implementações e recursos, não os existentes.

#### <a name="can-i-track-templates-deployed-from-a-non-microsoft-repository-like-github"></a>Posso rastrear modelos implantados a partir de um repositório não-Microsoft como o GitHub?

Sim, desde que o ID de rastreio esteja presente quando o modelo estiver implantado, o uso é rastreado. Para manter a associação entre si como editor e o seu modelo implantado a partir de um repositório não-Microsoft, primeiro descarregue uma cópia do seu modelo publicado (que conterá o ID de rastreio) da listagem de marketplace comercial da sua oferta no portal Azure. Publique esta versão no GitHub ou noutro repositório não-Microsoft.

Se o seu modelo não estiver listado no mercado comercial e incluir um GUID registado, certifique-se de que o GUID está presente na versão que publica para o GitHub ou outro repositório não-Microsoft.

#### <a name="does-the-customer-receive-reporting-as-well"></a>O cliente também recebe relatórios?

N.º Os clientes podem acompanhar o seu uso de todos os recursos ou grupos de recursos dentro do portal Azure. Os clientes não vêem o uso interrompido pelo ID de rastreio cua.

#### <a name="is-customer-usage-attribution-similar-to-the-digital-partner-of-record-dpor-or-partner-admin-link-pal"></a>A atribuição de utilização do cliente é semelhante ao parceiro digital de registo (DPOR) ou link de administração de parceiros (PAL)?

A atribuição de utilização do cliente é um mecanismo para associar o uso do Azure ao IP repetível e implantável de um parceiro - formando a associação no momento da implementação. A DPOR e a PAL destinam-se a associar um parceiro de consultoria (Systems Integrator) ou de gestão (Managed Service Provider) com a pegada Azure relevante de um cliente durante o tempo em que o parceiro está envolvido com o cliente.