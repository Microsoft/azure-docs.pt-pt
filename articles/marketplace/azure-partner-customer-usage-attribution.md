---
title: Atribuição de parceiro sacial do Mercado Comercial e da atribuição do uso do cliente
description: Obtenha uma visão geral do rastreio do uso do cliente para soluções Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: dsindona
ms.openlocfilehash: 5fec72ce5f86c1bee9ec0e978e458f5be454c8e3
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256591"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Atribuição de parceiro sacial do Mercado Comercial e da atribuição do uso do cliente

A atribuição do uso do cliente é um método para associar recursos Azure a executar em subscrições de clientes, implementados para executar a sua solução, consigo como parceiro. A formação destas associações em sistemas internos da Microsoft traz maior visibilidade à pegada Azure que executa o seu software. Ao adotar esta capacidade de rastreio, alinha-se com as equipas de vendas da Microsoft e ganha crédito pelos programas de parceiros da Microsoft.

Pode formar a associação via Azure Marketplace, o repositório Quickstart, os repositórios privados do GitHub e os compromissos com o cliente 1:1 que criam IP durável (como o desenvolvimento de uma app).

A atribuição de utilização do cliente suporta três opções de implementação:

- Modelos de Gestor de Recursos Azure: Os parceiros podem usar modelos de Gestor de Recursos para implementar os serviços Azure para executar o software do parceiro. Os parceiros podem criar um modelo de Gestor de Recursos para definir a infraestrutura e configuração da sua solução Azure. Um modelo de Gestor de Recursos permite que você e seus clientes implementem a sua solução ao longo do seu ciclo de vida. Pode estar confiante de que os seus recursos estão implantados num estado consistente.
- ApIs, Gestor de Recursos Azure: Os parceiros podem ligar diretamente para o Gestor de Recursos APIs para implementar um modelo de Gestor de Recursos ou para gerar as chamadas da API para fornecer diretamente serviços Azure.
- Terraform: Os parceiros podem usar a Terraform para implementar um modelo de Gestor de Recursos ou implementar diretamente os serviços Azure.

>[!IMPORTANT]
>- A atribuição do uso do cliente não se destina a acompanhar o trabalho dos integradores de sistemas, dos prestadores de serviços geridos ou das ferramentas concebidas para implementar e gerir o software em funcionamento no Azure.
>
>- A atribuição do uso do cliente é para novas implementações e NÃO suporta a marcação de recursos existentes que já foram implementados.
>
>- A atribuição de utilização do cliente é necessária para as ofertas da [Aplicação Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer) publicadas no Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um identificador de referência único que tem 32 dígitos hexadecimais. Para criar GUIDs para rastreio, deve utilizar um gerador GUID. A equipa de Armazenamento Azure criou um [formulário de gerador GUIA](https://aka.ms/StoragePartners) que lhe enviará um GUIA do formato correto e pode ser reutilizado nos diferentes sistemas de rastreio.

> [!NOTE]
> É altamente recomendável que utilize o [formulário de gerador GUID do Azure Storage](https://aka.ms/StoragePartners) para criar o seu GUID. Para mais informações, consulte as [nossas FAQ.](#faq)

Recomendamos que crie um GUID único para cada canal de oferta e distribuição para cada produto. Pode optar por utilizar um único GUID para os múltiplos canais de distribuição do produto se não quiser que o relatório seja dividido.

Se implementar um produto utilizando um modelo e estiver disponível tanto no Azure Marketplace como no GitHub, pode criar e registar dois GUIDS distintos:

- Produto A no Mercado Azure
- Produto A no GitHub

O reporte é feito pelo Microsoft Partner Network ID e PELO GUID.

Também pode rastrear o uso a um nível mais granular, registando GUIDs adicionais e alterando GUIDs entre planos, onde os planos são variantes de uma oferta.

## <a name="register-guids"></a>Registar GUIDs

Os GUIDs devem ser registados no Partner Center para permitir a atribuição do uso do cliente.

Depois de adicionar um GUID ao seu modelo ou no agente utilizador, e registar o GUID no Partner Center, futuras implementações são rastreadas.

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard).

1. Inscreva-se como editor de [marketplace comercial.](https://aka.ms/JoinMarketplace)

   * Os parceiros são obrigados a [ter um perfil no Partner Center.](https://docs.microsoft.com/azure/marketplace/become-publisher) É encorajado a listar a oferta no Azure Marketplace ou appSource.
   * Os parceiros podem registar vários GUIDs.
   * Os parceiros podem registar GUIDs para modelos e ofertas de solução não-marketplace.

1. No canto superior direito, selecione o ícone de engrenagem de definições e, em seguida, selecione **as definições do Desenvolvedor**.

1. Na página de **definições da Conta,** selecione **Adicionar Guia de Rastreio.**

1. Na caixa **GUID,** introduza o seu GUIA de rastreio. Introduza apenas o GUID sem o **prefixo pid.** Na caixa **Descrição,** insira o nome ou descrição da sua oferta.

1. Para registar mais de um GUID, **selecione Adicionar** Guia de Rastreio novamente. Caixas adicionais aparecem na página.

1. Selecione **Guardar**.

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager
Muitas soluções parceiras são implementadas usando modelos de Gestor de Recursos Azure. Se tiver um modelo de Gestor de Recursos disponível no Mercado Azure, no GitHub, ou como Quickstart, o processo de modificar o seu modelo para permitir a atribuição de utilização do cliente é direto para a frente.

> [!NOTE]
> Para mais informações sobre a criação e publicação de modelos de soluções, consulte
> * [Crie e implemente o seu primeiro modelo](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)de Gestor de Recursos.
>* [Oferta de aplicação Azure.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer)
>* Vídeo: Modelos de [solução de construção e aplicações geridas para o Mercado Azure.](https://channel9.msdn.com/Events/Build/2018/BRK3603)


Para adicionar um identificador globalmente único (GUID), efaz uma única modificação no ficheiro do modelo principal:

1. [Crie um GUID](#create-guids) utilizando o método sugerido e [registe o GUID](#register-guids).

1. Abra o modelo de Gestor de Recursos.

1. Adicione um novo recurso no ficheiro do modelo principal. O recurso precisa estar apenas no ficheiro **principal Template.json** ou **azuredeploy.json,** e não em quaisquer modelos aninhados ou ligados.

1. Introduza o valor GUID após o **prefixo pid** (por exemplo, pid-eb7927c8-dd66-43e1-b0cf-c346a42063).

1. Verifique se há erros no modelo.

1. Republique o modelo nos repositórios apropriados.

1. [Verifique o sucesso do GUID na implementação do modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Código de modelo do Gestor de Recursos da amostra

Para permitir o rastreio de recursos para o seu modelo, precisa adicionar o seguinte recurso adicional sob a secção de recursos. Por favor, certifique-se de modificar o código de amostra abaixo com as suas próprias inputs quando o adicionar ao ficheiro principal do modelo.
O recurso precisa de ser adicionado apenas no ficheiro **principal Template.json** ou **azuredeploy.json,** e não em quaisquer modelos aninhados ou ligados.

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

Em alguns casos, você pode preferir fazer chamadas diretamente contra o Gestor de Recursos REST APIs para implementar serviços Azure. [O Azure suporta vários SDKs](https://docs.microsoft.com/azure/?pivot=sdkstools) para ativar estas chamadas. Pode utilizar um dos SDKs ou ligar diretamente para as APIs rest para implantar recursos.

Se estiver a utilizar um modelo de Gestor de Recursos, deve marcar a sua solução seguindo as instruções descritas anteriormente. Se não estiver a usar um modelo de Gestor de Recursos e a fazer chamadas diretas de API, ainda pode marcar a sua implementação para associar o uso dos recursos Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marque uma implementação com as APIs do Gestor de Recursos

Para permitir a atribuição de utilização do cliente, quando conceber as suas chamadas API, inclua um GUID no cabeçalho do agente utilizador no pedido. Adicione o GUID para cada oferta ou SKU. Forforte a corda com o **prefixo pid** e inclua o GUID gerado pelo parceiro. Aqui está um exemplo do formato GUID para inserção no agente utilizador:

![Formato GUIA de exemplo](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> O formato da corda é importante. Se o **prefixo pid** não estiver incluído, não é possível consultar os dados. Diferentes SDKs seguem de forma diferente. Para implementar este método, reveja a abordagem de suporte e rastreio do seu SDK Azure preferido.

#### <a name="example-the-python-sdk"></a>Exemplo: O Python SDK

Para Python, use o atributo de **config.** Só pode adicionar o atributo a um UserAgent. Segue-se um exemplo:

![Adicione o atributo a um agente utilizador](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Adicione o atributo para cada cliente. Não há configuração estática global. Pode marcar uma fábrica de clientes para ter certeza que todos os clientes estão a seguir. Para mais informações, consulte esta amostra de [fábrica de clientes no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marque uma implementação utilizando o Azure PowerShell

Se utilizar recursos através do Azure PowerShell, apreenda o seu GUID utilizando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marque uma implantação utilizando o Azure CLI

Quando utilizar o CLI Azure para anexar o seu GUID, detete a **variável ambiente AZURE_HTTP_USER_AGENT.** Pode definir esta variável no âmbito de um script. Também pode definir a variável globalmente para o âmbito da concha:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Para mais informações, consulte [Azure SDK para Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Usar terraforma

O suporte à Terraform está disponível através do lançamento 1.21.0 da Azure Provider: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Este apoio aplica-se a todos os parceiros que implementem a sua solução via Terraform, bem como a todos os recursos utilizados e medidos pelo Fornecedor Azure (versão 1.21.0 ou posterior).

O fornecedor Azure para a Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) que é onde especifica o GUIA de rastreio que utiliza para a sua solução. O valor deste campo também pode ser obtido a partir da *Variável ambiente ARM_PARTNER_ID.*

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Os parceiros que pretendam obter a sua implantação via Terraform rastreado pela atribuição de uso do cliente precisam fazer o seguinte:

* Criar um GUID (o GUID deve ser adicionado para cada Oferta ou SKU)
* Atualize o seu Fornecedor Azure para definir o valor de *partner_id* ao GUID (NÃO pré-fixar o GUID com "pid-", basta defini-lo para o GUIA real)


## <a name="verify-the-guid-deployment"></a>Verifique a implantação guid

Depois de modificar o seu modelo e executar uma implementação de teste, use o seguinte script PowerShell para recuperar os recursos que implementou e marcou.

Pode utilizar o script para verificar se o GUID é adicionado com sucesso ao seu modelo de Gestor de Recursos. O script não se aplica às implementações de API ou Terraform do Gestor de Recursos.

Inicie sessão no Azure. Selecione a subscrição com a implementação que pretende verificar antes de executar o script. Executar o script dentro do contexto de subscrição da implementação.

Os parâmetros **GUIA** e **recursosGroup** da implantação são parâmetros necessários.

Pode [sacá-lo original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no GitHub.

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

Pode encontrar o relatório para atribuição de utilização[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)do cliente no seu painel de instrumentos do Partner Center (). Para ver o relatório, tem de usar as credenciais do Centro parceiro para iniciar sessão. Se encontrar algum problema com relatório ou iniciar sessão, crie um pedido de apoio seguindo a instrução na secção de suporte Get.

Escolha o modelo rastreado na lista de dropdown do Partner Association Type para ver o relatório.

![Relatório para atribuição de uso do cliente](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Notifique os seus clientes

Os parceiros devem informar os seus clientes sobre as implementações que utilizam a atribuição do uso do cliente. A Microsoft informa o uso do Azure que está associado a estas implementações ao parceiro. Os seguintes exemplos incluem conteúdo que pode utilizar para notificar os seus clientes sobre estas implementações. Nos exemplos, \<substitua o PARTNER> pelo nome da sua empresa. Os parceiros devem certificar-se de que a notificação está alinhada com as suas políticas de privacidade e recolha de dados, incluindo opções para os clientes serem excluídos do rastreio.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implementações de modelos de gestor de recursos

Ao implementar este modelo, a Microsoft é \<capaz de identificar a instalação do software PARTNER> com os recursos Azure que são implementados. A Microsoft é capaz de correlacionar os recursos Do Azure que são usados para suportar o software. A Microsoft recolhe estas informações para fornecer as melhores experiências com os seus produtos e para operar os seus negócios. Os dados são recolhidos e regidos pelas políticas de https://www.microsoft.com/trustcenterprivacidade da Microsoft, que podem ser encontradas em .

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implementações De SDK ou API

Quando implementa \<o partner> software, a \<Microsoft é capaz de identificar a instalação de software PARTNER> com os recursos Azure que são implementados. A Microsoft é capaz de correlacionar os recursos Do Azure que são usados para suportar o software. A Microsoft recolhe estas informações para fornecer as melhores experiências com os seus produtos e para operar os seus negócios. Os dados são recolhidos e regidos pelas políticas de https://www.microsoft.com/trustcenterprivacidade da Microsoft, que podem ser encontradas em .

## <a name="get-support"></a>Obter suporte

Existem dois canais de apoio dependendo das questões que está a enfrentar.

Se encontrar algum problema no Partner Center, como ver o relatório de atribuição de uso do cliente ou iniciar sessão, crie um pedido de apoio com a equipa de suporte do Partner Center aqui:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se necessitar de assistência para o Marketplace Onboarding e/ou atribuição de utilização do cliente em geral, como como configurar a atribuição de utilização do cliente, siga os passos abaixo:

1. Vá para a página de [apoio.](https://go.microsoft.com/fwlink/?linkid=844975)

1. Sob **o tipo de problema,** selecione Marketplace **Onboarding**.

1. Escolha a **categoria** para o seu problema:

   - Para problemas de associação de uso, selecione **Outros**.
   - Para problemas de acesso com o Mercado Azure, selecione Problema de **Acesso**.

     ![Escolha a categoria de emissão](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **Pedido de Início**.

1. Na página seguinte, introduza os valores necessários. Selecione **Continuar**.

1. Na página seguinte, introduza os valores necessários.

   > [!IMPORTANT]
   > Na caixa de **títulos Incident,** introduza o **Rastreio de Utilização ISV**. Descreva o seu problema em detalhe.

   ![Insira o rastreio de utilização do ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Complete o formulário e, em seguida, selecione **Submeter**.

Também pode receber orientação técnica de um Consultor Técnico do Microsoft Partner para cenários técnicos de pré-vendas, implementação e desenvolvimento de aplicações para compreender e incorporar a atribuição de utilização do cliente.

### <a name="how-to-submit-a-technical-consultation-request"></a>Como apresentar um pedido de consulta técnica

1. Visite [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Selecione infraestrutura e gestão cloud, e uma nova página será aberta para você ver a viagem técnica.
1. Em Serviços de Implantação, clique no botão enviar um pedido
1. Inscreva-se na utilização da sua Conta MSA (conta MPN) ou da sua AAD (conta Partner Dashboard); com base nas suas credenciais de inscrição, será aberto um formulário de pedido online:
    * Complete/reveja as informações de contacto.
    * Os detalhes da consulta podem ser pré-povoados ou selecionados a partir das quedas.
    * Introduza um título e a descrição do problema (forneça o máximo de detalhes possível).
1. Clique em Submeter

Ver instruções passo a passo com [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)imagens em .

### <a name="whats-next"></a>Passos seguintes

Será contactado por um Consultor Técnico do Microsoft Partner para configurar uma chamada para examinar as suas necessidades.

## <a name="faq"></a>FAQ

**Qual é o benefício de adicionar o GUID ao modelo?**

A Microsoft fornece aos parceiros uma visão das implementações dos clientes das suas soluções e insights sobre o seu uso influenciado. Tanto a Microsoft como o parceiro podem usar estas informações para impulsionar um envolvimento mais estreito entre as equipas de vendas. Tanto a Microsoft como o parceiro podem utilizar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure.

**Depois de um GUID ser adicionado, pode ser alterado?**

Sim, um cliente ou parceiro de implementação pode personalizar o modelo e pode alterar ou remover o GUID. Sugerimos que os parceiros descrevam proativamente o papel do recurso e do GUID aos seus clientes e parceiros para impedir a remoção ou edificação para o GUID. A alteração do GUID afeta apenas novas, não existentes, implantações e recursos.

**Posso rastrear modelos implantados a partir de um repositório não-Microsoft como o GitHub?**

Sim, desde que o GUID esteja presente quando o modelo for implantado, o uso é rastreado. Os parceiros devem continuar a registar os seus GUIDs.

**O cliente também recebe reportagens?**

Os clientes podem acompanhar a sua utilização de recursos individuais ou grupos de recursos definidos pelo cliente dentro do portal Azure. Os clientes não vêem o uso desfeito pelo GUID.

**Esta metodologia é semelhante ao Parceiro Digital da Record (DPOR)?**

Este novo método de ligação da implementação e utilização à solução de um parceiro fornece um mecanismo para ligar uma solução parceira à utilização do Azure. A DPOR destina-se a associar um parceiro de consultoria (Integrador de Sistemas) ou de gestão (Fornecedor de Serviços Geridos) com a subscrição do Azure de um cliente.

**Qual é o benefício de usar o formulário guid generator do Azure Storage?**

O formulário GUID Generator da Azure Storage é garantido para gerar um GUID do formato necessário. Além disso, se estiver a utilizar algum dos métodos de rastreio de planos de dados do Azure Storage, pode aproveitar o mesmo GUID para o rastreio de planos de controlo do Mercado. Isto permite-lhe alavancar um GUID unificado para atribuição de parceiros sem ter que manter GUIDS separados.

**Posso usar um VHD personalizado e privado para uma oferta de modelo de solução no Mercado Azure?**

Não, não pode. A imagem virtual da máquina deve vir [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)do Mercado Azure, ver: .

Você pode criar uma oferta VM no mercado usando o seu VHD personalizado e marcá-lo como Privado para que ninguém possa vê-lo. Em seguida, refira-se a este VM no seu modelo de solução.

**Falhou em atualizar a propriedade de *conteúdoVersão* para o modelo principal?**

Provavelmente um bug em alguns casos quando o modelo está sendo implementado usando um TemplateLink de outro modelo que espera conteúdo mais antigoVersão por alguma razão. A suver é utilizar a propriedade de metadados:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
