---
title: Como criar uma oferta SaaS no mercado comercial da Microsoft
description: Saiba como criar um novo software como oferta de serviço (SaaS) para listagem ou venda no Microsoft AppSource, Azure Marketplace ou através do programa Cloud Solution Provider (CSP) utilizando o portal de marketplace comercial no Microsoft Partner Center.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: 74d30b7c42002c8f134520e0198774eba1519bcd
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553843"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Como criar uma oferta SaaS no mercado comercial

Como editor de marketplace comercial, pode criar um software como oferta de serviço (SaaS) para que potenciais clientes possam comprar a sua solução técnica baseada em SaaS. Este artigo explica o processo de criação de uma oferta SaaS para o mercado comercial da Microsoft.

## <a name="before-you-begin"></a>Antes de começar

Se ainda não o fez, leia [a oferta do Plano SaaS para o mercado comercial.](plan-saas-offer.md) Irá explicar os requisitos técnicos da sua aplicação SaaS e as informações e ativos de que necessita quando criar a sua oferta. A menos que pretenda publicar uma listagem simples **(Contacte-me** com a opção de listagem) no mercado comercial, a sua aplicação SaaS deve satisfazer os requisitos técnicos em torno da autenticação.

> [!IMPORTANT]
> Recomendamos que crie uma oferta separada de desenvolvimento/teste (DEV) e uma oferta de produção separada (PROD). Este artigo descreve como criar uma oferta PROD. Para mais detalhes sobre a criação de uma oferta DEV, consulte [Criar uma oferta de desenvolvimento e teste.](create-saas-dev-test-offer.md)

## <a name="create-a-new-saas-offer"></a>Criar uma nova oferta SaaS

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
1. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
1. No **separador Visão** Geral, selecione **+ Software de oferta nova** como  >  **serviço**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Ilustra o menu de navegação à esquerda e a nova lista de ofertas.":::

1. Na nova caixa de diálogo de **oferta,** insira um **ID de oferta**. Este ID é visível no URL da listagem de mercado comercial e nos modelos Azure Resource Manager, se aplicável. Por exemplo, se introduzir **a oferta de teste-1** nesta caixa, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Cada oferta na sua conta deve ter uma identificação única de oferta.
   + Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres.
   + O ID da oferta não pode ser alterado depois de selecionar **Criar**.

1. Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

   + Este nome não é visível no mercado comercial e é diferente do nome da oferta e outros valores mostrados aos clientes.
   + O pseudónimo da oferta não pode ser alterado depois de selecionar **Criar**.
1. Para gerar a oferta e continuar, **selecione Criar**.

## <a name="configure-your-saas-offer-setup-details"></a>Configure os detalhes de configuração da sua oferta SaaS

No separador **Configuração 'Oferta',** nos detalhes da **Configuração,** escolherá se vende a sua oferta através da Microsoft ou gere as suas transações de forma independente. As ofertas vendidas através da Microsoft são referidas como _ofertas transacionáveis_, o que significa que a Microsoft facilita a troca de dinheiro por uma licença de software em nome da editora. Para obter mais informações sobre estas opções, consulte [as opções de listagem](plan-saas-offer.md#listing-options) e determine a sua [opção de publicação.](determine-your-listing-type.md)

1. Para vender através da Microsoft e fazer-nos facilitar transações para si, selecione **Sim**. Continue a [ativar uma unidade de teste](#enable-a-test-drive-optional).

1. Para listar a sua oferta através do mercado comercial e processar transações de forma independente, selecione **Nº,** e, em seguida, faça uma das seguintes:
   + Para fornecer uma subscrição gratuita para a sua oferta, **selecione Get it now (Grátis)**. Em seguida, na caixa **URL da Oferta** que aparece, introduza o URL (a começar por *http* ou https ) onde os *clientes* podem obter um ensaio através [da autenticação de um clique utilizando o Azure Ative Directory (Azure AD)](azure-ad-saas.md). Por exemplo, `https://contoso.com/saas-app`.
   + Para fornecer um teste gratuito de 30 dias, selecione **Teste Gratuito,** e depois na caixa **de URL trial** que aparece, insira o URL (a partir de *http* ou https ) onde os *clientes* podem aceder ao seu teste gratuito através [de autenticação de um clique utilizando o Azure Ative Directory (Azure AD)](azure-ad-saas.md). Por exemplo, `https://contoso.com/trial/saas-app`.
   + Para que potenciais clientes o contactem para adquirir a sua oferta, selecione **Contacte-me.**

## <a name="enable-a-test-drive-optional"></a>Ativar uma unidade de teste (opcional)

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes acesso a um ambiente pré-configurado por um número fixo de horas. Oferecer uma unidade de teste resulta numa taxa de conversão aumentada e gera cabos altamente qualificados. Para saber mais sobre test drives, veja [o que é um test drive?](./what-is-test-drive.md)

> [!TIP]
> Um test drive é diferente de um teste gratuito. Pode oferecer um test drive, teste gratuito, ou ambos. Ambos fornecem aos clientes a sua solução para um período de tempo fixo. Mas, um test drive também inclui uma visita prática e auto-guiada às principais características e benefícios do seu produto sendo demonstrados num cenário de implementação real.

**Para ativar uma unidade de teste**
1.  No **test drive**, selecione a caixa de **verificação de acionamento de teste.**
1.  Selecione o tipo de unidade de teste da lista que aparece.

## <a name="configure-lead-management"></a>Configurar a gestão de oportunidades potenciais

Ligue o seu sistema de gestão de relacionamento com o cliente (CRM) com a sua oferta de mercado comercial para que possa receber informações de contacto com o cliente quando um cliente manifestar interesse ou implementar o seu produto. Pode modificar esta ligação a qualquer momento durante ou depois de criar a oferta.

> [!NOTE]
> Tem de configurar a gestão de chumbo se estiver a vender a sua oferta através da Microsoft ou selecionou a opção de listagem **Contacte-me.** Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](partner-center-portal/commercial-marketplace-get-customer-leads.md)

### <a name="configure-the-connection-details-in-partner-center"></a>Configure os detalhes da ligação no Partner Center

1.  Sob **os comandos do Cliente,** selecione o link **'Ligar'.**
1. Na caixa de diálogo **de detalhes de Ligação,** selecione um destino de chumbo da lista.
1. Complete os campos que aparecem. Para etapas detalhadas, consulte os seguintes artigos:

   - [Configure a sua oferta de envio leva à mesa Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configure a sua oferta de envio de leads para Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
   - [Configure a sua oferta para enviar leva ao ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configure a sua oferta de envio leva ao Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configure a sua oferta de envio leva à Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração fornecida, selecione o link **Validate.**
1. Para fechar a caixa de diálogo, selecione **OK**.

## <a name="configure-microsoft-365-app-integration"></a>Configure a integração da Aplicação Microsoft 365

Pode iluminar a [descoberta unificada e a entrega](./plan-SaaS-offer.md) da sua oferta SaaS e qualquer consumo relacionado com a Aplicação Microsoft 365, ligando-os.

### <a name="integrate-with-microsoft-api"></a>Integrar-se com a Microsoft API

1. Se a sua oferta SaaS não se integrar com a Microsoft Graph API, selecione **Nº**. Continue a Link publicado clientes de consumo de aplicações Microsoft 365.  

1. Se a sua oferta SaaS se integrar com a Microsoft Graph API, selecione **Sim**, e, em seguida, forneça o ID da App Azure Ative Directory que criou e registou para integrar com a Microsoft Graph API. 

### <a name="link-published-microsoft-365-app-consumption-clients"></a>Link publicou clientes de consumo de aplicações microsoft 365

1. Se não tiver publicado o add-in do Office, a app Teams ou as soluções SharePoint Framework que funcionam com a sua oferta SaaS, selecione **No**.

1. Se publicou soluções de Office add-in, Teams ou SharePoint Framework que funcionam com a sua oferta SaaS, selecione **Sim**, então selecione **+Adicionar outro link AppSource** para adicionar novos links.  

1. Fornecer um link AppSource válido.

1. Continue a adicionar todos os links por seleção **+Adicione outro link AppSource** e forneça links AppSource válidos.  

1. A ordem que os produtos ligados são mostrados na página de listagem da oferta SaaS é indicada pelo valor Rank, pode alterá-la selecionando, retenha e mova o = ícone para cima e para baixo na lista. 

1. Pode eliminar um produto ligado selecionando **Eliminar** na linha do produto.  


> [!IMPORTANT]
> Se parar de vender um produto ligado, este não será automaticamente desvinculado da oferta SaaS, deve eliminá-lo da lista de produtos ligados e reenviar a oferta SaaS.  

 

## <a name="next-steps"></a>Passos seguintes

- [Como configurar as suas propriedades de oferta SaaS](create-new-saas-offer-properties.md)