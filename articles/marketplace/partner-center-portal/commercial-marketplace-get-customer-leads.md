---
title: Gestão de chumbo do mercado comercial da Microsoft
description: Saiba mais sobre gerar e receber pistas de clientes a partir das suas ofertas Microsoft AppSource e Azure Marketplace
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5d1bf859968c7b7a889abe635c917d0da11bf6e1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837333"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>O cliente lidera a sua oferta de marketplace comercial

Os leads são clientes interessados ou implementando as suas ofertas a partir do [Microsoft AppSource](https://appsource.microsoft.com) e [do Azure Marketplace](https://azuremarketplace.microsoft.com). Pode receber pistas de clientes após a sua oferta ser publicada no mercado comercial. Este artigo explica os seguintes conceitos de gestão de chumbo:

* A forma como a sua oferta de marketplace comercial gera o cliente leva a garantir que não perde oportunidades de negócio. 
* Como ligar o seu sistema de gestão de relacionamento com o cliente (CRM) à sua oferta para que possa gerir os seus leads numa localização central.
* Os dados de chumbo que lhe enviamos para que possa acompanhar os clientes que lhe contactaram.

## <a name="generate-customer-leads"></a>Gerar pistas de cliente

Aqui estão lugares onde uma pista é gerada:

- Um cliente consente em partilhar as suas informações depois de selecionar **contacte-me** a partir do mercado comercial. Esta pista é uma vantagem inicial de *interesse.* Partilhamos informações consigo sobre o cliente que manifestou interesse em obter o seu produto. A liderança é o topo do funil de aquisição.

    ![Dinâmica 365 Contacte-me](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Um cliente seleciona **Get It Now** (ou seleciona **Criar** no [portal Azure)](https://portal.azure.com/)para obter a sua oferta. Esta pista é uma pista *ativa.* Partilhamos informações consigo sobre o cliente que começou a implementar o seu produto.

    ![Botão SQL Get It Now](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server Criar botão](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Um cliente seleciona **Test Drive** ou **Free Trial** para experimentar a sua oferta. Test drives ou testes gratuitos são oportunidades aceleradas para você partilhar o seu negócio instantaneamente com potenciais clientes sem quaisquer barreiras de entrada.

    ![Botão De Tração De Teste Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Botão De Ensaio Gratuito Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Ligue-se ao seu sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Compreender os dados de chumbo

Cada chumbo que recebe durante o processo de aquisição do cliente tem dados em áreas específicas. O primeiro campo a ter `LeadSource` em conta é o campo, que segue este formato:**Oferta** **de Acção-Fonte** | .

**Fontes**: O valor para este campo é povoado com base no mercado que gerou o chumbo. Os valores possíveis são, `"AzureMarketplace"` `"AzurePortal"`e `"AppSource (SPZA)"`.

**Ações**: O valor para este campo é povoado com base na ação que o cliente tomou no mercado que gerou o chumbo.

Os valores possíveis são:

- **"INS"**: Stands para *instalação*. Esta ação está no Azure Marketplace ou appSource quando um cliente adquire o seu produto.
- **"PLT"**: Significa *julgamento liderado por parceiros.* Esta ação está no AppSource quando um cliente seleciona a opção **Contacte-me.**
- **"DNC"**: Os pontos *de espera não contactam*. Esta ação está no AppSource quando um parceiro que foi listado na sua página de aplicações é solicitado para ser contactado. Partilhamos uma notificação de que este cliente foi listado na sua aplicação, mas não precisa de ser contactado.
- **"Criar"**: Esta ação está apenas dentro do portal Azure e é gerada quando um cliente compra a sua oferta na sua conta.
- **"StartTestDrive"**: Esta ação destina-se apenas à opção **Test Drive** e é gerada quando um cliente inicia o seu test drive.

**Ofertas**: Pode ter várias ofertas no mercado comercial. O valor para este campo é povoado com base na oferta que gerou o chumbo. O ID da editora e o ID de oferta são ambos enviados neste campo e são valores que forneceu quando publicou a oferta para o mercado.

Os seguintes exemplos mostram `publisherid.offerid`valores no formato esperado: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informação do cliente

A informação do cliente é enviada através de vários campos. O exemplo que se segue mostra a informação do cliente que está contida num chumbo:

- Primeiro nome: João
- Apelido: Smith
- E-mail:\@jsmith microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Denominação: CTO

>[!NOTE]
>Nem todos os dados do exemplo anterior estão sempre disponíveis para cada chumbo. Como obterá pistas de vários passos, conforme mencionado na secção "Gerar leads de cliente", a melhor maneira de lidar com os cabos é desduplicar os registos e personalizar os seguimentos. Desta forma, cada cliente recebe uma mensagem apropriada, e cria uma relação única.

## <a name="best-practices-for-lead-management"></a>Boas práticas para a gestão de chumbo

- **Processo**: Defina um processo de venda claro, com marcos, análises e clara propriedade da equipa.
- **Qualificação**: Definir pré-requisitos, que indicam se um chumbo estava totalmente qualificado. Certifique-se de que os representantes de vendas ou de marketing qualificam os leads cuidadosamente antes de os levar em todo o processo de venda.
- **Continuação:** Não se esqueça de acompanhar. Espere que a transação típica exija 5 a 12 chamadas de seguimento.
- **Nurture**: Nurture as suas pistas para levá-lo a caminho de uma margem de lucro mais elevada.

## <a name="common-questions-about-lead-management"></a>Questões comuns sobre a gestão de chumbo

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso arranjar ajuda para preparar o meu destino principal?

Siga os passos na secção [Ligue-se ao seu sistema CRM,](#connect-to-your-crm-system)ou envie um bilhete de apoio através da [Ajuda e suporte](https://partner.microsoft.com/support/v2/?stage=1)do Partner Center . Em seguida, selecione **Oferta Criação** > **O seu tipo de oferta** > Configuração de**gestão de chumbo**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Sou obrigado a configurar um destino de chumbo para publicar uma oferta no mercado comercial?

A resposta depende do tipo de oferta que vai publicar. Software como serviço (SaaS) e Dynamics 365 Customer Engagement usam **contacte-me** para listar todas as ofertas da Dynamics 365 para Finanças e Operações, todas as ofertas Da Dynamics 365 Business Central e todas as ofertas do Serviço de Consultoria. Como resultado, eles requerem uma ligação a um destino de chumbo. Se o seu tipo de oferta não estava listado, não é necessária uma ligação a um destino de chumbo. Recomendamos que configure um destino de chumbo para que não perca oportunidades de negócio.

### <a name="how-can-i-find-the-test-lead"></a>Como posso encontrar o chumbo do teste?

Procure `"MSFT_TEST"` no seu destino principal. Aqui está um teste de amostra da Microsoft:

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas porque não estou a ver pistas?

Certifique-se de que a sua ligação ao destino principal é válida. Enviaremos um teste depois de selecionar **publicar** a sua oferta no Partner Center. Se vir o fio de ensaio, a ligação é válida. Também pode testar a sua ligação de chumbo tentando adquirir a pré-visualização da oferta durante a etapa de pré-visualização. Selecione **Get It Now,** **Contacte-me**ou **Teste Gratuito** na listagem no mercado comercial.

Além disso, certifique-se de que procura os dados certos. O conteúdo na secção [de dados de chumbo understand](#understand-lead-data) deste artigo descreve os dados de chumbo que enviamos para o seu destino principal.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Configurei o armazenamento do Azure Blob como o meu destino principal, mas porque não vejo a pista?

O armazenamento Azure Blob já não é suportado como um destino de chumbo, pelo que está a perder quaisquer pistas de clientes geradas pela sua oferta. Mude para qualquer uma das outras [opções](./commercial-marketplace-get-customer-leads.md)de destino de chumbo . 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um e-mail do mercado comercial, mas por que não encontro a pista no meu CRM?

É possível que o domínio de e-mail do utilizador final seja de .edu. Por razões de privacidade, não passamos informações pessoais do domínio .edu. Envie um bilhete de apoio através da [Ajuda e suporte do Partner Center.](https://partner.microsoft.com/support/v2/?stage=1)

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei uma mesa Azure como o meu destino principal. Como posso ver as pistas?

Pode aceder aos dados de chumbo armazenados na tabela Azure a partir do portal Azure. Também pode descarregar e instalar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para ver os dados da tabela da sua conta de armazenamento Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Configurei uma mesa Azure como o meu destino principal. Posso ser notificado sempre que um novo líder comercial do mercado é enviado?

Sim. Siga as instruções na gestão de [chumbo configure utilizando uma tabela Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para configurar um fluxo da Microsoft que envia um e-mail se um fio for adicionado à tabela Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Configurei a Salesforce como o meu destino principal, mas por que não encontro as pistas?

Verifique se o formulário web-to-lead é um campo obrigatório baseado numa lista de escolhas. Se for, mude o campo para um campo de texto não obrigatório.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com o meu destino principal, e perdi algumas pistas. Posso mandá-los num e-mail?

Devido a políticas de informação pessoal, não podemos partilhar informações de chumbo através de e-mail não seguro.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei uma mesa Azure como o meu destino principal. Quanto vai custar?

Os dados da geração de chumbo são baixos. É menos de 1 GB para quase todos os editores. O custo depende do número de pistas recebidas. Por exemplo, se 1.000 pistas forem recebidas num mês, o custo é de cerca de 50 cêntimos. Para obter mais informações sobre os preços de armazenamento, consulte o preço geral do [Armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/)

Se a sua pergunta não for respondida, contacte o Suporte da Microsoft através da [Ajuda e suporte](https://aka.ms/marketplacepublishersupport)do Partner Center . Em seguida, selecione **Oferta Criação** > **O seu tipo de oferta** > Configuração de**gestão de chumbo**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Estou a receber notificações de e-mail quando são recebidas novas pistas de clientes. Como posso configurar outra pessoa para receber estes e-mails?

Aceda à sua oferta no Partner Center e vá à página de **configuração da Oferta** >**Edição**de **Gestão** > de Chumbo. Atualize os endereços de e-mail no campo de **email contacte.**

## <a name="next-steps"></a>Passos seguintes

Após a configuração técnica estar em vigor, incorpore estes leads na sua estratégia de vendas e marketing e processos operacionais. Estamos interessados em entender melhor o seu processo de vendas geral e queremos trabalhar em estreita colaboração consigo para fornecer pistas de alta qualidade e dados suficientes para torná-lo bem sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar as pistas que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Informe-nos se está interessado em [fornecer feedback](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que a sua equipa de vendas tenha mais sucesso com os líderes do mercado comercial.
