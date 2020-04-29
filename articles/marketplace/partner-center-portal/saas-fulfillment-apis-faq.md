---
title: SaaS cumprindo APIs - FAQ / Mercado Azure
description: Experiências de descoberta e compra por parte dos clientes de uma oferta SaaS no Mercado Azure.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 6d3a84341d5221950da20f39456461dafc5d2e75
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275701"
---
# <a name="saas-fulfillment-apis---faq"></a>FAQ - SaaS fulfillment APIs

Os requisitos de integração com o Azure Marketplace para permitir aos clientes azure subscrever a oferta SaaS estão listados.

## <a name="discovery-experience"></a>Experiência de descoberta

Uma vez publicada a oferta, os utilizadores do Azure podem descobrir a oferta SaaS no Azure Marketplace. Os seus clientes poderão filtrar ofertas com base no tipo de produto (SaaS) e descobrir os serviços SaaS em que estão interessados.

## <a name="purchase-experience"></a>Experiência de compra

Uma vez que um utilizador esteja interessado num serviço SaaS específico, o utilizador pode subscrever-lhe a partir do Azure Marketplace.

### <a name="what-does-it-mean-for-an-azure-user-to-subscribe-to-a-saas-offer-in-azure-marketplace"></a>O que significa para um utilizador azure subscrever uma oferta SaaS no Azure Marketplace?

Isto significa que um utilizador pode ver os termos de utilização e declaração de privacidade associados ao serviço SaaS, e concordar em pagá-lo de acordo com os termos de faturação definidos por si, o editor da oferta SaaS, na fatura da Microsoft. Os utilizadores podem utilizar o seu perfil de pagamento existente em Azure para pagar o consumo de serviço SaaS.

Isto é benéfico por muitas razões. Os clientes podem agora descobrir e subscrever num só local usando a Microsoft Cloud Platform como uma fonte de confiança, sem ter de verificar todos os softwareis ISV que pretende utilizar. Além disso, os clientes podem usar o seu perfil de pagamento existente sem terem de pagar explicitamente cada software ISV de forma independente.

### <a name="is-the-user-charged-automatically-when-the-offer-is-subscribed"></a>O utilizador é cobrado automaticamente quando a oferta é subscrita?

Ao subscrever a oferta saaS, o utilizador concordou em pagar o consumo do serviço SaaS através da plataforma da Microsoft. No entanto, as taxas só começarão quando a oferta for consumida. O utilizador deve ir à sua oferta SaaS e confirmar a criação de conta para começar a consumir a oferta. Em seguida, notificará a Microsoft para começar a faturar para esta subscrição saaS do cliente.

### <a name="how-are-you-notified-when-a-user-subscribes-to-your-saas-offer"></a>Como é notificado quando um utilizador subscreve a sua oferta SaaS?

Depois de subscrever uma oferta, o utilizador azure pode descobrir e gerir todas as suas ofertas em Azure. Por defeito, o estado de uma oferta SaaS recém-subscrita é **"Provisioning, cumprimento pendente".** Neste estado, o utilizador azure será solicitado com uma ação de **'Configure Account',** de forma a navegar para a sua experiência de gestão de subscrição SaaS no portal Azure.

Quando o utilizador clicar na **'Conta Configurar',** serão redirecionados para o website do serviço SaaS. O URL para onde são navegados é fornecido pela editora no momento da publicação da oferta. Esta página é referida como a página de aterragem da editora. Os utilizadores do Azure devem poder fazer login na página de aterragem do SaaS com base nas credenciais AAD existentes em Azure.

Quando o utilizador Azure é redirecionado para a página de aterragem, é adicionado um token ao URL de consulta. Este símbolo é de curta duração, e válido por um período de tempo de 24 horas. Em seguida, pode detetar a presença deste símbolo e ligar para a API da Microsoft para obter mais contexto associado ao símbolo.

![Fluxo de subscrição do cliente](media/saas-metering-service-integration-flow-a.png)

Consulte o documento da API de cumprimento do [SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para obter mais informações sobre o contrato da API para lidar com cenários transatas no ciclo de vida de uma oferta SaaS.

### <a name="how-do-you-know-the-saas-offer-to-which-the-user-subscribes-in-azure"></a>Como conhece a oferta SaaS à qual o utilizador subscreve em Azure?

A resposta `Resolve` à API inclui a oferta e informação de plano associada à subscrição SaaS.

### <a name="how-can-the-azure-user-change-the-plan-associated-with-this-azure-subscription"></a>Como pode o utilizador azure alterar o plano associado a esta subscrição do Azure?

* O utilizador do Azure pode alterar o plano associado à subscrição SaaS diretamente na experiência SaaS, ou através da plataforma microsoft.

* As conversões podem ser feitas a qualquer momento do ciclo de faturação. Deve reconhecer qualquer conversão, que se tornará eficaz uma vez reconhecida.

* As tarifas pré-pagas **(mensais** ou **anuais)** são a favor. Qualquer excesso de idade emitido até ao momento da conversão será cobrado na próxima fatura. Novos excessos serão emitidos com base no novo plano.

>[!Note]
>Pode bloquear desvalorizações se não quiser apoiar caminhos de conversão específicos.

A sequência abaixo capta o fluxo quando um cliente Azure muda de plano na experiência SaaS:

![Fluxo de mudança de plano de cliente](media/saas-metering-service-integration-flow-b.png)

A sequência abaixo captura o fluxo quando um cliente Azure muda de plano na montra da Microsoft

![Fluxo de mudança de plano de loja de clientes](media/saas-metering-service-integration-flow-c.png)

### <a name="how-can-the-azure-user-unsubscribe-from-the-plan-associated-with-azure-subscription"></a>Como pode o utilizador Azure cancelar a subscrição do plano associado à subscrição do Azure?

Um utilizador do Azure pode cancelar a subscrição de uma oferta saaS adquirida, quer diretamente na experiência SaaS, quer através da plataforma microsoft. Uma vez que o utilizador desinscreva-se, deixará de ser cobrado a partir do próximo ciclo de faturação.

A sequência abaixo capta o fluxo quando um cliente Azure desabre da oferta SaaS na experiência SaaS:

![Cliente cancela subscrições na experiência SaaS](media/saas-metering-service-integration-flow-d.png)

A sequência abaixo captura o fluxo quando o utilizador azure desabre as subscrições na montra da Microsoft:

![Cliente cancela subscrições na montra da Microsoft](media/saas-metering-service-integration-flow-e.png)

## <a name="next-steps"></a>Passos seguintes

- Consulte o serviço de [medição do Marketplace APIs](./marketplace-metering-service-apis.md) para obter mais informações.
