---
title: Integre a sua oferta de mercado comercial da Microsoft com o Azure Ative Directory
description: Utilize o Azure Ative Directory para autenticar as suas ofertas microsoft AppSource e Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 07/24/2020
ms.openlocfilehash: c70d976a05cbeed058243829d0658693341636e9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131212"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integre a sua listagem de marketplace comercial com o Azure Ative Directory

 Este artigo explica os requisitos para integrar uma listagem de marketplace comercial ou oferta com o Azure Ative Directory (Azure AD). Azure AD é um serviço de identidade em nuvem que utiliza quadros padrão da indústria para permitir a autenticação com uma conta Microsoft. [Saiba mais sobre o Azure Ative Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Benefícios da AD Azure

Os clientes do Microsoft AppSource e do Azure Marketplace usam experiências no produto para pesquisar catálogos de listagem de lojas online. Estas ações exigem que os clientes inscrevam-se no produto. A integração AZURE AD proporciona os seguintes benefícios:

- Envolvimento mais rápido e uma experiência de cliente otimizada
- SSO (SSO) único para milhões de utilizadores empresariais
- Experiência consistente e de inscrição em todas as aplicações publicadas por diferentes parceiros
- Autenticação escalável e transversal para aplicações móveis e cloud

## <a name="offers-that-require-azure-ad"></a>Ofertas que requerem Azure AD

As várias [opções de listagem de](determine-your-listing-type.md) mercado comercial e tipos de oferta têm diferentes requisitos para a implementação da AD Azure. Consulte a seguinte tabela para mais detalhes.

| Tipo de oferta    | Azure AD SSO necessário para contactar-me?  | Azure AD SSO necessário para julgamento? | Azure AD SSO necessário para test drive?  | Azure AD SSO necessário para a Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Máquina Virtual | N/D | No | No | No |
| Azure Apps (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos geridos  | N/D | N/D | N/D | No |
| SaaS  | No | Yes | Yes | Yes |
| Contentores  | N/D | N/D | N/D | No |
| Serviços de Consultoria  | No | N/D | N/D | N/D |

Para obter mais informações sobre os requisitos técnicos da SaaS, consulte [a Azure AD e as ofertas transacionáveis da SaaS no mercado comercial.](./azure-ad-saas.md)

## <a name="azure-ad-integration"></a>Integração AD AZure

- Para mais detalhes sobre como integrar o Azure AD para o seu software transacionável como um serviço (SaaS) oferece, consulte [a AZure AD e as ofertas transacionáveis saaS no mercado comercial.](./azure-ad-saas.md)
- Para obter informações sobre como permitir um único sing-on integrando o Azure AD na sua listagem, consulte [o Azure Ative Directory para programadores](../active-directory/develop/index.yml).
- Para obter detalhes sobre o Azure AD single sign-on, veja [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="enable-a-trial-listing"></a>Ativar uma lista geminada de ensaios

A configuração automatizada do cliente pode aumentar a probabilidade de conversão. Quando o seu cliente seleciona a sua lista de testes e é redirecionado para o seu ambiente de teste, pode configurar o cliente diretamente sem exigir etapas adicionais de inscrição.

Durante a autenticação, a Azure AD envia um símbolo para a sua app ou oferta. As informações fornecidas pelo token permitem a criação de uma conta de utilizador na sua app ou oferta. Para saber mais, consulte [os tokens sample](../active-directory/develop/id-tokens.md).

Quando utiliza o AZure AD para ativar a autenticação de um clique na sua aplicação ou listagem de testes, você:

- Dinamize a experiência do cliente do mercado comercial até à sua listagem de testes.
- Mantenha a sensação de uma experiência no produto mesmo quando o utilizador é redirecionado do mercado comercial para o seu domínio ou ambiente de teste.
- Reduza a probabilidade de abandono quando os utilizadores são redirecionados porque não existem passos adicionais de inscrição.
- Reduzir as barreiras de implantação para a grande população de utilizadores de AD Azure.

## <a name="verify-azure-ad-integration"></a>Verifique a integração da AD Azure

### <a name="multitenant-solutions"></a>Soluções multitenant

Utilize a Azure AD para apoiar as seguintes ações:

- Registe a sua aplicação numa das lojas online do mercado comercial. Ver [registo de App](../active-directory/develop/quickstart-register-app.md) ou [certificação AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md) para mais informações.
- Ative a funcionalidade de suporte multitenancy no AZure AD para obter uma experiência de teste de um clique.

Se você é novo em usar Azure AD federado único sinal, tome estes passos:

1. Registe a sua aplicação no mercado comercial.
1. Desenvolver SSO com Azure AD utilizando [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) ou [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Ativar a funcionalidade de suporte multitenancy no Azure AD para proporcionar uma experiência de teste de um clique.

### <a name="single-tenant-solutions"></a>Soluções de inquilino único

Utilize a Azure AD para apoiar uma das seguintes ações:

- Adicione os utilizadores convidados ao seu diretório utilizando [o Azure AD B2B](../active-directory/external-identities/what-is-b2b.md).
- Configurar manualmente os ensaios para os clientes utilizando a opção de publicação **Contacte-me.**
- Desenvolver um test drive por cliente.
- Construa uma app de demonstração de amostras multi-inquilino que usa SSO.

## <a name="next-steps"></a>Passos seguintes

Se ainda não o fez, 

- [Conheça](https://azuremarketplace.microsoft.com/sell) o mercado comercial.

Para se registar no Partner Center, comece a criar uma nova oferta ou a trabalhar numa existente:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.