---
title: Gestão de chumbo do mercado comercial da Microsoft
description: Saiba como gerar e receber leads de clientes a partir das suas ofertas microsoft AppSource e Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 9ee433f226b37c8ffd6ad466cca7cbd844d53524
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535982"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>O cliente lidera a partir da sua oferta de mercado comercial

Os leads são clientes interessados ou a implementar as suas ofertas a partir do [Microsoft AppSource](https://appsource.microsoft.com) e [do Azure Marketplace.](https://azuremarketplace.microsoft.com) Pode receber os leads do cliente após a sua oferta ser publicada no mercado comercial. Este artigo explica os seguintes conceitos de gestão de chumbo:

* A forma como a sua oferta de mercado comercial gera o cliente leva a garantir que não perde oportunidades de negócio. 
* Como ligar o seu sistema de gestão de relacionamento com o cliente (CRM) à sua oferta para que possa gerir os seus leads numa localização central.
* Os dados de chumbo que lhe enviamos para que possa acompanhar os clientes que o contactaram.

## <a name="generate-customer-leads"></a>Gere a 8.000 que os

Aqui estão os lugares onde uma pista é gerada:

- Um cliente consente em partilhar as suas informações depois de selecionar **contacte-me** a partir do mercado comercial. Esta pista é uma vantagem inicial de *juros.* Partilhamos consigo informações sobre o cliente que manifestou interesse em obter o seu produto. O chumbo é o topo do funil de aquisição.

    ![Dinâmica 365 Contacte-me](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Um cliente seleciona **Get It Now** (ou seleciona **Criar** no [portal Azure)](https://portal.azure.com/)para obter a sua oferta. Esta pista é uma pista *ativa.* Partilhamos consigo informações sobre o cliente que começou a implementar o seu produto.

    ![SQL Get It Now botão](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Botão criar servidor do Windows](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Um cliente seleciona **Test Drive** ou **Teste Gratuito** para experimentar a sua oferta. Test drives ou testes gratuitos são oportunidades aceleradas para partilhar o seu negócio instantaneamente com potenciais clientes sem quaisquer barreiras de entrada.

    ![Botão de unidade de teste Dinâmico 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Botão de ensaio gratuito Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Ligue-se ao seu sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Compreender dados de chumbo

Cada chumbo que recebe durante o processo de aquisição do cliente tem dados em áreas específicas. O primeiro campo a ter em conta é o `LeadSource` campo, que segue este formato: **Oferta de Ação de Fonte**  |  **Offer**.

**Fontes**: O valor deste campo é povoado com base no mercado que gerou o chumbo. Os valores possíveis `"AzureMarketplace"` `"AzurePortal"` são, `"AppSource (SPZA)"` e.

**Ações**: O valor deste campo é povoado com base na ação que o cliente tomou no mercado que gerou o chumbo.

Os valores possíveis são:

- **"INS"**: Significa *instalação.* Esta ação está no Azure Marketplace ou AppSource quando um cliente adquire o seu produto.
- **"PLT"**: Significa julgamento liderado por *parceiros.* Esta ação está no AppSource quando um cliente seleciona a opção **Contacte-me.**
- **"DNC"**: Significa *não contactar*. Esta ação está no AppSource quando um parceiro que foi listado na sua página de aplicações é solicitado para ser contactado. Partilhamos uma notificação de que este cliente estava listado na sua aplicação, mas não precisa de ser contactado.
- **"Criar"** esta ação está apenas dentro do portal Azure e é gerada quando um cliente compra a sua oferta à sua conta.
- **"StartTestDrive"**: Esta ação destina-se apenas à opção **Test Drive** e é gerada quando um cliente inicia o seu test drive.

**Ofertas**: Pode ter várias ofertas no mercado comercial. O valor deste campo é povoado com base na oferta que gerou o chumbo. O ID da editora e o ID de oferta são ambos enviados neste campo e são valores que forneceu quando publicou a oferta ao mercado.

Os seguintes exemplos mostram valores no formato `publisherid.offerid` esperado: 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informação do cliente

A informação do cliente é enviada através de vários campos. O exemplo a seguir mostra a informação do cliente que está contida numa pista:

- Primeiro Nome: João
- Último Nome: Smith
- E-mail: jsmith \@ microsoft.com
- Telefone: 1234567890
- País: EUA
- Empresa: Microsoft
- Denominação: CTO

>[!NOTE]
>Nem todos os dados do exemplo anterior estão sempre disponíveis para cada chumbo. Como obtém pistas de vários passos, como mencionado na secção "Gerar leads de cliente", a melhor maneira de lidar com os leads é des duplicar os registos e personalizar os seguimentos. Desta forma, cada cliente recebe uma mensagem apropriada, e cria uma relação única.

## <a name="best-practices-for-lead-management"></a>Melhores práticas para gestão de chumbo

Aqui ficam algumas recomendações para conduzir condutores durante o seu ciclo de vendas:

- **Processo**: Defina um processo de venda claro, com marcos, análises e clara propriedade da equipa.
- **Qualificação**: Definir pré-requisitos, que indicam se uma vantagem foi totalmente qualificada. Certifique-se de que os representantes de vendas ou marketing se qualificam com cuidado antes de os levar em todo o processo de venda.
- **Seguimento**: Não se esqueça de acompanhar dentro de 24 horas. Obterá o chumbo na sua CRM de eleição imediatamente após o cliente implementar uma unidade de teste; enviar-lhes por e-mail dentro enquanto ainda estão quentes. Solicite agendar uma chamada para entender melhor se o seu produto é uma boa solução para o seu problema. Espere que a transação típica exija inúmeras chamadas de acompanhamento.
- **Nutrir:** Nutrir as suas pistas para levá-lo a caminho de uma margem de lucro mais elevada. Faça o check-in, mas não os bombardeie. Recomendamos que envie um e-mail com, pelo menos, algumas vezes antes de os fechar; não desista depois da primeira tentativa. Lembre-se, estes clientes diretamente se envolveram com o seu produto e passaram algum tempo em um teste gratuito; são grandes perspetivas.

## <a name="common-questions-about-lead-management"></a>Questões comuns sobre gestão de chumbo

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso conseguir ajuda para preparar o meu destino principal?

Siga os passos na secção [Ligue-se ao seu sistema CRM,](#connect-to-your-crm-system)ou envie um bilhete de apoio através [da Ajuda e suporte do Partner Center](https://aka.ms/marketplacepublishersupport). Em seguida, **selecione Criar Oferta**O seu tipo de  >  **configuração de**  >  **gestão de chumbo**de oferta .

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Sou obrigado a configurar um destino principal para publicar uma oferta no mercado comercial?

A resposta depende do tipo de oferta que está a publicar. Software como serviço (SaaS) e Dynamics 365 Utilização de Envolvimento com o Cliente **Contacte-me** para listar todas as ofertas Dynamics 365 para Finanças e Operações, todas as ofertas da Dynamics 365 Business Central e todas as ofertas do Serviço de Consultoria. Como resultado, requerem uma ligação a um destino de chumbo. Se o seu tipo de oferta não foi listado, uma ligação para um destino de chumbo não é necessária. Recomendamos que configuure um destino principal para que não perca oportunidades de negócio.

### <a name="how-can-i-find-the-test-lead"></a>Como posso encontrar o resultado do teste?

Procure `"MSFT_TEST"` no seu destino principal. Abaixo está um chumbo de teste de amostra da Microsoft. Note que o formato do chumbo de teste varia consoante o destino de chumbo.

```
{
    "UserDetails": {
      "FirstName": "MSFT_TEST_636573304831318844",
      "LastName": "MSFT_TEST_636573304831318844",
      "Email": "MSFT_TEST_636573304831318844@test.com",
      "Phone": "1234567890",
      "Country": "US",
      "Company": "MSFT_TEST_636573304831318844",
      "Title": "MSFT_TEST_636573304831318844"
    },
    "LeadSource": "AzureMarketplace",
    "ActionCode": "INS",
    "OfferTitle": "Contoso Test"
    "Description": "MSFT_TEST_636573304831318844"
}
```

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas por que não vejo pistas?

Certifique-se de que a sua ligação ao destino de chumbo é válida. Enviaremos um teste depois de selecionar **Publicar** a sua oferta no Partner Center. Se vir o chumbo do teste, a ligação é válida. Também pode testar a sua ligação de chumbo tentando adquirir a pré-visualização da oferta durante o passo de pré-visualização. Selecione **Get It Now**, **Contacte-me**ou **Teste Gratuito** na listagem no mercado comercial.

Além disso, certifique-se de que procura os dados certos. O conteúdo na secção de [dados de chumbo do Understand](#understand-lead-data) deste artigo descreve os dados de chumbo que enviamos para o seu destino principal.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Configurei o armazenamento do Azure Blob como o meu destino principal, mas porque não vejo a pista?

O armazenamento Azure Blob já não é suportado como um destino principal, por isso está a perder qualquer pista de clientes gerada pela sua oferta. Mude para qualquer uma das outras [opções](./commercial-marketplace-get-customer-leads.md)de destino de chumbo . 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um e-mail do mercado comercial, mas por que não consigo encontrar a liderança no meu CRM?

É possível que o domínio de e-mail do utilizador final seja de .edu. Por razões de privacidade, não transmitimos informações pessoais do domínio .edu. Envie um bilhete de apoio através [da Ajuda e suporte do Partner Center.](https://aka.ms/marketplacepublishersupport)

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei uma mesa Azure como o meu destino principal. Como posso ver as pistas?

Pode aceder aos dados de chumbo armazenados na tabela Azure a partir do portal Azure. Também pode descarregar e instalar [gratuitamente o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para ver os dados da tabela da sua conta de armazenamento Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Configurei uma mesa Azure como o meu destino principal. Posso ser notificado sempre que for enviado um novo líder comercial?

Yes. Siga as instruções na [gestão do chumbo Configure utilizando uma tabela Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) para configurar um fluxo Microsoft que envia um e-mail se um fio for adicionado à tabela Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Configurei a Salesforce como o meu destino principal, mas por que não encontro as pistas?

Verifique se o formulário web-to-lead é um campo obrigatório baseado numa lista de escolhas. Se for, mude o campo para um campo de texto não-mando.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com o meu destino principal, e perdi algumas pistas. Posso enviá-los para mim por e-mail?

Devido às políticas de informação pessoal, não podemos partilhar informações de chumbo através de e-mails não protegidos.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei uma mesa Azure como o meu destino principal. Quanto vai custar?

Os dados da geração de chumbo são baixos. É menos de 1 GB para quase todos os editores. O custo depende do número de pistas recebidas. Por exemplo, se 1.000 leads forem recebidos num mês, o custo é de cerca de 50 cêntimos. Para obter mais informações sobre os preços de armazenamento, consulte [os preços gerais do Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Se a sua pergunta não for respondida, contacte o Microsoft Support através da [Ajuda do Centro de Parceiros e suporte](https://aka.ms/marketplacepublishersupport). Em seguida, **selecione Criar Oferta**O seu tipo de  >  **configuração de**  >  **gestão de chumbo**de oferta .

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Estou a receber notificações por e-mail quando novas pistas de clientes são recebidas. Como posso configurar outra pessoa para receber estes e-mails?

Aceda à sua oferta no Partner Center e vá à página **de configuração da Oferta** **>Dição de Gestão de Chumbo.**  >  **Edit** Atualize os endereços de e-mail no campo **de e-mail de Contato.**

## <a name="next-steps"></a>Próximos passos

Após a configuração técnica, incorpore estes leads na sua estratégia atual de vendas e marketing e processos operacionais. Estamos interessados em compreender melhor o seu processo global de vendas e queremos trabalhar em estreita colaboração consigo para fornecer pistas de alta qualidade e dados suficientes para o tornar bem sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar os leads que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Informe-nos se estiver interessado em [fornecer feedback](mailto:AzureMarketOnboard@microsoft.com) e sugestões para que a sua equipa de vendas tenha mais sucesso com os líderes comerciais do mercado.
