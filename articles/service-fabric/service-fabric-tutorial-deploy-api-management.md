---
title: Integrar a Gestão de API no Service Fabric no Azure | Microsoft Docs
description: Saiba como começar a utilizar rapidamente com a API Management do Azure e encaminhar o tráfego para um serviço de back-end no Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/26/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 813a81e3812f9bb95ab11deec35adc6c05f1b58e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58312228"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integrar a gestão de API com o Service Fabric no Azure

A implementação da Gestão de API do Azure no Service Fabric é um cenário avançado.  A Gestão de API é útil se tiver de publicar APIs com um conjunto avançado de regras de encaminhamento para os seus serviços de back-end do Service Fabric. Geralmente, as aplicações da cloud precisam de um gateway de front-end que forneça um único ponto de entrada para utilizadores, dispositivos ou outras aplicações. No Service Fabric, esse gateway pode ser qualquer serviço, sem estado, concebido para a entrada de tráfego, tal como uma aplicação ASP.NET Core, os Hubs de Eventos, o Hub IoT ou a Gestão de API do Azure.

Este artigo mostra-lhe como configurar [gestão de API do Azure](../api-management/api-management-key-concepts.md) com o Service Fabric para encaminhar o tráfego para um serviço de back-end no Service Fabric.  Quando tiver terminado, terá implementado a Gestão de API numa VNET e configurado uma operação de API para enviar tráfego para serviços sem estado de back-end. Para saber mais sobre os cenários da Gestão de API do Azure com o Service Fabric, veja o artigo [Overview](service-fabric-api-management-overview.md) (Descrição Geral).

## <a name="availability"></a>Disponibilidade

> [!IMPORTANT]
> Esta funcionalidade está disponível na **Premium** e **programador** escalões da gestão de API devido a necessários virtual suporte de rede.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Instale o [módulo do Azure PowerShell na versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) ou a [CLI do Azure ](/cli/azure/install-azure-cli).
* Criar um segura [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) num grupo de segurança de rede.
* Se implementar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instale o [Visual Studio 2017](https://www.visualstudio.com) e as cargas de trabalho **desenvolvimento no Azure**, **desenvolvimento em ASP.NET e na Web** e **desenvolvimento em várias plataformas .NET Core**.  Em seguida, configure um [ambiente de desenvolvimento .NET](service-fabric-get-started.md).

## <a name="network-topology"></a>Topologia da rede

Agora que tem um segura [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) no Azure, implementar a gestão de API para a rede virtual (VNET) na sub-rede e no NSG designados para a gestão de API. Neste artigo, o modelo de Gestor de recursos de gestão de API está pré-configurado para utilizar os nomes da VNET, sub-rede e NSG que configurou no [tutorial de cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) este artigo implementa a seguinte topologia para o Azure na qual Gestão de API e o Service Fabric estão em sub-redes da mesma rede Virtual:

 ![Legenda da imagem][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Iniciar sessão no Azure e selecionar a sua subscrição

Inicie sessão na sua conta do Azure, selecione a sua subscrição antes de executar os comandos do Azure.

```powershell
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implementar um serviço de back-end do Service Fabric

Antes de configurar a Gestão de API para encaminhar o tráfego para um serviço de back-end do Service Fabric, primeiro precisa de um serviço em execução para aceitar pedidos.  

Crie um serviço básico de sem estado ASP.NET Core confiável usando o modelo de projeto de Web API padrão. Como resultado, é criado um ponto final de HTTP para o seu serviço, que vai expor através da Gestão de API do Azure.

Inicie o Visual Studio como Administrador e crie um serviço ASP.NET Core:

 1. No Visual Studio, selecione Ficheiro -> Novo Projeto.
 2. Selecione o modelo de Aplicação do Service Fabric em Cloud e dê-lhe o nome **"ApiApplication"**.
 3. Selecione o modelo de serviço sem estado ASP.NET Core e dê ao projeto o nome **"WebApiService"**.
 4. Selecione o modelo de projeto API Web ASP.NET Core 2.0.
 5. Uma vez criado o projeto, abra `PackageRoot\ServiceManifest.xml` e remova o atributo `Port` da configuração do recurso do ponto final:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    Remover a porta permite ao Service Fabric especificar uma porta dinamicamente a partir do intervalo de portas da aplicação, aberta través do Grupo de Segurança de Rede no modelo do Resource Manager do cluster, o que possibilita que o tráfego flua para a mesma a partir da Gestão de API.

 6. Prima F5 no Visual Studio para verificar se a API Web está disponível localmente.

    Abra o Service Fabric Explorer e desagregue para uma instância específica do serviço ASP.NET Core para ver o endereço base no qual o serviço está a escutar. Adicione `/api/values` ao endereço base e abra num browser, o que invoca o método Get em ValuesController no modelo da API Web. Esta ação devolve a resposta predefinida que o modelo fornece, uma matriz JSON que contém duas cadeias:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto final que vai expor através da Gestão de API no Azure.

 7. Por fim, implemente a aplicação no seu cluster no Azure. No Visual Studio, clique com o botão direito do rato no projeto Aplicação e selecione **Publicar**. Indique o ponto final do cluster (por exemplo, `mycluster.southcentralus.cloudapp.azure.com:19000`) para implementar a aplicação no cluster do Service Fabric no Azure.

Deve estar agora a ser executado no cluster do Service Fabric no Azure um serviço sem estado ASP.NET Core com o nome `fabric:/ApiApplication/WebApiService`.

## <a name="download-and-understand-the-resource-manager-templates"></a>Transferir e compreender os modelos do Azure Resource Manager

Transfira e guarde os modelos do Resource Manager e o ficheiro de parâmetros seguintes:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

O modelo *network-apim.json* implementa uma sub-rede e um grupo de segurança de rede novos na rede virtual em que está implementado o cluster do Service Fabric.

As secções seguintes descrevem os recursos que o modelo *apim.json* define. Para obter mais informações, siga as ligações para a documentação de referência do modelo em cada secção. Os parâmetros configuráveis definidos no ficheiro de parâmetros *apim.parameters.json* vão ser definidos mais adiante neste artigo.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) descreve a instância do serviço Gestão de API: nome, SKU ou camada, localização do grupo de recursos, informações do publicador e rede virtual.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) configura a segurança da Gestão de API. A Gestão de API tem de se autenticar com o seu cluster do Service Fabric para a deteção de serviço através de um certificado de cliente que tenha acesso ao cluster. Este artigo utiliza o mesmo certificado que especificou anteriormente quando criou o [cluster de Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor), que, por predefinição pode ser utilizado para aceder ao seu cluster.

Este artigo utiliza o mesmo certificado para autenticação de cliente e de segurança de nó para nó do cluster. Se tiver configurado um certificado de cliente separado, pode utilizá-lo para aceder ao seu cluster do Service Fabric. Indique o **nome**, a **palavra-passe** e os **dados** (cadeia com codificação base 64) do ficheiro de chave privada (.pfx) do certificado de cluster que especificou quando criou o cluster do Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) descreve o serviço de back-end para o qual o tráfego é reencaminhado.

Nos back-ends do Service Fabric, o cluster do Service Fabric é o back-end em vez de um serviço específico do Service Fabric. Isto permite que uma única política encaminhe para mais de um serviço no cluster. O campo **url** aqui é o nome completamente qualificado de um serviço no seu cluster para o qual todos os pedidos são encaminhados por predefinição caso não seja especificado qualquer nome de serviço numa política de back-end. Pode utilizar um nome de serviço fictício, tal como "fabric:/ficticio/servico" se não pretender ter um serviço de contingência. **resourceId** especifica o ponto final da gestão do cluster.  **clientCertificateThumbprint** e **serverCertificateThumbprints** identificam os certificados utilizados para autenticar no cluster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) cria um produto. Na Gestão de API do Azure, os produtos contêm uma ou mais APIs, bem como quotas de utilização e os termos de utilização. Depois de um produto ser publicado, os programadores podem subscrevê-lo e começar a utilizar as APIs do mesmo.

Introduza um **displayName** (nome a apresentar) e uma **description** (descrição) relevantes para o produto. Neste artigo, é necessária uma subscrição, mas a aprovação de subscrição por um administrador não é.  O **estado** deste produto é "publicado" e está visível para os subscritores.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) cria uma API. Uma API na Gestão de API representa um conjunto de operações que podem ser invocadas por aplicações cliente. Depois de as operações serem adicionadas, a API é adicionada a um produto e pode ser publicada. Após a publicação da API, esta pode ser subscrita e os programadores podem utilizá-la.

* **displayName** pode ser qualquer nome para a API. Neste artigo, utilize "Service Fabric App".
* **name** indica um nome exclusivo e descritivo para a API, como "service-fabric-app". É apresentado no portais do programador e do editor.
* **serviceUrl** referencia o serviço HTTP que implementa a API. A API de Gestão reencaminha os pedidos para este endereço. Nos back-ends do Service Fabric, o valor do URL não é utilizado. Pode pôr qualquer valor aqui. Neste artigo, por exemplo "http:\//servicefabric".
* **path** é anexado ao URL base do serviço Gestão de API. O URL base é comum a todas as APIs alojadas por uma instância do serviço Gestão de API. A Gestão de API distingue as APIs pelo respetivo sufixo, pelo que cada API tem de ter o seu sufixo exclusivo para um determinado editor.
* O campo **protocols** determina que protocolos podem ser utilizados para aceder à API. Neste artigo, listar **http** e **https**.
* **path** é um sufixo para a API. Neste artigo, utilize "myapp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) Antes de poder utilizar uma API da Gestão de API, é necessário adicionar operações à API.  Os clientes externos utilizam uma operação para comunicar com o serviço sem estado ASP.NET Core em execução no cluster do Service Fabric.

Para adicionar uma operação de API de front-end, preencha os valores:

* **displayName** e **description** descrevem a operação. Neste artigo, utilize "Values".
* **method** especifica o verbo HTTP.  Neste artigo, especifique **obter**.
* **urlTemplate** é anexado ao URL base da API e identifica uma operação HTTP individual.  Neste artigo, utilize `/api/values` se tiver adicionado o serviço de back-end .NET ou `getMessage` se tiver adicionado o serviço de back-end de Java.  Por predefinição, o caminho do URL especificado aqui é o caminho do URL enviado para o serviço de back-end do Service Fabric. Se utilizar aqui o mesmo caminho de URL do seu serviço, como, por exemplo, "/api/values", a operação funciona sem mais modificações. Também pode especificar aqui um caminho de URL diferente daquele que o serviço de back-end do Service Fabric utiliza, caso em que também tem de especificar, mais tarde, uma reescrita de caminho na política da operação.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) cria uma política de back-end, que vincula todos os elementos. É aqui que vai configurar o serviço de back-end do Service Fabric para o qual os pedidos são encaminhados. Pode aplicar esta política a todas as operações de API.  Para obter mais informações, veja [Policies overview](/azure/api-management/api-management-howto-policies) (Descrição Geral das Políticas).

A [configuração do back-end do Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) disponibiliza os controlos de encaminhamento de pedidos seguintes:

* Seleção de instâncias do serviço, mediante a especificação do nome de uma instância do serviço Service Fabric, seja hard-coded (por exemplo, `"fabric:/myapp/myservice"`) ou gerado a partir do pedido HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Resolução de partições mediante a geração de uma chave de partição através de qualquer esquema de partições do Service Fabric.
* Seleção de réplicas para serviços com estado.
* Condições de repetição de resolução que lhe permitem especificar as condições para voltar a resolver uma localização de serviço e reenviar um pedido.

**policyContent** é o conteúdo XML com escape JSON da política.  Neste artigo, crie uma política de back-end para encaminhar os pedidos diretamente para o serviço sem estado .NET ou Java implementado anteriormente. Adicione uma política `set-backend-service` nas políticas de entrada.  Substitua o valor *sf-service-instance-name* por `fabric:/ApiApplication/WebApiService`, se tiver implementado anteriormente o serviço de back-end .NET, ou por `fabric:/EchoServerApplication/EchoServerService`, se tiver implementado o serviço Java.  *backend-id* referencia um recurso de back-end; neste caso, o recurso `Microsoft.ApiManagement/service/backends` definido no modelo *apim.json*. *backend-id* também pode referenciar outro recurso de back-end que tenha sido criado com as APIs da Gestão de API. Neste artigo, definido *id de back-end* para o valor da *service_fabric_backend_name* parâmetro.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Para obter um conjunto completo dos atributos de políticas de back-ends do Service Fabric, veja a [documentação sobre o back-end da Gestão de API](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Definir parâmetros e implementar a Gestão de API

Preencha os parâmetros vazios seguintes em *apim.parameters.json* da sua implementação.

|Parâmetro|Valor|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Programador|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;cadeia com codificação base 64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;Cadeia XML&gt;|

*certificatePassword* e *serviceFabricCertificateThumbprint* têm de corresponder ao certificado de cluster que foi utilizado para configurar o cluster.

*serviceFabricCertificate* é o certificado como cadeia com codificação base 64, que pode ser gerada com o script abaixo:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

Em *inbound_policy*, substitua o valor *sf-service-instance-name* por `fabric:/ApiApplication/WebApiService`, se tiver implementado anteriormente o serviço de back-end .NET, ou por `fabric:/EchoServerApplication/EchoServerService`, se tiver implementado o serviço Java. *backend-id* referencia um recurso de back-end; neste caso, o recurso `Microsoft.ApiManagement/service/backends` definido no modelo *apim.json*. *backend-id* também pode referenciar outro recurso de back-end que tenha sido criado com as APIs da Gestão de API. Neste artigo, definido *id de back-end* para o valor da *service_fabric_backend_name* parâmetro.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Utilize o script abaixo para implementar o modelo do Resource Manager e os ficheiros de parâmetros da Gestão de API:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Testar

Agora, pode experimentar enviar um pedido para o serviço de back-end do Service Fabric através da Gestão de API diretamente no [portal do Azure](https://portal.azure.com).

 1. No serviço Gestão de API, selecione **API**.
 2. Na API **Service Fabric App** que criou nos passos anteriores, selecione o separador **Testar** e, em seguida, selecione a operação **Values**.
 3. Clique no botão **Enviar** para enviar um pedido de teste para o serviço de back-end.  Deverá ver uma resposta HTTP semelhante a:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é constituído por outros recursos do Azure, além do próprio recurso do cluster. A forma mais simples de eliminar o cluster e todos os recursos que consome é eliminando o grupo de recursos.

Inicie sessão no Azure e selecione o ID da subscrição com a qual pretende remover o cluster.  Pode encontrar o ID da subscrição ao iniciar sessão no [portal do Azure](https://portal.azure.com). Elimine o grupo de recursos e todos os recursos de cluster através do cmdlet [Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como utilizar [gestão de API](/azure/api-management/import-and-publish).

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
