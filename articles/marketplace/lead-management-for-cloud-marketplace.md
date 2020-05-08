---
title: Gestão de chumbo para o mercado comercial [ Azure Marketplace e AppSource
description: Uma visão geral de vários tópicos relacionados com ofertas editoriais e artefactos técnicos para o Azure Marketplace e AppSource
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: c41831f528ae425b35a04503180a956f67762b70
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82789849"
---
# <a name="lead-management-for-commercial-marketplace"></a>Gestão de chumbo para o mercado comercial

Os clientes são o centro de qualquer bom negócio. Na transformação das aquisições de produtos atualmente, os marketers precisam de se concentrar em conectar-se diretamente com os clientes e construir uma relação. É por isso que gerar leads de alta qualidade é uma ferramenta vital para o seu ciclo de vendas. Depois de listar a sua oferta no [Partner Center,](https://partner.microsoft.com/)existem ferramentas habilitadas para que receba programadamente informações de contacto do cliente imediatamente após um cliente expressar interesse ou implementar o seu produto no mercado. 

## <a name="what-are-leads-in-the-marketplace"></a>O que são pistas no mercado?

Os leads são de clientes interessados ou implantando os seus produtos no Mercado. Quer o seu produto esteja listado no Azure Marketplace ou no AppSource, poderá receber pistas dos clientes assim que este for configurado corretamente desde o seu CRM até à sua listagem no Partner Center. 

## <a name="how-to-connect-your-crm-system-with-partner-center"></a>Como ligar o seu sistema CRM ao Partner Center

Para começar a obter leads, o conector Lead Management no Partner Center foi concebido para que possa ser facilmente ligado com a informação de CRM a uma lista do sistema CRM disponível. Agora pode facilmente alavancar os leads gerados pelo mercado sem um esforço significativo de engenharia para se integrar com um sistema externo.

Aqui estão instruções passo a passo sobre como ligar cada um dos possíveis destinos de chumbo:

**Dynamics CRM Online** - Consulte a [gestão de chumbo configurar a Dynamics 365 para](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) obter instruções sobre como configurar o Dynamics CRM Online para obter leads.

**Marketo** - Consulte a [configure a gestão de chumbo em Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md) para obter instruções sobre a criação da Configuração de Chumbo marketo para obter leads.

**Salesforce** - Consulte a [configuração da gestão de chumbo para a Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md) para obter instruções sobre a configuração da sua instância Salesforce para obter leads.

**Tabela Azure** - Consulte a gestão de [chumbo configurar utilizando uma tabela Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) para obter instruções sobre a configuração da sua conta de armazenamento Azure para obter chumbos numa tabela Azure.

**Https Endpoint** - Consulte a [gestão de chumbo configurar utilizando um ponto final HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md) para obter o seu Ponto Final https para obter leads.

Assim que configurar corretamente o seu destino principal e ter atingido a Publish na sua oferta, validamos a ligação e enviaremos-lhe um teste de chumbo. Ao ver a oferta antes de ir ao vivo, também pode testar a sua ligação de chumbo tentando adquirir a oferta no ambiente de pré-visualização. É importante certificar-se de que as definições de chumbo permanecem atualizadas para que não perca nenhuma pista, por isso certifique-se de atualizar estas ligações sempre que algo tenha mudado na sua extremidade.

### <a name="what-are-the-next-steps"></a>Quais são os próximos passos?

Uma vez que a configuração técnica esteja em vigor, deve incorporar estes leads na sua estratégia de marketing & de vendas e processos operacionais. Estamos interessados em compreender melhor o seu processo de vendas global e queremos trabalhar em estreita colaboração consigo para fornecer leads de alta qualidade e dados suficientes para torná-lo bem sucedido. Congratulamo-nos com o seu feedback sobre como podemos otimizar e melhorar as pistas que lhe enviamos com dados adicionais para ajudar a tornar estes clientes bem sucedidos. Informe-nos se está interessado em fornecer feedback e sugestões para permitir que a sua equipa de vendas tenha mais sucesso com o Marketplace Leads.

## <a name="common-lead-configuration-errors-during-publishing-in-partner-center"></a><a id="publishing-config-errors"></a>Erros comuns de configuração de chumbo durante a publicação no Partner Center

**Não conseguiu salvar a liderança para a Dynamics CRM. Verifique as definições da conta DeCR MDinâmica. LastCRMError: Incapaz de iniciar sessão na Dynamics CRM, LastCRMException:** 

> Se a autenticação O365 foi selecionada, verifique se a conta de utilizador e a palavra-passe são válidas. Se a AAD foi selecionada, verifique se o ID do inquilino, o ID da aplicação e a chave secreta da aplicação correspondem ao que foi configurado no AAD. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md). Se o nome de utilizador/palavra-passe da conta for válido, certifique-se de que tem acesso à Dinâmica 365 e tem uma licença atribuída (Passos 11-15 se utilizar o Diretório Ativo do Azure ou as Definições de Segurança se utilizar um utilizador do Office). 

**Não conseguiu salvar a liderança para a Dynamics CRM. O utilizador não tem permissões para o atributo do código de chumbo na entidade principal** 

> A aplicação/utilizador está a faltar às funções de segurança do escritor principal do Microsoft Marketplace. Siga os passos 11-15 se utilizar o Diretório Ativo azure ou as Definições de Segurança se utilizar um utilizador do Office [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não conseguiu salvar o chumbo da Dynamics CRM utilizando a AAD. Exceção:: Inquilino não encontrado. Este caso pode acontecer se não houver subscrições ativas para o inquilino.**  

> O Id do Diretório fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o Id do Diretório com base nas instruções do Passo 2 (sob o Diretório Ativo Azure, a partir [daqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md).

**Não conseguiu salvar a liderança para a Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falhou - nenhuma função é atribuída ao utilizador.**  

> Resolução: Atribuir papel de segurança ao escritor principal do Microsoft Marketplace. Siga as instruções [aqui](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) sob as definições de segurança.

**Não conseguiu salvar o chumbo da Dynamics CRM utilizando a AAD. Exceção:: Não foi encontrada aplicação com identificador no diretório** 

> O Id de aplicação fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o Id do Diretório com base nas instruções do Passo 8 (sob o Diretório Ativo Azure, a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não conseguiu salvar o chumbo da Dynamics CRM utilizando a AAD. Exceção:: Identificador de inquilino solicitado não é válido e não é válido formato de domínio externo** 

> O Id do Diretório fornecido na secção de gestão de chumbo não é um diretório válido. Por favor, obtenha o Id do Diretório com base nas instruções do Passo 2 (sob o Diretório Ativo Azure, a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não conseguiu salvar o chumbo da Dynamics CRM utilizando a AAD. Exceção:: Erro validando credenciais.: Segredo de cliente inválido é fornecido.** 

> Resolução: Inscreva-se no portal Azure, verifique se a chave de aplicação corresponde à que está no Centro parceiro. Por favor, gere uma palavra-passe com base na instrução do Passo 10 (sob o Diretório Ativo Azure), a partir [daqui).](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md) 

**Não conseguiu salvar a liderança para a Dynamics CRM. LastCRMError: O canal de pedido cronometrado enquanto aguarda uma resposta depois das 00:02:00. Aumente o valor do tempo passado para a chamada para Solicitar ou aumente o valor do Envio no Encadernação. O tempo atribuído a esta operação pode ter sido uma parte de um tempo de tempo mais longo.**  

> Resolução: Inscreva-se no Partner Center, verifique os detalhes da Montra >> destino principal >> URL, verifique se é uma instância de CRM dinâmica válida.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O que são pistas e por que são importantes para mim como editor no Marketplace?** 

Os leads são clientes que estão a implantar os seus produtos a partir do Marketplace. Quer o seu produto esteja listado no [Azure Marketplace](https://azuremarketplace.microsoft.com) ou [no AppSource,](https://appsource.microsoft.com/)poderá receber pistas de clientes interessados no seu produto se tiver configurado o destino principal da sua oferta.  

**Onde posso arranjar ajuda para preparar o meu destino principal?** 

Você pode encontrar documentação no [Get customer leads](./partner-center-portal/commercial-marketplace-get-customer-leads.md) ou enviar um bilhete de suporte na Ajuda e [suporte](https://partner.microsoft.com/support/v2/?stage=1). Selecione tipo de oferta e gestão de chumbo. 

**Sou obrigado a configurar um destino de chumbo para publicar uma oferta no Marketplace?**

Sim, se estiver a publicar uma aplicação Contact Me SaaS ou Serviços de Consultoria.  
 
**Como posso confirmar que a configuração do chumbo está correta?**

Depois de configurar a sua oferta e de liderar o destino, publique a sua oferta. Na etapa de validação de chumbo, o Marketplace enviará um teste de chumbo para o destino de chumbo configurado na sua oferta. 

**Como posso encontrar o chumbo do teste?**

Procure "MSFT_TEST" no seu destino principal, aqui está um teste de amostra de dados principais: 

empresa = MSFT_TEST_636573304831318844 

país = EUA 

descrição = MSFT_TEST_636573304831318844 

e-mail =MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 \<Oferta nome> 

oid = 00Do00000000ZHog 

telefone = 1234567890 

título = MSFT_TEST_636573304831318844 

**Tenho uma oferta ao vivo, mas não vejo pistas?**

Cada chumbo terá dados passados em campos no seu destino de chumbo selecionado, os leads virão neste formato: **Source-Action. Oferta** 

  *Fontes:*

    "AzureMarketplace", 
    "AzurePortal", 
    "TestDrive",  
    "SPZA" (acronym for AppSource) 

  *Ações:*

    "INS" - Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    "PLT" - Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    "DNC" - Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    "Create" - This is inside Azure portal only and is whenever a customer purchases your offer to their account. 

    "StartTestDrive" - This is for Test Drives only and is whenever a customer starts their test drive. 


  *Ofertas:*

    "checkpoint.check-point-r77-10sg-byol", 
    "bitnami.openedxcypress", 
    "docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a" 

 

  *Aqui estão os dados da amostra da informação do cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Saiba mais em [Lead Info](./partner-center-portal/commercial-marketplace-get-customer-leads.md). 

**Configurei o Azure BLOB como o meu destino principal, porque não vejo a pista?** 

O chumbo só é escrito quando seleciona o armazenamento Azure BLOB como destino principal. Mude para a mesa Azure para receber o chumbo em tempo real.

**Recebi um e-mail do Marketplace, por que não encontro a pista no meu CRM?**  

É possível que o domínio de e-mail do utilizador final seja de .edu. Por razões de privacidade não passamos dados pessoais identificáveis do domínio .edu. Envie um bilhete de apoio na [Ajuda e suporte.](https://partner.microsoft.com/support/v2/?stage=1)

**Configurei a Azure Table/Azure BLOB como o meu destino principal, como posso ver as pistas?** 

Pode aceder à bolha ou à tabela do portal Azure, ou pode descarregar e instalar o [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para ver as mesas/bolhas da sua conta de armazenamento Azure. 

**Configurei a Mesa Azure como o meu destino principal, posso ser notificado sempre que uma nova pista é enviada pelo Marketplace?** 

Sim, siga as instruções para configurar a Tabela Azure + Função na documentação [aqui .](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md) 

**Configurei a Salesforce como o meu destino principal, por que não encontro as pistas?** 

Verifique se o formulário web para chumbo é um campo obrigatório baseado numa lista de escolhas. Se sim, mude o campo para um campo de texto não obrigatório.  
 
**Houve um problema com o meu destino principal, e perdi algumas pistas. Posso mandá-los num e-mail?** 

Devido às políticas de privacidade, não podemos partilhar informações de chumbo através de e-mail não seguro. 

**Configurei o Armazenamento Azure (BLOB/Tabela) como o meu destino principal, quanto é que vai custar?** 

Os dados da geração lead são baixos (<1 GB para quase todos os editores). O custo dependerá do número de pistas recebidas, se 1.000 pistas forem recebidas num mês, custa cerca de 50 cêntimos. 
