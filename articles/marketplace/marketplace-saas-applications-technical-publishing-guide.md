---
title: Guia de publicação de aplicações SaaS - Mercado comercial da Microsoft
description: Requisitos e recursos para a publicação de ofertas de aplicações SaaS para o Microsoft AppSource e Azure Marketplace.
services: Marketplace, Compute, Storage, Networking, Blockchain, Security, SaaS
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 9f488675e4f2437982e2426da73b4db1a713905d
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607291"
---
# <a name="saas-applications-offer-publishing-guide"></a>Guia de publicação de ofertas de aplicações SaaS

Pode publicar aplicações SaaS no mercado comercial com três chamadas diferentes para a ação: "Contacte-me", "Experimente agora" e "Get it Now". Este artigo explica estas três opções, incluindo requisitos para cada um. 

## <a name="offer-overview"></a>Oferta geral  

As aplicações SaaS estão disponíveis no Microsoft AppSource e no Azure Marketplace.  Ambas as lojas online apoiam lista, teste e ofertas de transações.

**Lista:**  A opção de publicação de Listagem é constituída por um tipo de oferta De contacto Me e é utilizada quando uma participação ao nível de Trial ou Transação não é viável. O benefício desta abordagem é que permite que os editores com uma solução no mercado comecem imediatamente a receber pistas que podem ser transformadas em negócios para aumentar o seu negócio.  
**Ensaio/Transação:**  O cliente tem a opção de comprar ou solicitar diretamente um teste para a sua solução. Proporcionar uma experiência trial aumenta o nível de envolvimento oferecido aos clientes e permite que os clientes explorem a sua solução antes de comprar. Com uma experiência Trial, terá melhores hipóteses de promoção nas lojas online, e deverá esperar mais e mais pistas mais ricas dos compromissos com os clientes. Os ensaios devem incluir apoio gratuito pelo menos durante o período experimental.  

| Oferta de apps SaaS | Requisitos empresariais | Requisitos Técnicos |  
| --- | --- | --- |  
| **Contacte-nos** | Sim | No |  
| **Power BI / Dinâmica** | Yes | Sim (Integração AD AD) |  
| **Aplicativos SaaS**| Yes | Sim (Integração AD AD) |     

## <a name="saas-list"></a>Lista SaaS

A chamada à ação para uma listagem saas sem julgamento e sem funcionalidade de faturação é "Contacte-me". 

Não é necessário configurar o Azure Ative Directory para listar uma aplicação SaaS. 

|Requisitos  |Detalhes  |
|---------|---------|
|A sua aplicação é uma oferta saaS  |   A sua solução é uma oferta SaaS e oferece um produto SaaS multitenant.      |


## <a name="saas-trial"></a>Julgamento de SaaS

Você fornece uma solução ou app usando um teste baseado em software-as-a-service (SaaS). As ofertas de ensaio gratuito podem ser apresentadas como uma conta experimental de utilização limitada ou de duração limitada. 


|Requisitos  |Detalhes  |
|---------|---------|
|A sua aplicação é uma oferta saaS  |   A sua solução é uma oferta SaaS e oferece um produto SaaS multitenant.      |
|A sua aplicação está ativada pela AAD     |   O cliente será re-direcionado para o seu domínio e irá transagir diretamente com o cliente       |


## <a name="saas-trial-technical-requirements"></a>Requisitos técnicos do ensaio saas

Os requisitos técnicos para aplicações SaaS são simples. Os editores só são obrigados a ser integrados com o Azure Ative Directory (Azure AD) a publicar. A integração AD do AD com aplicações está bem documentada e a Microsoft fornece vários SDKs e recursos para o conseguir.  

Para começar, recomendamos que tenha uma subscrição dedicada à sua publicação Azure Marketplace, permitindo-lhe isolar o trabalho de outras iniciativas. Uma vez feito isto, pode começar a implementar a sua aplicação SaaS nesta subscrição para iniciar o trabalho de desenvolvimento.  

A melhor documentação, amostras e orientação do Azure Ative Directory estão localizados nos seguintes locais: 

* [Guia do Programador de Diretórios Azure Ative](../active-directory/develop/index.yml)

* [Integrar no Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md)

* [Roteiro Azure - Segurança e Identidade](https://azure.microsoft.com/roadmap/?category=security-identity)

Para tutoriais de vídeo, reveja o seguinte:

* [Autenticação do Diretório Ativo Azure com Vittorio Bertocci](https://channel9.msdn.com/Shows/XamarinShow/Episode-27-Azure-Active-Directory-Authentication-with-Vittorio-Bertocci?term=azure%20active%20directory%20integration)

* [Briefing Técnico de Identidade Ativa do Azure - Parte 1 de 2](https://channel9.msdn.com/Blogs/MVP-Enterprise-Mobility/Azure-Active-Directory-Identity-Technical-Briefing-Part-1-of-2?term=azure%20active%20directory%20integration)

* [Briefing Técnico de Identidade Ativa do Azure - Parte 2 de 2](https://channel9.msdn.com/Blogs/MVP-Azure/Azure-Active-Directory-Identity-Technical-Briefing-Part-2-of-2?term=azure%20active%20directory%20integration)

* [Construir apps com o Microsoft Azure Ative Directory](https://channel9.msdn.com/Blogs/Windows-Development-for-the-Enterprise/Building-Apps-with-Microsoft-Azure-Active-Directory?term=azure%20active%20directory%20integration)

* [Microsoft Azure Vídeos focados no Ative Directory](https://azure.microsoft.com/resources/videos/index/?services=active-directory)

A formação gratuita do Azure Ative Directory está disponível em  
* [Microsoft Azure para IT Pros Content Series: Azure Ative Directory](https://mva.microsoft.com/training-courses/microsoft-azure-for-it-pros-content-series-azure-active-directory-16754?l=N0e23wtxC_2106218965)

Além disso, o Azure Ative Directory fornece um site para verificar as atualizações de serviço   
* [Atualizações do Serviço AD Azure](https://azure.microsoft.com/updates/?product=active-directory)

## <a name="using-azure-active-directory-to-enable-trials"></a>Utilização do Diretório Ativo Azure para permitir ensaios  

A Microsoft autentica todos os utilizadores do Marketplace com Azure AD, pelo que quando um utilizador autenticado clica na sua lista trial no Marketplace e é redirecionado para o seu ambiente Trial, pode fortalitar o utilizador diretamente num Trial sem necessitar de um passo adicional de entrada. O sinal que a sua aplicação recebe do Azure AD durante a autenticação inclui informações valiosas do utilizador que pode utilizar para criar uma conta de utilizador na sua aplicação, permitindo-lhe automatizar a experiência de fornecimento e aumentar a probabilidade de conversão. Para obter mais informações sobre o token, consulte [Sample Tokens](../active-directory/develop/active-directory-token-and-claims.md) .

A utilização do Azure AD para permitir a autenticação de 1 clique na sua app ou o Trial faz o seguinte:  
* Dinamiza a experiência do cliente de Marketplace a Trial.  
* Mantém a sensação de uma "experiência no produto" mesmo quando o utilizador é redirecionado do Marketplace para o seu ambiente de domínio ou Trial.  
* Diminui a probabilidade de abandono no redirecionamento, uma vez que não existe um passo adicional de inscrição.  
* Reduz as barreiras de implantação para a grande população de utilizadores de AD Azure.  

## <a name="certifying-your-azure-ad-integration-for-marketplace"></a>Certificando a sua integração AZure AD para o Marketplace  

Certifique a sua integração Azure AD de várias maneiras diferentes, dependendo se a sua aplicação é de inquilino único ou multi-inquilino, e se você é novo na Azure AD federado single sign-on (SSO), ou já apoiá-lo.  

**Para aplicações multi-arrendatários:**  

Se já apoiar a Azure AD, faça o seguinte:
1.    Registe a sua candidatura no portal Azure
2.    Ative a funcionalidade de suporte multi-arrendamento em Azure AD para obter uma experiência de teste de 'um clique'. Informações mais específicas podem ser [encontradas aqui.](../active-directory/develop/active-directory-integrating-applications.md)  

Se é novo na Azure AD Federated SSO, faça o seguinte: 
1.  Registe a sua candidatura no portal Azure
2.  Desenvolver SSO com AD AZure utilizando [OpenID Connect](../active-directory/develop/active-directory-protocols-openid-connect-code.md) ou [OAuth 2.0](../active-directory/develop/active-directory-protocols-oauth-code.md).
3.  Permitir funcionalidade de suporte de vários arrendamentos no AAD para obter experiência de teste de 'um clique' Mais informações específicas podem ser [encontradas aqui](../active-directory/develop/active-directory-devhowto-appsource-certified.md).  

**Para aplicação de um único inquilino, utilize qualquer uma das seguintes opções:**  
* Adicione os utilizadores ao seu diretório como utilizadores convidados usando [o Azure B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)
* Ensaios de fornecimento manual para clientes utilizando o 'Contato comigo'
* Desenvolver um 'Test Drive' por cliente
* Construa uma app de demonstração de amostras de vários inquilinos com SSO

## <a name="saas-subscriptions"></a>Assinaturas SaaS

Utilize o tipo de oferta de aplicações SaaS para permitir ao seu cliente comprar a sua solução técnica baseada no SaaS como subscrição. Os seguintes requisitos devem ser cumpridos para a sua aplicação SaaS:
- Preço e faturação do serviço num apartamento (mensal ou anual), ou a uma taxa por utilizador.
- Forneça um método para atualizar ou cancelar o serviço a qualquer momento.
A Microsoft acolhe a transação de comércio. A Microsoft cobra ao seu cliente em seu nome. Para oferecer uma App SaaS como subscrição, você deve integrar-se com as APIs de cumprimento saaS.  O seu serviço deve apoiar o fornecimento, upgrade e cancelamento.

| Requisito | Detalhes |  
|:--- |:--- |  
|Faturação e medição | A sua oferta tem um preço baseado no modelo de preços que seleciona antes de publicar (taxa fixa ou por utilizador).  Se utilizar o modelo de tarifa fixa, pode opcionalmente incluir dimensões adicionais usadas para cobrar aos clientes pelo uso não incluído na taxa fixa. |  
|Cancelamento | A sua oferta é cancelada pelo cliente a qualquer momento. |  
|Página de aterragem de transações | Você hospeda uma página de aterragem de transação com a marca Azure onde os utilizadores podem criar e gerir a sua conta de serviço SaaS. |   
| API de assinatura | Expõe um serviço que pode interagir com a Subscrição SaaS para criar, atualizar e eliminar uma conta de utilizador e um plano de serviço. As alterações da API crítica devem ser suportadas dentro de 24 horas. As alterações não críticas da API serão lançadas periodicamente. |  

>[!Note]
>O opt-in do canal parceiro Cloud Solution Providers (CSP) já está disponível.  Consulte os [Fornecedores de Soluções Cloud](./cloud-solution-providers.md) para obter mais informações sobre o marketing da sua oferta através dos canais parceiros da Microsoft CSP.

## <a name="next-steps"></a>Passos seguintes
Se ainda não o fez,

* [Conheça](https://azuremarketplace.microsoft.com/sell) o mercado.

Para se registar no Partner Center, comece a criar uma nova oferta ou a trabalhar numa existente:

* [Inscreva-se no Partner Center](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) para criar ou completar a sua oferta.
* Consulte [a criação de uma oferta de aplicação SaaS](./partner-center-portal/create-new-saas-offer.md) para obter mais informações.
