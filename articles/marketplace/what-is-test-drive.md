---
title: O que é uma versão de teste? Mercado comercial da Microsoft
description: Explicação da função de test drive do Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: bd3b77f80a414dd3db1d5106929fa0e215e34c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121872"
---
# <a name="what-is-a-test-drive"></a>O que é uma versão de teste?

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de *experimentar antes de comprar,* resultando numa maior conversão e geração de leads altamente qualificados. Um test drive dá vida ao seu produto num cenário de implementação no mundo real, ao mesmo tempo que gera leads altamente qualificados.

Os test drives são casos geridos que implementam a sua solução ou aplicação a pedido para os clientes que a solicitem. Uma vez atribuída uma instância de test drive, está disponível para uso por um período de tempo definido e, em seguida, eliminado para criar espaço para outro cliente.

Como editor, gere e configura as definições de test drive no Partner Center. Os detalhes de configuração técnica variam dependendo do tipo de oferta com que está a trabalhar. Para obter orientações detalhadas, consulte a ligação sob o [próximo passo](#next-step) no final deste tópico.

Os potenciais clientes descobrem o seu test drive no mercado comercial. Eles fornecem as suas informações de contacto e concordam com os termos da sua oferta e a política de privacidade, e depois acedem ao seu ambiente pré-configurado para experimentá-lo por um período de tempo fixo. Os clientes recebem um teste prático e auto-guiado das principais funcionalidades e benefícios do seu produto e você recebe uma vantagem valiosa.

## <a name="how-does-it-work"></a>Como funciona?

Como editor, gere e configura as definições de test drive dentro do Partner Center. Após a configuração, torna-se uma instância gerida que será implementada a pedido do cliente que o solicitou. Uma vez atribuída uma instância de test drive, está disponível para uso por um período de tempo definido e, em seguida, eliminado para criar espaço para outro cliente.

## <a name="types-of-test-drives"></a>Tipos de test drives

Existem diferentes unidades de teste disponíveis no mercado comercial para ofertas selecionadas dependendo do tipo de produto, cenário e mercado em que está:

- Azure Resource Manager
- Unidade de teste hospedada
    - Dynamics 365 for Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplicação lógica
- Power BI

Para obter detalhes sobre a configuração de um destes test drives, consulte a ligação sob o [passo seguinte](#next-step) no final deste tópico.

### <a name="azure-resource-manager-test-drive"></a>Unidade de teste do Gestor de Recursos Azure

Este modelo de implementação contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. O test drive Azure Resource Manager está disponível para estes tipos de oferta: 

- Aplicações do Azure
- Dynamics 365 for Business Central
- Dinâmica 365 para Envolvimento de Clientes & PowerApps
- Dynamics 365 for Operations
- SaaS
- Máquinas virtuais

>[!NOTE]
>Esta é a única opção de test drive para máquina virtual e ofertas de aplicações Azure.

### <a name="hosted-test-drive"></a>Unidade de teste hospedada

Um test drive hospedado remove a complexidade da configuração, permitindo que a Microsoft seja hospedada e mantenha o serviço que executa o fornecimento, implementação e desavisionamento do utilizador do test drive. Se tiver uma oferta no Microsoft AppSource, construa o seu test drive para se conectar com uma instância Dynamics AX/CRM ou qualquer outro recurso além de apenas Azure. Utilize este tipo de ofertas appSource para se conectar com estas ofertas Dynamics 365:

- Use [a Dynamics 365 para Business Central](partner-center-portal/create-new-operations-offer.md) para um sistema de planeamento de recursos empresariais business central, como finanças, operações, cadeia de fornecimento e CRM.
- Use [a Dynamics 365 para o Envolvimento do Cliente](partner-center-portal/create-new-customer-engagement-offer.md) num sistema de Envolvimento do Cliente, como vendas, serviço, serviço de projeto e serviço de campo.
- Utilizar [a Dynamics 365 para Operações](partner-center-portal/create-new-operations-offer.md) para um sistema de planeamento de recursos empresariais de Finanças e Operações, tais como finanças, operações e fabrico, cadeia de fornecimento.

### <a name="logic-app-test-drive"></a>Unidade de teste de aplicativo lógico

Este tipo de test drive não é hospedado pela Microsoft. Utilize-o para ligar a uma oferta Dynamics 365 ou a outro recurso personalizado.

### <a name="power-bi-test-drive"></a>Unidade de teste bi de potência

Este é simplesmente um link incorporado a um dashboard personalizado. Qualquer produto que demonstre apenas um visual power bi interativo deve utilizar este tipo de test drive.

## <a name="transforming-examples"></a>Transformando exemplos

O processo de transformar uma arquitetura de recursos num test drive pode ser assustador. Confira estes exemplos de como melhor transformar as arquiteturas atuais.

[Transforme um modelo de site em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transforme um modelo de máquina virtual em um test drive](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Transforme um modelo de gestor de recursos existente em uma unidade de teste](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Gerer os cabos a partir da sua unidade de teste

Um test drive de marketplace comercial é uma ótima ferramenta para os marketers. Recomendamos que incorpore-o nos seus esforços de ir ao mercado quando lançar para gerar mais pistas para o seu negócio. Para obter orientações detalhadas, consulte [os leads do Cliente da sua oferta de mercado comercial.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md)

Se fechar um acordo com um cabo de test drive, certifique-se de que o regista no [Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Além disso, gostaríamos de saber que o seu cliente ganha onde um test drive desempenhou um papel.

## <a name="other-resources"></a>Outros recursos

Recursos adicionais de test drive:

- [Melhores práticas técnicas](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; certifique-se de que o seu bloqueador pop-up está desligado)

## <a name="next-step"></a>Passo seguinte

- [Configuração técnica da versão de teste](test-drive-technical-configuration.md)
