---
title: Azure Ative Directy e ofertas transacionáveis da SaaS no mercado comercial
description: Saiba como o Azure Ative Directory funciona com ofertas transacionáveis do SaaS no mercado comercial da Microsoft.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 0d8e2ee684bc08ec23e052229d50b7e9d62c0ecb
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87328496"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Azure AD e ofertas transacionáveis da SaaS no mercado comercial

O serviço de gestão de identidade e acesso baseado na nuvem da Microsoft, [o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) ajuda os utilizadores a iniciar e aceder a recursos internos e externos. No mercado comercial da Microsoft, o Azure AD torna as ofertas transacionáveis saaS mais fáceis e seguras para todos, incluindo editores, compradores e utilizadores. Com o Azure AD, os editores podem automatizar o fornecimento de utilizadores ao seu software como aplicações de serviço (SaaS), e os próprios compradores podem gerir estes utilizadores a provisionados. 

Além disso, [o Azure AD single sign-on](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (SSO) fornece segurança e comodidade quando os utilizadores insinuam-se em apps em Azure AD. Um envolvimento mais rápido e experiências otimizadas também inspiram a confiança do comprador e do utilizador desde a primeira interação com a aplicação SaaS de uma editora. Isto dá uma impressão positiva que constrói visibilidade e encoraja a repetição do negócio.

Seguindo as orientações deste artigo, ajudará a certificar a sua oferta saaS no mercado comercial. Para mais detalhes sobre a certificação, leia as políticas detalhadas de [certificação do mercado comercial,](https://aka.ms/commercial-marketplace-certification-policies#100-general)incluindo as [específicas do SaaS.](https://aka.ms/commercial-marketplace-certification-policies#1000-software-as-a-service-saas)

## <a name="before-you-begin"></a>Before you begin

Ao [criar a sua oferta SaaS](./partner-center-portal/create-new-saas-offer.md) no Partner Center, escolha entre um conjunto de opções específicas de chamada para ação que serão exibidas na listagem de ofertas. A sua escolha determina como a sua oferta é transacionada no mercado comercial. As ofertas vendidas através da Microsoft são chamadas de ofertas transacionáveis. Cobramos ao cliente em seu nome por todas as ofertas transacionáveis. Se optar por vender através da Microsoft e nos fizer realizar transações em seu nome (a opção **Sim),** então escolheu criar uma oferta transacionável e este artigo é para si. Recomendamos que o leia na íntegra.

Se optar por apenas listar a sua oferta embora o mercado comercial e as transações de processo de forma independente (a opção **No),** tem três opções para saber como os potenciais clientes vão aceder à sua oferta: Obtenha-a agora (gratuitamente), Teste gratuito e Contacte-me. Se selecionar **Obtenha-o agora (gratuito)** ou **teste gratuito,** este artigo não é para si. Em vez disso, consulte [Build the landing page para a sua oferta saaS gratuita ou experimental no mercado comercial](./azure-ad-free-or-trial-landing-page.md) para obter mais informações. Se selecionar **Contacte-me,** não existem responsabilidades diretas de editor. Continue a criar a sua oferta no Centro de Parceiros.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>Como a Azure AD trabalha com o mercado comercial para ofertas saas

A Azure AD permite a compra, realização e gestão perfeitas de soluções de marketplace comercial. A Figura 1 mostra como o editor, o comprador e o utilizador interagem para comprar e ativar uma subscrição. Também mostra como os clientes usam e gerem as aplicações SaaS que obtêm do mercado comercial. Para efeitos desta ilustração, o comprador é o utilizador de aplicação SaaS que inicia uma compra no mercado comercial.

Como mostrado na Figura 1, quando um comprador seleciona a sua oferta, iniciam uma cadeia de fluxos de trabalho que inclui compra, subscrição e gestão de utilizadores. Dentro desta cadeia, você como o editor é responsável por certos requisitos, com a Microsoft a fornecer suporte em pontos-chave.

***Figura 1: Utilização de Azure AD para ofertas saas no mercado comercial***

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Ilustra a gestão de compras, gestão de subscrição e etapas opcionais de gestão do utilizador.":::

As seguintes secções fornecem detalhes sobre os requisitos para cada passo de processo.

## <a name="process-steps-for-purchase-management"></a>Etapas de processo para gestão de compras

Este valor mostra os quatro passos de processo para a gestão da compra.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Ilustra os quatro passos de processo para a gestão da compra.":::

Esta tabela fornece detalhes para as etapas do processo de gestão de compras.

| Passo de processo | Ação de editor | Recomendado ou necessário para editores |
| ------------ | ------------- | ------------- |
| 1. O comprador entra no mercado comercial com a sua identidade de identificação Azure e seleciona uma oferta SaaS. | Não é necessária nenhuma ação de editor. | Não aplicável |
| 2. Após a compra, o comprador seleciona a **conta Configure** no Azure Marketplace ou **Configure agora** no AppSource, que direciona o comprador para a página de aterragem da editora para esta oferta. O comprador deve poder fazer login no pedido saaS da editora com a Azure AD SSO e só deve ser solicitado o consentimento mínimo que não requer a aprovação do administrador AD da Azure. | Desenhe uma [página de aterragem](azure-ad-transactable-saas-landing-page.md) para a oferta para que receba um utilizador com a sua identidade AZure AD ou Microsoft (MSA) e facilite qualquer provisão ou configuração adicional que seja necessária. | Obrigatório |
| 3. A editora solicita detalhes de compra da API de cumprimento do SaaS. | Utilizando um [token](./partner-center-portal/pc-saas-registration.md) de acesso gerado a partir do ID de aplicação da página de aterragem, [ligue para o ponto final de resolução](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) para obter detalhes sobre a compra. | Obrigatório |
| 4. Através do Azure AD e da Microsoft Graph API, a editora reúne os detalhes da empresa e do utilizador necessários para a disponibilização do comprador na aplicação SaaS da editora.  | Decomponha o token do utilizador Azure AD para encontrar nome e e-mail, ou [ligue para a API do Microsoft Graph](https://docs.microsoft.com/graph/use-the-api) e use permissões delegadas para [obter informações](https://docs.microsoft.com/graph/api/user-get) sobre o utilizador que está iniciado. | Obrigatório |
||||

## <a name="process-steps-for-subscription-management"></a>Etapas de processo para gestão de subscrições

Este número mostra os dois passos de processo para a gestão da subscrição.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Ilustra os dois passos de processo para a gestão de subscrições.":::

Esta tabela descreve os detalhes sobre os passos do processo de gestão de subscrição.

| Passo de processo | Ação de editor | Recomendado ou necessário para editores |
| ------------ | ------------- | ------------- |
| 5. A editora gere a subscrição da aplicação SaaS através da API de cumprimento do SaaS. | Lidar com alterações de subscrição e outras tarefas de gestão através das APIs de cumprimento do [SaaS](./partner-center-portal/pc-saas-fulfillment-api-v2.md).<br><br>Este passo requer um token de acesso como descrito no passo 3 do processo. | Obrigatório |
| 6. Ao utilizar preços medidos, o editor emite eventos de utilização à API do serviço de medição. | Se a sua aplicação SaaS apresentar faturação baseada em uso, faça notificações de utilização através do [serviço de medição do Marketplace APIs](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Este passo requer um token de acesso como descrito no Passo 3. | Necessário para a medição |
||||

## <a name="process-steps-for-user-management"></a>Etapas de processo para gestão de utilizadores

Este número mostra os três passos de processo para a gestão do utilizador.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Ilustra os três passos de processo opcionais para a gestão do utilizador.":::

Os passos de processo 7 a 9 são passos opcionais do processo de gestão do utilizador. Proporcionam benefícios adicionais para os editores que suportam a Azure AD um único sign-on (SSO). Esta tabela descreve os detalhes sobre os passos do processo de gestão do utilizador.

| Passo de processo | Ação de editor | Recomendado ou necessário para editores |
| ------------ | ------------- | ------------- |
| 7. Os administradores da AD Azure na empresa do comprador podem gerir opcionalmente o acesso aos utilizadores e grupos através do Azure AD. | Não é necessária qualquer ação de editor para o permitir se o Azure AD SSO for criado para os utilizadores (Passo 9). | Não aplicável |
| 8. O Serviço de Provisionamento AZURE AD comunica alterações entre a Azure AD e a aplicação SaaS da editora. | [Implementar um ponto final SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) para receber atualizações do Azure AD à medida que os utilizadores são adicionados e removidos. | Recomendado |
| 9. Após a permissão e provisão da aplicação, os utilizadores da empresa do comprador podem utilizar o Azure AD SSO para iniciar sessão na aplicação SaaS da editora. | [Utilize o Azure AD SSO](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) para permitir que os utilizadores entrem uma vez com uma conta para a aplicação SaaS da editora. | Recomendado |
||||

## <a name="next-steps"></a>Passos seguintes

- [Criar uma oferta SaaS no mercado comercial](./partner-center-portal/create-new-saas-offer.md)
- [Construa a página de aterragem para a sua oferta de SaaS transacionável no mercado comercial](./azure-ad-transactable-saas-landing-page.md)
