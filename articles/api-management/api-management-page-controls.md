---
title: Azure API Management controle | Microsoft Docs
description: Saiba mais sobre os controlos de página disponíveis para uso em modelos de portal de desenvolvedores na Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b826c986a0af26bb79fb0823e4e8626f0165d460
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250028"
---
# <a name="azure-api-management-page-controls"></a>Controlos de página da AZure API
A Azure API Management fornece os seguintes controlos para utilização nos modelos do portal do desenvolvedor.  
  
Para utilizar um controlo, coloque-o na localização desejada no modelo do portal do desenvolvedor. Alguns controlos, como o controlo [de ações de aplicações,](#app-actions) têm parâmetros, como mostra o exemplo seguinte:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Os valores para os parâmetros são passados como parte do modelo de dados para o modelo. Na maioria dos casos, pode simplesmente colar no exemplo fornecido para que cada controlo funcione corretamente. Para obter mais informações sobre os valores dos parâmetros, pode consultar a secção do modelo de dados para cada modelo em que pode ser utilizado um controlo.  

Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controlos de página de modelo do portal do desenvolvedor  
  
-   [ações de aplicações](#app-actions)  
-   [signin básico](#basic-signin)  
-   [controlo de paging](#paging-control)  
-   [fornecedores](#providers)  
-   [controlo de pesquisa](#search-control)  
-   [inscrever-se](#sign-up)  
-   [subscreva botão](#subscribe-button)  
-   [subscrição-cancelamento](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> ações de aplicações  
 O `app-actions` controlo fornece uma interface de utilizador para interagir com aplicações na página de perfil do utilizador no portal do desenvolvedor.  
  
 ![controlo de ações&#45;de aplicações](./media/api-management-page-controls/APIM-app-actions-control.png "Controlo de ações de aplicações da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|appId|A identificação do requerimento.|  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `app-actions` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Aplicações](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> signin básico  
 O `basic-signin` controlo fornece um controlo para a recolha de informações de inscrição do utilizador na página de inscrição no portal do desenvolvedor.  
  
 ![controlo básico&#45;signin](./media/api-management-page-controls/APIM-basic-signin-control.png "Controlo básico de sinalização APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `basic-signin` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> controlo de paging  
 A `paging-control` funcionalidade de paging fornece funcionalidade sonora nas páginas do portal do programador que exibem uma lista de itens.  
  
 ![controlo de paging](./media/api-management-page-controls/APIM-paging-control.png "Controlo de paging APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `paging-control` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de edição](api-management-issue-templates.md#IssueList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> fornecedores  
 O `providers` controlo fornece um controlo para a seleção de fornecedores de autenticação na página de inscrição no portal do desenvolvedor.  
  
 ![controlo dos fornecedores](./media/api-management-page-controls/APIM-providers-control.png "Controlo de fornecedores da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `providers` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> controlo de pesquisa  
 A `search-control` funcionalidade de pesquisa fornece funcionalidades de pesquisa em páginas do portal do desenvolvedor que exibem uma lista de itens.  
  
 ![controlo de pesquisa](./media/api-management-page-controls/APIM-search-control.png "Controlo de pesquisa da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `search-control` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> inscrever-se  
 O `sign-up` controlo fornece um controlo para a recolha de informações de perfil do utilizador na página de inscrição no portal do desenvolvedor.  
  
 ![assinar&#45;até o controlo](./media/api-management-page-controls/APIM-sign-up-control.png "Controlo de inscrição da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `sign-up` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Inscrever-se](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscreva botão  
 O `subscribe-button` sistema fornece um controlo para a subscrição de um utilizador a um produto.  
  
 ![subscrever&#45;controlo de botões](./media/api-management-page-controls/APIM-subscribe-button-control.png "Controlo de botões de subscrição APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `subscribe-button` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Produto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscrição-cancelamento  
 O `subscription-cancel` controlo fornece um controlo para cancelar uma subscrição de um produto na página de perfil do utilizador no portal do desenvolvedor.  
  
 ![subscrição&#45;cancelar controlo](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Controlo de cancelamento de subscrição da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|subscriptionId|A identificação da assinatura para cancelar.|  
|cancelUrl|A subscrição cancela a URL.|  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvedores  
 O `subscription-cancel` controlo pode ser utilizado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Produto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](api-management-developer-portal-templates.md).
