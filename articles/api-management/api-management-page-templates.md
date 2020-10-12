---
title: Modelos de página na Azure API Management / Microsoft Docs
description: Saiba como personalizar o conteúdo dos modelos de página do portal do programador na Azure API Management.
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
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 24d026785025dba4ae45de404edec67c2cf3871a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335590"
---
# <a name="page-templates-in-azure-api-management"></a>Modelos de página na Gestão API da Azure
A Azure API Management fornece-lhe a capacidade de personalizar o conteúdo das páginas do portal do desenvolvedor usando um conjunto de modelos que configuram o seu conteúdo. Utilizando a sintaxe [DotLiquid](http://dotliquidmarkup.org/) e o editor à sua escolha, como [DotLiquid para Designers,](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)e um conjunto fornecido de recursos de [cordas](api-management-template-resources.md#strings)localizados, [recursos glifos](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), você tem uma grande flexibilidade para configurar o conteúdo das páginas como você acha adequado usando estes modelos.  
  
 Os modelos desta secção permitem personalizar o conteúdo do sinal, inscrever-se e página não encontrada no portal do desenvolvedor.  
  
-   [Iniciar sessão](#SignIn)  
  
-   [Registar-me](#SignUp)  
  
-   [Página não encontrada](#PageNotFound)  
  
> [!NOTE]
>  Os modelos predefinidos da amostra estão incluídos na seguinte documentação, mas estão sujeitos a alterações devido a melhorias contínuas. Pode ver os modelos de predefinição ao vivo no portal do desenvolvedor navegando para os modelos individuais pretendidos. Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a> Iniciar sessão  
 O modelo de **sinal permite-lhe** personalizar o sinal na página no portal do desenvolvedor.  
  
 ![Assinar na página](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Sinal de APIM em modelos de portal de desenvolvedor de página")  
  
### <a name="default-template"></a>Modelo de padrão  
  
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
 Este modelo pode utilizar os [seguintes controlos de página.](api-management-page-controls.md)  
  
-   [signin básico](api-management-page-controls.md#basic-signin)  
  
-   [fornecedores](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Modelo de dados  
 [Sinal de utilizador na](api-management-template-data-model-reference.md#UseSignIn) entidade.  
  
### <a name="sample-template-data"></a>Dados do modelo de amostra  
  
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
  
##  <a name="sign-up"></a><a name="SignUp"></a> Registar-me  
 O modelo **de inscrição** permite-lhe personalizar a página de inscrição no portal do desenvolvedor.  
  
 ![Inscreva-se na página](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM inscreva-se modelos do portal do desenvolvedor de página")  
  
### <a name="default-template"></a>Modelo de padrão  
  
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
 Este modelo pode utilizar os [seguintes controlos de página.](api-management-page-controls.md)  
  
-   [inscrever-se](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Modelo de dados  
 [Entidade de inscrição do](api-management-template-data-model-reference.md#UserSignUp) utilizador.  
  
### <a name="sample-template-data"></a>Dados do modelo de amostra  
  
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
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a> Página não encontrada  
 O modelo **de página não encontrado** permite-lhe personalizar a página não encontrada no portal do desenvolvedor.  
  
 ![Página não encontrada](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "Modelos do portal do desenvolvedor de páginas não encontrados")  
  
### <a name="default-template"></a>Modelo de padrão  
  
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
 Este modelo não pode utilizar quaisquer [controlos de página](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|referênciasCódigo|string|Código gerado se esta página foi apresentada como resultado de um erro interno.|  
|errorCode|string|Código gerado se esta página foi apresentada como resultado de um erro interno.|  
|emailCorpo|string|O corpo de e-mail gerado se esta página foi apresentada como resultado de um erro interno.|  
|solicitadoUrl|string|A URL solicitou quando a página não foi encontrada.|  
|referrerUrl|string|O URL de referência para a URL solicitada.|  
  
### <a name="sample-template-data"></a>Dados do modelo de amostra  
  
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
Para obter mais informações sobre o trabalho com modelos, consulte [como personalizar o portal de desenvolvedores da API Management utilizando modelos](api-management-developer-portal-templates.md).
