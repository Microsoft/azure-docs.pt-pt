---
title: O que é uma versão de teste? Mercado comercial da Microsoft
description: Explicação da função de test drive do Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 06/19/2020
ms.openlocfilehash: e44d5d94a8dc172962a26f3e0dae9ccbb7f8a865
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818895"
---
# <a name="what-is-a-test-drive"></a>O que é uma versão de teste?

Um test drive é uma ótima maneira de mostrar a sua oferta a potenciais clientes, dando-lhes a opção de experimentar antes de comprar, gerando leads altamente qualificados e resultando numa maior conversão. Um test drive dá vida ao seu produto num cenário de implementação no mundo real. Os clientes que experimentam o seu produto estão a demonstrar uma clara intenção de comprar uma solução semelhante. Use isto a seu favor, acompanhando com cabos mais avançados.

Os seus clientes também beneficiam de um test drive. Ao permitir-lhes experimentar primeiro o seu produto, está a reduzir o atrito do processo de compra. Além disso, o test drive é pré-aprovisionado, ou seja, os clientes não têm que descarregar, configurar ou configurar o produto.

## <a name="how-does-it-work"></a>Como funciona?

Os test drives são casos geridos que lançam a sua solução ou aplicação a pedido para os clientes que a solicitam. Uma vez atribuída uma instância de test drive, está disponível para utilização por esse cliente durante um período definido. Após o fim do período, é então apagado para criar espaço para outro cliente.

Como editor, gere e configura as definições de test drive no Partner Center. Os detalhes de configuração técnica variam dependendo do tipo de oferta. Para obter orientações detalhadas, consulte a [configuração técnica](./test-drive-technical-configuration.md)do test drive .

Os potenciais clientes descobrem o seu test drive como CTA na sua oferta no [AppSource.](https://appsource.microsoft.com/en-US/) Eles fornecem as suas informações de contacto e concordam com os termos da sua oferta e a política de privacidade, e depois têm acesso ao seu ambiente pré-configurado para experimentá-lo por um período fixo. Os clientes recebem um teste prático e auto-guiado das principais funcionalidades e benefícios do seu produto e você recebe uma vantagem valiosa.

## <a name="types-of-test-drives"></a>Tipos de test drives

Existem diferentes unidades de teste disponíveis no mercado comercial para ofertas selecionadas dependendo do tipo de produto, cenário e mercado em que está:

- Azure Resource Manager
    - Aplicações Azure
    - SaaS
    - Máquinas Virtuais
- Unidade de teste hospedada
    - Dinâmica 365 para Business Central (atualmente não suportada)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplicação lógica (apenas no modo de suporte)
- Power BI

Para obter mais informações sobre a configuração de um destes test drives, consulte a [configuração técnica do test drive](./test-drive-technical-configuration.md). 

### <a name="azure-resource-manager-test-drive"></a>Unidade de teste do Gestor de Recursos Azure

Este modelo de implementação contém todos os recursos Azure que compõem a sua solução. Os produtos que se adequam a este cenário utilizam apenas recursos Azure. O test drive Azure Resource Manager está disponível para estes tipos de oferta: 

- Aplicações do Azure
- SaaS
- Máquinas virtuais

>[!NOTE]
>Esta é a única opção de test drive para máquina virtual e ofertas de aplicações Azure.

### <a name="hosted-test-drive-recommended"></a>Unidade de teste hospedada (Recomendado)

Um test drive hospedado remove a complexidade da configuração, permitindo que a Microsoft seja hospedada e mantenha o serviço que executa o fornecimento do utilizador do test drive e desa provisionamento. Se tiver uma oferta no Microsoft AppSource, construa o seu test drive para se conectar com uma instância Dynamics AX/CRM. Pode utilizar os seguintes tipos de ofertas appSource:

- Use [a Dynamics 365 para o Envolvimento do Cliente e aplicações de energia](dynamics-365-customer-engage-offer-setup.md) para um sistema de envolvimento do cliente, como vendas, serviço, serviço de projeto e serviço de campo.
- Utilizar [a Dynamics 365 para Operações](partner-center-portal/create-new-operations-offer.md) para um sistema de planeamento de recursos empresariais de Finanças e Operações, tais como finanças, operações e fabrico, cadeia de fornecimento.

### <a name="logic-app-test-drive"></a>Unidade de teste de aplicativo lógico

Este tipo de test drive não é hospedado pela Microsoft e utiliza modelos Azure Resource Manager (ARM) para tipos de oferta Dinâmica AX/CRM. Terá de executar o modelo ARM para criar os recursos necessários na sua subscrição Azure. Logic App Test Drive está atualmente apenas no modo de suporte e não é recomendado pela Microsoft Para detalhes sobre a configuração de uma Unidade de Teste de Aplicação Lógica, consulte [a configuração técnica do test drive](./test-drive-technical-configuration.md).

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

- [Melhores práticas de Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; certifique-se de que o seu bloqueador pop-up está desligado)

## <a name="next-step"></a>Passo seguinte

- [Configuração técnica da versão de teste](test-drive-technical-configuration.md)