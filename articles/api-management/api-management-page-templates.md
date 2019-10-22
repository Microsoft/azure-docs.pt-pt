---
title: Modelos de página no gerenciamento de API do Azure | Microsoft Docs
description: Saiba como personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1189689566da84df6d6b8845bf7c81bd8aa5f1ab
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70072232"
---
# <a name="page-templates-in-azure-api-management"></a>Modelos de página no gerenciamento de API do Azure
O gerenciamento de API do Azure fornece a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configuram seu conteúdo. Usando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor de sua escolha, como [DotLiquid para designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto fornecido de [recursos de cadeia de caracteres](api-management-template-resources.md#strings)localizados, recursos de [glifos](api-management-template-resources.md#glyphs)e [controles de página](api-management-page-controls.md), você tem grande flexibilidade para configurar o conteúdo das páginas como você vê para se ajustar usando esses modelos.  
  
 Os modelos nesta seção permitem que você personalize o conteúdo das páginas entrar, inscrever-se e página não encontrada no portal do desenvolvedor.  
  
-   [Entrar](#SignIn)  
  
-   [Inscreva-se](#SignUp)  
  
-   [Página não encontrada](#PageNotFound)  
  
> [!NOTE]
>  Os modelos padrão de exemplo estão incluídos na documentação a seguir, mas estão sujeitos a alterações devido a melhorias contínuas. Você pode exibir os modelos padrão ao vivo no portal do desenvolvedor navegando até os modelos individuais desejados. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a>Entrar  
 O modelo de **entrada** permite que você personalize a página de entrada no portal do desenvolvedor.  
  
 ![Página de entrada](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Modelos do portal do desenvolvedor da página de entrada do APIM")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Controlos  
 Este modelo pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [entrada básica](api-management-page-controls.md#basic-signin)  
  
-   [fornecedor](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Modelo de dados  
 Entidade [de entrada do usuário](api-management-template-data-model-reference.md#UseSignIn) .  
  
### <a name="sample-template-data"></a>Dados de modelo de exemplo  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="SignUp"></a>Inscrição  
 O modelo de **inscrição** permite que você personalize a página de inscrição no portal do desenvolvedor.  
  
 ![Página de inscrição](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "Página de inscrição do APIM modelos do portal do desenvolvedor")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Controlos  
 Este modelo pode usar os seguintes [controles de página](api-management-page-controls.md).  
  
-   [inscrever-se](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Modelo de dados  
 Entidade [de inscrição do usuário](api-management-template-data-model-reference.md#UserSignUp) .  
  
### <a name="sample-template-data"></a>Dados de modelo de exemplo  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a>Página não encontrada  
 O modelo **página não encontrada** permite que você personalize a página página não encontrada no portal do desenvolvedor.  
  
 ![Página não encontrada](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM de página não encontrado modelos do portal do desenvolvedor")  
  
### <a name="default-template"></a>Modelo padrão  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Controlos  
 Este modelo não pode usar nenhum [controle de página](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|referenceCode|string|Código gerado se essa página foi exibida como resultado de um erro interno.|  
|ErrorCode|string|Código gerado se essa página foi exibida como resultado de um erro interno.|  
|emailBody|string|O corpo do email gerado se essa página foi exibida como resultado de um erro interno.|  
|requestedUrl|string|A URL solicitada quando a página não foi encontrada.|  
|referrerUrl|string|A URL de referenciador para a URL solicitada.|  
  
### <a name="sample-template-data"></a>Dados de modelo de exemplo  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do desenvolvedor de gerenciamento de API usando modelos](api-management-developer-portal-templates.md).
