---
title: Configurar leads do cliente | Azure Marketplace
description: Configure clientes potenciais no Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pabutler
ms.openlocfilehash: 312e172321c25248f1b0801bdbccf71762319989
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818817"
---
<a name="get-customer-leads"></a>Obter oportunidades potenciais
==================

Este artigo explica como criar clientes potenciais do cliente usando o Portal do Cloud Partner. Você pode conectar esses clientes potenciais ao seu sistema CRM e integrá-los ao pipeline de vendas.

## <a name="leads"></a>Orienta

Os leads são clientes interessados em ou que estão implantando seus produtos do [Azure Marketplace](https://azuremarketplace.microsoft.com/) ou do [AppSource](https://appsource.microsoft.com).

### <a name="azure-marketplace"></a>Azure Marketplace

1.  O cliente usa um "Test Drive" de sua oferta. As unidades de teste são uma oportunidade acelerada para você compartilhar seus negócios instantaneamente com clientes potenciais sem nenhuma violação de entrada. Todas as unidades de teste geram um lead para um cliente que está interessado em experimentar seu produto para saber mais. Saiba mais sobre as unidades de teste no [test drive do Azure Marketplace](https://azuremarketplace.azureedge.net/documents/azure-marketplace-test-drive-program.pdf).

    ![Exemplos de test drive do Marketplace](./media/cloud-partner-portal-get-customer-leads/test-drive-offer.png)
 

<!-- -->

1. O cliente consenti-se em compartilhar suas informações depois de selecionar "obter agora". Esse Lead é um representante de **interesse inicial** , onde compartilhamos informações sobre clientes que expressou interesse em obter seu produto. O cliente potencial é a parte superior do funil de aquisição.

   ![Opção obter agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button.png)

1. O cliente seleciona "comprar" no [portal do Azure](https://portal.azure.com/) para obter seu produto. Esse Lead é um Lead **ativo** , no qual compartilhamos informações sobre um cliente que começou a implantar seu produto.

   ![Opção de compra](./media/cloud-partner-portal-get-customer-leads/purchase-button.png)


### <a name="appsource"></a>AppSource

1.  O cliente fez um "Test Drive" para sua oferta. As unidades de teste são uma oportunidade acelerada para você compartilhar seus negócios instantaneamente com clientes potenciais sem nenhuma violação de entrada. Todas as unidades de teste gerarão um cliente potencial de um usuário que está interessado em experimentar seu produto para saber mais. Saiba mais sobre test drives no [AppSource Test Drive](https://appsource.microsoft.com/blogs/want-to-try-an-app-take-a-test-drive).

    ![Exemplo de Test Drive](./media/cloud-partner-portal-get-customer-leads/test-drive-offer-2.png)

2.  O cliente consenti-se em compartilhar suas informações depois de selecionar "obter agora". Esse cliente potencial é um representante de **interesse inicial** , onde compartilhamos informações sobre clientes que expressam interesse em obter seu produto. O cliente potencial é a parte superior do funil de aquisição.

      ![Opção obter agora](./media/cloud-partner-portal-get-customer-leads/get-it-now-button-2.png)


3.  O cliente seleciona "entre em contato comigo" em sua oferta. Esse cliente potencial é um Lead **ativo** , no qual compartilhamos informações sobre um cliente que pede que seja seguido sobre seu produto.

    ![Opção entre em contato comigo](./media/cloud-partner-portal-get-customer-leads/contact-me-image.png)

<a name="lead-data"></a>Dados de Lead
---------

Cada cliente potencial que você recebe durante o processo de aquisição do cliente tem dados em campos específicos. Como você obterá clientes potenciais de várias etapas, a melhor maneira de lidar com os Leads é eliminar a duplicação e personalizar os acompanhamentos. Dessa forma, cada cliente está recebendo uma mensagem apropriada e você está criando uma relação exclusiva.

### <a name="lead-source"></a>Origem do cliente potencial

O formato de uma fonte de Lead é a-de **ação** de **origem** |  a **oferta**

**Sources**: "AzureMarketplace", "AzurePortal", "testdrive" e "APPSOURCE (SPZA)"

**Ações**:
- "INS"--instalação. Esta ação está no Azure Marketplace ou AppSource quando um cliente compra seu produto.
- "PLT" – significa avaliação de LED de parceiro. Essa ação está em AppSource quando um cliente usa a opção entre em contato comigo.
- "DNC"--não contate. Essa ação está em AppSource quando um parceiro que foi listado na página do aplicativo é solicitado a ser contatado. Estamos compartilhando a cabeça de que esse cliente estava em lista cruzada em seu aplicativo, mas eles não precisam ser contatados.
- "Criar"--esta ação está somente dentro do portal do Azure e é gerada quando um cliente compra sua oferta para sua conta.
- "StartTestDrive"--essa ação destina-se apenas a unidades de teste e é gerada quando um cliente inicia sua test drive.

**Ofertas**

Os exemplos a seguir mostram identificadores exclusivos que são atribuídos a um Publicador e uma oferta específica: Checkpoint. Check-Point-R77-10SG-byol, BitNami. openedxcypress e docusign. 3701c77e-1cfa-4c56-91e6-3ed0b622145a.


### <a name="customer-info"></a>Informações do cliente

Os campos no exemplo a seguir mostram as informações do cliente contidas em um cliente potencial.
- Nome: João
- Sobrenome: Smith
- Email: jsmith\@microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Título: CTO

>[!Note]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial.

Estamos trabalhando ativamente para aprimorar os leads, portanto, se houver um campo de dados que você não vê aqui, mas gostaria de ter, [envie-nos seus comentários](mailto:AzureMarketOnboard@microsoft.com).

<a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como conectar seu sistema CRM ao Portal do Cloud Partner
------------------------------------------------------------

Para começar a obter clientes potenciais, criamos nosso conector de gerenciamento de Lead na Portal do Cloud Partner para que você possa facilmente conectar suas informações de CRM, e vamos fazer a conexão para você. Agora você pode aproveitar facilmente os leads gerados pelo Marketplace sem um esforço de engenharia significativo para se integrar a um sistema externo.

![Conector de gerenciamento de Lead](./media/cloud-partner-portal-get-customer-leads/lead-management-connector.png)

Podemos gravar clientes potenciais em uma variedade de sistemas de CRM ou diretamente em uma tabela de armazenamento do Azure, na qual você pode gerenciar os leads, mas você gostaria de fazer isso. Cada um dos links a seguir fornece instruções para se conectar a possíveis destinos de cliente potencial:

-   [Dynamics CRM Online](./cloud-partner-portal-lead-management-instructions-dynamics.md) para obter instruções sobre como configurar o Dynamics CRM Online para obter clientes potenciais.
-   [Marketo](./cloud-partner-portal-lead-management-instructions-marketo.md) para obter as instruções para configurar a configuração de Lead do marketo para obter clientes potenciais.
-    [Salesforce](./cloud-partner-portal-lead-management-instructions-salesforce.md) para obter instruções para configurar sua instância do Salesforce para obter clientes potenciais.
-    [Tabela do Azure](./cloud-partner-portal-lead-management-instructions-azure-table.md) para obter as instruções para configurar sua conta de armazenamento do Azure para obter clientes potenciais em uma tabela do Azure.
-   [Ponto de extremidade https](./cloud-partner-portal-lead-management-instructions-https.md) para obter as instruções para configurar seu ponto de extremidade HTTPS para obter clientes potenciais.

Depois de configurar o destino do cliente potencial e publicar sua oferta, validaremos a conexão e enviaremos um líder de teste. Ao exibir a oferta antes de entrar no ar, você também pode testar sua conexão de cliente potencial tentando adquirir a oferta por conta própria no ambiente de visualização. É importante certificar-se de que as configurações de Lead permaneçam atualizadas para que você não perca nenhum cliente potencial, portanto, atualize essas conexões sempre que algo tiver mudado no final.

<a name="what-next"></a>O que vem a seguir?
----------

Quando a configuração técnica estiver em vigor, você deverá incorporar esses leads em suas vendas atuais & estratégia de marketing e processos operacionais. Estamos muito interessados em entender melhor seu processo de vendas geral e deseja trabalhar junto com você para fornecer leads de alta qualidade e dados suficientes para que você tenha êxito. Agradecemos seus comentários sobre como é possível otimizar e aprimorar os leads que enviamos a você com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você estiver interessado em [fornecer comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que sua equipe de vendas seja mais bem-sucedida com leads do Marketplace.
