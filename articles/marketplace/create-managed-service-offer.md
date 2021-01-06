---
title: Como criar uma oferta de Serviço Gerido no mercado comercial da Microsoft
description: Saiba como criar uma nova oferta de Serviço Gerido para o Azure Marketplace utilizando o programa de marketplace comercial no Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918411"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Como criar uma oferta de Serviço Gerido para o mercado comercial

Este artigo explica como criar uma oferta de Serviço Gerido para o mercado comercial da Microsoft usando o Partner Center.

Para publicar uma oferta de Serviço Gerido, deve ter ganho uma Competência Gold ou Silver Microsoft na Cloud Platform. Se ainda não o fez, leia [Plano uma oferta de Serviço Gerido para o mercado comercial.](./plan-managed-service-offer.md) Irá ajudá-lo a preparar os ativos de que necessita quando criar a oferta no Partner Center.

## <a name="create-a-new-offer"></a>Criar uma nova oferta

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/home).
2. No menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Overview**.
3. No separador Visão Geral, selecione **+ Nova oferta** Serviço  >  **Gerido**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Ilustra o menu de navegação à esquerda.":::

4. Na nova caixa de diálogo de **oferta,** insira um **ID de oferta**. Este é um identificador único para cada oferta na sua conta. Este ID é visível no URL da listagem de mercado comercial e nos modelos Azure Resource Manager, se aplicável. Por exemplo, se introduzir a oferta de teste-1 nesta caixa, o endereço web da oferta será `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Cada oferta na sua conta deve ter uma identificação única de oferta.
    * Utilize apenas letras minúsculas e números. Pode incluir hífens e sublinhados, mas sem espaços, e está limitado a 50 caracteres.
    * O ID da Oferta não pode ser alterado depois de selecionar **Criar**.

5. Insira **um pseudónimo de Oferta.** Este é o nome usado para a oferta no Partner Center. Não é visível nas lojas online e é diferente do nome da oferta mostrado aos clientes.
6. Para gerar a oferta e continuar, **selecione Criar**.

## <a name="configure-lead-management"></a>Configurar a gestão de oportunidades potenciais

Ligue o seu sistema de gestão de relacionamento com o cliente (CRM) à sua oferta de mercado comercial para que possa receber informações de contacto com o cliente quando um cliente manifestar interesse no seu serviço de consultoria. Pode modificar esta ligação a qualquer momento durante ou depois de criar a oferta. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](./partner-center-portal/commercial-marketplace-get-customer-leads.md)

Para configurar a gestão de chumbo no Partner Center:

1. No Partner Center, aceda ao **separador De configuração da Oferta.**
2. Sob **os comandos do Cliente,** selecione o link **'Ligar'.**
3. Na caixa de diálogo **de detalhes de Ligação,** selecione um destino de chumbo da lista.
4. Complete os campos que aparecem. Para etapas detalhadas, consulte os seguintes artigos:

    * [Configure a sua oferta de envio leva à mesa Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Configure a sua oferta de envio de leads para Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (anteriormente Dynamics CRM Online)
    * [Configure a sua oferta para enviar leva ao ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Configure a sua oferta de envio leva ao Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Configure a sua oferta de envio leva à Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Para validar a configuração fornecida, selecione o **link Validate**.
6. Quando configurar os dados de ligação, selecione **Connect**.
7. Selecione **Guardar rascunho**.

Depois de submeter a sua oferta para publicação no Partner Center, validaremos a ligação e enviaremos um teste. Enquanto pré-visualiza a oferta antes de entrar em direto, teste a sua ligação de chumbo tentando comprar a oferta no ambiente de pré-visualização.

> [!TIP]
> Certifique-se de que a ligação ao destino principal permanece atualizada para não perder nenhuma pista.

## <a name="configure-offer-properties"></a>Configure propriedades de oferta

Na página propriedades da sua oferta no Partner Center, definirá as categorias aplicáveis à sua oferta e os contratos legais. Esta informação garante que o seu Serviço Gerido é apresentado corretamente na loja online e oferecido ao conjunto certo de clientes.

### <a name="select-a-category"></a>Selecione uma categoria

Em **Categorias**, selecione pelo menos uma e até cinco categorias para agrupar a sua oferta nas áreas de pesquisa de mercado comercial apropriadas.

### <a name="provide-terms-and-conditions"></a>Fornecer termos e condições

Em **Legal,** forneça os seus termos e condições para esta oferta. Os clientes serão obrigados a aceitá-los antes de utilizarem a oferta. Também pode fornecer o URL onde os seus termos e condições podem ser encontrados.

**Selecione Guardar o projeto** antes de continuar.

## <a name="next-step"></a>Passo seguinte

* [Configure a sua oferta de serviço gerido](./create-managed-service-offer-listing.md)