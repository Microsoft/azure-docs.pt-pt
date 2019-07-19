---
title: Guia de publicação técnica de aplicativos SaaS do Azure Marketplace
description: Guia passo a passo e publicação de listas de verificação para publicar aplicativos SaaS no Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: keithcharlie
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: kevidal
ms.openlocfilehash: 92c3452e07dee126666e6ee1fe0c46b1f8f6dfa4
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876523"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guia de publicação de aplicativos SaaS

Os aplicativos SaaS podem ser publicados no Marketplace com três chamadas diferentes para ação: "Entre em contato comigo", "Experimente agora" e "Obtenha agora". Este guia explica essas três opções, incluindo requisitos para cada uma delas. 

## <a name="offer-overview"></a>Visão geral da oferta  

Os aplicativos SaaS estão disponíveis nas lojas do Azure. a tabela a seguir descreve as opções disponíveis atuais:

| Opção de vitrine | Listagem | Avaliação/Transact |  
| --- | --- | --- |  
| AppSource | Sim (entre em contato comigo) | Sim (PowerBI/Dynamics) |
| Mercado do Azure | Não | Sim (aplicativos SaaS) |   

**Lista**  A opção de publicação de listagem consiste em um tipo de oferta contact me e é usada quando uma participação em nível de transação ou avaliação não é viável. A vantagem dessa abordagem é que ela permite que os editores com uma solução no mercado comecem imediatamente a receber clientes potenciais que podem ser transformados em negócios para aumentar o seu negócio.  
**Avaliação/transação:**  O cliente tem a opção de comprar diretamente ou solicitar uma avaliação para sua solução. Fornecer uma experiência de avaliação aumenta o nível de envolvimento oferecido aos clientes e permite que os clientes explorem sua solução antes de comprar. Com uma experiência de avaliação, você terá melhores chances de promoção nas vitrines, e você deve esperar mais clientes potenciais e mais ricos dos compromissos do cliente. As avaliações devem incluir suporte gratuito pelo menos durante o período de avaliação.  

| Oferta de aplicativos SaaS | Requisitos do Negócio | Requisitos Técnicos |  
| --- | --- | --- |  
| **Contacte-nos** | Sim | Não |  
| **PowerBI/Dynamics** | Sim | Sim (integração do Azure AD) |  
| **Aplicações SaaS**| Sim | Sim (integração do Azure AD) |     

## <a name="saas-list"></a>Lista de SaaS

O plano de ação para uma listagem de SaaS sem avaliação e nenhuma funcionalidade de cobrança é "entre em contato comigo". 

Você não precisa configurar Azure Active Directory para listar um aplicativo SaaS. 

|Requisitos  |Detalhes  |
|---------|---------|
|Seu aplicativo é uma oferta de SaaS  |   Sua solução é uma oferta de SaaS e você oferece um produto SaaS multilocatário.      |


## <a name="saas-trial"></a>Avaliação de SaaS

Você fornece uma solução ou um aplicativo usando uma avaliação baseada em SaaS (software como serviço) gratuita para teste. As ofertas de avaliação gratuita podem ser apresentadas como uma conta de avaliação de uso limitado ou de duração limitada. 


|Requisitos  |Detalhes  |
|---------|---------|
|Seu aplicativo é uma oferta de SaaS  |   Sua solução é uma oferta de SaaS e você oferece um produto SaaS multilocatário.      |
|Seu aplicativo está habilitado para AAD     |   O cliente será redirecionado ao seu domínio e você receberá o transacte diretamente com o cliente       |


## <a name="saas-trial-technical-requirements"></a>Requisitos técnicos de avaliação de SaaS

Os requisitos técnicos para aplicativos SaaS são simples. Os Publicadores só precisam ser integrados ao Azure Active Directory (Azure AD) para serem publicados. A integração do Azure AD com aplicativos é bem documentada e a Microsoft fornece vários SDKs e recursos para fazer isso.  

Para começar, recomendamos que você tenha uma assinatura dedicada à publicação do Azure Marketplace, permitindo que você isole o trabalho de outras iniciativas. Quando isso for feito, você poderá começar a implantar seu aplicativo SaaS nesta assinatura para iniciar o trabalho de desenvolvimento.  

A melhor documentação, exemplos e diretrizes da Azure Active Directory estão localizados nos seguintes sites: 

* [Guia do desenvolvedor de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integração com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrando aplicativos com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Roteiro do Azure-segurança e identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

Para tutoriais em vídeo, examine o seguinte:

* [Autenticação de Azure Active Directory com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Resumo técnico de identidade de Azure Active Directory – parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Resumo técnico de identidade de Azure Active Directory-parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Criando aplicativos com Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure vídeos focados em Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

O treinamento gratuito Azure Active Directory está disponível em  
* [Microsoft Azure para a série de conteúdo dos profissionais de ti: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Além disso, Azure Active Directory fornece um site para verificar se há atualizações de serviço   
* [Atualizações de serviço do Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Usando Azure Active Directory para habilitar avaliações  

A Microsoft autentica todos os usuários do Marketplace com o Azure AD, portanto, quando um usuário autenticado clica na sua listagem de avaliação no Marketplace e é redirecionado para seu ambiente de avaliação, você pode provisionar o usuário diretamente em uma avaliação sem a necessidade de um etapa de entrada adicional. O token que seu aplicativo recebe do Azure AD durante a autenticação inclui informações valiosas do usuário que você pode usar para criar uma conta de usuário em seu aplicativo, permitindo que você automatize a experiência de provisionamento e aumente a probabilidade de conversão. Para obter mais informações sobre o token, consulte tokens de [exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Usar o Azure AD para habilitar a autenticação de um clique em seu aplicativo ou avaliação faz o seguinte:  
* Simplifica a experiência do cliente do Marketplace para avaliação.  
* Mantém a sensação de uma "experiência no produto" mesmo quando o usuário é redirecionado do Marketplace para o seu ambiente de domínio ou de avaliação.  
* Diminui a probabilidade de abandono no redirecionamento porque não há uma etapa de entrada adicional.  
* Reduz as barreiras de implantação para a grande população de usuários do Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificando sua integração do Azure AD para o Marketplace  

Certificar sua integração do Azure AD de algumas maneiras diferentes, dependendo se seu aplicativo é de locatário único ou multilocatário e se você é novo no SSO (logon único) federado do Azure AD ou já dá suporte a ele.  

**Para aplicativos multilocatários:**  

Se você já oferece suporte ao Azure AD, faça o seguinte:
1.  Registrar seu aplicativo no portal do Azure
2.  Habilite o recurso de suporte multilocação no Azure AD para obter uma experiência de avaliação "com um clique". Informações mais específicas podem ser encontradas [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Se você for novo no SSO Federado do Azure AD, faça o seguinte: 
1.  Registrar seu aplicativo no portal do Azure
2.  Desenvolva o SSO com o Azure AD usando o [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ou o [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Habilite o recurso de suporte multilocação no AAD para obter uma experiência de avaliação com "um clique" mais informações específicas podem ser encontradas [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Para um aplicativo de locatário único, use qualquer uma das seguintes opções:**  
* Adicionar usuários ao seu diretório como usuários convidados usando o [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Provisionar manualmente Avaliações para clientes usando ' entrar em contato comigo '
* Desenvolver um ' test drive ' por cliente
* Criar um aplicativo de demonstração de exemplo multilocatário com SSO

## <a name="saas-subscriptions"></a>Assinaturas de SaaS

Use o tipo de oferta de aplicativo SaaS para permitir que seu cliente compre sua solução técnica baseada em SaaS como uma assinatura. Os requisitos a seguir devem ser atendidos para seu aplicativo SaaS:
- Preço e faturar o serviço a uma taxa plana, mensal.
- Forneça um método para atualizar ou cancelar o serviço a qualquer momento.
A Microsoft hospeda a transação de comércio. A Microsoft cobra seu cliente em seu nome. Para usar Bill a um aplicativo SaaS como uma assinatura, você deve habilitar sua própria API de serviço de gerenciamento de assinatura. Sua API de serviço de gerenciamento de assinatura deve se comunicar diretamente com as APIs de Azure Resource Manager. Sua API do serviço de gerenciamento de assinatura deve dar suporte ao provisionamento, atualização e cancelamento de serviço.

| Requisito | Detalhes |  
|:--- |:--- |  
|Cobrança e medição | Sua oferta é cobrada a uma taxa fixa mensal. Não há suporte para os preços baseados em uso e os recursos de "" verdadeiro "com base em uso no momento. |  
|Cancelamento | Sua oferta é cancelável pelo cliente a qualquer momento. |  
|Página de aterrissagem da transação | Você hospeda uma página de aterrissagem de transação de marca do Azure na qual os usuários podem criar e gerenciar sua conta de serviço SaaS. |   
| API de assinatura | Você expõe um serviço que pode interagir com a assinatura de SaaS para criar, atualizar e excluir uma conta de usuário e um plano de serviço. As alterações de API críticas devem ter suporte dentro de 24 horas. As alterações de API não críticas serão liberadas periodicamente. |  

>[!Note]
>A aceitação do canal de parceiros do CSP (provedores de soluções na nuvem) já está disponível.  Consulte os [provedores de soluções de nuvem](./cloud-solution-providers.md) para obter mais informações sobre como comercializar sua oferta por meio dos canais de parceiros do Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes
Se você ainda não fez isso,

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Faça logon no portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
- Consulte [oferta de aplicativo SaaS do Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer) para obter mais informações.
