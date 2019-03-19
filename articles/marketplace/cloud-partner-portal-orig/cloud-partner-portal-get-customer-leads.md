---
title: Configurar as oportunidades potenciais | Documentos da Microsoft
description: Configure as oportunidades potenciais no Portal de parceiros de nuvem.
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
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 2a425e607ea7dac394ab90a3fed4d4026056bbc1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58118583"
---
<a name="get-customer-leads"></a>Obter oportunidades potenciais
==================

Este artigo explica como criar oportunidades potenciais com o Portal de parceiro de Cloud. Pode ligar destas oportunidades potenciais para seu sistema CRM e integrá-los no seu pipeline de vendas.

## <a name="leads"></a>Oportunidades potenciais

Oportunidades potenciais são os clientes que estejam interessados em ou estiver a implementar seus produtos a partir da [do Azure Marketplace](https://azuremarketplace.microsoft.com/) ou a partir de [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  Cliente demora um "Test Drive" da sua oferta. Os Test Drives são uma oportunidade acelerada para partilhar o seu negócio instantaneamente com potenciais clientes, sem qualquer as barreiras de entrada. Todas as versões de teste, gerar uma oportunidade potencial de um cliente que está interessado em fazer o seu produto para obter mais informações. Saiba mais sobre versões de teste em [do Azure Marketplace Test-Drive](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exemplos de unidade de teste do Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. Cliente dá consentimento a partilhar as suas informações depois de selecionar "Obter agora". É isso gerou um **inicial interesse** líder, onde podemos partilhar informações sobre o cliente que tenha a expressou interesse em obter o seu produto. O líder é a parte superior do funil aquisição.

   ![Obtenha-a opção de agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. Cliente seleciona "Adquirir" a [Portal do Azure](https://portal.azure.com/) para ter seu produto. É isso gerou um **Active Directory** líder, onde podemos compartilhar informações sobre um cliente que foi iniciado para implementar o seu produto.

   ![Opção de compra](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  Cliente demorou um "Test Drive" para a sua oferta. Os Test Drives são uma oportunidade acelerada para partilhar o seu negócio instantaneamente com potenciais clientes, sem qualquer as barreiras de entrada. Todas as versões de teste irá gerar uma oportunidade potencial de um cliente que está interessado em experimentar o seu produto para obter mais informações. Saiba mais sobre versões de teste em [versão de teste do AppSource](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exemplo de unidade de teste](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  Cliente dá consentimento a partilhar as suas informações depois de selecionar "Obter agora". É isso gerou um **inicial interesse** líder, onde podemos partilhar informações sobre o cliente que expresse interesse em obter o seu produto. O líder é a parte superior do funil aquisição.

      ![Obtenha-a opção de agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  Cliente selecionar "Contactar-me" na sua oferta. É isso gerou um **Active Directory** líder, onde podemos compartilhar informações sobre um cliente que solicita a ser seguido cópia de segurança com informações sobre seus produtos.

    ![Contactar-me a opção](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Levar dados
---------

Cada oportunidade potencial que receberá durante o processo de aquisição do cliente tem dados em campos específicos. Uma vez que receberá oportunidades potenciais de vários passos, é a melhor maneira de lidar com as oportunidades potenciais remover a duplicação e personalizar os seguimentos. Desta forma cada cliente está a receber uma mensagem adequada e estiver a criar uma relação exclusiva.

### <a name="lead-source"></a>Líder de origem

O formato de uma origem de oportunidade potencial é **origem**-**ação** |  **oferecem**

**Origens**: "AzureMarketplace", "AzurePortal", "TestDrive", and "AppSource (SPZA)"

**Ações**:
- "INS" – instalação. Esta ação está no Azure Marketplace ou AppSource quando um cliente compra seu produto.
- "PLT" – significa servidor de parceiro levou a versão de avaliação. Esta ação está no AppSource quando um cliente utilizar o contacto me opção.
- "DNC" – não entre em contato com. Esta ação, está no AppSource, quando um parceiro que foi cruzado listado na página da aplicação obtém pediu para ser contactado. Estamos a partilhar as mentes até que esse cliente estava cruzada listado na sua aplicação, mas não precisam ser contatado.
- Esta ação "Criar" – é apenas dentro do Portal do Azure e é gerada quando um cliente adquire a sua oferta para a respetiva conta.
- "StartTestDrive" – Esta ação destina-se apenas para testar unidades e é gerada quando um cliente inicia a unidade de teste.

**Ofertas**

Os exemplos seguintes mostram os identificadores exclusivos que estão atribuídos a um fabricante e uma oferta específica: checkpoint.check-ponto-r77-10sg-byol, bitnami.openedxcypress e docusign.3701c77e-1cfa - 4C 56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informações de clientes

Os campos no exemplo a seguir mostram as informações do cliente que estão contidas numa oportunidade potencial.
- FirstName: John
- Apelido: Smith
- E-mail: jsmith\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Título: CTO

>[!Note]
>Nem todos os dados no exemplo anterior está sempre disponível para cada oportunidade potencial.

Estamos a trabalhar ativamente em melhorar as oportunidades potenciais, por isso, se existe um campo de dados que não vir aqui, mas gostaria de ter, volte [envie-nos seus comentários](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como ligar o seu sistema CRM com o Portal de parceiros da Cloud
------------------------------------------------------------

Para iniciar a obtenção de oportunidades potenciais, criamos nossos conector de gestão de levar no Portal de parceiros de Cloud, para que possa se conectar facilmente as informações de CRM, e vamos fazer com que a ligação para si. Agora pode aproveitar facilmente as oportunidades potenciais geradas pelo marketplace sem um esforço significativo de engenharia para integrar com um sistema externo.

![Levar o conector de gestão](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Podemos escrever oportunidades potenciais numa variedade de sistemas CRM ou diretamente para um armazenamento do Azure de tabela em que pode gerenciar as oportunidades potenciais no entanto desejar. Cada um dos links a seguir fornecem instruções para ligar aos destinos de oportunidades potenciais possíveis:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) com as instruções sobre como configurar o Dynamics CRM Online para obter oportunidades potenciais.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) com as instruções para configurar a configuração de levar Marketo para obter oportunidades potenciais.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) para obter instruções sobre como configurar a instância do Salesforce obter oportunidades potenciais.
-    [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) para obter instruções para configurar a sua conta de armazenamento do Azure para obter oportunidades potenciais numa tabela do Azure.
-   [Ponto final de HTTPS](./cloud-partner-portal-lead-management-instructions-https.md) com as instruções para configurar o ponto final de Https para obter oportunidades potenciais.

Depois de configurar o destino de oportunidades potenciais e publicar a sua oferta, vamos validar a ligação e enviar-lhe um líder de teste. Quando está a visualizar a oferta antes de ir ao vivo, também pode testar a ligação de oportunidades potenciais tentando adquirir a oferta por conta própria no ambiente de pré-visualização. É importante certificar-se de que o mantenha-se do líder de definições atualizadas para que não perca quaisquer oportunidades potenciais, por isso, certifique-se de que atualiza essas conexões sempre que algo foi alterado de sua parte.

<a name="what-next"></a>O que se segue?
----------

Assim que o técnico configurar estiver em vigor, deve incorporar destas oportunidades potenciais vendas atuais e estratégia de marketing e processos operacionais. Estamos muito interessados em entender melhor as vendas gerais de processam e pretendem trabalhar de perto com no fornecimento de oportunidades potenciais de alta qualidade e dados suficientes para torná-lo com êxito. Apreciamos os seus comentários sobre como podemos otimizar e melhorar as oportunidades potenciais que podemos enviar-lhe com dados adicionais para ajudar a tornar estes clientes com êxito. Indique se estiver interessado em [fornecendo comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para ativar a sua equipa de vendas ser mais bem sucedido com oportunidades potenciais do Marketplace.
