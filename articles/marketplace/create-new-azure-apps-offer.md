---
title: Como criar uma oferta de aplicação Azure no mercado comercial
description: Saiba como criar uma nova oferta de aplicação Azure para listagem ou venda no Azure Marketplace, ou através do programa Cloud Solution Provider (CSP) utilizando o portal de marketplace comercial no Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94370222"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Como criar uma oferta de aplicação Azure no mercado comercial

Como editor de marketplace comercial, pode criar uma oferta de aplicações Azure para que potenciais clientes possam comprar a sua solução. Este artigo explica o processo de criação de uma oferta de aplicação Azure para o mercado comercial da Microsoft.

Se ainda não o fez, leia [Plano uma oferta de candidatura da Azure para o mercado comercial.](plan-azure-application-offer.md) Fornecerá os recursos e ajudá-lo-á a recolher as informações e bens de que necessita quando criar a sua oferta.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).

1. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.

1. Na página 'Vista Geral', selecione **+ Nova oferta**  >  **Aplicação Azure**.

    ![Ilustra o menu de navegação à esquerda.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. Na nova caixa de diálogo de **oferta,** insira um **ID de oferta**. Este é um identificador único para cada oferta na sua conta. Este ID é visível no URL da listagem de mercado comercial e nos modelos Azure Resource Manager, se aplicável. Por exemplo, se introduzir a oferta de teste-1 nesta caixa, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Cada oferta na sua conta deve ter uma identificação única de oferta.
     * Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres.
     * O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

1. Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center.

     * Este nome só é visível no Partner Center e é diferente do nome da oferta e outros valores mostrados aos clientes.
     * O pseudónimo Oferta não pode ser alterado depois de selecionar **Criar**.

1. Para gerar a oferta e continuar,  **selecione Criar**.

## <a name="configure-your-azure-application-offer-setup-details"></a>Configure os detalhes de configuração da sua oferta de aplicação Azure

No separador **Configuração 'Oferta',** nos detalhes da **Configuração,** escolherá se configurar uma unidade de teste. Também ligará o seu sistema de gestão de relacionamento com o cliente (CRM) com a sua oferta de mercado comercial.

### <a name="enable-a-test-drive-optional"></a>Ativar uma unidade de teste (opcional)

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes acesso a um ambiente pré-configurado por um número fixo de horas. Oferecer uma unidade de teste resulta numa taxa de conversão aumentada e gera cabos altamente qualificados. Para saber mais sobre test drives, consulte [test drive](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Para ativar uma unidade de teste

- No **test drive**, selecione a caixa de **verificação de acionamento de teste.**

### <a name="customer-lead-management"></a>Gestão de chumbo do cliente

Ligue o seu sistema de gestão de relacionamento com o cliente (CRM) com a sua oferta de mercado comercial para que possa receber informações de contacto com o cliente quando um cliente manifestar interesse ou implementar o seu produto.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Para configurar os detalhes da ligação no Partner Center

1. Sob **os comandos do Cliente,** selecione o link **'Ligar'.**
1. Na caixa de diálogo **de detalhes de Ligação,** selecione um destino de chumbo da lista.
1. Complete os campos que aparecem. Para etapas detalhadas, consulte os seguintes artigos:

   - [Configure a sua oferta de envio leva à mesa Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Configure a sua oferta de envio de leads para Dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
   - [Configure a sua oferta para enviar leva ao ponto final HTTPS](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Configure a sua oferta de envio leva ao Marketo](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Configure a sua oferta de envio leva à Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Para validar a configuração fornecida, selecione o link **Validate,** se aplicável.
1. Para fechar a caixa de diálogo, selecione **Connect**.
1. **Selecione Guardar o rascunho** antes de continuar para o separador seguinte: Propriedades.

> [!NOTE]
> Certifique-se de que a ligação ao destino principal permanece atualizada para não perder nenhuma pista. Certifique-se de que atualiza estas ligações sempre que algo tiver mudado.

## <a name="next-steps"></a>Passos seguintes

- [Como configurar a sua aplicação Azure oferece propriedades](create-new-azure-apps-offer-properties.md)
