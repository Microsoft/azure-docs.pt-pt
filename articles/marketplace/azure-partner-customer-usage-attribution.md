---
title: Parceiro do Azure e atribuição de uso do cliente | Azure Marketplace
description: Visão geral de como controlar o uso do cliente para soluções do Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: pabutler
ms.openlocfilehash: 0bbe0e9718a25fdff76e591bf388bf829518502e
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105618"
---
# <a name="azure-partner-customer-usage-attribution"></a>Atribuição da utilização do cliente de parceiro do Azure

Como um parceiro de software do Azure, suas soluções exigem componentes do Azure ou precisam ser implantados diretamente na infraestrutura do Azure. Os clientes que implantam uma solução de parceiro e provisionar seus próprios recursos do Azure podem achar difícil obter visibilidade do status da implantação e obter uma fibra óptica no impacto no crescimento do Azure. Ao adicionar um nível mais alto de visibilidade, você se alinha com as equipes de vendas da Microsoft e tem crédito pelos programas de parceiros da Microsoft.

A Microsoft agora oferece um método para ajudar os parceiros a acompanharem melhor o uso do Azure de implantações de clientes de seus softwares no Azure. O novo método usa Azure Resource Manager para orquestrar a implantação dos serviços do Azure.

Como um parceiro da Microsoft, você pode associar o uso do Azure a qualquer recurso do Azure que você provisionar em nome de um cliente. Você pode formar a associação por meio do Azure Marketplace, do repositório de início rápido, dos repositórios GitHub privados e do compromisso de um cliente único. A atribuição de uso do cliente dá suporte a três opções de implantação:

- Modelos de Azure Resource Manager: Os parceiros podem usar modelos do Resource Manager para implantar os serviços do Azure para executar o software do parceiro. Os parceiros podem criar um modelo do Resource Manager para definir a infraestrutura e a configuração de sua solução do Azure. Um modelo do Resource Manager permite que você e seus clientes implantem sua solução durante todo o ciclo de vida. Você pode ter certeza de que seus recursos são implantados em um estado consistente.
- APIs de Azure Resource Manager: Os parceiros podem chamar as APIs do Gerenciador de recursos diretamente para implantar um modelo do Resource Manager ou para gerar as chamadas de API para provisionar diretamente os serviços do Azure.
- Terraform Os parceiros podem usar o Orchestrator de nuvem como o Terraform para implantar um modelo do Resource Manager ou implantar diretamente os serviços do Azure.

A atribuição de uso do cliente destina-se à nova implantação e não dá suporte à marcação de recursos existentes que já foram implantados.

A atribuição de uso do cliente é necessária na [aplicativo Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): oferta de modelo de solução publicada no Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager
Muitas soluções de parceiros são implantadas na assinatura de um cliente usando modelos do Resource Manager. Se você tiver um modelo do Resource Manager que está disponível no Azure Marketplace, no GitHub ou como um guia de início rápido, o processo para modificar o modelo para habilitar a atribuição de uso do cliente deve ser direto para frente.

Para obter mais informações sobre como criar e publicar modelos de solução, consulte

* [Crie e implante seu primeiro modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Oferta de aplicativo Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Vídeo: [Criação de modelos de solução e aplicativos gerenciados para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Adicionar um GUID ao seu modelo

Para adicionar um GUID (identificador global exclusivo), faça uma única modificação no arquivo de modelo principal:

1. [Crie um GUID](#create-guids) usando o método sugerido e [Registre o GUID](#register-guids-and-offers).

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso no arquivo de modelo principal. O recurso precisa estar somente no arquivo **MainTemplate. JSON** ou **azuredeploy. JSON** , e não em nenhum modelo aninhado ou vinculado.

1. Insira o valor de GUID após o prefixo **pid** (por exemplo, PID-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Verifique se há erros no modelo.

1. Republique o modelo nos repositórios apropriados.

1. [Verifique o êxito do GUID na implantação do modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Código de modelo do Resource Manager de exemplo

Para habilitar os recursos de acompanhamento do modelo, você precisa adicionar o recurso adicional a seguir na seção recursos. Certifique-se de modificar o código de exemplo abaixo com suas próprias entradas ao adicioná-lo ao arquivo de modelo principal.
O recurso precisa ser adicionado somente no arquivo **MainTemplate. JSON** ou **azuredeploy. JSON** , e não em nenhum modelo aninhado ou vinculado.

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

## <a name="use-the-resource-manager-apis"></a>Usar as APIs do Gerenciador de recursos

Em alguns casos, talvez você prefira fazer chamadas diretamente em relação às APIs REST do Gerenciador de recursos para implantar os serviços do Azure. O [Azure dá suporte a vários SDKs](https://docs.microsoft.com/azure/#pivot=sdkstools) para habilitar essas chamadas. Você pode usar um dos SDKs ou chamar as APIs REST diretamente para implantar recursos.

Se você estiver usando um modelo do Resource Manager, deverá marcar sua solução seguindo as instruções descritas anteriormente. Se você não estiver usando um modelo do Resource Manager e fizer chamadas de API diretas, você ainda poderá marcar sua implantação para associar o uso de recursos do Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marcar uma implantação com as APIs do Gerenciador de recursos

Para habilitar a atribuição de uso do cliente, quando você cria suas chamadas à API, inclua um GUID no cabeçalho do agente do usuário na solicitação. Adicione o GUID para cada oferta ou SKU. Formate a cadeia de caracteres com o **pid-** prefix e inclua o GUID gerado pelo parceiro. Aqui está um exemplo do formato de GUID para inserção no agente do usuário:

![Exemplo de formato de GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> O formato da cadeia de caracteres é importante. Se o prefixo **pid** não estiver incluído, não será possível consultar os dados. SDKs diferentes acompanham diferentemente. Para implementar esse método, examine a abordagem de suporte e acompanhamento para seu SDK do Azure preferido.

#### <a name="example-the-python-sdk"></a>Exemplo: O SDK do Python

Para Python, use o atributo **config** . Você só pode adicionar o atributo a um UserAgent. Segue-se um exemplo:

![Adicionar o atributo a um agente do usuário](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Adicione o atributo para cada cliente. Não há configuração estática global. Você pode marcar uma fábrica de cliente para ter certeza de que cada cliente está rastreando. Para obter mais informações, consulte este [exemplo de fábrica de cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marcar uma implantação usando o Azure PowerShell

Se você implantar recursos por meio do Azure PowerShell, acrescente o GUID usando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marcar uma implantação usando o CLI do Azure

Ao usar o CLI do Azure para acrescentar o GUID, defina a variável de ambiente **AZURE_HTTP_USER_AGENT** . Você pode definir essa variável dentro do escopo de um script. Você também pode definir a variável globalmente para o escopo do Shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Para obter mais informações, consulte [SDK do Azure para linguagem go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Usar Terraform

O suporte para Terraform está disponível por meio da versão 1.21.0 do provedor [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019)do Azure:.  Esse suporte se aplica a todos os parceiros que implantam sua solução via Terraform e todos os recursos implantados e medidos pelo provedor do Azure (versão 1.21.0 ou posterior).

O provedor do Azure para Terraform adicionou um novo campo opcional chamado [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) , que é onde você especifica o GUID de rastreamento que você usa para sua solução. O valor desse campo também pode ser originado da variável de ambiente *ARM_PARTNER_ID* .

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Os parceiros que desejam fazer sua implantação por meio do Terraform acompanhado pela atribuição de uso do cliente precisam fazer o seguinte:

* Criar um GUID (o GUID deve ser adicionado para cada oferta ou SKU)
* Atualize seu provedor do Azure para definir o valor de *partner_id* para o GUID (não corrija o GUID com "PID-", basta defini-lo como o GUID real)

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um número de referência exclusivo que tem 32 dígitos hexadecimais. Para criar GUIDs para acompanhamento, você deve usar um gerador de GUID. A equipe de armazenamento do Azure criou um [formulário de gerador de GUID](https://aka.ms/StoragePartners) que enviará um email a um GUID do formato correto e poderá ser reutilizado nos diferentes sistemas de controle.

> [!Note]
> É altamente recomendável que você use o [formulário gerador de GUID do armazenamento do Azure](https://aka.ms/StoragePartners) para criar seu GUID. Para obter mais informações, consulte nossas [perguntas frequentes](#faq).

Recomendamos que você crie um GUID exclusivo para cada oferta e canal de distribuição para cada produto. Você pode optar por usar um único GUID para vários canais de distribuição do produto se não quiser que os relatórios sejam divididos.

Se você implantar um produto usando um modelo e ele estiver disponível no Azure Marketplace e no GitHub, você poderá criar e registrar 2 GUIDs distintos:

*   Produto A no Azure Marketplace
*   Produto A no GitHub

O relatório é feito pelo valor do parceiro (ID do parceiro da Microsoft) e pelos GUIDs.

Você também pode acompanhar os GUIDs em um nível mais granular, como o SKU, em que os SKUs são variantes de uma oferta.

## <a name="register-guids-and-offers"></a>Registrar GUIDs e ofertas

Os GUIDs devem ser registrados para habilitar a atribuição de uso do cliente.

Todos os registros de GUIDs de modelo são feitos no Partner Center.

Depois de adicionar o GUID ao modelo ou no agente do usuário, e registrar o GUID no Partner Center, todas as implantações serão rastreadas.

1. Inscreva-se como um [Editor do Marketplace comercial](https://aka.ms/JoinMarketplace).

   * Os parceiros precisam [ter um perfil no Partner Center](https://docs.microsoft.com/azure/marketplace/become-publisher). Você é incentivado a listar a oferta no Azure Marketplace ou no AppSource.
   * Os parceiros podem registrar vários GUIDs.
   * Os parceiros podem registrar um GUID para os modelos e ofertas de solução que não são do Marketplace.

1. Entre no [Partner Center](https://partner.microsoft.com/dashboard).

1. No canto superior direito, selecione o ícone de engrenagem configurações e, em seguida, selecione **configurações do desenvolvedor**.

1. Na **página Configurações de conta**, selecione **Adicionar GUID de acompanhamento.**

1. Na caixa **GUID** , insira seu GUID de rastreamento. Insira apenas o GUID sem o prefixo **pid** . Na caixa **Descrição** , insira o nome ou a descrição da oferta.

1. Para registrar mais de um GUID, selecione **Adicionar GUID de acompanhamento** novamente. Caixas adicionais aparecem na página.

1. Selecione **Guardar**.


## <a name="verify-the-guid-deployment"></a>Verificar a implantação de GUID

Depois de modificar o modelo e executar uma implantação de teste, use o seguinte script do PowerShell para recuperar os recursos que você implantou e marcou.

Você pode usar o script para verificar se o GUID foi adicionado com êxito ao seu modelo do Resource Manager. O script não se aplica à API do Resource Manager nem a implantações de Terraform.

Inicie sessão no Azure. Selecione a assinatura com a implantação que você deseja verificar antes de executar o script. Execute o script dentro do contexto de assinatura da implantação.

O **GUID** e o nome do **resourcegroup** da implantação são parâmetros obrigatórios.

Você pode obter [o script original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no github.

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

Você pode encontrar o relatório para atribuição de uso do cliente em seu parceiro do Partner Center analisar o painel. ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). Para ver o relatório, você precisa usar suas credenciais do Partner Center para entrar. Se você encontrar problemas com o relatório ou entrar, crie uma solicitação de suporte seguindo a instrução na seção obter suporte.

Escolha modelo controlado na lista suspensa do tipo de associação de parceiro para ver o relatório.

![Relatório de atribuição de uso do cliente](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Notifique seus clientes

Os parceiros devem informar seus clientes sobre implantações que usam a atribuição de uso do cliente. A Microsoft informa o uso do Azure associado a essas implantações ao parceiro. Os exemplos a seguir incluem o conteúdo que você pode usar para notificar seus clientes sobre essas implantações. Nos exemplos, substitua \<parceiro > pelo nome da sua empresa. Os parceiros devem garantir que a notificação se alinhe às políticas de privacidade e de coleta de dados, incluindo opções para que os clientes sejam excluídos do rastreamento.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implantações de modelo do Resource Manager

Quando você implanta esse modelo, a Microsoft é capaz de identificar a instalação \<do parceiro > software com os recursos do Azure que são implantados. A Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e para operar seus negócios. Os dados são coletados e governados pelas políticas de privacidade da Microsoft, que podem ser https://www.microsoft.com/trustcenter encontradas em.

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implantações de SDK ou API

Ao implantar \<o parceiro > software, a Microsoft é capaz de identificar a instalação \<do parceiro > software com os recursos do Azure que são implantados. A Microsoft é capaz de correlacionar os recursos do Azure que são usados para dar suporte ao software. A Microsoft coleta essas informações para fornecer as melhores experiências com seus produtos e para operar seus negócios. Os dados são coletados e governados pelas políticas de privacidade da Microsoft, que podem ser https://www.microsoft.com/trustcenter encontradas em.

## <a name="get-support"></a>Obter suporte

Há dois canais de suporte, dependendo dos problemas que você está enfrentando.

Se você encontrar problemas no Partner Center, como ver o relatório de atribuição de uso do cliente ou entrar, crie uma solicitação de suporte com a equipe de suporte do Partner Center aqui:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Se você precisar de assistência para integração do Marketplace e/ou atribuição de uso do cliente em geral, por exemplo, como configurar a atribuição de uso do cliente, siga as etapas abaixo:

1. Vá para a [página de suporte](https://go.microsoft.com/fwlink/?linkid=844975).

1. Em **tipo de problema**, selecione **integração do Marketplace**.

1. Escolha a **categoria** para o problema:

   - Para problemas de associação de uso, selecione **outro**.
   - Para problemas de acesso com o CPP do Azure Marketplace, selecione **problema de acesso**.

     ![Escolher a categoria do problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **Iniciar solicitação**.

1. Na página seguinte, insira os valores necessários. Selecione **Continuar**.

1. Na página seguinte, insira os valores necessários.

   > [!Important]
   > Na caixa **título do incidente** , insira **controle de uso de ISV**. Descreva seu problema em detalhes.

   ![Insira o acompanhamento de uso do ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Preencha o formulário e, em seguida, selecione **Enviar**.

Você também pode receber orientações técnicas de um consultor técnico de parceiros da Microsoft para cenários técnicos de pré-vendas, implantação e desenvolvimento de aplicativos para entender e incorporar a atribuição de uso do cliente.

### <a name="how-to-submit-a-technical-consultation-request"></a>Como enviar uma solicitação de consultoria técnica

1. Visite [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Selecione infraestrutura e gerenciamento de nuvem e uma nova página será aberta para que você veja a jornada técnica.
1. Em serviços de implantação, clique no botão Enviar uma solicitação
1. Entre usando sua MSA (conta do MPN) ou seu AAD (conta do painel do parceiro); com base nas suas credenciais de entrada, um formulário de solicitação online será aberto:
    * Conclua/revise as informações de contato.
    * Os detalhes de consultoria podem ser preenchidos previamente ou selecionados nos menus suspensos.
    * Insira um título e a descrição do problema (forneça o máximo de detalhes possível).
1. Clique em enviar

Veja as instruções passo a passo com capturas de tela [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)em.

### <a name="whats-next"></a>O que vem a seguir

Você será contatado por um consultor técnico de parceiros da Microsoft para configurar uma chamada para definir o escopo de suas necessidades.

## <a name="faq"></a>FAQ

**Qual é o benefício de adicionar o GUID ao modelo?**

A Microsoft fornece aos parceiros uma visão das implantações de clientes de suas soluções e informações sobre o uso influenciado. A Microsoft e o parceiro podem usar essas informações para impulsionar o envolvimento mais próximo entre as equipes de vendas. A Microsoft e o parceiro podem usar os dados para obter uma visão mais consistente do impacto de um parceiro individual no crescimento do Azure.

**Depois que um GUID é adicionado, ele pode ser alterado?**

Sim, um cliente ou parceiro de implementação pode personalizar o modelo e pode alterar ou remover o GUID. Sugerimos que os parceiros descrevam proativamente a função do recurso e do GUID para seus clientes e parceiros para evitar a remoção ou edição no GUID. A alteração do GUID afeta apenas novos, não existentes, implantações e recursos.

**Posso rastrear modelos implantados de um repositório que não seja da Microsoft, como o GitHub?**

Sim, contanto que o GUID esteja presente quando o modelo for implantado, o uso será acompanhado. Os parceiros precisam ter um perfil no CPP para registrar GUIDs usados para a implantação fora do Azure Marketplace.

**O cliente também recebe relatórios?**

Os clientes podem controlar o uso de recursos individuais ou grupos de recursos definidos pelo cliente dentro do portal do Azure.

**Essa é uma metodologia semelhante ao DPOR (parceiro digital de registro)?**

Esse novo método de conexão da implantação e do uso à solução de um parceiro fornece um mecanismo para vincular uma solução de parceiro ao uso do Azure. O DPOR destina-se a associar um parceiro de consultoria (integrador de sistemas) ou de gerenciamento (provedor de serviços gerenciado) à assinatura do Azure de um cliente.

**Qual é o benefício de usar o formulário de gerador de GUID do armazenamento do Azure?**

O formulário do gerador de GUID do armazenamento do Azure é garantido para gerar um GUID do formato necessário. Além disso, se você estiver usando qualquer um dos métodos de controle do plano de dados do armazenamento do Azure, poderá aproveitar o mesmo GUID para acompanhamento do plano de controle do Marketplace. Isso permite que você aproveite um único GUID unificado para atribuição de parceiros sem precisar manter GUIDs separados.

**Posso usar um VHD personalizado e privado para uma oferta de modelo de solução no Azure Marketplace?**

Não, você não pode. A imagem da máquina virtual deve vir do Azure Marketplace, consulte: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Você pode criar uma oferta de VM no Marketplace usando seu VHD personalizado e marcá-la como particular para que ninguém possa vê-la. Em seguida, faça referência a essa VM em seu modelo de solução.

**Falha ao atualizar a propriedade *contentVersion* para o modelo principal?**

Provavelmente um bug em alguns casos, quando o modelo está sendo implantado usando um TemplateLink de outro modelo que espera contentVersion mais antiga por algum motivo. A solução alternativa é usar a propriedade Metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
