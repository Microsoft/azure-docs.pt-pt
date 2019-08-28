---
title: Controles de página de gerenciamento de API do Azure | Microsoft Docs
description: Saiba mais sobre os controles de página disponíveis para uso em modelos de portal do desenvolvedor no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: b720d64f5b0a5f66375083b7854658aa754db150
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072293"
---
# <a name="azure-api-management-page-controls"></a>Controles de página de gerenciamento de API do Azure
O gerenciamento de API do Azure fornece os seguintes controles para uso nos modelos de portal do desenvolvedor.  
  
Para usar um controle, coloque-o no local desejado no modelo do portal do desenvolvedor. Alguns controles, como o controle de [ações de aplicativo](#app-actions) , têm parâmetros, conforme mostrado no exemplo a seguir:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Os valores para os parâmetros são passados como parte do modelo de dados para o modelo. Na maioria dos casos, você pode simplesmente colar o exemplo fornecido para cada controle para que ele funcione corretamente. Para obter mais informações sobre os valores de parâmetro, você pode ver a seção de modelo de dados para cada modelo no qual um controle pode ser usado.  
  
 Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Controles de página de modelo do portal do desenvolvedor  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [Fornecedores](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [assinar-botão](#subscribe-button)  
-   [assinatura-cancelar](#subscription-cancel)  
  
##  <a name="app-actions"></a>ações de aplicativo  
 O `app-actions` controle fornece uma interface do usuário para interagir com aplicativos na página de perfil do usuário no portal do desenvolvedor.  
  
 ![controle&#45;de ações do aplicativo](./media/api-management-page-controls/APIM-app-actions-control.png "Aplicativo APIM – controle de ações")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|appId|A ID do aplicativo.|  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `app-actions` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Aplicações](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>entrada básica  
 O `basic-signin` controle fornece um controle para coletar informações de entrada do usuário na página de entrada no portal do desenvolvedor.  
  
 ![controle&#45;de entrada básico](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM básico – controle de entrada")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `basic-signin` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>controle de paginação  
 O `paging-control` fornece a funcionalidade de paginação nas páginas do portal do desenvolvedor que exibem uma lista de itens.  
  
 ![controle] de paginação (./media/api-management-page-controls/APIM-paging-control.png "Controle") de paginação APIM  
  
### <a name="usage"></a>Utilização  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `paging-control` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Lista de APIs](api-management-api-templates.md#APIList)  
  
-   [Lista de problemas](api-management-issue-templates.md#IssueList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>fornecedor  
 O `providers` controle fornece um controle para a seleção de provedores de autenticação na página de entrada no portal do desenvolvedor.  
  
 ![controle de provedores](./media/api-management-page-controls/APIM-providers-control.png "Controle de provedores de APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `providers` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Iniciar sessão](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>controle de pesquisa  
 O `search-control` fornece a funcionalidade de pesquisa nas páginas do portal do desenvolvedor que exibem uma lista de itens.  
  
 ![controle de pesquisa](./media/api-management-page-controls/APIM-search-control.png "Controle de pesquisa do APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `search-control` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Lista de APIs](api-management-api-templates.md#APIList)  
  
-   [Lista de produtos](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>inscrever-se  
 O `sign-up` controle fornece um controle para coletar informações de perfil de usuário na página de inscrição no portal do desenvolvedor.  
  
 ![controle&#45;de inscrição](./media/api-management-page-controls/APIM-sign-up-control.png "Controle de inscrição APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `sign-up` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Inscreva-se](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>assinar-botão  
 O `subscribe-button` fornece um controle para inscrever um usuário em um produto.  
  
 ![controle&#45;do botão assinar](./media/api-management-page-controls/APIM-subscribe-button-control.png "Controle de botão de assinatura APIM")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parâmetros  
 Nenhum.  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `subscribe-button` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Remessa](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>assinatura-cancelar  
 O `subscription-cancel` controle fornece um controle para cancelar uma assinatura de um produto na página de perfil do usuário no portal do desenvolvedor.  
  
 ![controle&#45;] de cancelamento de assinatura (./media/api-management-page-controls/APIM-subscription-cancel-control.png "Assinatura do APIM – cancelar controle")  
  
### <a name="usage"></a>Utilização  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parâmetros  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|subscriptionId|A ID da assinatura a ser cancelada.|  
|cancelUrl|A assinatura cancela a URL.|  
  
### <a name="developer-portal-templates"></a>Modelos do portal do desenvolvedor  
 O `subscription-cancel` controle pode ser usado nos seguintes modelos de portal do desenvolvedor:  
  
-   [Remessa](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).