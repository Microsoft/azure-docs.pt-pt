---
title: SaaS fulfillção API FAQ - Mercado comercial da Microsoft
description: Conheça vários dos requisitos de integração para o mercado comercial da Microsoft para permitir que os clientes da Azure subscrevam as ofertas da SaaS.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/19/2021
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 760e7210d054e44dfec6d6a6e480baecd04d6807
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044129"
---
# <a name="common-questions-about-saas-fulfillment-apis"></a>Perguntas comuns sobre APIs de cumprimento de SaaS

Este artigo descreve vários dos requisitos de integração para o mercado comercial da Microsoft para permitir que os clientes da Azure subscrevam as ofertas da SaaS.

## <a name="discovery-experience"></a>Experiência de descoberta

Uma vez publicada uma oferta SaaS, os utilizadores do Azure podem descobri-la no Azure Marketplace. Os seus clientes podem filtrar ofertas com base no tipo de produto (SaaS) e descobrir os serviços SaaS em que estão interessados.

## <a name="purchase-experience"></a>Experiência de compra

Uma vez que um utilizador esteja interessado num serviço SaaS específico, o utilizador pode subscrevê-lo no Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>O que significa para um utilizador da Azure subscrever uma oferta SaaS no Azure Marketplace?

Significa que um utilizador pode ver os termos de utilização e declaração de privacidade associados ao serviço SaaS, e concordar em pagá-lo de acordo com os termos de faturação definidos por si, o editor da oferta SaaS, na fatura da Microsoft. Os utilizadores podem usar o seu perfil de pagamento existente em Azure para pagar o consumo do serviço SaaS.

Esta capacidade é benéfica de várias formas. Os clientes podem agora descobrir e subscrever num só local usando a Microsoft Cloud Platform como uma fonte fidedigna, sem ter de verificar todos os softwares ISV que pretende utilizar. Além disso, os clientes podem usar o seu perfil de pagamento existente sem terem de pagar explicitamente cada software ISV de forma independente.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>O utilizador é cobrado automaticamente quando a oferta é subscrita?

Ao subscrever a oferta SaaS, o utilizador concordou em pagar o consumo do serviço SaaS através da plataforma Microsoft. No entanto, os encargos só começarão quando a oferta for consumida. O utilizador deve ir à sua oferta SaaS e confirmar a criação de conta para começar a consumir a oferta. Em seguida, notificará a Microsoft para começar a faturar para esta subscrição saaS do cliente.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Como é notificado quando um utilizador subscreve a sua oferta SaaS?

Depois de subscrever uma oferta, o utilizador Azure pode descobrir e gerir todas as suas ofertas em Azure. Por defeito, o estado de uma oferta saaS recentemente subscrita é mostrado como **Provisioning, cumprimento pendente**. Neste estado, o utilizador do Azure será solicitado com uma ação à **Conta Configure,** de forma a navegar pela sua experiência de gestão de subscrição SaaS no portal Azure.

Quando o utilizador seleciona **a Conta Configure,** são redirecionados para o website do serviço SaaS. A editora configura o URL no momento da publicação da oferta. Esta página é referida como a página de aterragem do editor. Os utilizadores do Azure insinam-se na página de aterragem do SaaS com base nas suas credenciais existentes no Azure Ative Directory (Azure AD) em Azure.

> [!IMPORTANT]
> Tem de assinar no utilizador de compra utilizando o Azure Ative Directory, Single Sign On (Azure AD SSO) conforme indicado pela [política](/legal/marketplace/certification-policies?context=/azure/marketplace/context/context). A `mail` propriedade sobre o recurso do utilizador recuperado a partir da Microsoft Graph API dá-lhe as informações de contacto para o caso do Azure AD e `userPrincipalName` para a MSA. É possível que o campo "mail" esteja vazio para Azure AD, e o utilizador pode não ter um e-mail gravado. Se for esse o caso, recomendamos que detete isso e peça um e-mail de contacto. Esta é a sua única oportunidade de obter um e-mail de contacto para contactar um cliente durante ou após o processo de embarque do cliente.

Quando o utilizador Azure é redirecionado para a página de aterragem, um sinal é adicionado ao URL de consulta. Este símbolo é de curta duração e válido por um período de tempo de 24 horas. Em seguida, pode detetar a presença deste token e ligar para a API da Microsoft para obter mais contexto associado ao token.

![Fluxo de subscrição de clientes](media/saas-metering-service-integration-flow-a.png)

Para obter mais informações sobre o contrato da API para lidar com cenários transacionados no ciclo de vida de uma oferta SaaS, consulte [a API de realização de SaaS.](pc-saas-fulfillment-api-v2.md)

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Como sabe a oferta do SaaS à qual o utilizador subscreve em Azure?

A resposta à `Resolve` API inclui a oferta e a informação do plano associada à subscrição do SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Como pode o utilizador Azure alterar o plano associado a esta subscrição do Azure?

* O utilizador do Azure pode alterar o plano associado à subscrição saaS diretamente na experiência SaaS, ou através da plataforma Microsoft.

* As conversões podem ser feitas a qualquer momento no ciclo de faturação. É-lhe pedido que reconheça qualquer conversão, que se tornará eficaz uma vez reconhecida.

* As tarifas do plano pré-pago **(mensal** ou **anual)** são pró-avaliadas. Qualquer excesso de tempo emitido até ao momento da conversão será cobrado na próxima fatura. Novos excessos serão emitidos com base no novo plano.

>[!Note]
>Pode bloquear as desvalorizações se não quiser suportar caminhos de conversão específicos.

A sequência abaixo capta o fluxo quando um cliente Azure altera um plano na experiência SaaS:

![Fluxo de mudança de plano de cliente](media/saas-metering-service-integration-flow-b.png)

A sequência abaixo capta o fluxo quando um cliente Azure altera um plano na loja online da Microsoft:

![Fluxo de mudança de plano de loja online do cliente](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Como pode o utilizador Azure cancelar a subscrição do plano associado à subscrição do Azure?

Um utilizador do Azure pode cancelar a subscrição de uma oferta SaaS comprada, quer diretamente na experiência SaaS, quer através da plataforma microsoft. Uma vez que o utilizador não se subscreva, deixará de ser cobrado a partir do próximo ciclo de faturação.

A sequência abaixo capta o fluxo quando um cliente Azure não subscreve a oferta saaS na experiência SaaS:

![O cliente desinscreve-se na experiência SaaS](media/saas-metering-service-integration-flow-d.png)

A sequência abaixo captura o fluxo quando o utilizador Azure não subscreve a loja online da Microsoft:

![Cliente desinscreve-se na loja online da Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Passos seguintes

[APIs do serviço de medição do Marketplace](./marketplace-metering-service-apis.md)
