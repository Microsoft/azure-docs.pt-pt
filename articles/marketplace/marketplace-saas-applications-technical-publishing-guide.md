---
title: Guia de publicação de aplicações azure Marketplace SaaS
description: Guia passo a passo e listas de verificação para publicação de aplicações SaaS no Azure Marketplace
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 1bc2feb4d8f3f103802bd823c66cd3f54ac5d74a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687546"
---
# <a name="saas-applications-offer-publishing-guide"></a>SaaS aplica guia de publicação

As aplicações SaaS podem ser publicadas no mercado com três chamadas diferentes para a ação: "Contacte-me", "Experimente agora", e "Get It Now". Este guia explica estas três opções, incluindo requisitos para cada um. 

## <a name="offer-overview"></a>Visão geral da oferta  

Aplicações SaaS estão disponíveis em ambas as Lojas Azure O seguinte quadro descreve as opções disponíveis atuais:

| Opção storefront | Listagem | Julgamento/Transact |  
| --- | --- | --- |  
| AppSource | Sim | Sim |
| Mercado Azure | Não | Sim |   

**Lista:**  A opção de publicação de Listagem consiste num tipo de oferta contacte-me e é utilizada quando não é viável uma participação a nível de Teste ou Transação. O benefício desta abordagem é que permite que os editores com uma solução no mercado comecem imediatamente a receber pistas que podem ser transformadas em negócios para aumentar o seu negócio.  
**Trial/Transação:**  O cliente tem a opção de comprar ou solicitar diretamente um teste para a sua solução. A experiência Experimental aumenta o nível de envolvimento oferecido aos clientes e permite que os clientes explorem a sua solução antes de comprar. Com uma experiência de Trial, você terá melhores chances de promoção nas montras, e você deve esperar mais e mais pistas mais ricas a partir de compromissos com o cliente. Os ensaios devem incluir apoio gratuito, pelo menos durante o período experimental.  

| Oferta de aplicativos SaaS | Requisitos empresariais | Requisitos Técnicos |  
| --- | --- | --- |  
| **Contacte-nos** | Sim | Não |  
| **PowerBI / Dinâmica** | Sim | Sim (integração da AD Azure) |  
| **Aplicações SaaS**| Sim | Sim (integração da AD Azure) |     

## <a name="saas-list"></a>Lista SaaS

A chamada para uma listagem SaaS sem julgamento e sem funcionalidade de faturação é "Contacte-me". 

Não é necessário configurar o Diretório Ativo Azure para listar uma aplicação SaaS. 

|Requisitos  |Detalhes  |
|---------|---------|
|A sua aplicação é uma oferta SaaS  |   A sua solução é uma oferta SaaS e oferece um produto SaaS multiarrendatário.      |


## <a name="saas-trial"></a>Julgamento de SaaS

Fornece uma solução ou app utilizando um teste baseado em software-as-a-service (SaaS) baseado em software.as-a-service (SaaS). As ofertas de experimentação gratuitas podem ser apresentadas como uma conta experimental de uso limitado ou de duração limitada. 


|Requisitos  |Detalhes  |
|---------|---------|
|A sua aplicação é uma oferta SaaS  |   A sua solução é uma oferta SaaS e oferece um produto SaaS multiarrendatário.      |
|A sua aplicação está ativada por AAD     |   O cliente será redirecionado para o seu domínio e irá transacionar com o cliente diretamente       |


## <a name="saas-trial-technical-requirements"></a>Requisitos técnicos de ensaio saaS

Os requisitos técnicos para aplicações SaaS são simples. As editoras só são obrigadas a ser integradas com o Azure Ative Directory (Azure AD) para serem publicados. A integração da Azure AD com aplicações está bem documentada e a Microsoft fornece vários SDKs e recursos para o conseguir.  

Para começar, recomendamos que tenha uma subscrição dedicada à sua publicação Azure Marketplace, permitindo-lhe isolar o trabalho de outras iniciativas. Uma vez feito isto, pode começar a implementar a sua aplicação SaaS nesta subscrição para iniciar o trabalho de desenvolvimento.  

A melhor documentação, amostras e orientação do Diretório Ativo Azure estão localizados nos seguintes locais: 

* [Guia de desenvolvedor estoma ativo azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)

* [Integração com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-to-integrate)

* [Integração de Aplicações com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

* [Azure Roadmap - Segurança e Identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

Para tutoriais de vídeo, reveja o seguinte:

* [Autenticação do Diretório Ativo Azure com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Briefing Técnico de Identidade do Diretório Ativo Azure - Parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Briefing Técnico de Identidade do Diretório Ativo Azure - Parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Construir aplicativos com o Diretório Ativo microsoft Azure](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Vídeos Microsoft Azure focados no Diretório Ativo](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

Formação gratuita de Diretório Ativo Azure está disponível em  
* [Microsoft Azure para IT Pros Content Series: Diretório Ativo Azure](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Além disso, o Azure Ative Directory fornece um site para verificar se há Atualizações de Serviço   
* [Atualizações do Serviço AD Azure](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Utilização do Diretório Ativo Azure para permitir ensaios  

A Microsoft autentica todos os utilizadores do Marketplace com AD Azure, pelo que quando um utilizador autenticado clica através da sua listagem de Teste no Marketplace e é redirecionado para o seu ambiente Trial, pode fornecer o utilizador diretamente a um Trial sem necessitar de um passo adicional de entrada. O símbolo que a sua aplicação recebe do Azure AD durante a autenticação inclui informações valiosas do utilizador que pode utilizar para criar uma conta de utilizador na sua app, permitindo-lhe automatizar a experiência de provisionamento e aumentar a probabilidade de conversão. Para obter mais informações sobre o símbolo, consulte [Sample Tokens](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims) .

Utilizar o AD Azure para permitir a autenticação de 1 clique na sua aplicação ou o Trial faz o seguinte:  
* Simplifica a experiência do cliente do Marketplace para o Trial.  
* Mantém a sensação de uma "experiência no produto" mesmo quando o utilizador é redirecionado do Marketplace para o seu domínio ou ambiente experimental.  
* Diminui a probabilidade de abandono no redirecionamento porque não há um passo adicional de inscrição.  
* Reduz as barreiras de implantação para a grande população de utilizadores de Anúncios Azure.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificando a sua integração de Anúncios Azure para o Marketplace  

Certifique a sua integração de AD Azure de várias maneiras, dependendo se a sua aplicação é de inquilino único ou multi-inquilino, e se você é novo em Azure AD federado único sign-on (SSO), ou já o suporta.  

**Para candidaturas multi-arrendatárias:**  

Se já apoia a Azure AD, faça o seguinte:
1.    Registe a sua candidatura no portal Azure
2.    Ative a funcionalidade de suporte multi-arrendamento em Azure AD para obter uma experiência de teste de "um clique". Informações mais específicas podem ser encontradas [aqui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)  

Se é novo no Azure AD Federated SSO, faça o seguinte: 
1.  Registe a sua candidatura no portal Azure
2.  Desenvolver SSO com Azure AD utilizando [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ou [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code).
3.  Ativar a funcionalidade de suporte a vários arrendamentos em AAD para obter experiência de teste de 'um clique' Mais informações específicas podem ser encontradas [aqui](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).  

**Para aplicação de um único inquilino, utilize qualquer uma das seguintes opções:**  
* Adicione utilizadores ao seu diretório como utilizadores convidados usando [o Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)
* Fornecer manualmente ensaios para os clientes utilizando 'Contacte-me'
* Desenvolver um 'Test Drive' por cliente
* Construa uma aplicação de demonstração de amostra sumiquilinária com sSO

## <a name="saas-subscriptions"></a>Assinaturas SaaS

Utilize o tipo de oferta de aplicações SaaS para permitir ao seu cliente comprar a sua solução técnica baseada no SaaS como subscrição. Os seguintes requisitos devem ser cumpridos para a sua aplicação SaaS:
- Preço e faturação do serviço a um apartamento (mensal ou anual), ou a uma taxa por utilizador.
- Forneça um método para atualizar ou cancelar o serviço a qualquer momento.
A Microsoft é a anfitriã da transação de comércio. A Microsoft cobra ao seu cliente em seu nome. Para oferecer uma App SaaS como subscrição, deve integrar-se com as APIs de cumprimento do SaaS.  O seu serviço deve apoiar o fornecimento, a atualização e o cancelamento.

| Requisito | Detalhes |  
|:--- |:--- |  
|Faturação e medição | A sua oferta tem um preço baseado no modelo de preços que seleciona antes de publicar (taxa fixa ou por utilizador).  Se utilizar o modelo de taxa fixa, pode incluir opcionalmente dimensões adicionais utilizadas para cobrar aos clientes pelo uso não incluído na taxa fixa. |  
|Cancelamento | A sua oferta é cancelada pelo cliente a qualquer momento. |  
|Página de aterragem de transações | Você hospeda uma página de desembarque de transações de marca Azure onde os utilizadores podem criar e gerir a sua conta de serviço SaaS. |   
| API de subscrição | Expõe um serviço que pode interagir com a Subscrição SaaS para criar, atualizar e eliminar uma conta de utilizador e plano de serviço. As alterações críticas da API devem ser suportadas dentro de 24 horas. As alterações não críticas da API serão divulgadas periodicamente. |  

>[!Note]
>O canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções cloud](./cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros do Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes
Se ainda não o fez,

* [Saiba](https://azuremarketplace.microsoft.com/sell) mais sobre o mercado.

Para se registar no Partner Center, comece a criar uma nova oferta ou a trabalhar numa existente:

* [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
* Consulte a criação de uma oferta de [aplicação SaaS](./partner-center-portal/create-new-saas-offer.md) para mais informações.
