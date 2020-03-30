---
title: Ative uma listagem Microsoft AppSource e Azure Marketplace utilizando o Diretório Ativo do Azure [ O Diretório Ativo] do Azure [ Azure
description: Ative um tipo de listagem utilizando o Diretório Ativo Azure no Azure Marketplace e appSource para editores de aplicações e serviços.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dsindona
ms.openlocfilehash: 45855038e60dcdc3be4f98cfdceed69df5e8c946
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286324"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Ativar uma listagem do AppSource e do Marketplace com o Azure Active Directory

 O Azure Ative Directory (Azure AD) é um serviço de identidade na nuvem que permite a autenticação com uma conta Microsoft. A Azure AD utiliza quadros padrão da indústria. [Saiba mais sobre o Diretório Ativo Azure.](https://azure.microsoft.com/services/active-directory)

## <a name="azure-ad-benefits"></a>Benefícios da AD Azure

Os clientes do Microsoft AppSource e do Azure Marketplace utilizam experiências no produto para pesquisar os catálogos de listagem. Estas ações exigem que os clientes assinem o produto. A integração da AD Azure proporciona os seguintes benefícios:

- Envolvimento mais rápido e uma experiência otimizada do cliente
- Inscrição única (SSO) para milhões de utilizadores empresariais
- Experiência consistente e de inscrição entre aplicações publicadas por diferentes parceiros
- Autenticação escalável e transversal para aplicações móveis e cloud

## <a name="offers-that-require-azure-ad"></a>Ofertas que requerem Anúncio Azure

As várias [opções de listagem e tipos](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) de oferta para AppSource e Azure Marketplace têm diferentes requisitos para a implementação da AD Azure. Consulte a tabela seguinte para mais detalhes:

| **Tipo de oferta**    | **Azure AD SSO necessário?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Contacte-me | Avaliação | Versão de Teste | Transt |
| Máquina Virtual | N/D | Não | Não | Não |
| Aplicativos Azure (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos geridos  | N/D | N/D | N/D | Não |
| SaaS  | Não | Sim | Sim | Sim |
| Contentores  | N/D | N/D | N/D | Não |
| Serviços de Consultoria  | Não | N/D | N/D | N/D |

Para mais informações sobre os requisitos técnicos do SaaS, consulte o Guia de Publicação de [Oferta de Aplicações SaaS.](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)

## <a name="azure-ad-integration"></a>Integração da AD Azure

- Para obter informações sobre como ativar um único sign-on integrando o Azure AD na sua listagem, consulte [o Diretório Ativo Do Azure para programadores]( https://aka.ms/aaddev).
- Para obter detalhes sobre o único sinal de entrada da Azure AD, veja [o que é o acesso à aplicação e o único registo com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="enable-a-trial-listing"></a>Ativar uma listagem de julgamento

A configuração automatizada do cliente pode aumentar a probabilidade de conversão. Quando o seu cliente seleciona a sua listagem de teste e é redirecionado para o seu ambiente experimental, pode configurar o cliente diretamente sem necessitar de passos adicionais de inscrição.

Durante a autenticação, a Azure AD envia um sinal para a sua app ou oferta. As informações fornecidas pelo utilizador pelo token permitem a criação de uma conta de utilizador na sua app ou oferta. Para saber mais, consulte [as fichas da amostra.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

Quando utilizar o Azure AD para ativar a autenticação de um clique na sua aplicação ou listagem de testes, o mesmo:

- Agilize a experiência do cliente do Marketplace para a sua listagem de testes.
- Mantenha a sensação de uma experiência no produto mesmo quando o utilizador é redirecionado do Marketplace para o seu domínio ou ambiente experimental.
- Reduza a probabilidade de abandono quando os utilizadores são redirecionados porque não existem etapas adicionais de inscrição.
- Reduza as barreiras de implantação para a grande população de utilizadores de Anúncios Azure.

## <a name="verify-azure-ad-integration"></a>Verificar a integração da AD Azure

### <a name="multitenant-solutions"></a>Soluções multiarrendatárias

Utilize a AD Azure para apoiar as seguintes ações:

- Registe a sua aplicação numa das montras do Marketplace. Consulte o registo de [aplicativos](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) ou a [certificação AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) para obter mais informações.
- Ative a funcionalidade de suporte multitenancy em Azure AD para obter uma experiência de teste de um clique.

Se você é novo em usar Azure AD federado único sign-on, tome estes passos:

1. Registe a sua aplicação no Marketplace.
1. Desenvolva SSO com AD Azure utilizando [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ou [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Ative a funcionalidade de suporte multitenancy em Azure AD para proporcionar uma experiência de teste de um clique.

### <a name="single-tenant-solutions"></a>Soluções de inquilino único

Utilize a AD Azure para apoiar uma das seguintes ações:

- Adicione os utilizadores convidados ao seu diretório utilizando [o Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Configurar manualmente os ensaios para os clientes utilizando a opção de publicação **Contact me.**
- Desenvolver um test drive por cliente.
- Construa uma aplicação de demonstração de amostras multi-inquilinos que utilize O SSO.

## <a name="next-steps"></a>Passos seguintes

- Certifique-se de que [se registou no Azure Marketplace.](https://azuremarketplace.microsoft.com/sell)
- Veja [como criar uma conta partner center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para obter mais informações sobre como criar ou completar a sua oferta.
