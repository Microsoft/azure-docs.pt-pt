---
title: Configurar o backend de tecido de serviço na Azure API Management | Microsoft Docs
description: Como criar um backend de serviço de tecido de serviço na Azure API Management usando o portal Azure
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3dda6f18c2bf92b537c2f4be1c6a0a3b70cdc28a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815887"
---
# <a name="set-up-a-service-fabric-backend-in-api-management-using-the-azure-portal"></a>Configurar um backend de tecido de serviço na Gestão API utilizando o portal Azure

Este artigo mostra como configurar um serviço [de Tecido de Serviço](../service-fabric/service-fabric-api-management-overview.md) como um backend API personalizado usando o portal Azure. Para fins de demonstração, mostra como configurar um serviço básico apátrida ASP.NET Core Reliable como o backend do Tecido de Serviço.

Para obter [antecedentes, consulte Backends na API Management](backends.md).

## <a name="prerequisites"></a>Pré-requisitos

Pré-requisitos para configurar um serviço de amostra num cluster de tecido de serviço que executa o Windows como um backend personalizado:

* **Windows development environment** - Install [Visual Studio 2019](https://www.visualstudio.com) and **the Azure development**, ASP.NET and web **development**- and **.NET Core cross-platform development** workloads. Em seguida, configure um [ambiente de desenvolvimento .NET](../service-fabric/service-fabric-get-started.md).

* **Cluster de tecido de** serviço - Ver [Tutorial: Implementar um cluster de tecido de serviço que executa o Windows numa rede virtual Azure](../service-fabric/service-fabric-tutorial-create-vnet-and-windows-cluster.md). Pode criar um cluster com um certificado X.509 existente ou para efeitos de teste criar um novo certificado auto-assinado. O cluster é criado numa rede virtual.

* **App De Serviço de Amostra** - Crie uma aplicação Web API e implemente para o cluster de Tecido de Serviço, conforme descrito na [Integração da Gestão da API com Tecido de Serviço em Azure.](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

    Estes passos criam um serviço básico de ASP.NET apátrida ASP.NET core fiável usando o modelo de projeto de API web padrão. Mais tarde, expõe o ponto final HTTP para este serviço através da Azure API Management.

    Tome nota do nome da aplicação, por `fabric:/myApplication/myService` exemplo. 

* **Instância de Gestão da API** - Uma instância de Gestão de API existente ou nova no nível **Premium** ou  **Developer** e na mesma região que o cluster de Tecidos de Serviço. Se precisar de um, [crie uma instância de Gestão API.](get-started-create-service-instance.md)

* **Rede virtual** - Adicione a sua instância de Gestão API à rede virtual que criou para o seu cluster de Tecido de Serviço. A API Management requer uma sub-rede dedicada na rede virtual.

  Para obter medidas que permitam a conectividade da rede virtual para o exemplo de Gestão da API, consulte [Como utilizar a Azure API Management com redes virtuais.](api-management-using-with-vnet.md)

## <a name="create-backend---portal"></a>Criar backend - portal

### <a name="add-service-fabric-cluster-certificate-to-api-management"></a>Adicionar certificado de cluster de tecido de serviço à API Management

O certificado de cluster de tecido de serviço é armazenado e gerido num cofre chave Azure associado ao cluster. Adicione este certificado à sua instância de Gestão de API como certificado de cliente.

Para obter medidas para adicionar um certificado à sua instância de Gestão de API, consulte [Como garantir serviços de backend utilizando a autenticação de certificado de cliente na Azure API Management](api-management-howto-mutual-certificates.md). 

> [!NOTE]   
> Recomendamos a adição do certificado à API Management, referindo-se ao certificado de cofre-chave. 

### <a name="add-service-fabric-backend"></a>Adicionar backend de tecido de serviço

1. No [portal Azure,](https://portal.azure.com)navegue para o seu caso de Gestão API.
1. Em **APIs**, **selecione Backends**  >  **+ Add**.
1. Insira um nome de backend e uma descrição opcional
1. No **Tipo**, selecione **Tecido de Serviço.**
1. Em **URL runtime**, insira o nome do serviço de backend Desempenhia do Tecido de Serviço a que a API Management irá encaminhar os pedidos. Exemplo: `fabric:/myApplication/myService`. 
1. No **número máximo de recaída de resolução de divisórias,** insira um número entre 0 e 10.
1. Insira o ponto final de gestão do cluster de Tecido de Serviço. Este ponto final é o URL do cluster na `19080` porta, por exemplo, `https://mysfcluster.eastus.cloudapp.azure.com:19080` .
1. No **certificado Cliente,** selecione o certificado de cluster de Tecido de Serviço que adicionou à sua instância de Gestão de API na secção anterior.
1. No **método de autorização de ponto final de Gestão,** introduza uma impressão digital ou o nome X509 de um certificado utilizado pelo serviço de gestão de clusters de tecido de serviço para comunicação TLS.
1. Ativar a **cadeia de certificados Validate** e validar as definições **do nome do certificado.**
1. Nas **credenciais de Autorização,** fornecer credenciais, se necessário, para chegar ao serviço de backend configurado em Tecido de Serviço. Para a aplicação de amostras utilizada neste cenário, não são necessárias credenciais de autorização.
1. Selecione **Criar**.

:::image type="content" source="media/backends/create-service-fabric-backend.png" alt-text="Criar um backend de tecido de serviço":::

## <a name="use-the-backend"></a>Use o backend

Para utilizar um backend personalizado, refira-o utilizando a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) apólice. Esta política transforma o URL de base de serviço de backend padrão de um pedido de API para um backend especificado, neste caso o backend do Tecido de Serviço. 

A `set-backend-service` política pode ser útil com uma API existente para transformar um pedido de entrada para um backend diferente do especificado nas definições de API. Para fins de demonstração neste artigo, crie uma API de teste e desa cimente a política de direcionar os pedidos de API para o backend do Service Fabric. 

### <a name="create-api"></a>Criar API

Siga os passos em [Adicionar uma API manualmente](add-api-manually.md) para criar uma API em branco.

* Nas definições de API, deixe o URL de **serviço Web** em branco.
* Adicione um **sufixo DE URL API,** como *tecido.*

  :::image type="content" source="media/backends/create-blank-api.png" alt-text="Criar API em branco":::

### <a name="add-get-operation-to-the-api"></a>Adicionar operação GET à API

Tal como mostrado no [Serviço de Back-end do Tecido de Serviço,](../service-fabric/service-fabric-tutorial-deploy-api-management.md#deploy-a-service-fabric-back-end-service)a amostra ASP.NET serviço Core implantado no cluster De Tecido de Serviço suporta uma única operação HTTP GET na trajetória URL `/api/values` .

A resposta padrão nesse caminho é uma matriz JSON de duas cordas:

```json
["value1", "value2"]
```

Para testar a integração da Gestão da API com o cluster, adicione a operação GET correspondente à API no `/api/values` caminho:

1. Selecione a API que criou no passo anterior.
1. Selecione **+ Adicionar Operação**.
1. Na janela **Frontend, introduza** os seguintes valores e selecione **Guardar**.

     | Definição             | Valor                             | 
    |---------------------|-----------------------------------|
    | **Nome a apresentar**    | *Backend de teste*                       |  
    | **URL** | GET                               | 
    | **URL**             | `/api/values`                           | 
    
    :::image type="content" source="media/backends/configure-get-operation.png" alt-text="Adicionar operação GET à API":::

### <a name="configure-set-backend-policy"></a>Política deconfigure `set-backend`

Adicione a [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) política à API de teste.

1. No **separador Design,** na secção **de processamento de Entrada,** selecione o ícone do editor de código **</>** () 
1. Posicione o cursor dentro do elemento **&lt; de entrada &gt;**
1. Adicione a seguinte declaração política. Em `backend-id` , substitua o nome do seu backend Desempenhia do Seu Tecido de Serviço.

   É `sf-resolve-condition` uma condição de repetição se a partição do aglomerado não for resolvida. O número de retréis foi definido ao configurar o backend.

    ```xml
    <set-backend-service backend-id="mysfbackend" sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")"  />
    ```
1. Selecione **Guardar**.

    :::image type="content" source="media/backends/set-backend-service.png" alt-text="Configure a política de serviço de backend-backend":::

### <a name="test-backend-api"></a>Teste backend API

1. No separador **Teste,** selecione a operação **GET** criada numa secção anterior.
1. Selecione **Enviar**.

Quando devidamente configurado, a resposta HTTP mostra um código de sucesso HTTP e exibe o JSON devolvido do serviço Backend Service Fabric.

:::image type="content" source="media/backends/test-backend-service.png" alt-text="Backend de tecido de serviço de teste":::

## <a name="next-steps"></a>Passos seguintes

* Saiba como [configurar políticas](api-management-advanced-policies.md) para encaminhar pedidos para um backend
* Backends também podem ser configurados usando os modelos API Management [REST API](/rest/api/apimanagement/2020-06-01-preview/backend), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)ou [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md)

