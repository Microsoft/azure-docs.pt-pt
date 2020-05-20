---
title: Integre a sua oferta de marketplace comercial da Microsoft com o Azure Ative Directory
description: Utilize o Diretório Ativo Azure para autenticar as ofertas do Microsoft AppSource e do Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 17cbfe92744ad96f2b5651b7e2f47a6443337068
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658040"
---
# <a name="integrate-your-commercial-marketplace-listing-with-azure-active-directory"></a>Integre a sua listagem de marketplace comercial com o Azure Ative Directory

 Este artigo explica os requisitos para integrar uma listagem ou oferta de marketplace comercial com o Azure Ative Directory (Azure AD). O Azure AD é um serviço de identidade na nuvem que utiliza quadros padrão da indústria para permitir a autenticação com uma conta Microsoft. [Saiba mais sobre o Diretório Ativo Azure.](https://azure.microsoft.com/services/active-directory)

## <a name="azure-ad-benefits"></a>Benefícios da AD Azure

Os clientes microsoft AppSource e Azure Marketplace usam experiências no produto para pesquisar catálogos de listagem de lojas. Estas ações exigem que os clientes assinem o produto. A integração da AD Azure proporciona os seguintes benefícios:

- Envolvimento mais rápido e uma experiência otimizada do cliente
- Inscrição única (SSO) para milhões de utilizadores empresariais
- Experiência consistente e de inscrição entre aplicações publicadas por diferentes parceiros
- Autenticação escalável e transversal para aplicações móveis e cloud

## <a name="offers-that-require-azure-ad"></a>Ofertas que requerem Anúncio Azure

As várias [opções de listagem de](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) mercado comercial e tipos de oferta têm requisitos diferentes para a implementação da AD Azure. Consulte a tabela seguinte para mais detalhes.

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

- Para obter informações sobre como ativar um único sign-on integrando o Azure AD na sua listagem, consulte [o Diretório Ativo Do Azure para programadores]( https://docs.microsoft.com/azure/active-directory/develop/).
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

Se ainda não o fez, 

- [Saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o mercado.

Para se registar no Partner Center, comece a criar uma nova oferta ou a trabalhar numa existente:

- [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
