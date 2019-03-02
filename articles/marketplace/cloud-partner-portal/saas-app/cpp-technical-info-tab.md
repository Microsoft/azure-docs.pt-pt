---
title: SaaS application oferta técnica de configuração do Azure | Documentos da Microsoft
description: Configure informações técnicas para a oferta de aplicação SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 383f65ea925a3575afd988e07784be643201c6b8
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57214508"
---
# <a name="saas-application-technical-info-tab"></a>Separador de informações técnicas de aplicação SaaS

O separador de informações técnicas fornece o formulário de configuração técnica. Utilize este formulário para escolher o tipo de aplicação SaaS (aplicação) que está a criar e configurar a forma como a sua aplicação é fornecida aos clientes.

![Formulário de técnicas de configuração](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>Formulário de configuração do técnico

Este formulário tem 2 campos: Produto e a chamada à ação.

### <a name="product-field"></a>Campo de produto

Pode fornecer uma aplicação SaaS para ambos as seguintes lojas:
- Um utilizador empresarial, selecionando o **listagem** opção.
- Para um utilizador de administrador de TI, selecionando **venda através de Microsoft**.
Para ajudar a decidir que tipo de aplicação SaaS que está criando, leia [compreender seleção vitrine](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).

#### <a name="sell-through-microsoft"></a>Venda através da Microsoft
Para criar essa experiência, precisa configurar as seguintes partes:

- Ligar o seu site do serviço de SaaS com as APIs de SaaS da Microsoft. O [venda de SaaS através do Azure – APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) artigo explica como criar esta ligação.
- Ativar venda através do Azure no Portal do Cloud Partner sob a forma de configuração técnica e forneça as informações necessárias. Para obter mais informações sobre este modelo de faturação e como ele é implementado, consulte [SaaS – venda através do Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions).

 ![Venda através de formulário da Microsoft](./media/saas-techinfo-sellthrough-ms.png)

A tabela seguinte descreve os campos obrigatórios para venda por meio da Microsoft.

|  **Nome do campo**   |  **Descrição**  |
|  ---------------  |  ---------------  |
|    IDs de subscrição de pré-visualização               |    Todos os os subscrição do Azure identificadores utilizados para testar a sua oferta na pré-visualização antes de ser publicamente disponível.               |
|     Instruções de introdução              |   Instruções para partilhar com os seus clientes para os ajudar a ligar à sua aplicação SaaS. Básicos tags de HTML são permitidas, por exemplo: &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;, etc.                |
|    URL da página de destino  |   O URL do site que irá ser direcionar seus clientes aterrar em depois de adquirir a partir do portal do Azure. Este URL também será o ponto final que irão receber a ligação de APIs para facilitar o comércio com a Microsoft.                |
|  Connection Webhook    |  Para todos os eventos assíncronos que a Microsoft tem de enviar a em nome do cliente (exemplo: Subscrição do Azure tornou-se inválida), pedimos que nos forneça um webhook da ligação. Se ainda não tiver um sistema de webhook no local, a configuração mais simples é fazer com uma aplicação de lógica de ponto final de HTTP que irá escutar quaisquer eventos que está a ser postados nele e, em seguida, manipulá-las adequadamente. Para obter mais informações, consulte <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">chamar, acionar, ou aninhar fluxos de trabalho com pontos de extremidade HTTP no logic apps</a>                |
|  Inquilino do Azure AD ID e o ID de aplicação      |   No portal do Azure, é necessário que crie uma aplicação do Active Directory para que possamos validar a ligação entre nossos dois serviços estiver atrás de uma comunicação autenticada. Para estes campos, crie uma aplicação do AD e cole o Id de inquilino correspondente e o Id da aplicação necessária. Tenha em atenção que o id da aplicação está associado à sua publisherID. Por conseguinte, certifique-se de que o mesmo ID da aplicação como em todas as ofertas.             |


Por fim, se selecionar **venda através de Microsoft**, há outra guia nova oferta com o nome **planos**. 

O [separador planos](./cpp-plans-tab.md) lista os planos específicos e seus preços correspondentes, que oferece suporte a sua aplicação SaaS. Até hoje, vamos permitir preços mensais, com a capacidade de permitir para 1 ou 3-meses de acesso gratuito. Estes planos e preços devem corresponder os planos exatos e os preços que tem no seu próprio site de aplicação SaaS.

>[!NOTE] 
>Planos só são necessários se escolher o destino de venda por meio da Microsoft.

### <a name="call-to-action-field"></a>Chamada para o campo de ação

A chamada para o campo de ação permite-lhe escolher a mensagem que aparece no botão de aquisição da sua oferta. As seguintes opções estão disponíveis:

- Livre-se escolher esta opção, lhe for pedido para introduzir um URL de avaliação em que os clientes podem obter acesso à sua aplicação SaaS. Por exemplo: https://contoso.com/trial
- Gratuito avaliação – se escolher esta opção, lhe for pedido para introduzir um URL de avaliação em que os clientes podem obter acesso à sua aplicação SaaS. Por exemplo: https://contoso.com/trial
- Contactem-me

Para obter mais informações sobre a chamada para opções de ação, consulte escolha uma opção de publicação.

>[!Note]
>Fornecedores de soluções (CSP) parceiro canal optar ativamente por participar na cloud agora disponível.  Veja [fornecedores de soluções Cloud](../../cloud-solution-providers.md) para obter mais informações sobre a sua oferta através do CSP da Microsoft de marketing de parceiros canais.

## <a name="next-steps"></a>Passos Seguintes

- [Separador planos (opcional)](./cpp-plans-tab.md)
- [Separador Informações de Canal](./cpp-channel-info-tab.md)
