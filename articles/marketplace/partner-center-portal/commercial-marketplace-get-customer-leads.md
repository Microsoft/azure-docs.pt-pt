---
title: Configurar leads do cliente | Azure Marketplace
description: Configurar leads do cliente no Marketplace comercial.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 31dcc8c1e35b627b231dbe2a62998c8514d05a20
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69902653"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Clientes potenciais de sua oferta do Marketplace

Os leads são clientes interessados ou implantando suas ofertas do [Azure Marketplace](https://azuremarketplace.microsoft.com) ou do [AppSource](https://appsource.microsoft.com). Você receberá leads do cliente quando sua oferta for publicada no Marketplace. Este artigo explicará:

* Como sua oferta do Marketplace gera clientes potenciais, garantindo que você não perca as oportunidades de negócios. 
* Conecte seu CRM à sua oferta, para que você possa gerenciar seus leads em um local central.
* Entenda os dados de Lead que enviamos para você, para que você possa acompanhar os clientes que chegaram a você.

## <a name="generate-customer-leads"></a>Gerar leads do cliente

Aqui estão os locais onde um cliente potencial é gerado:

1. Quando um cliente consentir o compartilhamento de suas informações depois de selecionar "entre em contato comigo" no Marketplace. Esse cliente potencial é um representante de **interesse inicial** , no qual compartilhamos informações sobre o cliente que expressou interesse em obter seu produto. O cliente potencial é a parte superior do funil de aquisição.

      ![Dynamics 365 contate-me](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Quando um cliente seleciona "obter agora" ou "criar" (na [portal do Azure](https://portal.azure.com/)) para obter sua oferta, esse Lead é um **Lead ativo**, no qual compartilhamos informações sobre um cliente que começou a implantar seu produto.

    ![Obtenha agora o SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Criação do Windows Server](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Um cliente usa um "Test Drive" ou inicia uma "avaliação gratuita" de sua oferta. As unidades de teste ou as avaliações gratuitas são oportunidades aceleradas para você compartilhar seus negócios instantaneamente com clientes potenciais, sem qualquer barreiras de entrada.

    ![Unidade de teste do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Unidade de teste do Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Conectar-se ao sistema CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Entender os dados do cliente potencial

Cada cliente potencial que você recebe durante o processo de aquisição do cliente tem dados em campos específicos. O primeiro campo a ser examinado é o `LeadSource` campo, que segue este formato: **Oferta de ação** | de origem.

**Fontes**: O valor desse campo é populado com base no Marketplace que gerou o cliente potencial. Os valores possíveis `"AzureMarketplace"`são `"AzurePortal"`, e `"AppSource (SPZA)"`.

**Ações**: O valor desse campo é populado com base na ação que o cliente realizou no Marketplace, que gerou o cliente potencial. 

Os valores possíveis são:

- "INS"--instalação. Esta ação está no Azure Marketplace ou AppSource quando um cliente compra seu produto.
- "PLT" – significa avaliação de LED de parceiro. Essa ação está em AppSource quando um cliente usa a opção entre em contato comigo.
- "DNC"--não contate. Essa ação está em AppSource quando um parceiro que foi listado na página do aplicativo é solicitado a ser contatado. Estamos compartilhando a cabeça de que esse cliente estava em lista cruzada em seu aplicativo, mas eles não precisam ser contatados.
- "Criar"--essa ação está apenas dentro do portal do Azure e é gerada quando um cliente compra sua oferta para sua conta.
- "StartTestDrive"--essa ação destina-se apenas a unidades de teste e é gerada quando um cliente inicia sua test drive.

**Oferece**: Você pode ter várias ofertas no Marketplace. O valor desse campo é preenchido com base na oferta que gerou o cliente potencial. A ID do Publicador e a ID da oferta são enviadas neste campo e são os valores que você forneceu quando publicou a oferta no Marketplace.

Os exemplos a seguir mostram valores de exemplo no formato `publisherid.offerid`esperado: 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informações do cliente

As informações do cliente são enviadas por vários campos. O exemplo a seguir mostra as informações do cliente contidas em um cliente potencial.

- FirstName João
- LastName Smith
- Email: jsmith\@Microsoft.com
- Telemóvel 1234567890
- País: EUA
- Corporativa Microsoft
- Título CTO

>[!Note]
>Nem todos os dados no exemplo anterior estão sempre disponíveis para cada cliente potencial. Como você obterá clientes potenciais de várias etapas, conforme mencionado na seção leads do cliente, a melhor maneira de lidar com os Leads é eliminar a duplicação dos registros e personalizar os acompanhamentos. Dessa forma, cada cliente está recebendo uma mensagem apropriada e você está criando uma relação exclusiva.

## <a name="best-practices-for-lead-management"></a>Práticas recomendadas para o gerenciamento de leads

1. *Processo* -defina um processo de vendas claro, com Marcos, KPIs e desmarque a propriedade da equipe.
2. *Qualificação* -definir pré-requisitos, que indicam se um cliente potencial foi totalmente qualificado. Garanta que os representantes de vendas ou marketing qualifiquem os leads cuidadosamente antes de conduzi-los por meio do processo de vendas completo.
3. *Acompanhamento* -não se esqueça de acompanhar, espere que a transação típica exija 5 a 12 chamadas de acompanhamento
4. *Incentivá* -incentivá seus leads para ajudá-lo a obter uma margem de lucro mais alta.

## <a name="leads-frequently-asked-questions"></a>Perguntas frequentes sobre leads

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso obter ajuda para configurar meu destino de Lead?

Você pode encontrar a documentação [aqui](#connect-to-your-crm-system) ou enviar um tíquete de suporte por meio de aka.ms/marketplacepublishersupport e selecionar **"oferta de criação"** → **seu tipo de oferta** → **"configuração de gerenciamento de Lead".**

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Eu precisei configurar um destino de Lead para publicar uma oferta no Marketplace?

A resposta depende do tipo de oferta que você está publicando. O SaaS e o Dynamics 365 para ofertas de envolvimento do cliente para listar como ' entre em contato comigo ', todas as Dynamics 365 para operações oferecem, todas as ofertas do Dynamics 365 Business central e todas as ofertas de serviços de consultoria exigem uma conexão com um destino de cliente potencial. Se o tipo de oferta não tiver sido listado, isso não será necessário. No entanto, é recomendável configurar um destino de cliente potencial para que você não perca as oportunidades de negócios.

### <a name="how-can-i-find-the-test-lead"></a>Como posso encontrar o Lead de teste?

`“MSFT_TEST”` Procure em seu destino de Lead, aqui está um líder de teste de exemplo da Microsoft:

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

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas não estou vendo clientes potenciais?

Verifique se a conexão com o destino do cliente potencial é válida. Nós lhe enviaremos um líder de teste depois que você clicar em publicar em sua oferta no Partner Center. Se você vir o Lead de teste, a conexão será válida. Você também pode testar sua conexão de cliente potencial tentando adquirir a versão prévia da oferta durante a etapa de visualização clicando em "obter agora", "contatos para mim" ou "avaliação gratuita" na lista no Marketplace.

Além disso, verifique se você está procurando os dados corretos. O conteúdo na seção [entender os dados do cliente potencial](#understand-lead-data) deste documento descreve os dados de Lead que enviamos para o destino do cliente potencial.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>Configurei o BLOB do Azure como meu destino de Lead, por que não vejo o cliente potencial?

Não há mais suporte para o destino de Lead de blob do Azure. portanto, você não tem nenhum cliente potencial gerado por sua oferta. Alterne para qualquer uma das outras [Opções de destino de Lead](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um email do Marketplace, por que não consigo encontrar o cliente potencial no meu CRM?

É possível que o domínio de email do usuário final seja de. edu. Por motivos de privacidade, não passamos dados de PII do domínio. edu. Envie um tíquete de suporte por meio do aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei a tabela do Azure como meu destino de Lead, como posso exibir os leads?

Você pode acessar os dados do cliente potencial armazenados na tabela do Azure no portal do Azure ou pode baixar e instalar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir os dados das tabelas da sua conta de armazenamento do Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>Configurei a tabela do Azure como meu destino de Lead, posso ser notificado sempre que um novo cliente potencial for enviado pelo Marketplace?

Sim, siga as instruções para configurar um Microsoft Flow que envia um email se um cliente potencial for adicionado à tabela do Azure na documentação [aqui](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>Configurei o Salesforce como meu destino de Lead, por que não consigo encontrar os leads?

Verifique se o formulário "Web to Lead" é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Ocorreu um problema com o meu destino de Lead e eu perdi alguns clientes potenciais. Posso tê-los enviados por email?

Devido a políticas PII (informações de identificação particular), não podemos compartilhar informações de Lead por emails não seguros.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei a tabela do Azure como meu destino de Lead, quanto custará?

Os dados de Gen de Lead são baixos (< 1 GB para quase todos os Publicadores). O custo dependerá do número de clientes potenciais recebidos, se 1.000 clientes potenciais forem recebidos em um mês, custarão cerca de 50 centavos. Para obter mais informações sobre preços de armazenamento, consulte [preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/).

Se sua pergunta ainda não foi respondida, entre em contato com o suporte por meio de aka.ms/marketplacepublishersupport e selecione **"criação de oferta"** → **seu tipo de oferta** → **"configuração de gerenciamento de Lead".** 

## <a name="next-steps"></a>Passos seguintes

Depois que a configuração técnica estiver em vigor, você deverá incorporar esses leads em suas vendas atuais & estratégia de marketing e processos operacionais. Estamos interessados em entender melhor seu processo de vendas geral e quero trabalhar junto com você para fornecer leads de alta qualidade e dados suficientes para que você tenha êxito. Agradecemos seus comentários sobre como é possível otimizar e aprimorar os leads que enviamos a você com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você estiver interessado em [fornecer comentários](mailto:AzureMarketOnboard@microsoft.com) e sugestões para permitir que sua equipe de vendas seja mais bem-sucedida com leads do Marketplace.
