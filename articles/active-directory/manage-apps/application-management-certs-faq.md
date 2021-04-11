---
title: Certificados de Gestão de Aplicações Azure Ative Application Management frequentemente perguntas
description: Aprenda respostas a perguntas frequentes (FAQ) sobre a gestão de certificados para apps usando o Azure Ative Directory como Fornecedor de Identidade (IdP).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: secherka, mifarca, shchaur, shravank, sureshja
ms.openlocfilehash: 928bf02e2d628379738483b40631e36f0f929176
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803807"
---
# <a name="azure-active-directory-azure-ad-application-management-certificates-frequently-asked-questions"></a>Certificados de Gestão de Aplicações Azure Ative (Azure AD) frequentemente questionados

Esta página responde frequentemente a perguntas sobre a gestão dos certificados para apps usando o Azure Ative Directory (Azure AD) como Fornecedor de Identidade (IdP).

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Existe uma maneira de gerar uma lista de certificados de assinatura SAML expirando?

Pode exportar todos os registos de aplicações com segredos expirando, certificados e seus proprietários para as aplicações especificadas do seu diretório em um ficheiro CSV através [de scripts PowerShell](app-management-powershell-samples.md). 

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Onde posso encontrar a informação sobre em breve expirar as etapas de renovação dos certificados?

Pode encontrar os passos [aqui](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Como posso personalizar a data de validade dos certificados emitidos pela Azure AD?

Por predefinição, o Azure AD configura um certificado para expirar após três anos quando é criado automaticamente durante a configuração de um único sinal de inscrição DA SAML. Como não pode alterar a data de um certificado depois de o guardar, precisa de criar um novo certificado. Para etapas sobre como fazê-lo, consulte [Personalize a data de validade do certificado da federação e reenrmine-a para um novo certificado](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Como posso automatizar as notificações de expiração dos certificados?

A Azure AD enviará uma notificação por e-mail 60, 30 e 7 dias antes do termo do certificado SAML. Pode adicionar mais de um endereço de e-mail para receber notificações. 

> [!NOTE]
> Pode adicionar até 5 endereços de e-mail à lista de Notificações (incluindo o endereço de e-mail do administrador que adicionou a aplicação). Se precisar de mais pessoas para ser notificado, use os e-mails da lista de distribuição. 

Para especificar os e-mails a que pretende que as notificações sejam enviadas, consulte [os endereços de notificação de e-mail para expiração do certificado](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration).

Não existe opção de editar ou personalizar estas notificações de email recebidas de `aadnotification@microsoft.com` . No entanto, pode exportar registos de aplicações com segredos e certificados expirando através [de scripts PowerShell.](app-management-powershell-samples.md)

## <a name="who-can-update-the-certificates"></a>Quem pode atualizar os certificados?

O proprietário da aplicação ou Administrador Global ou Administrador de Aplicações pode atualizar os certificados através do portal Azure UI, PowerShell ou Microsoft Graph.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Preciso de mais detalhes sobre as opções de assinatura de certificados.

No Azure AD, pode configurar opções de assinatura de certificado e o algoritmo de assinatura de certificado. Para saber mais, consulte [opções avançadas de assinatura de certificados SAML para aplicações AD AZure.](certificate-signing-options.md)

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Preciso substituir o certificado para aplicações de procuração de aplicações AD AZure e precisar de mais instruções.

Para substituir certificados para aplicações de procuração de aplicações AD AD Azure, consulte [a amostra PowerShell - Substitua o certificado nas aplicações Proxy de aplicações](scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Como posso gerir certificados para domínios personalizados no Azure AD Application Proxy?

Para configurar uma aplicação no local para utilizar um domínio personalizado, precisa de um domínio personalizado do Azure Ative Directory, um certificado PFX para o domínio personalizado e uma aplicação no local para configurar. Para saber mais, consulte [os domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md). 

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Preciso atualizar o certificado de assinatura do símbolo no lado da inscrição. Onde posso obtê-lo no lado da AD do Azure?

Pode renovar um Certificado SAML X.509, consulte [o certificado de assinatura SAML](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>O que é a Azure AD a assinar a chave de capotamento da chave?

Pode encontrar mais detalhes [aqui.](../develop/active-directory-signing-key-rollover.md) 

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Como posso renovar o certificado de encriptação do token da aplicação?

Para renovar um certificado de encriptação de ficha de aplicação, consulte [como renovar um certificado de encriptação simbólica para uma aplicação da empresa.](howto-saml-token-encryption.md) 

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Como posso renovar o certificado de assinatura de ficha de inscrição?

Para renovar um certificado de assinatura de ficha de pedido, consulte [Como renovar um certificado de assinatura simbólica para um pedido de empresa](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Como atualizei o Azure AD depois de mudar os certificados da federação?

Para atualizar o Azure AD depois de alterar os certificados da federação, consulte [os certificados da federação Renovar para o Microsoft 365 e o Azure Ative Directory](../hybrid/how-to-connect-fed-o365-certs.md).
