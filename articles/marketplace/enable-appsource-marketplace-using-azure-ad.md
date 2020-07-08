---
title: Integre a sua oferta de mercado comercial da Microsoft com o Azure Ative Directory
description: Utilize o Azure Ative Directory para autenticar as suas ofertas microsoft AppSource e Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: e2fff55fd9161aa41411dcb1abbf8ae0c1bae49c
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85957839"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integre a sua listagem de marketplace comercial com o Azure Ative Directory

 Este artigo explica os requisitos para integrar uma listagem de marketplace comercial ou oferta com o Azure Ative Directory (Azure AD). Azure AD é um serviço de identidade em nuvem que utiliza quadros padrão da indústria para permitir a autenticação com uma conta Microsoft. [Saiba mais sobre o Azure Ative Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Benefícios da AD Azure

Os clientes do Microsoft AppSource e do Azure Marketplace usam experiências no produto para pesquisar catálogos de listagem de lojas. Estas ações exigem que os clientes inscrevam-se no produto. A integração AZURE AD proporciona os seguintes benefícios:

- Envolvimento mais rápido e uma experiência de cliente otimizada
- SSO (SSO) único para milhões de utilizadores empresariais
- Experiência consistente e de inscrição em todas as aplicações publicadas por diferentes parceiros
- Autenticação escalável e transversal para aplicações móveis e cloud

## <a name="offers-that-require-azure-ad"></a>Ofertas que requerem Azure AD

As várias [opções de listagem de](determine-your-listing-type.md) mercado comercial e tipos de oferta têm diferentes requisitos para a implementação da AD Azure. Consulte a seguinte tabela para mais detalhes.

| Tipo de oferta    | Azure AD SSO necessário para contactar-me?  | Azure AD SSO necessário para julgamento? | Azure AD SSO necessário para test drive?  | Azure AD SSO necessário para a Transact |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
| Máquina Virtual | N/D | Não | Não | Não |
| Azure Apps (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos geridos  | N/D | N/D | N/D | Não |
| SaaS  | Não | Sim | Sim | Sim |
| Contentores  | N/D | N/D | N/D | Não |
| Serviços de Consultoria  | Não | N/D | N/D | N/D |

Para obter mais informações sobre os requisitos técnicos do SaaS, consulte [o Guia de Publicação de Ofertas de Aplicações SaaS.](marketplace-saas-applications-technical-publishing-guide.md)

## <a name="azure-ad-integration"></a>Integração AD AZure

- Para obter informações sobre como permitir um único sing-on integrando o Azure AD na sua listagem, consulte [o Azure Ative Directory para programadores](../active-directory/develop/index.yml).
- Para obter detalhes sobre o Azure AD single sign-on, veja [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](../active-directory/manage-apps/what-is-single-sign-on.md)

## <a name="enable-a-trial-listing"></a>Ativar uma lista geminada de ensaios

A configuração automatizada do cliente pode aumentar a probabilidade de conversão. Quando o seu cliente seleciona a sua lista de testes e é redirecionado para o seu ambiente de teste, pode configurar o cliente diretamente sem exigir etapas adicionais de inscrição.

Durante a autenticação, a Azure AD envia um símbolo para a sua app ou oferta. As informações fornecidas pelo token permitem a criação de uma conta de utilizador na sua app ou oferta. Para saber mais, consulte [os tokens sample](../active-directory/develop/id-tokens.md).

Quando utiliza o AZure AD para ativar a autenticação de um clique na sua aplicação ou listagem de testes, você:

- Dinamize a experiência do cliente do Marketplace para a sua listagem de testes.
- Mantenha a sensação de uma experiência no produto mesmo quando o utilizador é redirecionado do Marketplace para o seu domínio ou ambiente de teste.
- Reduza a probabilidade de abandono quando os utilizadores são redirecionados porque não existem passos adicionais de inscrição.
- Reduzir as barreiras de implantação para a grande população de utilizadores de AD Azure.

## <a name="verify-azure-ad-integration"></a>Verifique a integração da AD Azure

### <a name="multitenant-solutions"></a>Soluções multitenant

Utilize a Azure AD para apoiar as seguintes ações:

- Registe a sua aplicação numa das montras do Marketplace. Ver [registo de App](../active-directory/develop/quickstart-register-app.md) ou [certificação AppSource](../active-directory/azuread-dev/howto-get-appsource-certified.md) para mais informações.
- Ative a funcionalidade de suporte multitenancy no AZure AD para obter uma experiência de teste de um clique.

Se você é novo em usar Azure AD federado único sinal, tome estes passos:

1. Registe a sua aplicação no Marketplace.
1. Desenvolver SSO com Azure AD utilizando [OAuth 2.0](../active-directory/azuread-dev/v1-protocols-oauth-code.md) ou [OpenID Connect](../active-directory/azuread-dev/v1-protocols-openid-connect-code.md).
1. Ativar a funcionalidade de suporte multitenancy no Azure AD para proporcionar uma experiência de teste de um clique.

### <a name="single-tenant-solutions"></a>Soluções de inquilino único

Utilize a Azure AD para apoiar uma das seguintes ações:

- Adicione os utilizadores convidados ao seu diretório utilizando [o Azure AD B2B](../active-directory/b2b/what-is-b2b.md).
- Configurar manualmente os ensaios para os clientes utilizando a opção de publicação **Contacte-me.**
- Desenvolver um test drive por cliente.
- Construa uma app de demonstração de amostras multi-inquilino que usa SSO.

## <a name="next-steps"></a>Próximos passos

Se ainda não o fez, 

- [Conheça](https://azuremarketplace.microsoft.com/sell) o mercado.

Para se registar no Partner Center, comece a criar uma nova oferta ou a trabalhar numa existente:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
