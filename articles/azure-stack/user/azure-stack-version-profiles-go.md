---
title: Através de perfis de versão de API com GO no Azure Stack | Documentos da Microsoft
description: Saiba como utilizar perfis de versão de API com o GO no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/19/2019
ms.openlocfilehash: fa6b22b7bd2a390cd7f02e8707584563ed1f4a19
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58116950"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Utilizar perfis de versão de API com o Go no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="go-and-version-profiles"></a>Perfis de go e versão

Um perfil é uma combinação de diferentes tipos de recursos com versões diferentes de diferentes serviços. Utilizar um perfil de ajuda a misturar e combinar entre diferentes tipos de recursos. Perfis podem fornecer as seguintes vantagens:

- Estabilidade para a sua aplicação, bloqueando a versões de API específicas.
- Compatibilidade para a sua aplicação com o Azure Stack e datacenters regionais do Azure.

No Go SDK, perfis estão disponíveis sob o caminho de perfis, com a respetiva versão na **DD-MM-AAAA** formato. Neste momento, é a mais recente versão de perfil de API do Azure Stack **2017-03-09**. Para importar um determinado serviço de um perfil, importe seu módulo correspondente do perfil. Por exemplo, para importar **computação** serviço de **2017-03-09** de perfil, utilize o seguinte código:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Instale o Azure SDK para Go

1. Instale o Git. Para obter instruções, consulte [introdução - instalar o Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Instalar o [linguagem de programação Go](https://golang.org/dl). Perfis de API do Azure requerem Go versão 1.9 ou mais recente.
3. Instale o SDK para Go do Azure e as respetivas dependências, executando o seguinte comando do bash:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>O SDK do Go

Pode encontrar mais informações sobre o SDK GO do Azure nas seguintes ligações:

- O Azure SDK em Ir [instalar o SDK do Azure para Go](/go/azure/azure-sdk-go-install).
- O SDK Go do Azure está disponível publicamente no GitHub no [do azure sdk para go](https://github.com/Azure/azure-sdk-for-go) repositório.

### <a name="go-autorest-dependencies"></a>Dependências de go-AutoRest

SDK do Go depende do Azure **Go-AutoRest** módulos para enviar pedidos REST para pontos finais do Gestor de recursos do Azure. Tem de importar do Azure **Go-AutoRest** dependências do módulo do [Azure Go-AutoRest no GitHub](https://github.com/Azure/go-autorest). Pode encontrar os comandos de bash de instalar o **instalar** secção.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Como utilizar perfis de Go SDK no Azure Stack

Para executar um exemplo de código do Go no Azure Stack, siga estes passos:

1. Instale o Azure SDK para Go e as respetivas dependências. Para obter instruções, consulte a secção anterior, [instalar o Azure SDK para Go](#install-azure-sdk-for-go).
2. Obtenha as informações de metadados a partir do ponto final do Resource Manager. O ponto final devolve um ficheiro JSON com as informações necessárias para executar código Go.

   > [!NOTE]  
   > O **ResourceManagerUrl** no Azure Stack Development Kit (ASDK) é: `https://management.local.azurestack.external/`  
   > O **ResourceManagerUrl** em sistemas integrados é: `https://management.<region>.<fqdn>/`  
   > Para obter os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   Ficheiro JSON de exemplo:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Se não está disponível, criar uma subscrição e guarde o ID de subscrição a ser utilizado mais tarde. Para obter informações sobre a criação de uma subscrição, veja [criar subscrições de ofertas no Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

4. Criar um serviço principal com **subscrição** âmbito e **proprietário** função. Guarde o ID de principal de serviço e o segredo. Para obter informações sobre como criar um principal de serviço para o Azure Stack, veja [criar principal de serviço](azure-stack-create-service-principals.md). O ambiente do Azure Stack está agora configurado.

5. Importe um módulo de serviço do perfil de Go SDK no seu código. É a versão atual do perfil do Azure Stack **2017-03-09**. Por exemplo, para importar o módulo de rede a partir da **2017-03-09** tipo de perfil, utilize o seguinte código:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. Na sua função, criar e autenticar-se um cliente com um **New** chamada de função do cliente. Para criar um cliente de rede virtual, pode utilizar o seguinte código:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Definir `<baseURI>` para o **ResourceManagerUrl** valor utilizado no passo 2. Definir `<subscriptionID>` para o **SubscriptionID** valor guardado no passo 3.

   Para criar o token, consulte a secção seguinte.  

7. Invoca métodos da API com o cliente que criou no passo anterior. Por exemplo, para criar uma rede virtual com o cliente do passo anterior, veja o exemplo seguinte:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Para obter um exemplo completo de criação de uma rede virtual no Azure Stack com o perfil de Go SDK, consulte a [exemplo](#example).

## <a name="authentication"></a>Authentication

Para obter o **Authorizer** propriedade a partir do Azure Active Directory com o SDK do Go, instalar o **Go AutoRest** módulos. Estes módulos devem ter sido previamente instalados com a instalação do "Go SDK"; Se não estiver, instale o [pacote de autenticação a partir do GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

O Authorizer tem de ser definido como authorizer para o cliente de recursos. Existem diferentes formas de obter os tokens de authorizer no Azure Stack, utilizando as credenciais do cliente:

1. Se um principal de serviço com a função de proprietário da subscrição estiver disponível, ignore este passo. Caso contrário, crie uma [principal de serviço](azure-stack-create-service-principals.md) e atribua-lhe uma função de "proprietário" [confinada à sua subscrição](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Guarde o ID da aplicação principal de serviço e o segredo.

2. Importar os **adal** pacote do Go AutoRest no seu código.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Criar uma **oauthConfig** utilizando o método NewOAuthConfig **adal** módulo.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Definir `<activeDirectoryEndpoint>` para o valor da `loginEndpoint` propriedade a partir do `ResourceManagerUrl` metadados obtidos na secção anterior deste documento. Definir o `<tenantID>` valor ao seu ID de inquilino do Azure Stack.

4. Por fim, crie um token do principal de serviço utilizando o `NewServicePrincipalToken` método a partir do **da adal** módulo:

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Definir `<activeDirectoryResourceID>` para um dos valores na lista de "público" a partir do **ResourceManagerUrl** metadados de obteve na secção anterior deste artigo.
    Definir `<clientID>` para a aplicação do principal de serviço ID é guardado quando principal de serviço foi criado na secção anterior deste artigo.
    Definir `<clientSecret>` para o segredo de aplicação principal de serviço guardado quando o principal de serviço foi criado na secção anterior deste artigo.

## <a name="example"></a>Exemplo

Este exemplo mostra um exemplo de código do Go que cria uma rede virtual no Azure Stack. Para obter exemplos completos do SDK do Go, veja a [repositório de exemplos do SDK Go do Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Exemplos do Azure Stack estão disponíveis no caminho híbrida dentro das pastas de serviço do repositório.

> [!NOTE]  
> Para executar o código neste exemplo, certifique-se de que a subscrição utilizada tem o **rede** fornecedor de recursos listado como **registado**. Para verificar, procure a subscrição no portal do Azure Stack e selecione **fornecedores de recursos.**

1. Importe os pacotes necessários em seu código. Utilize o perfil mais recente disponível no Azure Stack para importar o módulo de rede:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Defina variáveis do seu ambiente. Para criar uma rede virtual, tem de ter um grupo de recursos.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Agora que definiu as variáveis de ambiente, adicione um método para criar um token de autenticação utilizando o **adal** pacote. Para obter mais informações sobre a autenticação, consulte a secção anterior.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Adicionar o `main` método. O `main` método primeiro obtém um token através do método que está definido no passo anterior. Em seguida, cria um cliente ao utilizar um módulo de rede do perfil. Por fim, ele cria uma rede virtual.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletion(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

## <a name="next-steps"></a>Passos Seguintes

- [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
- [Configurar o ambiente do PowerShell do utilizador do Azure Stack](azure-stack-powershell-configure-user.md)  
