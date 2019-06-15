---
title: Controlos de página de gestão de API do Azure | Documentos da Microsoft
description: Saiba mais sobre os controles de página disponíveis para utilização em modelos de portais de programador na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60656987"
---
# <a name="azure-api-management-page-controls"></a>Controlos de página de gestão de API do Azure
Gestão de API do Azure fornece os seguintes controlos para utilização em que o desenvolvedor modelos de portais.  
  
Para usar um controle, coloque-a no local desejado no modelo do portal do desenvolvedor. Alguns controles, como o [ações de aplicação](#app-actions) controlar, ter parâmetros, como mostrado no exemplo a seguir:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Os valores para os parâmetros são transmitidos em como parte do modelo de dados para o modelo. Na maioria dos casos, pode simplesmente colar no exemplo fornecido para cada controle para que funcione corretamente. Para obter mais informações sobre os valores de parâmetros, pode ver a secção de modelo de dados para cada modelo no qual um controle pode ser utilizado.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controlos de página do modelo do portal de programador  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [Fornecedores](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> ações de aplicação  
 O `app-actions` controle fornece uma interface do usuário para interagir com aplicações na página de perfil do utilizador no portal do programador.  
  
 ![aplicação&#45;controlo de ações](./media/api-management-page-controls/APIM-app-actions-control.png "controlo de ações de aplicação do APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|appId|O id da aplicação.|  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `app-actions` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Aplicações](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> basic-signin  
 O `basic-signin` controle fornece um controle para a recolha de informações de início de sessão do utilizador a página de início de sessão no portal do programador.  
  
 ![básico&#45;controlo de início de sessão](./media/api-management-page-controls/APIM-basic-signin-control.png "controlo de início de sessão básico do APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `basic-signin` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> controle de paginação  
 O `paging-control` fornece a funcionalidade de paginação no desenvolvedor de páginas de portal que apresentam uma lista de itens.  
  
 ![controle de paginação](./media/api-management-page-controls/APIM-paging-control.png "controle de paginação de APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `paging-control` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de problemas](api-management-issue-templates.md#IssueList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> Fornecedores  
 O `providers` controle fornece um controlo de seleção de provedores de autenticação na página de início de sessão no portal do programador.  
  
 ![controlo de provedores](./media/api-management-page-controls/APIM-providers-control.png "controle de fornecedores APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `providers` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 O `search-control` fornece funcionalidade de pesquisa no desenvolvedor de páginas de portal que apresentam uma lista de itens.  
  
 ![controlo de pesquisa](./media/api-management-page-controls/APIM-search-control.png "controle de pesquisa do APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `search-control` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Lista de API](api-management-api-templates.md#APIList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> sign-up  
 O `sign-up` controle fornece um controle para recolher informações de perfil de utilizador na página de inscrição no portal do programador.  
  
 ![início de sessão&#45;configurar o controlo](./media/api-management-page-controls/APIM-sign-up-control.png "controlo de inscrição de APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `sign-up` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Inscreva-se](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscrever-botão  
 O `subscribe-button` fornece um controlo de assinatura de um utilizador a um produto.  
  
 ![Subscreva&#45;controlo de botão](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM subscrever-controle de botão")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `subscribe-button` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Produto](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> Cancelar subscrição  
 O `subscription-cancel` controle fornece um controle para cancelar uma subscrição a um produto na página de perfil do utilizador no portal do programador.  
  
 ![subscrição&#45;Cancelar controle](./media/api-management-page-controls/APIM-subscription-cancel-control.png "controlo de cancelamento da subscrição do APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|subscriptionId|O id da subscrição para cancelar.|  
|cancelUrl|A subscrição cancela o URL.|  
  
### <a name="developer-portal-templates"></a>Modelos de portais de programador  
 O `subscription-cancel` controle pode ser usado em modelos de portal de programador seguintes:  
  
-   [Produto](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](api-management-developer-portal-templates.md).