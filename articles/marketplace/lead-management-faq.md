---
title: Questões de gestão de chumbo e resolução de problemas - Microsoft Partner Center
description: Leia sobre erros e questões comuns ao configurar o mercado comercial lidera no Partner Center
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 10/01/2020
ms.openlocfilehash: 681498073a5f111e3babd50443623589ffeec02f
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330925"
---
# <a name="common-questions-and-troubleshooting-for-lead-configuration"></a>Perguntas comuns e resolução de problemas para a configuração do chumbo

Este artigo responde a algumas questões comuns sobre gestão de chumbo para as suas ofertas de marketplace comercial. Também aborda erros que poderá encontrar ao configurar que conduz ao seu sistema de gestão de relacionamento com o cliente (CRM) no Partner Center.

## <a name="common-questions-about-lead-management"></a>Questões comuns sobre gestão de chumbo

#### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Onde posso conseguir ajuda para preparar o meu destino principal?

Consulte [os leads do Cliente da sua oferta de mercado comercial](partner-center-portal/commercial-marketplace-get-customer-leads.md) para uma visão geral de como ligar o seu sistema CRM às suas ofertas de mercado comercial. Se tiver um erro, reveja a orientação de resolução de problemas abaixo. Para obter mais apoio, envie um bilhete de apoio através [da Ajuda do Centro de Parceiros e suporte.](https://aka.ms/marketplacepublishersupport) Em seguida, **selecione Criar Oferta**O seu tipo de  >  **configuração de**  >  **gestão de chumbo**de oferta .

#### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Sou obrigado a configurar um destino principal para publicar uma oferta no mercado comercial?

A resposta depende do tipo de oferta que está a publicar. Software como serviço (SaaS) e Dynamics 365 Utilização de Envolvimento com o Cliente **Contacte-me** para listar todas as ofertas Dynamics 365 para Finanças e Operações, todas as ofertas da Dynamics 365 Business Central e todas as ofertas do Serviço de Consultoria. Como resultado, requerem uma ligação a um destino de chumbo. Se o seu tipo de oferta não foi listado, uma ligação para um destino de chumbo não é necessária. Recomendamos que configuure um destino principal para que não perca oportunidades de negócio.

#### <a name="how-can-i-find-the-test-lead"></a>Como posso encontrar o resultado do teste?

Procure `"MSFT_TEST"` no seu destino principal. Abaixo está um chumbo de teste de amostra da Microsoft. Note que o formato do chumbo de teste varia consoante o destino de chumbo.

```
{
    "userDetails": {
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

#### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>Tenho uma oferta ao vivo, mas por que não vejo pistas?

Certifique-se de que a sua ligação ao destino de chumbo é válida. Enviaremos um teste depois de selecionar **Publicar** a sua oferta no Partner Center. Se vir o chumbo do teste, a ligação é válida. Também pode testar a sua ligação de chumbo tentando adquirir a pré-visualização da oferta durante o passo de pré-visualização. Selecione **Get It Now**, **Contacte-me**ou **Teste Gratuito** na listagem no mercado comercial.

Além disso, certifique-se de que procura os dados certos. Consulte [os dados de chumbo](partner-center-portal/commercial-marketplace-get-customer-leads.md) para obter uma explicação dos dados de chumbo que enviamos para o seu destino principal.

#### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>Configurei o armazenamento do Azure Blob como o meu destino principal, mas porque não vejo a pista?

O armazenamento Azure Blob já não é suportado como um destino principal, por isso está a perder qualquer pista de clientes gerada pela sua oferta. Mude para qualquer uma das outras [opções](partner-center-portal/commercial-marketplace-get-customer-leads.md)de destino de chumbo . 

#### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>Recebi um e-mail do mercado comercial, mas por que não consigo encontrar a liderança no meu CRM?

É possível que o domínio de e-mail do utilizador final seja de .edu. Por razões de privacidade, não transmitimos informações pessoais do domínio .edu. Envie um bilhete de apoio através [da Ajuda e suporte do Partner Center.](https://aka.ms/marketplacepublishersupport)

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>Configurei uma mesa Azure como o meu destino principal. Como posso ver as pistas?

Pode aceder aos dados de chumbo armazenados na tabela Azure a partir do portal Azure. Também pode descarregar e instalar [gratuitamente o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para ver os dados da tabela da sua conta de armazenamento Azure.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>Configurei uma mesa Azure como o meu destino principal. Posso ser notificado sempre que for enviado um novo líder comercial?

Sim. Siga as instruções na [gestão do chumbo Configure utilizando uma tabela Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para configurar um fluxo Microsoft que envia um e-mail se um fio for adicionado à tabela Azure.

#### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>Configurei a Salesforce como o meu destino principal, mas por que não encontro as pistas?

Verifique se o formulário web-to-lead é um campo obrigatório baseado numa lista de escolhas. Se for, mude o campo para um campo de texto não-mando.

#### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Houve um problema com o meu destino principal, e perdi algumas pistas. Posso enviá-los para mim por e-mail?

Devido às políticas de informação pessoal, não podemos partilhar informações de chumbo através de e-mails não protegidos.

#### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>Configurei uma mesa Azure como o meu destino principal. Quanto irá custar?

Os dados da geração de chumbo são baixos. É menos de 1 GB para quase todos os editores. O custo depende do número de pistas recebidas. Por exemplo, se 1.000 leads forem recebidos num mês, o custo é de cerca de 50 cêntimos. Para obter mais informações sobre os preços de armazenamento, consulte [os preços gerais do Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Se a sua pergunta não for respondida, contacte o Microsoft Support através da [Ajuda do Centro de Parceiros e suporte](https://aka.ms/marketplacepublishersupport). Em seguida, **selecione Criar Oferta**O seu tipo de  >  **configuração de**  >  **gestão de chumbo**de oferta .

#### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Estou a receber notificações por e-mail quando novas pistas de clientes são recebidas. Como posso configurar outra pessoa para receber estes e-mails?

Aceda à sua oferta no Partner Center e vá à página **de configuração da Oferta** > Dição de **Gestão de Chumbo.**  >  **Edit** Atualize os endereços de e-mail no campo **de e-mail de Contato.**

## <a name="troubleshooting-lead-configuration-errors"></a><a id="publishing-config-errors"></a> Erros de configuração de chumbo de resolução de problemas

**Não consegui guardar a vantagem para a Dynamics CRM. Verifique as definições da conta CrM Dynamics. LastCRMError: Incapaz de iniciar sposição na Dynamics CRM, LastCRMException:** 

> Se a autenticação do Microsoft 365 foi selecionada, verifique se a conta de utilizador e a palavra-passe são válidas. Se o Azure Ative Directory foi selecionado, verifique se o ID do inquilino, iD de aplicação e chave secreta de aplicação correspondem ao que foi criado no Diretório Ativo Azure. Siga as instruções [aqui.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) Se o nome de utilizador/palavra-passe da conta for válido, certifique-se de que tem acesso à Dynamics 365 e tem uma licença atribuída (Passos 11-15 se utilizar o Azure Ative Directory ou Configurações de Segurança se utilizar um utilizador do Office). 

**Não consegui guardar a vantagem para a Dynamics CRM. O utilizador não tem permissões para o atributo de código de chumbo na entidade líder** 

> A aplicação/utilizador está a perder funções de segurança para o escritor principal do Microsoft Marketplace. Siga os passos 11-15 se utilizar o Azure Ative Directory ou definições de segurança se utilizar um utilizador do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: Inquilino não encontrado. Este caso pode acontecer se não houver assinaturas ativas para o inquilino.**  

> O Diretório Id fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções do Passo 2 (sob diretório ativo Azure) [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não consegui guardar a vantagem para a Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falhou - nenhuma função é atribuída ao utilizador.**  

> Resolução: Atribuir papel de Segurança ao escritor principal do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) nas definições de Segurança.

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: Aplicação com identificador não foi encontrada no diretório** 

> O ID de aplicação fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções do Passo 8 (sob diretório Azure Ative, a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: O identificador de inquilino solicitado não é válido e não é um formato de domínio externo válido** 

> O Diretório Id fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções do Passo 2 (sob diretório Azure Ative, a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: Credenciais de validação de erros.: É fornecido um segredo de cliente inválido.** 

> Resolução: Inscreva-se no portal Azure, verifique se a chave de aplicação corresponde ao que está no Centro de Parceiros. Por favor, gere a palavra-passe com base na instrução do Passo 10 (sob o Diretório Ativo Azure), a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não consegui guardar a vantagem para a Dynamics CRM. LastCRMError: O canal de pedido foi cronometrado enquanto aguarda uma resposta depois das 00:02:00. Aumente o valor de tempo limite passado para a chamada para Pedir ou aumente o valor sendTimeout na Ligação. O tempo atribuído a esta operação pode ter sido uma parte de um tempo mais longo.**  

> Resolução: Inscreva-se no Partner Center, verifique a configuração da Oferta >> o cliente conduz >> URL, verifique se é uma instância de CRM dinâmica válida.

