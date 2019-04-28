---
title: Atribuição de utilização de parceria e clientes do Azure
description: Descrição geral sobre como monitorizar a utilização de cliente para soluções do Azure Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
manager: nunoc
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: 078815185ddb6018a394401f57f7557ac3aedb73
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123433"
---
# <a name="azure-partner-customer-usage-attribution"></a>Atribuição da utilização do cliente de parceiro do Azure

Como um parceiro de software para o Azure, as soluções necessitam de componentes do Azure ou precisam de ser implementadas diretamente da infraestrutura do Azure. Os clientes que implementar uma solução de parceiro e aprovisionar os seus próprios recursos do Azure podem encontrar dificuldades para ganhar visibilidade para o estado da implementação e obter óptica sobre o impacto no crescimento do Azure. Quando adiciona um nível mais elevado de visibilidade, se alinham com a equipa de vendas da Microsoft e obter crédito para programas de parceria da Microsoft.

Agora, a Microsoft oferece um método para ajudar os parceiros a controlar melhor a utilização do Azure de implementações dos clientes de seu software no Azure. O novo método utiliza o Azure Resource Manager para orquestrar a implementação de serviços do Azure.

Como um parceiro da Microsoft, pode associar a utilização do Azure com todos os recursos do Azure que for aprovisionado em nome de um cliente. É possível formar a associação através do Azure Marketplace, o repositório início rápido, repositórios de GitHub privados e captação de clientes de um para um. Atribuição de utilização do cliente oferece suporte a três opções de implementação:

- Modelos do Azure Resource Manager: Parceiros podem utilizar modelos do Resource Manager para implementar os serviços do Azure para executar o software do parceiro. Parceiros, podem criar um modelo do Resource Manager para definir a infraestrutura e a configuração da sua solução do Azure. Um modelo do Resource Manager permite que e seus clientes, para implementar a solução durante seu ciclo de vida. Pode ter a certeza de que os recursos são implementados num estado consistente.
- APIs do Resource Manager do Azure: Parceiros podem chamar as APIs do Resource Manager diretamente a implementar um modelo do Resource Manager ou a gerar as chamadas à API para aprovisionar diretamente os serviços do Azure.
- Terraform: Parceiros podem utilizar o orchestrator de cloud, como o Terraform para implementar um modelo do Resource Manager ou diretamente implementar serviços do Azure.

Atribuição de utilização do cliente é para a nova implementação e não suporta a identificação de recursos existentes que já foram implementados.

É necessária a atribuição de utilização do cliente [aplicação do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): oferta de modelo de solução publicada no Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Utilizar os modelos do Resource Manager
Muitas soluções de parceiros são implementadas na subscrição de um cliente, utilizando modelos do Resource Manager. Se tiver um modelo do Resource Manager que está disponível no Azure Marketplace, no GitHub, ou como um guia de introdução, o processo para modificar o modelo para ativar a atribuição de utilização do cliente deve ser simples.

Para obter mais informações sobre como criar e publicar modelos de solução, consulte

* [Criar e implementar o modelo do Resource Manager primeira](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Oferta de aplicação do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Vídeo: [Criação de modelos de soluções e aplicações geridas para o Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Adicionar um GUID ao seu modelo

Para adicionar um identificador exclusivo global (GUID), certifique-se um única modificações no arquivo de modelo principal:

1. [Criar um GUID](#create-guids) usando o método sugerido e [registar o GUID](#register-guids-and-offers).

1. Abra o modelo do Resource Manager.

1. Adicione um novo recurso no ficheiro de modelo principal. O recurso tem de estar no **maintemplate. JSON** ou **azuredeploy. JSON** ficheiro apenas e não em qualquer aninhadas ou ligado modelos.

1. Introduza o valor GUID depois do **pid -** prefixo (por exemplo, pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Verifique o modelo para quaisquer erros.

1. Voltar a publicar o modelo nos repositórios apropriados.

1. [Verificar o êxito do GUID da implementação de modelo](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Código de modelo do Gestor de recursos de exemplo

Para ativar o controlo de recursos para o modelo, terá de adicionar o seguinte recurso adicional na secção de recursos. Certifique-se modificar o abaixo o código de exemplo com suas próprias entradas quando adicioná-lo para o ficheiro de modelo principal.
O recurso tem de ser adicionados à **maintemplate. JSON** ou **azuredeploy. JSON** ficheiro apenas e não em qualquer aninhadas ou ligado modelos.

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

## <a name="use-the-resource-manager-apis"></a>Utilizar as APIs do Resource Manager

Em alguns casos, poderá preferir efetuar chamadas diretamente contra as APIs de REST do Resource Manager para implementar serviços do Azure. [O Azure suporta vários SDKs](https://docs.microsoft.com/azure/#pivot=sdkstools) para permitir que essas chamadas. Pode utilizar um dos SDKs do ou chamar as APIs de REST diretamente para implementar recursos.

Se estiver a utilizar um modelo do Resource Manager, deve Etiquetar sua solução ao seguir as instruções descritas anteriormente. Se não estiver a utilizar um modelo do Resource Manager e fazer chamadas diretas de API, pode ainda marcar a implementação para associar a utilização de recursos do Azure.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Marcar uma implementação com as APIs do Resource Manager

Para ativar a utilização do cliente a atribuição e, ao estruturar as chamadas de API, incluem um GUID no cabeçalho do agente de utilizador no pedido. Adicione o GUID para cada oferta ou SKU. Formatar a cadeia de caracteres com o **pid -** prefixo e incluir o GUID gerado pelo parceiro. Eis um exemplo do formato de GUID para inserção no agente de utilizador:

![Formato de GUID de exemplo](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> O formato da cadeia de caracteres é importante. Se o **pid -** prefixo não está incluído, não é possível consultar os dados. SDKs diferentes controlam de forma diferente. Para implementar esse método, reveja o suporte e a abordagem de controlo para o Azure SDK preferencial.

#### <a name="example-the-python-sdk"></a>Exemplo: O SDK Python

Para o Python, utilize o **config** atributo. Só pode adicionar o atributo para um UserAgent. Segue-se um exemplo:

![Adicione o atributo para um agente de utilizador](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Adicione o atributo para cada cliente. Não existe nenhuma configuração estática global. Pode marcar um alocador de cliente para não se esqueça de que controlo de todos os clientes. Para obter mais informações, consulte esta [exemplo de fábrica do cliente no GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Marcar uma implementação ao utilizar o Azure PowerShell

Se implementar recursos através do Azure PowerShell, acrescente o GUID usando o seguinte método:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Marcar uma implementação com a CLI do Azure

Ao utilizar a CLI do Azure para o GUID de acréscimo, defina o **AZURE_HTTP_USER_AGENT** variável de ambiente. Pode definir esta variável dentro do escopo de um script. Também pode definir a variável global para o âmbito de shell:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Para obter mais informações, consulte [Azure SDK para Go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Utilize o Terraform

O suporte para o Terraform está disponível através 1.21.0 do fornecedor do Azure de versão: [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Este suporte aplica-se a todos os parceiros que implementar a sua solução por meio do Terraform, e todos os recursos implementados e limitados pelo fornecedor do Azure (versão 1.21.0 ou posterior).

O fornecedor do Azure para o Terraform adicionado um novo campo opcional chamado [ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id) que é onde especifica o GUID que utilizar para a sua solução de controlo. O valor deste campo também pode ter origem no *ARM_PARTNER_ID* variável de ambiente.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Parceiros que desejam obter a implantação por meio do Terraform rastreada pela atribuição de utilização do cliente tem de fazer o seguinte:

* Criar um GUID (o GUID deve ser adicionado para cada oferta ou SKU)
* Atualizar o seu fornecedor do Azure para definir o valor da *partner_id* para o GUID (não pré-correção o GUID com "pid-", basta defini-lo para o GUID real)

## <a name="create-guids"></a>Criar GUIDs

Um GUID é um número de referência exclusivo que tem 32 dígitos hexadecimais. Para criar os GUIDs para acompanhamento, deve utilizar um gerador GUID. A equipe de armazenamento do Azure criou um [formulário de gerador GUID](https://aka.ms/StoragePartners) que será enviada uma mensagem é um GUID no formato correto e pode ser reutilizado entre os sistemas de controle diferentes.

> [!Note]
> É altamente recomendável que use [formulário de gerador GUID do armazenamento do Azure](https://aka.ms/StoragePartners) para criar o GUID. Para obter mais informações, consulte nosso [FAQ](#faq).

Recomendamos que crie um GUID exclusivo para cada oferta e a distribuição do canal para cada produto. Pode optar por utilizar um GUID único para vários canais de distribuição do produto se não pretender que o relatório ser dividida.

Se implementar um produto ao utilizar um modelo e está disponível no Marketplace do Azure e no GitHub, pode criar e registar 2 GUIDS distintos:

*   Produto A no Azure Marketplace
*   Produto A no GitHub

A comunicação é efetuada pelo valor de parceiro (ID de parceiro da Microsoft) e os GUIDs.

Também pode controlar GUIDs num nível mais granular, como o SKU, onde os SKUs são variantes de uma oferta.

## <a name="register-guids-and-offers"></a>Registe-se de GUIDs e ofertas

Os GUIDs têm de estar registados para ativar a atribuição de utilização do cliente.

Todos os registos para o modelo GUIDs são feitos por meio do Azure Marketplace Cloud Partner Portal (CPP).

Depois de adicionar o GUID para o modelo ou no agente de utilizador e registar o GUID no CPP, todas as implementações são controladas.

1. Aplicar a [do Azure Marketplace](https://aka.ms/listonazuremarketplace) e obtenha acesso ao CPP.

   * Os parceiros são necessários para [têm um perfil no CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Está cancelá-lo para listar a oferta no Azure Marketplace ou no AppSource.
   * Parceiros podem registrar vários GUIDs.
   * Parceiros podem registar um GUID para os modelos de soluções externas e ofertas.

1. Inicie sessão para o [Portal de parceiros da Cloud](https://cloudpartner.azure.com/).

1. No canto superior direito, selecione o ícone de conta e, em seguida, selecione **perfil do publicador**.

   ![Selecione o perfil do publicador](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. Sobre o **página de perfil**, selecione **Adicionar GUID de controlo.**

   ![Selecione Adicionar GUID de controlo](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. Na **GUID controlo** , introduza o seu controlo GUID. Introduza apenas o GUID sem o **pid -** prefixo. Na **Descrição personalizada** , introduza o nome da oferta ou a descrição.

   ![Página de perfil](media/marketplace-publishers-guide/guid-dev-center-login.png)

   ![Introduza o GUID e descrição da oferta](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Para registar mais do que um GUID, selecione **Adicionar GUID controlo** novamente. Caixas adicionais são apresentados na página.

   ![Selecione Adicionar GUID controlo novamente](media/marketplace-publishers-guide/guid-dev-center-example-add.png)

   ![Introduza outro GUID e descrição da oferta](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Selecione **Guardar**.

   ![Selecionar guardar](media/marketplace-publishers-guide/guid-dev-center-save.png)

Depois de adicionar o GUID para o modelo ou no agente de utilizador e registar o GUID no CPP, todas as implementações são controladas.

## <a name="verify-the-guid-deployment"></a>Verificar a implementação de GUID

Depois de alterar o seu modelo e executar uma implementação de teste, utilize o seguinte script do PowerShell para obter os recursos que implementou e etiquetados.

Pode usar o script para verificar se o GUID é adicionado com êxito para o modelo do Resource Manager. O script não se aplica a implementação de API do Resource Manager.

Inicie sessão no Azure. Selecione a subscrição com a implementação que pretende verificar antes de executar o script. Execute o script dentro do contexto de subscrição da implementação.

O **GUID** e **resourceGroup** nome da implementação são parâmetros necessários.

Pode obter [o script original](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) no GitHub.

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

Pode encontrar o relatório de atribuição de utilização do cliente no dashboard do Centro de parceiros analisar. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)).

Escolha o modelo controlado na lista pendente do tipo de associação de parceiro para ver o relatório.

![Relatório de atribuição de utilização do cliente](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Notificar os seus clientes

Parceiros devem informar aos clientes sobre as implementações que utilizam a atribuição de utilização do cliente. Microsoft comunica a utilização do Azure associado a estas implementações para o parceiro. Os exemplos seguintes incluem conteúdo que pode utilizar para notificar os seus clientes sobre estas implementações. Nos exemplos, substitua \<parceiro > com o nome da sua empresa. Parceiros devem certificar-se de que a notificação se alinha com os seus dados políticas de privacidade e a coleção, incluindo opções para os clientes a excluir de controlo.

### <a name="notification-for-resource-manager-template-deployments"></a>Notificação para implementações de modelo do Resource Manager

Ao implementar este modelo, a Microsoft é capaz de identificar a instalação do \<parceiro > software com os recursos do Azure que são implementados. A Microsoft é capaz de correlacionar os recursos do Azure que são utilizados para suportar o software. A Microsoft recolhe estas informações para fornecer as experiências melhores com seus produtos e para operar seus negócios. Os dados são recolhidos e regem-se pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Notificação para implementações de SDK ou a API

Quando implementa \<parceiro > software, a Microsoft é capaz de identificar a instalação do \<parceiro > software com os recursos do Azure que são implementados. A Microsoft é capaz de correlacionar os recursos do Azure que são utilizados para suportar o software. A Microsoft recolhe estas informações para fornecer as experiências melhores com seus produtos e para operar seus negócios. Os dados são recolhidos e regem-se pelas políticas de privacidade da Microsoft, que podem ser encontradas em https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Obter suporte

Se precisar de assistência de integração do Marketplace e/ou atribuição de utilização do cliente, siga estes passos.

1. Vá para o [página de suporte](https://go.microsoft.com/fwlink/?linkid=844975).

1. Sob **tipo de problema**, selecione **inclusão de Marketplace**.

1. Escolha o **categoria** para o seu problema:

   - Para problemas de utilização de associação, selecione **outros**.
   - Para problemas de acesso com CPP de mercado do Azure, selecione **problemas de acesso ao**.

     ![Escolha a categoria de problema](media/marketplace-publishers-guide/lu-article-incident.png)

1. Selecione **iniciar pedido**.

1. Na página seguinte, introduza os valores necessários. Selecione **Continuar**.

1. Na página seguinte, introduza os valores necessários.

   > [!Important]
   > Na **título do incidente** , introduza **controlo de utilização de ISV**. Descreva o seu problema em detalhes.

   ![Introduza o controlo de utilização de ISV para o título do incidente](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Preencha o formulário e, em seguida, selecione **submeter**.

Também pode receber orientação técnica do Microsoft Partner consultor técnico de pré-vendas técnicas, implantação e cenários de desenvolvimento de aplicações para compreender e incorporar a atribuição de utilização do cliente.

### <a name="how-to-submit-a-technical-consultation-request"></a>Como submeter um pedido de consultoria técnica

1. Visite [ https://aka.ms/TechnicalJourney ](https://aka.ms/TechnicalJourney).
1. Selecione infraestrutura de nuvem e gestão e uma nova página serão aberta para que possa ver a viagem técnica.
1. Em serviços de implantação, clique num botão de pedido de envio
1. Inicie sessão com a sua MSA (conta da MPN) ou o AAD (conta de Dashboard de parceiro). com base nas suas credenciais de início de sessão, irá abrir um formulário de solicitação online:
    * Concluir/Revisão das informações de contacto.
    * Os detalhes de consultoria podem ser preenchidos previamente ou selecione listas pendentes.
    * Introduza um título e a descrição do problema (fornecer tantos detalhes quanto possível).
1. Clique em submeter

Ver instruções passo a passo com capturas de ecrã em [ https://aka.ms/TechConsultInstructions ](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>O que se segue

Será contactado por um consultor técnico do parceiro Microsoft para configurar uma chamada para definir o âmbito suas necessidades.

## <a name="faq"></a>FAQ

**O que é o benefício da adição o GUID para o modelo?**

Microsoft fornece aos parceiros com uma visão de implementações dos clientes das suas soluções e informações na respetiva utilização influenced. A Microsoft e o parceiro podem utilizar estas informações para o envolvimento de mais de perto de unidade entre as equipas de vendas. A Microsoft e o parceiro podem utilizar os dados para obter uma visão mais consistente de impacto de um parceiro individuais no crescimento do Azure.

**Depois de um GUID é adicionado, pode ela ser alterada?**

Sim, um parceiro de implementação ou do cliente pode personalizar o modelo e pode alterar ou remover o GUID. Sugerimos que parceiros descrevem proativamente a função do recurso e GUID para seus clientes e parceiros para impedir a remoção ou edições feitas para o GUID. O GUID a alteração afeta apenas as implementações de novo, não existentes e recursos.

**Pode controlar implementados a partir de um repositório de terceiros como o GitHub de modelos?**

Sim, desde que o GUID está presente quando o modelo é implementado, a utilização é controlada. Parceiros têm de ter um perfil no CPP para registar os GUIDs utilizados para a implementação fora do Azure Marketplace.

**O cliente receber relatórios também?**

Os clientes podem controlar a utilização de recursos individuais ou grupos de recursos definida pelo cliente no portal do Azure.

**Esta metodologia é semelhante para o parceiro de registo Digital (DPOR)?**

Este novo método de conexão a implementação e utilização a solução de um parceiro fornece um mecanismo para ligar uma solução de parceiro a utilização do Azure. DPOR destina-se para associar uma consultoria (integrador de sistemas) ou o parceiro de gestão (fornecedor de serviços geridos) com a subscrição do Azure de um cliente.

**O que é o benefício de usar o formulário de gerador de GUID do armazenamento do Azure?**

Formulário de gerador de GUID do armazenamento do Azure é garantido para gerar um GUID no formato necessário. Além disso, se estiver a utilizar qualquer um plano de dados do armazenamento do Azure métodos de controle, pode aproveitar o mesmo GUID para o plano de controlo do mercado de controlo. Isto permite-lhe tirar partido de um GUID um unificada para atribuição de parceiro sem a necessidade de manter GUIDS separados.

**Pode utilizar um VHD privado, personalizado para uma oferta de modelo de solução no Azure Marketplace?**

Não, não é possível. A imagem de máquina virtual tem de ser proveniente do Azure Marketplace, consulte: [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Pode criar uma oferta VM no marketplace usando seu VHD personalizado e marcá-la como privada, para que ninguém pode vê-lo. Em seguida, a referência a esta VM no seu modelo de solução.

**Falha ao atualizar *contentVersion* propriedade para o modelo principal?**

Provavelmente um bug em alguns casos quando o modelo está a ser implementado usando um TemplateLink de outro modelo que esperam contentVersion mais antigo por algum motivo. A solução alternativa é usar a propriedade de metadados:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
