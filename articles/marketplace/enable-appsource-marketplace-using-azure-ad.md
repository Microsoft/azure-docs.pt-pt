---
title: Ativar uma listagem de Microsoft AppSource e do Azure Marketplace utilizando o Azure Active Directory | Azure
description: Ative um tipo de lista com o Azure Active Directory no Azure Marketplace e AppSource para editores de aplicações e serviços.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 950371fd92d255ed01dfa89d5c2fcbded686a971
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443084"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Ativar uma listagem do AppSource e do Marketplace com o Azure Active Directory

 Azure Active Directory (Azure AD) é um serviço de identidade de cloud que permite a autenticação com uma conta Microsoft. Azure AD utiliza estruturas de norma da indústria. [Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Benefícios do Azure AD

Os clientes do Microsoft AppSource e do Azure Marketplace utilizam experiências no produto para procurar os catálogos de listagem. Estas ações requerem aos clientes iniciar sessão para o produto. Integração do Azure AD fornece as seguintes vantagens:

- Envolvimento mais rápido e uma experiência otimizada do cliente
- Início de sessão único (SSO) para milhões de utilizadores empresariais
- Experiência consistente e início de sessão em todas as aplicações publicadas por diferentes parceiros
- Autenticação escalável e multiplataformas para dispositivos móveis e aplicações na cloud

## <a name="offers-that-require-azure-ad"></a>Ofertas que necessitam do Azure AD

As várias [opções de listagem e oferecem tipos](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para AppSource e do Azure Marketplace têm diferentes requisitos para a implementação do Azure AD. Consulte a tabela seguinte para obter mais detalhes:

| **Tipo de oferta**    | **Necessário SSO do Azure AD?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Contactar-Me | Avaliação | Versão de Teste | Transact |
| Máquina virtual | N/A | Não | Não | Não |
| Aplicações do Azure (modelo de solução)  | N/A | N/D | N/D | N/A |
| Aplicações geridas  | N/A | N/D | N/A | Não |
| SaaS  | Não | Sim | Sim | Sim |
| Contentores  | N/A | N/D | N/A | Não |
| Serviços de consultoria  | Não | N/A | N/D | N/A |

Para obter mais informações sobre os requisitos técnicos de SaaS, veja [guia de publicação da oferta de aplicações de SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integração do Azure AD

- Para obter informações sobre como ativar o início de sessão único com a integração do Azure AD em sua listagem, consulte [do Azure Active Directory para programadores]( https://aka.ms/aaddev).
- Para obter detalhes sobre o Azure AD início de sessão único, consulte [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Ativar uma listagem de avaliação

Configuração do cliente automatizada pode aumentar a probabilidade de conversão. Quando o cliente seleciona sua listagem de avaliação e é redirecionado para o seu ambiente de avaliação, pode configurar o cliente diretamente sem a necessidade de passos de início de sessão adicionais.

Durante a autenticação, o Azure AD envia um token à sua aplicação ou oferta. As informações de utilizador fornecidas pelo token permitem a criação de uma conta de utilizador na sua aplicação ou oferta. Para obter mais informações, consulte [tokens de exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Quando utilizar o Azure AD para ativar a autenticação de um clique na sua aplicação ou uma lista de avaliação,:

- Simplificar a experiência do cliente do Marketplace para sua listagem de avaliação.
- Manter o funcionamento de uma experiência no produto até mesmo quando o utilizador é redirecionado a partir do Marketplace para o seu ambiente de domínio ou de avaliação.
- Reduza a probabilidade de abandonment quando os utilizadores são redirecionados porque existem não existem passos de início de sessão adicionais.
- Reduza as barreiras de implementação para a população grande de utilizadores do Azure AD.

## <a name="verify-azure-ad-integration"></a>Certifique-se de integração do Azure AD

### <a name="multitenant-solutions"></a>Soluções de multi-inquilinos

Utilize o Azure AD para suportar as seguintes ações:

- Registe a sua aplicação das lojas do Marketplace. Modo de exibição [registo de aplicações](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) ou [certificação AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) para obter mais informações.
- Ative a funcionalidade de suporte de arquitetura "multitenancy" no Azure AD para obter uma experiência de avaliação num único clique.

Se estiver a utilizar federado do Azure AD início de sessão único, siga estes passos:

1. Registe a sua aplicação no Marketplace.
1. Desenvolver com o SSO com o Azure AD [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ou [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Ative a funcionalidade de suporte de arquitetura "multitenancy" no Azure AD para fornecer uma experiência de avaliação num único clique.

### <a name="single-tenant-solutions"></a>Soluções de inquilino único

Utilize o Azure AD para suportar uma das seguintes ações:

- Adicionar utilizadores convidados para o seu diretório utilizando [do Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Configure manualmente as versões de avaliação para os clientes, utilizando o **contactar-Me** opção de publicação.
- Desenvolva uma versão de teste de cada cliente.
- Crie uma aplicação de demonstração de exemplo do multi-inquilino que utiliza o SSO.

## <a name="next-steps"></a>Passos Seguintes

- Certifique-se de que [registado no Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Ver [como criar uma conta do Centro de parceiros](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para obter mais informações sobre como criar ou concluir sua oferta.
