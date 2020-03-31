---
title: Controlos da página azure API Management / Microsoft Docs
description: Conheça os controlos de página disponíveis para utilização em modelos de portal de desenvolvimento na Gestão aPI Azure.
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
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244020"
---
# <a name="azure-api-management-page-controls"></a>Controlos da página azure API Management
A Azure API Management fornece os seguintes controlos para utilização nos modelos do portal do desenvolvedor.  
  
Para utilizar um controlo, coloque-o no local desejado no modelo do portal do desenvolvedor. Alguns controlos, como o controlo [de aplicações,](#app-actions) têm parâmetros, como mostra o seguinte exemplo:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Os valores dos parâmetros são passados como parte do modelo de dados para o modelo. Na maioria dos casos, pode simplesmente colar o exemplo fornecido para cada controlo para que funcione corretamente. Para obter mais informações sobre os valores do parâmetro, pode ver a secção do modelo de dados para cada modelo em que um controlo pode ser utilizado.  

Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controlos de página de modelo de portal do desenvolvedor  
  
-   [app-actions](#app-actions)  
-   [signina básica](#basic-signin)  
-   [paging-control](#paging-control)  
-   [fornecedores](#providers)  
-   [controlo de pesquisa](#search-control)  
-   [inscrição](#sign-up)  
-   [botão de assinatura](#subscribe-button)  
-   [cancelamento de assinatura](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a>app-actions  
 O `app-actions` controlo fornece uma interface de utilizador para interagir com aplicações na página do perfil do utilizador no portal do desenvolvedor.  
  
 ![controlo de ações&#45;app](./media/api-management-page-controls/APIM-app-actions-control.png "Controlo de ações de aplicações da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|appId|A identificação do pedido.|  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `app-actions` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Aplicações](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a>signina básica  
 O `basic-signin` controlo fornece um controlo para recolher informações de acesso ao utilizador na página de sessão no portal do desenvolvedor.  
  
 ![controlo de sinalização&#45;básico](./media/api-management-page-controls/APIM-basic-signin-control.png "Controlo de sinalização básica APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `basic-signin` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>paging-control  
 A `paging-control` funcionalidade de paging nas páginas do portal do programador que exibem uma lista de itens.  
  
 ![controlo de paging](./media/api-management-page-controls/APIM-paging-control.png "Controlo de paging APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `paging-control` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Lista API](api-management-api-templates.md#APIList)  
  
-   [Lista de emissão](api-management-issue-templates.md#IssueList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>fornecedores  
 O `providers` controlo fornece um controlo para a seleção de fornecedores de autenticação na página de inscrição no portal do desenvolvedor.  
  
 ![controlo de fornecedores](./media/api-management-page-controls/APIM-providers-control.png "Controlo de fornecedores APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `providers` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a>controlo de pesquisa  
 A `search-control` funcionalidade de pesquisa nas páginas do portal do programador que exibem uma lista de itens.  
  
 ![controlo de pesquisa](./media/api-management-page-controls/APIM-search-control.png "Controlo de pesquisa da APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `search-control` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Lista API](api-management-api-templates.md#APIList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>inscrição  
 O `sign-up` controlo fornece um controlo para recolher informações sobre o perfil do utilizador na página de inscrição no portal do desenvolvedor.  
  
 ![assinar&#45;controlo](./media/api-management-page-controls/APIM-sign-up-control.png "Controlo de inscrição apim apim")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `sign-up` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Registar-me](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a>botão de assinatura  
 O `subscribe-button` fornece um controlo para subscrever um utilizador a um produto.  
  
 ![subscrever&#45;controlo de botões](./media/api-management-page-controls/APIM-subscribe-button-control.png "Controlo de botões de subscrição APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `subscribe-button` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Produto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a>cancelamento de assinatura  
 O `subscription-cancel` controlo fornece um controlo para cancelar uma subscrição de um produto na página de perfil do utilizador no portal do desenvolvedor.  
  
 ![&#45;cancelar o controlo](./media/api-management-page-controls/APIM-subscription-cancel-control.png "Controlo de cancelamento de subscrição apim apim")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|subscriptionId|A identificação da assinatura para cancelar.|  
|cancelarUrl|A subscrição cancela URL.|  
  
### <a name="developer-portal-templates"></a>Modelos de portal de desenvolvimento  
 O `subscription-cancel` controlo pode ser utilizado nos seguintes modelos de portal de desenvolvimento:  
  
-   [Produto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o trabalho com modelos, consulte como personalizar o portal de desenvolvimento de [gestão da API utilizando modelos](api-management-developer-portal-templates.md).
