---
title: Configure os leads do cliente [ Mercado Azure
description: Configure os leads do cliente no Portal do Parceiro cloud.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 56012fb2a907a6db6f87554660ee36b99a3dcbf9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280325"
---
<a name="get-customer-leads"></a>Obter oportunidades potenciais
==================

Este artigo explica como criar pistas de cliente usando o Portal do Parceiro cloud. Pode ligar estas pistas ao seu sistema CRM e integrá-las no seu pipeline de vendas.

## <a name="leads"></a>Clientes potenciais

Os leads são clientes interessados, ou estão a implementar os seus produtos a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou do [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  O cliente recebe um "Test Drive" da sua oferta. Test Drives são uma oportunidade acelerada para você partilhar o seu negócio instantaneamente com potenciais clientes sem quaisquer barreiras à entrada. Todas as Test Drives geram uma pista para um cliente que está interessado em experimentar o seu produto para saber mais. Saiba mais sobre test drives no [Azure Marketplace Test Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exemplos de test drive do mercado](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. O cliente consente em partilhar as suas informações depois de selecionar "Get it now". Este chumbo é um chumbo **de interesse inicial,** onde partilhamos informações sobre o cliente que manifestou interesse em obter o seu produto. A liderança é o topo do funil de aquisição.

   ![Obtenha agora opção](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. O cliente seleciona "Comprar" no [Portal Azure](https://portal.azure.com/) para obter o seu produto. Este chumbo é um chumbo **ativo,** onde partilhamos informações sobre um cliente que começou a implementar o seu produto.

   ![Opção de compra](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  O cliente levou um "Test Drive" para a sua oferta. Test Drives são uma oportunidade acelerada para você partilhar o seu negócio instantaneamente com potenciais clientes sem quaisquer barreiras à entrada. Todas as Test Drives gerarão uma vantagem de um cliente que está interessado em experimentar o seu produto para saber mais. Saiba mais sobre test drives no [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exemplo de test drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  O cliente consente em partilhar as suas informações depois de selecionar "Get it now". Este chumbo é um chumbo **de interesse inicial,** onde partilhamos informações sobre o cliente que expressa interesse em obter o seu produto. A liderança é o topo do funil de aquisição.

      ![Obtenha agora opção](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  O cliente seleciona "Contacte-me" na sua oferta. Este chumbo é um chumbo **ativo,** onde partilhamos informações sobre um cliente que pede para ser acompanhado sobre o seu produto.

    ![Contacte-me a opção](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Dados de Chumbo
---------

Cada chumbo que recebe durante o processo de aquisição do cliente tem dados em áreas específicas. Como você vai obter pistas de vários passos, a melhor maneira de lidar com as pistas é desduplicar e personalizar os seguimentos. Desta forma, cada cliente recebe uma mensagem apropriada, e está a criar uma relação única.

### <a name="lead-source"></a>Fonte de chumbo

O formato para uma fonte de chumbo é **Source**-**Action** |  **Offer**

**Fontes**: "AzureMarketplace", "AzurePortal", "TestDrive" e "AppSource (SPZA)"

**Ações:**
- "INS" - Instalação. Esta ação é no Azure Marketplace ou AppSource quando um cliente compra o seu produto.
- "PLT" - Significa julgamento liderado por parceiro. Esta ação está no AppSource quando um cliente utiliza a opção Contacte-me.
- "DNC" - Não contacte. Esta ação está no AppSource quando um Parceiro que foi riscado listado na sua página de aplicações é solicitado para ser contactado. Estamos a partilhar o aviso de que este cliente foi riscado na sua aplicação, mas não precisam de ser contactados.
- "Criar" -- Esta ação está apenas dentro do Portal Azure e é gerada quando um cliente compra a sua oferta para a sua conta.
- "StartTestDrive" -- Esta ação é apenas para Test Drives, e é gerada quando um cliente inicia o seu test drive.

**Ofertas**

Os seguintes exemplos mostram identificadores únicos que são atribuídos a uma editora e uma oferta específica: checkpoint.check-point-r77-10sg-byol, bitnami.openedxcypress e docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informação do Cliente

Os campos no exemplo seguinte mostram a informação do cliente que está contida num chumbo.
- Primeiro nome: João
- Apelido: Smith
- E-mail:\@jsmith microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Denominação: CTO

>[!Note]
>Nem todos os dados do exemplo anterior estão sempre disponíveis para cada chumbo.

Estamos a trabalhar ativamente na melhoria dos leads, por isso, se houver um campo de dados que não vê aqui, mas que gostaria de ter, por favor [envie-nos o seu feedback.](mailto:AzureMarketOnboard@microsoft.com)

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como ligar o seu sistema CRM ao Portal do Parceiro cloud
------------------------------------------------------------

Para começar a obter pistas, construímos o nosso conector de Gestão de Chumbo no Portal do Parceiro cloud para que possa ligar facilmente as suas informações de CRM, e faremos a ligação para si. Agora pode facilmente alavancar os leads gerados pelo mercado sem um esforço significativo de engenharia para se integrar com um sistema externo.

![Conector de gestão de chumbo](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Podemos escrever pistas em uma variedade de sistemas CRM ou diretamente para uma Tabela de Armazenamento Azure onde você pode gerir as pistas como você quiser. Cada um dos seguintes links fornece instruções para a ligação a possíveis destinos de chumbo:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) para obter as instruções sobre como configurar o Dynamics CRM Online para obter leads.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) para obter as instruções para a configuração do Marketo Lead Configuration para obter pistas.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) para obter instruções para configurar a sua instância Salesforce para obter pistas.
-    [Mesa Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) para obter as instruções para configurar a sua conta de armazenamento Azure para obter chumbos numa mesa Azure.
-   [Https Endpoint](./cloud-partner-portal-lead-management-instructions-https.md) para obter as instruções para configurar o seu Ponto Final Https para obter leads.

Depois de configurar o seu destino principal e publicar a sua oferta, validamos a ligação e enviaremos-lhe um teste de chumbo. Ao ver a oferta antes de ir ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização. É importante certificar-se de que as definições de chumbo permanecem atualizadas para que não perca nenhuma pista, por isso certifique-se de atualizar estas ligações sempre que algo tenha mudado na sua extremidade.

<a name="what-next"></a>O que vem a seguir?
----------

Uma vez que a configuração técnica esteja em vigor, deve incorporar estes leads nas suas vendas atuais & estratégia de marketing e processos operacionais. Estamos muito interessados em compreender melhor o seu processo de vendas global e queremos trabalhar em estreita colaboração consigo no fornecimento de leads de alta qualidade e dados suficientes para o tornar bem-sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar as pistas que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Por favor, avise-nos se está interessado em [fornecer feedback](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que a sua equipa de vendas tenha mais sucesso com o Marketplace Leads.
