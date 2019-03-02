---
title: Técnico de aplicações de SaaS do Azure Marketplace guia de publicação
description: Guia passo a passo e listas de verificação de publicação para a publicação de aplicações de SaaS no Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
documentationcenter: ''
author: keithcharlie
manager: nunoc
editor: keithcharlie
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/09/2018
ms.author: keithcharlie
ms.openlocfilehash: b653b0276cedea1e3b45adf7a9dc390b24f0d03f
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213624"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guia de publicação da oferta de aplicações de SaaS

Aplicações SaaS podem ser publicadas no marketplace com três chamadas diferentes a ação: "Contacte-Me," "Experimente agora" e "Get-lo agora." Este guia explica estas três opções, incluindo os requisitos para cada um. 

## <a name="offer-overview"></a>Descrição geral da oferta  

Aplicações SaaS estão disponíveis em ambas as lojas do Azure a tabela seguinte descreve as opções disponíveis atuais:

| Opção vitrine | Listagem | Versão de avaliação/Transact |  
| --- | --- | --- |  
| AppSource | Sim (contactar-Me) | Sim (Power BI/Dynamics) |
| Mercado do Azure | Não | Sim (aplicações de SaaS) |   

**Lista de:**  A opção de publicação de listagem é composta por um contacto-Me o tipo de oferta e é utilizado quando uma participação de nível de versão de avaliação ou de transação não é viável. A vantagem dessa abordagem é que permite que os publicadores com uma solução de colocação no mercado começar imediatamente a receção de oportunidades potenciais que podem ser transformados em negociações para aumentar a sua empresa.  
**Versão de avaliação/transação:**  O cliente tem a opção de comprar ou peça uma avaliação para a sua solução de diretamente. Para proporcionar uma experiência de avaliação, aumenta o nível de envolvimento oferecido aos clientes e permite que os clientes explorar a solução antes de comprar. Com uma experiência de avaliação, terá mais chances de promoção nas lojas e deve esperar oportunidades potenciais mais e mais avançadas do envolvimento com o cliente. As versões de avaliação tem de incluir suporte gratuito, pelo menos, durante o período de avaliação.  

| Oferta de aplicações de SaaS | Requisitos do Negócio | Requisitos Técnicos |  
| --- | --- | --- |  
| **Contacte-nos** | Sim | Não |  
| **Power BI / Dynamics** | Sim | Sim (integração do Azure AD) |  
| **Aplicações SaaS**| Sim | Sim (integração do Azure AD) |     

## <a name="saas-list"></a>Lista de SaaS

O apelo à ação para obter uma listagem de SaaS com nenhuma versão de avaliação e nenhuma funcionalidade de faturação é "Contacte meu redor". 

Não é necessário configurar o Azure Active Directory para listar uma aplicação SaaS. 

|Requisitos  |Detalhes  |
|---------|---------|
|A aplicação é um oferta de SaaS  |   A sua solução é um oferta de SaaS e oferecer um produto de SaaS multi-inquilino.      |


## <a name="saas-trial"></a>SaaS Trial

Fornecer uma solução ou a aplicação com o gratuito-para-Experimente, software-como-serviço (SaaS)-com base em versão de avaliação. Ofertas de avaliação gratuita podem ser apresentadas como uma conta de avaliação de utilização limitada ou com duração limitada. 


|Requisitos  |Detalhes  |
|---------|---------|
|A aplicação é um oferta de SaaS  |   A sua solução é um oferta de SaaS e oferecer um produto de SaaS multi-inquilino.      |
|A sua aplicação está ativado do AAD     |   O cliente será novamente direcionado ao seu domínio e será transact diretamente com o cliente       |


## <a name="saas-trial-technical-requirements"></a>Requisitos técnicos de versão de avaliação de SaaS

Os requisitos técnicos para aplicações SaaS são simples. Só os publicadores têm de ser integradas no Azure Active Directory (Azure AD) para ser publicado. Integração do AD do Azure com aplicações esteja bem documentada e a Microsoft fornece vários SDKs e recursos para conseguir isso.  

Para começar, é recomendável que tem uma subscrição dedicada para a publicação do Azure Marketplace, permitindo-lhe isolar o trabalho a partir de outras iniciativas. Em seguida pode começar a implementar a aplicação SaaS nesta subscrição para iniciar o trabalho de desenvolvimento.  

A documentação do Azure Active Directory, exemplos e documentação de orientação melhor estão localizados nos seguintes sites: 

* [Guia para programadores do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integração com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integrar aplicações com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Mapa do Azure - segurança e identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

Para tutoriais em vídeo, reveja o seguinte:

* [Autenticação do Azure Active Directory com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Resumo de técnicas de identidade do Active Directory do Azure - parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Resumo de técnicas de identidade do Active Directory do Azure - parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Criar aplicações com o Microsoft Azure Active Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Vídeos do Microsoft Azure, com foco no Active Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Formação gratuita do Azure Active Directory está disponível em  
* [Microsoft Azure para IT Pros Content Series: Azure Active Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Além disso, o Azure Active Directory fornece um site para verificar a existência de atualizações de serviço   
* [Atualizações de serviço do Azure AD](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Utilizar o Azure Active Directory para ativar as versões de avaliação  

A Microsoft autentica todos os utilizadores do Marketplace com o Azure AD, portanto, quando um utilizador autenticado clica na sua listagem de avaliação gratuita no Marketplace e é redirecionado para o seu ambiente de avaliação, pode aprovisionar o utilizador diretamente para uma versão de avaliação sem a necessidade de um início de sessão passo adicional. O token que a aplicação recebe do Azure AD durante a autenticação inclui informações de utilizador valioso que pode utilizar para criar uma conta de utilizador na sua aplicação, permitindo-lhe automatizar a experiência de aprovisionamento e aumentar a probabilidade de conversão. Para obter mais informações sobre o token, consulte [Tokens de exemplo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Utilizar o Azure AD para ativar a autenticação de 1 Clique à sua aplicação ou a versão de avaliação faz o seguinte:  
* Simplifica a experiência do cliente do Marketplace à versão de avaliação.  
* Mantém o funcionamento de uma experiência"produto", mesmo quando do utilizador é redirecionado do Marketplace para o seu domínio ou o ambiente de avaliação.  
* Diminui a probabilidade de abandonment no redirecionamento porque não existe um passo adicional do início de sessão.  
* Reduz as barreiras de implementação para a população grande de utilizadores do Azure AD.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificação da sua integração do Azure AD para o Marketplace  

Pode certificar a sua integração do AD do Azure de diversas formas diferentes, dependendo de seu aplicativo é inquilino único ou vários inquilinos e se estiver familiarizado para único federado do Azure AD início de sessão (SSO), ou já suportá-la.  

**Para aplicações multi-inquilino:**  

Se já tiver suporte para o Azure AD, faça o seguinte:
1.  Registar a sua aplicação no portal do Azure
2.  Ative a funcionalidade de suporte de vários inquilinos do Azure AD para obter uma experiência de avaliação de um só clique. Pode encontrar informações mais específicas [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications).  

Se estiver familiarizado com o SSO federado. do Azure AD, efetue o seguinte: 
1.  Registar a sua aplicação no portal do Azure
2.  Desenvolver o SSO com a utilização do Azure AD [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ou [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Ativar o suporte de vários inquilinos pode ser encontrado o recurso do AAD para obter informações mais específicas da experiência de avaliação num só clique [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Para a aplicação de inquilino único, utilize qualquer uma das seguintes opções:**  
* Adicionar utilizadores ao seu diretório, como os utilizadores convidados com [B2B do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Aprovisionar manualmente as versões de avaliação para clientes com "Contactar-Me"
* Desenvolver um versão de teste por-cliente
* Criar uma aplicação de demonstração de exemplo do multi-inquilino com SSO

## <a name="saas-subscriptions"></a>Subscrições de SaaS

Utilize o tipo de oferta de aplicação SaaS para ativar o seu cliente comprar a sua solução baseada em SaaS, técnica como uma subscrição. Tem de ser cumpridos os seguintes requisitos para a sua aplicação SaaS:
- Preços e faturação do serviço numa taxa mensal fixa.
- Fornecem um método para atualizar ou cancelar o serviço a qualquer momento.
A Microsoft hospeda a transação de comércio. A Microsoft cobra o cliente em seu nome. Para utilizar uma aplicação SaaS de fatura como uma subscrição, tem de ativar API do serviço de gestão própria subscrição. A API de serviço de gestão de subscrição têm de comunicar diretamente com as APIs do Azure Resource Manager. A API de serviço de gestão de subscrição tem de suportar o aprovisionamento do serviço, atualizar e cancelar.

| Requisito | Detalhes |  
|:--- |:--- |  
|Faturação e medição | A oferta é o preço de uma taxa fixa mensal. Com base na utilização de preços e capacidades de baseada na utilização de "true-up" não são suportadas neste momento. |  
|Cancelamento | A oferta é pode ser cancelada pelo cliente em qualquer altura. |  
|Página de destino de transação | Alojar uma página de destino de transação de associação de marcas do Azure, onde os utilizadores podem criar e gerir as respetivas contas de serviço SaaS. |   
| API de subscrição | Expor um serviço que pode interagir com a subscrição de SaaS para criar, atualizar e eliminar um plano de serviço e de conta de utilizador. Críticas de API de alterações têm de ser suportadas dentro de 24 horas. Alterações na API não críticas serão lançadas periodicamente. |  

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora está disponível.  Veja [fornecedores de soluções Cloud](./cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="next-steps"></a>Passos Seguintes
Se ainda não fez isso,

- [Registar](https://azuremarketplace.microsoft.com/sell) no marketplace.

Se é registrado e estiver a criar uma nova oferta ou trabalhando num já existente

- [Inicie sessão no Portal de parceiro de Cloud](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
- Ver [oferta de aplicação SaaS do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-offer) para obter mais informações.
