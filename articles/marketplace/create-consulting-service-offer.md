---
title: Como criar uma oferta de serviços de consultoria no mercado comercial da Microsoft
description: Saiba como criar uma nova oferta de serviço de consultoria para o Microsoft AppSource ou Azure Marketplace utilizando o programa de marketplace comercial no Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92754450"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Como criar uma oferta de serviços de consultoria no mercado comercial

Este artigo explica como criar uma oferta de serviço de consultoria para o mercado comercial da Microsoft usando o Partner Center. 

## <a name="before-you-begin"></a>Antes de começar

Para publicar uma oferta de serviço de consultoria, deve cumprir determinados requisitos de elegibilidade para demonstrar conhecimentos especializados na sua área. Se ainda não o fez, leia [Plano uma oferta de serviço de consultoria para o mercado comercial.](./plan-consulting-service-offer.md) Descreve os pré-requisitos para um serviço de consultoria e os ativos de que precisa quando criar a oferta com o Partner Center.

## <a name="create-a-new-consulting-service-offer"></a>Criar uma nova oferta de serviço de consultoria

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2.  No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview** .
3.  No separador Visão Geral, selecione + serviço de **consultoria de oferta**  >  **nova.**

    ![Ilustra o menu de navegação à esquerda.](./media/new-offer-consulting-service.png)

4. Na nova caixa de diálogo de **oferta,** insira um **ID de oferta** . Este ID é visível no URL da listagem de mercado comercial. Por exemplo, se introduzir a oferta de teste-1 nesta caixa, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Cada oferta na sua conta deve ter uma identificação única de oferta.
    * Utilize apenas letras minúsculas e números. O ID da oferta pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres.
    * O ID da oferta não pode ser alterado depois de selecionar **Criar** .

5. Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center. Não é visível nas lojas online e é diferente do nome da oferta mostrado aos clientes.
6. Para gerar a oferta e continuar, **selecione Criar** .

## <a name="configure-lead-management"></a>Configurar a gestão de oportunidades potenciais

Ligue o seu sistema de gestão de relacionamento com o cliente (CRM) à sua oferta de mercado comercial para que possa receber informações de contacto com o cliente quando um cliente manifestar interesse no seu serviço de consultoria. Pode modificar esta ligação a qualquer momento durante ou depois de criar a oferta. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](./partner-center-portal/commercial-marketplace-get-customer-leads.md)

Para configurar a gestão de chumbo no Partner Center:

1.  No Partner Center, aceda ao **separador De configuração da Oferta.**
2.  Sob **os comandos do Cliente,** selecione o link **'Ligar'.**
3.  Na caixa de diálogo **de detalhes de Ligação,** selecione um destino de chumbo da lista.
4.  Complete os campos que aparecem. Para etapas detalhadas, consulte os seguintes artigos:

    * [Configure a sua oferta de envio leva à mesa Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configure a sua oferta de envio de leads para Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
    * [Configure a sua oferta para enviar leva ao ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configure a sua oferta de envio leva ao Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configure a sua oferta de envio leva à Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Para validar a configuração fornecida, selecione o **link Validate** .
6.  Quando configurar os dados de ligação, selecione **Connect** .
7.  Selecione **Guardar rascunho** .

Depois de submeter a sua oferta para publicação no Partner Center, validaremos a ligação e enviaremos um teste. Enquanto pré-visualiza a oferta antes de entrar em direto, teste a sua ligação de chumbo tentando comprar a oferta no ambiente de pré-visualização.

> [!TIP]
> Certifique-se de que a ligação ao destino principal permanece atualizada para não perder nenhuma pista.

## <a name="next-steps"></a>Passos seguintes

* [Como configurar as propriedades do seu serviço de consultoria](./create-consulting-service-offer-properties.md)