---
title: Configure os leads do cliente [ Mercado Azure
description: Configure os leads do cliente no mercado comercial.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 05b166b2ec46900d9e3972025efb581d9619ec6a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252656"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Clientes potenciais da oferta do marketplace

Os leads são clientes interessados, ou implementando as suas ofertas a partir do [Azure Marketplace](https://azuremarketplace.microsoft.com) ou do [AppSource](https://appsource.microsoft.com). Receberá os clientes assim que a sua oferta for publicada no mercado. Este artigo explicará:

* A forma como a sua oferta de marketplace gera clientes lidera, garantindo que não perde oportunidades de negócio. 
* Ligue o seu CRM à sua oferta, para que possa gerir as suas pistas num local central.
* Compreenda os dados de chumbo que lhe enviamos, para que possa acompanhar os clientes que lhe contactaram.

## <a name="generate-customer-leads"></a>Gerar pistas de cliente

Aqui estão lugares onde uma pista é gerada:

1. Quando um cliente consente em partilhar as suas informações depois de selecionar "Contacte-me" do mercado. Este chumbo é um chumbo **de interesse inicial,** onde partilhamos informações sobre o cliente que manifestou interesse em obter o seu produto. A liderança é o topo do funil de aquisição.

      ![Dinâmica 365 Contacte-me](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Quando um cliente seleciona "Get It Now" ou "Create" (no [portal Azure)](https://portal.azure.com/)para obter a sua oferta, este chumbo é um **chumbo ativo**, onde partilhamos informações sobre um cliente que começou a implementar o seu produto.

    ![SQL Obtê-lo agora](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Criação do servidor do Windows](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Um cliente faz um "Test Drive" ou inicia um "Teste Gratuito" da sua oferta. Test Drives ou testes gratuitos são oportunidades aceleradas para você partilhar o seu negócio instantaneamente com potenciais clientes sem quaisquer barreiras de entrada.

    ![Dinâmica 365 Test Drive](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Dinâmica 365 Test Drive](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Ligue-se ao seu sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Compreender os dados de chumbo

Cada chumbo que recebe durante o processo de aquisição do cliente tem dados em áreas específicas. O primeiro campo a ter `LeadSource` em conta é o campo, que segue este formato:**Oferta** **de Acção-Fonte** | .

**Fontes**: O valor para este campo é povoado com base no mercado que gerou o chumbo. Os valores possíveis são, `"AzureMarketplace"` `"AzurePortal"`e `"AppSource (SPZA)"`.

**Ações**: O valor para este campo é povoado com base na ação que o cliente tomou no mercado, o que gerou o chumbo. 

Os valores possíveis são:

- "INS" - Instalação. Esta ação é no Azure Marketplace ou AppSource quando um cliente compra o seu produto.
- "PLT" - Significa julgamento liderado por parceiro. Esta ação está no AppSource quando um cliente utiliza a opção Contacte-me.
- "DNC" - Não contacte. Esta ação está no AppSource quando um Parceiro que foi riscado listado na sua página de aplicações é solicitado para ser contactado. Estamos a partilhar o aviso de que este cliente foi riscado na sua aplicação, mas não precisam de ser contactados.
- "Criar" -- Esta ação está apenas dentro do portal Azure e é gerada quando um cliente compra a sua oferta para a sua conta.
- "StartTestDrive" -- Esta ação é apenas para Test Drives, e é gerada quando um cliente inicia o seu test drive.

**Ofertas**: Pode ter várias ofertas no mercado. O valor para este campo é povoado com base na oferta que gerou o chumbo. O ID da Editora e o ID da Oferta são ambos enviados neste campo e são valores que forneceu quando publicou a oferta para o mercado.

Os exemplos que se seguem `publisherid.offerid`mostram valores de exemplo no formato esperado: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informação do Cliente

A informação do cliente é enviada através de vários campos. O exemplo que se segue mostra a informação do cliente que está contida num chumbo.

- Primeiro nome: João
- Apelido: Smith
- E-mail:\@jsmith microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Denominação: CTO

>[!Note]
>Nem todos os dados do exemplo anterior estão sempre disponíveis para cada chumbo. Como obterá pistas de vários passos, conforme mencionado na secção Customer Leads, a melhor maneira de lidar com os leads é desduplicar os registos e personalizar os seguimentos. Desta forma, cada cliente recebe uma mensagem apropriada, e está a criar uma relação única.

## <a name="best-practices-for-lead-management"></a>Boas práticas para a gestão de chumbo

1. *Processo* - Defina um processo de venda claro, com marcos, KPIs e clara propriedade da equipa.
2. *Qualificação* - Definir pré-requisitos, que indicam se um chumbo foi totalmente qualificado. Certifique-se de que os representantes de vendas ou de marketing qualificam as pistas cuidadosamente antes de as levar em todo o processo de venda.
3. *Acompanhamento* - Não se esqueça de acompanhar, espere que a transação típica exija 5 a 12 chamadas de seguimento
4. *Nurture* - Crie as suas pistas, de modo a levá-lo a caminho de uma margem de lucro mais elevada.

## <a name="leads-frequently-asked-questions"></a>Pistas frequentemente feitas perguntas

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso arranjar ajuda para preparar o meu destino principal?

Pode encontrar documentação [aqui](#connect-to-your-crm-system) ou submeter um bilhete de apoio através aka.ms/marketplacepublishersupport depois selecione **'offer creation'** → **o seu tipo de oferta** → 'configuração de gestão de **chumbo'.**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Sou obrigado a configurar um destino de chumbo para publicar uma oferta no mercado?

A resposta depende do tipo de oferta que está a publicar. A SaaS e a Dynamics 365 para o Envolvimento com o Cliente oferecem-se para listar como 'Contacte-me', todas as ofertas da Dynamics 365 para Operações, todas as ofertas da Dynamics 365 Business Central, e todas as ofertas do Serviço de Consultoria requerem uma ligação a um destino de chumbo. Se o seu tipo de oferta não foi listado, então não é necessário. No entanto, recomenda-se configurar um destino de chumbo para que não perca oportunidades de negócio.

### <a name="how-can-i-find-the-test-lead"></a>Como posso encontrar o chumbo do teste?

Procure `"MSFT_TEST"` no seu destino principal, aqui está um teste de amostra da Microsoft:

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas não vejo pistas?

Certifique-se de que a sua ligação ao destino principal é válida. Enviaremos um teste depois de publicar a sua oferta no Partner Center. Se vir o fio de ensaio, a ligação é válida. Também pode testar a sua ligação de chumbo tentando adquirir a pré-visualização da oferta durante a etapa de pré-visualização clicando em "get it now", "contacta-me" ou "teste gratuito" na listagem no mercado.

Além disso, certifique-se de que procura os dados certos. O conteúdo na secção de [dados de chumbo understand](#understand-lead-data) deste documento descreve os dados de chumbo que enviamos para o seu destino principal.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Configurei o Azure BLOB como o meu destino principal, porque não vejo a pista?

O destino de chumbo Azure Blob já não é suportado, pelo que lhe faltam quaisquer pistas de cliente geradas pela sua oferta. Mude para qualquer uma das outras [opções](./commercial-marketplace-get-customer-leads.md)de destino de chumbo . 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um e-mail do Marketplace, por que não encontro a pista no meu CRM?

É possível que o domínio de e-mail do utilizador final seja de .edu. Por razões de privacidade, não passamos informações privadas identificáveis do domínio .edu. Envie um bilhete de apoio através aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei a Mesa Azure como o meu destino principal, como posso ver as pistas?

Pode aceder aos dados de chumbo armazenados na Tabela Azure a partir do portal Azure, ou pode descarregar e instalar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para ver gratuitamente os dados das tabelas da sua conta de armazenamento Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Configurei a Mesa Azure como o meu destino principal, posso ser notificado sempre que uma nova pista é enviada pelo Marketplace?

Sim, siga as instruções para configurar um fluxo da Microsoft que envie um e-mail se um fio for adicionado à Tabela Azure na documentação [aqui .](./commercial-marketplace-lead-management-instructions-azure-table.md)

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Configurei a Salesforce como o meu destino principal, por que não encontro as pistas?

Verifique se o formulário "web to lead" é um campo obrigatório baseado numa lista de escolhas. Se sim, mude o campo para um campo de texto não obrigatório.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com o meu destino principal, e perdi algumas pistas. Posso mandá-los num e-mail?

Devido a políticas de informação privadas identificáveis, não podemos partilhar informações de chumbo através de e-mail não seguro.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei a Mesa Azure como o meu destino principal, quanto vai custar?

Os dados da geração lead são baixos (<1 GB para quase todos os editores). O custo dependerá do número de pistas recebidas, se 1.000 pistas forem recebidas num mês, custa cerca de 50 cêntimos. Para obter mais informações sobre os preços de armazenamento, consulte [os preços de armazenamento.](https://azure.microsoft.com/pricing/details/storage/)

Se a sua pergunta ainda não for respondida, contacte o Suporte através aka.ms/marketplacepublishersupport, em seguida, selecione **'offer creation'** → **o seu tipo de oferta** → 'configuração de gestão de **chumbo'.** 

### <a name="i-am-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-who-to-receive-these-emails"></a>Estou a receber notificações por e-mail quando são recebidos novos clientes. Como posso configurar quem receber estes e-mails?

Aceda à sua oferta no Partner Center e navegue para a página de **configuração da Oferta** - >**Edição**de **Gestão** -> de Chumbo. Atualize os endereços de e-mail no campo de **email contacte.**

## <a name="next-steps"></a>Passos seguintes

Uma vez que a configuração técnica esteja em vigor, deve incorporar estes leads nas suas vendas atuais & estratégia de marketing e processos operacionais. Estamos interessados em compreender melhor o seu processo de vendas global e queremos trabalhar em estreita colaboração consigo para fornecer leads de alta qualidade e dados suficientes para torná-lo bem sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar as pistas que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Informe-nos se está interessado em [fornecer feedback](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que a sua equipa de vendas tenha mais sucesso com o Marketplace Leads.
