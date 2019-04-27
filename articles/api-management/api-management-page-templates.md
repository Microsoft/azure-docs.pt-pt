---
title: Página de modelos na gestão de API do Azure | Documentos da Microsoft
description: Saiba como personalizar o conteúdo de páginas de portal de programador com um conjunto de modelos na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1fbafcdab938a0f8653df48631d7733cc58a3668
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60656820"
---
# <a name="page-templates-in-azure-api-management"></a>Modelos de página na API Management do Azure
Gestão de API do Azure fornece-lhe a capacidade de personalizar o conteúdo de páginas de portal de programador usando um conjunto de modelos que configurar o seu conteúdo. Usando [DotLiquid](http://dotliquidmarkup.org/) sintaxe e o editor à sua escolha, tal como [DotLiquid para Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), e um conjunto fornecido de localizadas [recursos de cadeias de caracteres](api-management-template-resources.md#strings), [glifo recursos](api-management-template-resources.md#glyphs), e [controlos de página](api-management-page-controls.md), tem uma grande flexibilidade para configurar o conteúdo das páginas, conforme ache usá-los.  
  
 Os modelos nesta secção permitem-lhe personalizar o conteúdo de início de sessão, início de sessão a cópia de segurança e página não encontrada páginas no portal do programador.  
  
-   [Iniciar sessão](#SignIn)  
  
-   [Inscreva-se](#SignUp)  
  
-   [Página não encontrada](#PageNotFound)  
  
> [!NOTE]
>  Modelos predefinidos de exemplo estão incluídos na documentação do seguinte, mas estão sujeitas a alterações devido a melhorias contínuas. Pode ver os modelos predefinidos em direto no portal do programador ao navegar para os modelos individuais pretendidos. Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a> Iniciar sessão  
 O **iniciar sessão** modelo permite-lhe personalizar o página no portal do Programador de início de sessão.  
  
 ![Inicie sessão na página](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "início de sessão APIM nos modelos de Portal do desenvolvedor de páginas")  
  
### <a name="default-template"></a>Modelo predefinido  
  
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
 Este modelo pode usar o seguinte procedimento [controlos de página](api-management-page-controls.md).  
  
-   [basic-signin](api-management-page-controls.md#basic-signin)  
  
-   [Fornecedores](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Modelo de dados  
 [Início de sessão de utilizador](api-management-template-data-model-reference.md#UseSignIn) entidade.  
  
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
  
##  <a name="SignUp"></a> Inscreva-se  
 O **Inscreva-se** modelo permite-lhe personalizar a página no portal do Programador de inscrição.  
  
 ![Inscreva-se a página](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM início de sessão dos modelos de Portal do desenvolvedor de páginas")  
  
### <a name="default-template"></a>Modelo predefinido  
  
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
 Este modelo pode usar o seguinte procedimento [controlos de página](api-management-page-controls.md).  
  
-   [sign-up](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Modelo de dados  
 [Inscrição de utilizadores](api-management-template-data-model-reference.md#UserSignUp) entidade.  
  
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
  
##  <a name="PageNotFound"></a> Página não encontrada  
 O **página não encontrada** modelo permite-lhe personalizar a página não encontrada página no portal do programador.  
  
 ![Não foi encontrado página](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM não encontrado desenvolvedor de páginas dos modelos de portais")  
  
### <a name="default-template"></a>Modelo predefinido  
  
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
 Este modelo não pode utilizar qualquer [controlos de página](api-management-page-controls.md).  
  
### <a name="data-model"></a>Modelo de dados  
  
|Propriedade|Tipo|Descrição|  
|--------------|----------|-----------------|  
|referenceCode|string|Código gerado se esta página foi apresentada como resultado de um erro interno.|  
|errorCode|string|Código gerado se esta página foi apresentada como resultado de um erro interno.|  
|emailBody|string|E-mail body gerado se esta página foi apresentada como resultado de um erro interno.|  
|requestedUrl|string|O URL solicitado quando a página não foi encontrada.|  
|referrerUrl|string|O URL de referência para o URL pedido.|  
  
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

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre como trabalhar com modelos, consulte [como personalizar o portal do Programador de gestão de API através de modelos](api-management-developer-portal-templates.md).
