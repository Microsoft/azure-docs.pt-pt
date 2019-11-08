---
title: Habilitar uma listagem do Microsoft AppSource e do Azure Marketplace usando Azure Active Directory | Azure
description: Habilite um tipo de listagem usando Azure Active Directory no Azure Marketplace e AppSource para Publicadores de aplicativo e serviço.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/12/2018
ms.author: pabutler
ms.openlocfilehash: 9423263270313659017b16a5e34511e7ef385af5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825891"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Ativar uma listagem do AppSource e do Marketplace com o Azure Active Directory

 O Azure Active Directory (AD do Azure) é um serviço de identidade de nuvem que habilita a autenticação com um conta Microsoft. O Azure AD usa estruturas padrão do setor. [Saiba mais sobre Azure Active Directory](https://azure.microsoft.com/services/active-directory).

## <a name="azure-ad-benefits"></a>Benefícios do Azure AD

Os clientes do Microsoft AppSource e do Azure Marketplace usam experiências no produto para pesquisar os catálogos de listagem. Essas ações exigem que os clientes entrem no produto. A integração do Azure AD oferece os seguintes benefícios:

- Compromisso mais rápido e uma experiência otimizada para o cliente
- SSO (logon único) para milhões de usuários empresariais
- Experiência de entrada consistente entre aplicativos publicados por parceiros diferentes
- Autenticação escalonável e de plataforma cruzada para aplicativos móveis e de nuvem

## <a name="offers-that-require-azure-ad"></a>Ofertas que exigem o Azure AD

As várias [Opções de listagem e tipos de oferta](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) para AppSource e Azure Marketplace têm requisitos diferentes para a implementação do Azure AD. Consulte a tabela a seguir para obter detalhes:

| **Tipo de oferta**    | **O SSO do Azure AD é necessário?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Entre em contato comigo | Avaliação | Versão de Teste | Transact |
| Máquina Virtual | N/D | Não | Não | Não |
| Aplicativos do Azure (modelo de solução)  | N/D | N/D | N/D | N/D |
| Aplicativos gerenciados  | N/D | N/D | N/D | Não |
| SaaS  | Não | Sim | Sim | Sim |
| Contentores  | N/D | N/D | N/D | Não |
| Serviços de consultoria  | Não | N/D | N/D | N/D |

Para obter mais informações sobre os requisitos técnicos de SaaS, consulte [Guia de publicação de aplicativos SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Integração do Azure AD

- Para obter informações sobre como habilitar o logon único integrando o Azure AD à sua listagem, consulte [Azure Active Directory para desenvolvedores]( https://aka.ms/aaddev).
- Para obter detalhes sobre o logon único do Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Habilitar uma listagem de avaliação

A configuração automatizada do cliente pode aumentar a probabilidade de conversão. Quando o cliente seleciona sua listagem de avaliação e é redirecionado para seu ambiente de avaliação, você pode configurar o cliente diretamente sem a necessidade de etapas de entrada adicionais.

Durante a autenticação, o Azure AD envia um token para seu aplicativo ou oferta. As informações de usuário fornecidas pelo token permitem a criação de uma conta de usuário em seu aplicativo ou oferta. Para saber mais, confira [tokens de exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Ao usar o Azure AD para habilitar a autenticação com um clique no aplicativo ou na listagem de avaliação, você:

- Simplifique a experiência do cliente do Marketplace para sua listagem de avaliação.
- Mantenha a sensação de uma experiência no produto mesmo quando o usuário for redirecionado do Marketplace para o seu ambiente de domínio ou de avaliação.
- Reduza a probabilidade de abandono quando os usuários são redirecionados porque não há etapas de entrada adicionais.
- Reduza as barreiras de implantação para a grande população de usuários do Azure AD.

## <a name="verify-azure-ad-integration"></a>Verificar a integração do Azure AD

### <a name="multitenant-solutions"></a>Soluções multilocatários

Use o Azure AD para dar suporte às seguintes ações:

- Registre seu aplicativo em uma das vitrines do Marketplace. Exiba o [registro de aplicativo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) ou a [certificação AppSource](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified) para obter mais informações.
- Habilite o recurso de suporte multilocação no Azure AD para obter uma experiência de avaliação com um clique.

Se você não estiver familiarizado com o uso do logon único federado do Azure AD, execute estas etapas:

1. Registre seu aplicativo no Marketplace.
1. Desenvolva o SSO com o Azure AD usando [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) ou [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code).
1. Habilite o recurso de suporte multilocação no Azure AD para fornecer uma experiência de avaliação com um clique.

### <a name="single-tenant-solutions"></a>Soluções de locatário único

Use o Azure AD para dar suporte a uma das seguintes ações:

- Adicione usuários convidados ao seu diretório usando o [Azure ad B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
- Configure manualmente as avaliações para clientes usando a opção de publicação **entre em contato comigo** .
- Desenvolva um test drive por cliente.
- Crie um aplicativo de demonstração de exemplo multilocatário que usa o SSO.

## <a name="next-steps"></a>Passos seguintes

- Certifique-se de que você se [registrou no Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
- Consulte [como criar uma conta do Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) para obter mais informações sobre como criar ou concluir sua oferta.
