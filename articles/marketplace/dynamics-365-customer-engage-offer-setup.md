---
title: Crie uma oferta dinâmica 365 para o envolvimento do cliente & powerApps no Microsoft AppSource (Azure Marketplace).
description: Crie uma oferta dinâmica 365 para o envolvimento do cliente & powerApps no Microsoft AppSource (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 04/20/2021
ms.openlocfilehash: d8b3a2da7ccbbbf866763dbe5c8c59b00e7abd10
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107820507"
---
# <a name="how-to-create-a-dynamics-365-for-customer-engagement--powerapps-offer"></a>Como criar uma oferta Dynamics 365 para o Envolvimento do Cliente & PowerApps

Este artigo descreve como criar uma oferta Dynamics 365 para o Envolvimento do Cliente & PowerApps. Todas as ofertas para a Dynamics 365 passam pelo nosso processo de certificação. A experiência experimental permite que os utilizadores implementem a sua solução para um ambiente Live Dynamics 365.

Antes de começar, crie uma conta de marketplace comercial no [Partner Center](partner-center-portal/create-account.md) e garanta que está inscrita no programa de marketplace comercial.

## <a name="before-you-begin"></a>Antes de começar

Revisão [Plano de Revisão uma oferta dynamics 365](marketplace-dynamics-365.md). Irá explicar os requisitos técnicos desta oferta e listará as informações e os ativos de que necessita quando a criar.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. Na página 'Visão Geral', selecione **+ Nova oferta**  >  **Dynamics 365 para Envolvimento do Cliente & PowerApps**.

    :::image type="content" source="media/dynamics-365/new-offer-dynamics-365-customer-engagement.png" alt-text="Mostra as opções do menu do painel esquerdo e o botão 'Nova oferta' com o selecionador de Envolvimento do Cliente.":::

> [!IMPORTANT]
> Após a publicação de uma oferta, quaisquer edições que faça no Partner Center só aparecem no Microsoft AppSource depois de republicar a oferta. Certifique-se de que sempre reedita uma oferta depois de mudá-la.

## <a name="new-offer"></a>Nova oferta

Introduza um **ID de oferta**. Este é um identificador único para cada oferta na sua conta.

- Este ID é visível para os clientes no endereço web para a oferta e nos modelos Azure Resource Manager, se aplicável.
- Utilize apenas letras minúsculas e números. O ID pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres. Por exemplo, se o seu ID de editor estiver `testpublisherid` e introduzir a oferta de **teste-1,** o endereço web da oferta será `https://appsource.microsoft.com/product/dynamics-365/testpublisherid.test-offer-1` .
- O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

- Este nome não é usado no AppSource. É diferente do nome da oferta e outros valores mostrados aos clientes.
- Este nome não pode ser alterado depois de selecionar **Criar**.

Selecione **Criar** para gerar a oferta. O Partner Center abre a página **de configuração da Oferta.**

## <a name="alias"></a>Alias

Introduza um nome descritivo que usaremos para se referir a esta oferta apenas dentro do Partner Center. O pseudónimo da oferta (pré-povoado com o que inseriu quando criou a oferta) não será usado no mercado e é diferente do nome da oferta mostrado aos clientes. Se pretender atualizar o nome da oferta mais tarde, consulte a página [de listagem 'Oferta'.](dynamics-365-customer-engage-offer-listing.md)

## <a name="setup-details"></a>Detalhes da configuração

Para **Como pretende que os potenciais clientes interajam com esta oferta de listagem?**

- **Obtenha-o agora (grátis)** – Liste a sua oferta aos clientes gratuitamente.
- **Teste gratuito (listagem)** – Liste a sua oferta aos clientes com um link para um teste gratuito. Os testes gratuitos de listagem de ofertas são criados, geridos e configurados pelo seu serviço e não têm subscrições geridas pela Microsoft.

    > [!NOTE]
    > Os tokens que a sua aplicação receberá através do seu link de teste só podem ser utilizados para obter informações do utilizador através do Azure Ative Directory (Azure AD) para automatizar a criação de conta na sua app. As contas da Microsoft não são suportadas para autenticação utilizando este token.

- **Contacte-me** – Colete informações de contacto com o cliente ligando o seu sistema de Gestão de Relacionamento com o Cliente (CRM). O cliente será solicitado permissão para partilhar as suas informações. Estes detalhes do cliente, juntamente com o nome da oferta, ID e fonte de mercado onde encontraram a sua oferta, serão enviados para o sistema CRM que configuraste. Para obter mais informações sobre a configuração do seu CRM, consulte [os leads do Cliente](#customer-leads).

## <a name="test-drive"></a>Unidade de teste

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes acesso a um ambiente pré-configurado por um número fixo de horas. Oferecer uma unidade de teste resulta numa taxa de conversão aumentada e gera cabos altamente qualificados. Para saber mais, comece com [o que é um test drive?](what-is-test-drive.md)

> [!TIP]
> Um test drive é diferente de um teste gratuito. Pode oferecer um test drive, teste gratuito, ou ambos. Ambos fornecem aos clientes a sua solução para um período de tempo fixo. Mas, um test drive também inclui uma visita prática e auto-guiada às principais características e benefícios do seu produto sendo demonstrados num cenário de implementação real.

Para ativar uma unidade de teste, selecione a **caixa de verificação de unidade de teste** e selecione o tipo de unidade de **teste**. Configurará o test drive mais tarde. Com a unidade de teste, também deve configurar a sua oferta para um sistema CRM para condutores de clientes (ver secção seguinte). Para remover o test drive da sua oferta, limpe esta caixa de verificação.

## <a name="customer-leads"></a>Ligações ao cliente

[!INCLUDE [Connect lead management](partner-center-portal/includes/connect-lead-management.md)]

Para mais informações, consulte [os leads do Cliente da sua oferta de mercado comercial.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

**Selecione Guardar** o rascunho antes de continuar para o separador seguinte no menu de navegação à esquerda, **Propriedades**.

## <a name="next-steps"></a>Passos seguintes

- [Configure propriedades de oferta](dynamics-365-customer-engage-properties.md)
- [Melhores práticas de listagem de ofertas](gtm-offer-listing-best-practices.md)