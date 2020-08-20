---
title: Gestão de chumbo para o mercado comercial Azure Marketplace e AppSource
description: Uma visão geral de vários tópicos relacionados com ofertas de publicação e artefactos técnicos para o Azure Marketplace e AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/14/2020
ms.openlocfilehash: b08324654a046b5a2296f6e533107c1b26a0e1fc
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606885"
---
# <a name="lead-management-for-the-commercial-marketplace"></a>Gestão de chumbo para o mercado comercial

Os clientes são o centro de qualquer bom negócio. Na transformação das aquisições de produtos de hoje, os marketers precisam de se concentrar em conectar-se diretamente com os clientes e construir uma relação. É por isso que gerar cabos de alta qualidade é uma ferramenta vital para o seu ciclo de vendas. Depois de enumerar a sua oferta no [Partner Center,](https://partner.microsoft.com/)existem ferramentas habilitadas para que possa receber programáticamente as informações de contacto do cliente imediatamente após um cliente manifestar interesse ou implementar o seu produto no mercado. 

## <a name="what-are-leads-in-the-marketplace"></a>Quais são as pistas no mercado?

Os leads são de clientes interessados ou implantando os seus produtos a partir do Marketplace. Quer o seu produto esteja listado no Azure Marketplace ou no AppSource, poderá receber leads dos clientes assim que este estiver configurado corretamente do seu CRM para a sua(s) listing(s) no Partner Center. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Como ligar o seu sistema CRM ao Partner Center

Para começar a obter leads, o conector Lead Management no Partner Center foi concebido para que possa ser facilmente ligado com as informações de CRM a uma lista de sistemas CRM disponíveis. Agora pode facilmente aproveitar os leads gerados pelo mercado sem um esforço significativo de engenharia para se integrar com um sistema externo.

Aqui estão instruções passo a passo sobre como ligar cada um dos possíveis destinos de chumbo:

**Dynamics CRM Online** - Consulte [a gestão de chumbo configurada para a Dynamics 365 para o Envolvimento do Cliente](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) para obter instruções sobre como configurar a Dynamics CRM Online para obter leads.

**Marketo** - Consulte [a gestão de chumbo de configuração no Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) para obter instruções sobre a criação da Configuração de Chumbo do Marketo para obter leads.

**Salesforce** - Consulte a gestão de chumbo de [configuração da Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) para obter instruções sobre a configuração da sua instância Salesforce para obter leads.

**Tabela Azure** - Consulte [a gestão do chumbo de configuração utilizando uma tabela Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para obter instruções sobre a configuração da sua conta de armazenamento Azure para obter leads numa tabela Azure.

**Https Endpoint** - Consulte a gestão do chumbo de [configuração utilizando um ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) para obter instruções sobre a configuração do seu Https Endpoint para obter leads.

Uma vez configurado o seu destino principal corretamente e tiver atingido a Publicação na sua oferta, validaremos a ligação e enviar-lhe-emos um lead de teste. Ao visualizar a oferta antes de ir ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização. É importante certificar-se de que as definições de chumbo se mantêm atualizadas para que não perca nenhuma pista, por isso certifique-se de atualizar estas ligações sempre que algo tiver mudado na sua parte.

### <a name="what-are-the-next-steps"></a>Quais são os próximos passos?

Uma vez que a configuração técnica esteja em vigor, você deve incorporar estes leads nas suas vendas correntes & estratégia de marketing e processos operacionais. Estamos interessados em compreender melhor o seu processo global de vendas e queremos trabalhar em estreita colaboração consigo no fornecimento de leads de alta qualidade e dados suficientes para o tornar bem sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar os leads que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Informe-nos se estiver interessado em fornecer feedback e sugestões para que a sua equipa de vendas tenha mais sucesso com a Marketplace Leads.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a> Erros comuns de configuração de chumbo durante a publicação no Partner Center

**Não consegui guardar a vantagem para a Dynamics CRM. Verifique as definições da conta CrM Dynamics. LastCRMError: Incapaz de iniciar sposição na Dynamics CRM, LastCRMException:** 

> Se a autenticação O365 foi selecionada, verifique se a conta de utilizador e a palavra-passe são válidas. Se a AAD foi selecionada, verifique se o ID do inquilino, iD de aplicação e chave secreta da aplicação correspondem ao que foi criado na AAD. Siga as instruções [aqui.](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) Se o nome de utilizador/palavra-passe da conta for válido, certifique-se de que tem acesso à Dynamics 365 e tem uma licença atribuída (Passos 11-15 se utilizar o Azure Ative Directory ou Configurações de Segurança se utilizar um utilizador do Office). 

**Não consegui guardar a vantagem para a Dynamics CRM. O utilizador não tem permissões para o atributo de código de chumbo na entidade líder** 

> A aplicação/utilizador está a perder funções de segurança para o escritor principal do Microsoft Marketplace. Siga os passos 11-15 se utilizar o Azure Ative Directory ou as Definições de Segurança se utilizar um utilizador do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: Inquilino não encontrado. Este caso pode acontecer se não houver assinaturas ativas para o inquilino.**  

> O Diretório Id fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções do Passo 2 (sob o Diretório Azure Ative, a partir [daqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não consegui guardar a vantagem para a Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falhou - nenhuma função é atribuída ao utilizador.**  

> Resolução: Atribuir papel de Segurança ao escritor principal do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) nas definições de Segurança.

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: Aplicação com identificador não foi encontrada no diretório** 

> O ID de aplicação fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções do Passo 8 (sob diretório Azure Ative, a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: O identificador de inquilino solicitado não é válido e não é um formato de domínio externo válido** 

> O Diretório Id fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o ID do Diretório com base nas instruções do Passo 2 (sob diretório Azure Ative, a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não foi possível guardar o chumbo para a Dynamics CRM utilizando o AAD. Exceção:: Credenciais de validação de erros.: É fornecido um segredo de cliente inválido.** 

> Resolução: Inscreva-se no portal Azure, verifique se a chave de aplicação corresponde ao que está no Centro de Parceiros. Por favor, gere a palavra-passe com base na instrução do Passo 10 (sob o Diretório Ativo Azure), a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não consegui guardar a vantagem para a Dynamics CRM. LastCRMError: O canal de pedido foi cronometrado enquanto aguarda uma resposta depois das 00:02:00. Aumente o valor de tempo limite passado para a chamada para Pedir ou aumente o valor sendTimeout na Ligação. O tempo atribuído a esta operação pode ter sido uma parte de um tempo mais longo.**  

> Resolução: Inscreva-se no Partner Center, consulte os detalhes da Storefront >> destino lead >> URL, verifique se é uma instância de CRM dinâmica válida.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O que são pistas e por que são importantes para mim como editora no Marketplace?** 

Os leads são clientes que estão a implementar os seus produtos a partir do Marketplace. Quer o seu produto esteja listado no [Azure Marketplace](https://azuremarketplace.microsoft.com) ou [no AppSource,](https://appsource.microsoft.com/)poderá receber pistas de clientes interessados no seu produto se tiver configurado o destino principal da sua oferta.  

**Onde posso conseguir ajuda para preparar o meu destino principal?** 

Pode encontrar documentação na [Get customer leads](./partner-center-portal/commercial-marketplace-get-customer-leads.md) ou enviar um bilhete de apoio na Help e [suporte.](https://aka.ms/marketplacepublishersupport) Selecione tipo de oferta e gestão de chumbo. 

**Sou obrigado a configurar um destino principal para publicar uma oferta no Marketplace?**

Sim, se estiver a publicar uma aplicação Contact Me SaaS ou Serviços de Consultoria.  

**Como posso confirmar que a configuração do chumbo está correta?**

Depois de configurar a sua oferta, e destino principal, publique a sua oferta. Na etapa de validação de chumbo, o Marketplace enviará uma pista de teste para o destino de chumbo configurado na sua oferta. 

**Como posso encontrar o resultado do teste?**

Procure por "MSFT_TEST" no seu destino principal, aqui está um dado de chumbo de teste de amostra: 

```text
company = MSFT_TEST_636573304831318844 

country = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

encoding = UTF-8 

encoding = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|\<Offer Name> 

oid = 00Do0000000ZHog 

phone = 1234567890 

title = MSFT_TEST_636573304831318844 
```

**Tenho uma oferta ao vivo, mas não estou a ver pistas?**

Cada chumbo terá dados passados em campos no seu destino de chumbo selecionado, os leads virão neste formato: **Source-Action/ Oferta**

- *Fontes:*
  - AzureMarketplace
  - Portal do Azure
  - TestDrive  
  - SPZA (acrónimo para AppSource)

- *Ações:*
  - "INS" - Significa Instalação. Isto está no Azure Marketplace ou AppSource sempre que um cliente carregar no botão para adquirir o seu produto.
  - "PLT" - Significa Julgamento Liderado por Parceiros. Isto está no AppSource sempre que um cliente toca no botão Contacte-me.
  - "DNC" - Significa Não Contactar. Isto está no AppSource sempre que um Parceiro que estava cruzado listado na sua página de aplicações é solicitado para ser contactado. Estamos a partilhar os avisos de que este cliente estava listado na sua app, mas não precisam de ser contactados.
  - "Create" - Isto é apenas dentro do portal Azure e é sempre que um cliente compra a sua oferta à sua conta.
  - "StartTestDrive" - Isto é apenas para Test Drives e é sempre que um cliente inicia o seu test drive.

- *Ofertas:*
  - "checkpoint.check-point-r77-10sg-byol",
  - "bitnami.openedxcypress",
  - "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a"

*Aqui estão os dados da amostra da informação do cliente*

```json
{ 
"FirstName":"John",
"LastName":"Smith",
"Email":"jsmith@microsoft.com",
"Phone":"1234567890",
"Country":"US",
"Company":"Microsoft",
"Title":"CTO"
}
```

Saiba mais em [Informações de Chumbo.](./partner-center-portal/commercial-marketplace-get-customer-leads.md) 

**Configurei Azure BLOB como o meu destino principal, por que não vejo a pista?** 

O chumbo só é escrito quando seleciona o armazenamento Azure BLOB como destino principal. Mude para a mesa Azure para receber o chumbo em tempo real.

**Recebi um e-mail do Marketplace, por que não consigo encontrar a pista no meu CRM?**  

É possível que o domínio de e-mail do utilizador final seja de .edu. Por razões de privacidade, não transmitimos dados pessoais identificáveis do domínio .edu. Envie um bilhete de apoio na [Ajuda e suporte.](https://aka.ms/marketplacepublishersupport)

**Configurei a Azure Table/Azure BLOB como o meu destino principal, como posso ver as pistas?** 

Pode aceder à bolha ou à mesa a partir do portal Azure, ou pode descarregar e instalar gratuitamente [o Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) para ver as mesas/bolhas da sua conta de armazenamento Azure. 

**Configurei a Azure Table como o meu destino principal, posso ser notificado sempre que um novo chumbo for enviado pelo Marketplace?** 

Sim, siga as instruções para configurar a Tabela Azure + Função na documentação [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md). 

**Configurei a Salesforce como o meu destino principal, porque não consigo encontrar as pistas?**

Verifique se a web para o formulário de chumbo é um campo obrigatório baseado numa lista de escolhas. Se sim, mude o campo para um campo de texto não obrigatório.  
 
**Houve um problema com o meu destino principal, e perdi algumas pistas. Posso enviá-los para mim por e-mail?** 

Devido às políticas de privacidade, não podemos partilhar informações de chumbo através de e-mail não seguro. 

**Configurei o Azure Storage (BLOB/Table) como o meu destino principal, quanto vai custar?** 

Os dados da gen de chumbo são baixos (<1 GB para quase todos os editores). O custo dependerá do número de leads recebidos, se 1.000 leads forem recebidos num mês, custa cerca de 50 cêntimos. 
