---
title: Parceiro comercial do Marketplace e atribuição de uso do cliente
description: Obtenha uma visão geral do rastreio do uso do cliente para soluções Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: 9c70f8d728786e8aff8da33f9a39b8c2cfaafdc4
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295631"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Parceiro comercial do Marketplace e atribuição de uso do cliente

A atribuição de utilização do cliente é um método para associar os recursos da Azure em execução em subscrições de clientes, implantados para executar a sua solução, consigo como parceiro. A formação destas associações em sistemas internos da Microsoft traz uma maior visibilidade à pegada Azure que executa o seu software. Quando adota esta capacidade de rastreio, alinha-se com as equipas de vendas da Microsoft e ganha crédito para os programas parceiros da Microsoft.

Pode formar a associação via Azure Marketplace, o repositório Quickstart, repositórios privados do GitHub e 1:1 de envolvimento com os clientes que criam IP durável (como o desenvolvimento de uma app).

A atribuição de utilização do cliente suporta três opções de implementação:

- Modelos do Gestor de Recursos Azure: Os parceiros podem usar modelos de Gestor de Recursos para implementar os serviços Azure para executar o software do parceiro. Os parceiros podem criar um modelo de Gestor de Recursos para definir a infraestrutura e configuração da sua solução Azure. Um modelo de Gestor de Recursos permite que você e os seus clientes implementem a sua solução ao longo do seu ciclo de vida. Pode estar confiante de que os seus recursos são implantados num estado consistente.
- APIs: Os parceiros podem ligar diretamente para as APIs do Gestor de Recursos para implementar um modelo de Gestor de Recursos ou para gerar as chamadas API para a prestação direta de serviços Azure.
- Terraform: Os parceiros podem utilizar o Terraform para implementar um modelo de Gestor de Recursos ou implementar diretamente serviços Azure.

>[!IMPORTANT]
>- A atribuição de utilização do cliente não se destina a acompanhar o trabalho de integradores de sistemas, prestadores de serviços geridos ou ferramentas concebidas para implementar e gerir software em execução no Azure.
>
>- A atribuição de utilização do cliente destina-se a novas implementações e NÃO suporta a marcação de recursos existentes que já foram implementados.
>
>- A atribuição de utilização do cliente é necessária para ofertas [da Azure Application](./partner-center-portal/create-new-azure-apps-offer.md) publicadas no Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um identificador de referência único que tem 32 dígitos hexadémicos. Para criar GUIDs para rastreio, deve utilizar um gerador GUID. A equipa de Armazenamento Azure criou um [formulário de gerador GUID](https://aka.ms/StoragePartners) que lhe enviará um GUID do formato correto e pode ser reutilizado através dos diferentes sistemas de rastreio.

> [!NOTE]
> É altamente recomendável que utilize [o formulário de gerador GUID do Azure Storage](https://aka.ms/StoragePartners) para criar o seu GUID. Para mais informações, consulte as nossas [FAQ.](#faq)

Recomendamos que crie um GUID único para cada canal de oferta e distribuição para cada produto. Pode optar por utilizar um único GUID para os múltiplos canais de distribuição do produto se não quiser que o relatório seja dividido.

Se implementar um produto utilizando um modelo e estiver disponível tanto no Azure Marketplace como no GitHub, pode criar e registar dois GUIDS distintos:

- Produto A no Mercado Azure
- Produto A no GitHub

A reportagem é feita pela Microsoft Partner Network ID e PELA GUID.

Também pode acompanhar o uso a um nível mais granular, registando GUIDs adicionais e alterando GUIDs entre planos, onde os planos são variantes de uma oferta.

## <a name="register-guids"></a>Registar GUIDs

Os GUIDs devem estar registados no Partner Center para permitir a atribuição de utilização do cliente.

Depois de adicionar um GUID ao seu modelo ou ao agente do utilizador, e registar o GUID no Partner Center, as futuras implementações são rastreadas.

> [!NOTE]
> Se estiver a publicar a sua oferta [de Aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md) para o Mercado Azure através do Partner Center, qualquer novo GUID utilizado dentro do seu modelo será automaticamente registado no perfil do Centro de Parceiros quando o modelo estiver carregado.  

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard).

1. Inscreva-se como [editor de mercado comercial.](https://aka.ms/JoinMarketplace)

   * Os parceiros são obrigados a [ter um perfil no Partner Center.](https://docs.microsoft.com/azure/marketplace/become-publisher) Você é encorajado a listar a oferta no Azure Marketplace ou AppSource.
   * Os parceiros podem registar vários GUIDs.
   * Os parceiros podem registar GUIDs para modelos e ofertas de soluções não-marketplace.

1. No canto superior direito, selecione o ícone de engrenagem de definições e, em seguida, selecione **as definições do Desenvolvedor**.

1. Na **página de definições**de conta, selecione **Add Tracking GUID.**

1. Na caixa **GUID,** insira o seu tracking GUID. Introduza apenas o GUID sem o **prefixo pid.** Na caixa **Descrição,** insira o nome ou descrição da sua oferta.

1. Para registar mais do que um GUID, **selecione Add Tracking GUID** novamente. Caixas adicionais aparecem na página.

1. Selecione **Guardar**.

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager
Muitas soluções parceiras são implementadas usando modelos de Gestor de Recursos Azure. Se tiver um modelo de Gestor de Recursos disponível no Azure Marketplace, no GitHub ou como um Quickstart, o processo para modificar o seu modelo para permitir a atribuição de utilização do cliente é diretamente para a frente.

> [!NOTE]
> Para obter mais informações sobre a criação e publicação de modelos de solução, consulte
> * [Crie e implemente o seu primeiro modelo de Gestor de Recursos.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)
>* [Oferta de Aplicação Azure](./partner-center-portal/create-new-azure-apps-offer.md).
>* Vídeo: [Modelos de solução de construção e aplicações geridas para o Mercado Azure](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Para adicionar um identificador globalmente único (GUID), faça uma única modificação no ficheiro do modelo principal:

1. [Crie um GUID](#create-guids) utilizando o método sugerido e [registe o GUID](#register-guids).

1. Abra o modelo de Gestor de Recursos.

1. Adicione um novo recurso no ficheiro do modelo principal. O recurso precisa de estar apenas no ficheiro **mainTemplate.json** ou **azuredeploy.json,** e não em nenhum modelo aninhado ou ligado.

1. Introduza o valor GUID após o **pid-prefixo** (por exemplo, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Verifique se o modelo é de eventuais erros.

1. Republicar o modelo nos repositórios apropriados.

1. [Verifique o sucesso do GUID na implementação do modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Código de modelo do gestor de recursos da amostra

Para ativar os recursos de rastreio do seu modelo, precisa adicionar o seguinte recurso adicional na secção de recursos. Por favor, certifique-se de modificar o código de amostra abaixo com as suas próprias entradas quando o adicionar ao ficheiro do modelo principal.
O recurso precisa de ser adicionado apenas no ficheiro **mainTemplate.json** ou **azuredeploy.json,** e não em modelos aninhados ou ligados.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
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

## <a name="use-the-resource-manager-apis"></a>Utilize as APIs do Gestor de Recursos

Em alguns casos, você pode preferir fazer chamadas diretamente contra as APIs do Gestor de Recursos REST para implantar serviços Azure. [O Azure suporta vários SDKs](https://docs.microsoft.com/azure/?pivot=sdkstools) para ativar estas chamadas. Pode utilizar um dos SDKs ou ligar diretamente para as APIs REST para mobilizar recursos.

Se estiver a utilizar um modelo de Gestor de Recursos, deverá marcar a sua solução seguindo as instruções descritas anteriormente. Se não estiver a usar um modelo de Gestor de Recursos e a fazer chamadas diretas de API, ainda pode marcar a sua implementação para associar o uso dos recursos Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marque uma implementação com as APIs do Gestor de Recursos

Para ativar a atribuição de utilização do cliente, quando conceber as suas chamadas API, inclua um GUIADO no cabeçalho do agente do utilizador no pedido. Adicione o GUID para cada oferta ou SKU. Formatar a cadeia com o prefixo **pid** e incluir o GUID gerado pelo parceiro. Aqui está um exemplo do formato GUID para inserção no agente do utilizador:

![Exemplo de formato GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> O formato da corda é importante. Se o prefixo **pid** não estiver incluído, não é possível consultar os dados. Diferentes SDKs seguem diferente. Para implementar este método, reveja a abordagem de suporte e rastreio para o seu Azure SDK preferido.

#### <a name="example-the-python-sdk"></a>Exemplo: The Python SDK

Para Python, use o atributo **config.** Só pode adicionar o atributo a um UserAgent. Eis um exemplo:

![Adicione o atributo a um agente de utilizador](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Adicione o atributo para cada cliente. Não há nenhuma configuração estática global. Pode marcar uma fábrica de clientes para ter a certeza que todos os clientes estão a seguir o rasto. Para mais informações, consulte esta [amostra de fábrica de clientes no GitHub.](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marque uma implementação utilizando o Azure PowerShell

Se utilizar recursos através da Azure PowerShell, apedte o seu GUID utilizando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marque uma implantação utilizando o CLI Azure

Quando utilizar o CLI Azure para anexar o seu GUID, desaprote o **AZURE_HTTP_USER_AGENT** variável ambiente. Pode definir esta variável no âmbito de um script. Também pode definir a variável globalmente para o âmbito da concha:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Para mais informações, consulte [Azure SDK for Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Utilizar Terraform

O suporte para a Terraform está disponível através da versão 1.21.0 do Azure Provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019) .  Este suporte aplica-se a todos os parceiros que implementem a sua solução via Terraform, bem como a todos os recursos mobilizados e medidos pelo Azure Provider (versão 1.21.0 ou posterior).

O fornecedor Azure para a Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) que é onde especifica o tracking GUID que utiliza para a sua solução. O valor deste campo também pode ser obtido a partir da *Variável ambiente ARM_PARTNER_ID.*

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Os parceiros que pretendam obter a sua implementação via Terraform rastreados pela atribuição de utilização do cliente precisam de fazer o seguinte:

* Criar um GUID (o GUID deve ser adicionado para cada Oferta ou SKU)
* Atualizar o seu Fornecedor Azure para definir o valor de *partner_id* ao GUID (NÃO pré-fixe o GUID com "pid-", basta defini-lo para o verdadeiro GUID)


## <a name="verify-the-guid-deployment"></a>Verifique a implementação DO GUID

Depois de modificar o seu modelo e executar uma implementação de teste, use o seguinte script PowerShell para recuperar os recursos que implementou e marcou.

Pode utilizar o script para verificar se o GUID é adicionado com sucesso ao seu modelo de Gestor de Recursos. O script não se aplica às implementações de API ou Terraform do Gestor de Recursos.

Inicie sessão no Azure. Selecione a subscrição com a implementação que pretende verificar antes de executar o script. Execute o script no contexto de subscrição da implementação.

São necessários parâmetros orientados para o **grupo GUID** e **recursos** da implantação.

Podes obter [o guião original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Relatório

Pode encontrar o relatório para a atribuição de utilização do cliente no painel de instrumentos do Partner Center [https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure) (). Para ver o relatório, tem de usar as credenciais do Centro de Parceiros para iniciar sinsumento. Se encontrar algum problema com relatório ou inscrição, crie um pedido de apoio seguindo a instrução na secção de suporte Get.

Escolha o Modelo Rastreado na lista de dropdown do Tipo de Associação de Parceiros para ver o relatório.

![Relatório para atribuição de utilização do cliente](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Notifique os seus clientes

Os parceiros devem informar os seus clientes sobre as implementações que utilizam a atribuição de uso do cliente. A Microsoft reporta o uso do Azure que está associado a estas implementações ao parceiro. Os exemplos que se seguem incluem conteúdo que pode utilizar para notificar os seus clientes sobre estas implementações. Nos exemplos, \<PARTNER> substitua-o pelo nome da empresa. Os parceiros devem certificar-se de que a notificação está alinhada com as suas políticas de privacidade e recolha de dados, incluindo opções para que os clientes sejam excluídos do rastreio.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implementações de modelos do Gestor de Recursos

Ao implementar este modelo, a Microsoft é capaz de identificar a instalação de \<PARTNER> software com os recursos Azure que são implantados. A Microsoft é capaz de correlacionar os recursos Azure que são usados para suportar o software. A Microsoft recolhe estas informações para fornecer as melhores experiências com os seus produtos e operar os seus negócios. Os dados são recolhidos e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter .

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implementações de SDK ou API

Quando implementa \<PARTNER> software, a Microsoft é capaz de identificar a instalação de \<PARTNER> software com os recursos Azure que são implantados. A Microsoft é capaz de correlacionar os recursos Azure que são usados para suportar o software. A Microsoft recolhe estas informações para fornecer as melhores experiências com os seus produtos e operar os seus negócios. Os dados são recolhidos e regidos pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter .

## <a name="get-support"></a>Obter suporte

Existem dois canais de suporte dependendo das questões que está a enfrentar.

Se encontrar algum problema no Centro de Parceiros, como ver o relatório de atribuição de uso do cliente ou iniciar sessão, crie um pedido de apoio com a equipa de apoio do Centro de Parceiros aqui:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se necessitar de assistência para o Marketplace Onboarding e/ou atribuição de utilização do cliente em geral, como como configurar a atribuição de utilização do cliente, siga os passos abaixo:

1. Vá à [página de suporte.](https://go.microsoft.com/fwlink/?linkid=844975)

1. No **tipo Problema**, selecione Marketplace **Onboarding**.

1. Escolha a **categoria** para o seu problema:

   - Para questões de associação de utilização, selecione **Outros**.
   - Para problemas de acesso com o Mercado Azure, selecione **Access Problem**.

     ![Escolha a categoria de emissão](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **Pedido de Início**.

1. Na página seguinte, insira os valores necessários. Selecione **Continuar**.

1. Na página seguinte, insira os valores necessários.

   > [!IMPORTANT]
   > Na caixa **de título incidente,** insira o **rastreio de utilização ISV.** Descreva o seu problema em detalhe.

   ![Insira o rastreio de utilização ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Preencha o formulário e, em seguida, **selecione Enviar por isso**.

Também pode receber orientação técnica de um Consultor Técnico de Parceiros da Microsoft para cenários técnicos de pré-venda, implementação e desenvolvimento de aplicações para compreender e incorporar a atribuição de uso do cliente.

### <a name="how-to-submit-a-technical-consultation-request"></a>Como apresentar um pedido de consulta técnica

1. Visite [os Serviços Técnicos do Parceiro.](https://aka.ms/TechnicalJourney)
1. Selecione cloud infraestrutura e gestão, e uma nova página será aberta para você ver a viagem técnica.
1. Nos Serviços de Implantação, clique no botão Enviar um botão de pedido
1. Inscreva-se utilizando a sua MSA (conta MPN) ou a sua AAD (conta Partner Dashboard); com base nas suas credenciais de inscrição, um formulário de pedido on-line será aberto:
    * Complete/reveja as informações de contacto.
    * Os detalhes da consulta podem ser pré-povoados ou selecionar a partir das desistências.
    * Introduza um título e a descrição do problema (forneça o máximo de detalhes possível).
1. Clique em Submeter

Consulte instruções passo a passo com imagens na [Utilização de Pré-Venda Técnica e Serviços de Implantação](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Passos seguintes

Será contactado por um Consultor Técnico de Parceiros da Microsoft para configurar uma chamada para a scope das suas necessidades.

## <a name="faq"></a>FAQ

**Qual é o benefício de adicionar o GUID ao modelo?**

A Microsoft fornece aos parceiros uma visão das implementações dos clientes das suas soluções e insights sobre a sua utilização influenciada. Tanto a Microsoft como o parceiro podem usar esta informação para impulsionar um maior envolvimento entre as equipas de vendas. Tanto a Microsoft como o parceiro podem usar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure.

**Depois de um GUID ser adicionado, pode ser alterado?**

Sim, um cliente ou parceiro de implementação pode personalizar o modelo e pode alterar ou remover o GUID. Sugerimos que os parceiros descrevam proativamente o papel do recurso e da GUID aos seus clientes e parceiros para impedir a remoção ou edições para o GUID. A alteração do GUID afeta apenas novas, não existentes, implantações e recursos.

**Posso rastrear modelos implantados a partir de um repositório não-Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo estiver implantado, a utilização é rastreada. Os sócios ainda devem registar os seus GUIDs.

**O cliente também recebe relatórios?**

Os clientes podem acompanhar a sua utilização de recursos individuais ou grupos de recursos definidos pelo cliente dentro do portal Azure. Os clientes não vêem o uso interrompido pela GUID.

**Esta metodologia é semelhante ao Parceiro Digital da Record (DPOR)?**

Este novo método de ligação da implantação e utilização à solução de um parceiro fornece um mecanismo para ligar uma solução de parceiro à utilização do Azure. A DPOR destina-se a associar um parceiro de consultoria (Systems Integrator) ou de gestão (Managed Service Provider) com a assinatura Azure de um cliente.

**Qual é o benefício de usar o formulário guid generator da Azure Storage?**

O formulário GUID Generator da Azure Storage é garantido para gerar um GUID do formato necessário. Além disso, se estiver a utilizar algum dos métodos de rastreio de planos de dados do Azure Storage, pode aproveitar o mesmo GUID para o rastreio de planos de controlo do Mercado. Isto permite-lhe alavancar um GUID unificado único para a atribuição de parceiros sem ter de manter o GUIDS separado.

**Posso usar um VHD personalizado e privado para uma oferta de modelo de solução no Azure Marketplace?**

Não, não pode. A imagem da máquina virtual deve vir do Mercado Azure, consulte: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) .

Você pode criar uma oferta VM no mercado usando o seu VHD personalizado e marcá-lo como Privado para que ninguém possa vê-lo. Em seguida, refira-se a este VM no seu modelo de solução.

**Falha na atualização *de conteúdoVersion* propriedade para o modelo principal?**

Provavelmente um bug em alguns casos quando o modelo está sendo implementado usando um TemplateLink de outro modelo que espera conteúdo mais antigoVersão por alguma razão. A solução é utilizar a propriedade de metadados:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
