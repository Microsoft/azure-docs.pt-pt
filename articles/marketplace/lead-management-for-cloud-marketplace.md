---
title: Gerenciamento de leads para o Marketplace de nuvem | Azure Marketplace e AppSource
description: Uma visão geral de vários tópicos relacionados à publicação de ofertas e a artefatos técnicos para o Azure Marketplace e o AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.topic: article
ms.date: 10/05/2018
ms.author: pabutler
ms.openlocfilehash: a89921d4b5ec25bc0c924646d3bd41aecf3e8d9c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67870930"
---
# <a name="lead-management-for-cloud-marketplace"></a>Gerenciamento de leads para o Marketplace de nuvem


Os clientes são o centro de qualquer bom negócio. Na transformação das aquisições de produtos atuais, os comerciantes precisam se concentrar na conexão com os clientes diretamente e na criação de uma relação. É por isso que a geração de leads de alta qualidade é uma ferramenta vital para seu ciclo de vendas. Depois de listar sua oferta no [portal do Cloud Partner](https://cloudpartner.azure.com/), há ferramentas habilitadas para que você receba programaticamente as informações de contato do cliente imediatamente depois que um cliente expressa o interesse ou implanta seu produto no Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>O que são clientes potenciais no Marketplace?

Os leads são de clientes que estão interessados ou implantando seus produtos do Marketplace. Se seu produto estiver listado no Azure Marketplace ou AppSource, você poderá receber clientes potenciais de clientes depois que ele for configurado corretamente do seu CRM para suas listas no Portal do Cloud Partner 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como conectar seu sistema CRM ao portal de parceiros de nuvem

Para começar a obter clientes potenciais, o conector de gerenciamento de leads na Portal do Cloud Partner foi projetado para que possa ser facilmente conectado com suas informações de CRM a uma lista de sistemas CRM disponíveis. Agora você pode aproveitar facilmente os leads gerados pelo Marketplace sem um esforço de engenharia significativo para se integrar a um sistema externo.

Aqui estão as instruções passo a passo sobre como conectar cada um dos possíveis destinos de cliente potencial:

 - [Clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) no Dynamics CRM Online para obter as instruções sobre como configurar o Dynamics CRM Online para obter clientes potenciais.

Clique aqui para obter as instruções para configurar a configuração de Lead do marketo para obter clientes potenciais.[](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo)  - 

Salesforce - [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) para obter instruções para configurar sua instância do Salesforce para obter clientes potenciais.

**Tabela do Azure** – [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) para obter as instruções para configurar sua conta de armazenamento do Azure para obter clientes potenciais em uma tabela do Azure.

**Ponto de extremidade https** – [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) para obter as instruções para configurar seu ponto de extremidade HTTPS para obter clientes potenciais.

Depois de configurar o destino de Lead corretamente e clicar em publicar na sua oferta, validaremos a conexão e enviaremos um líder de teste. Ao exibir a oferta antes de entrar no ar, você também pode testar sua conexão de cliente potencial tentando adquirir a oferta por conta própria no ambiente de visualização. É importante certificar-se de que as configurações de Lead permaneçam atualizadas para que você não perca nenhum cliente potencial, portanto, atualize essas conexões sempre que algo tiver mudado no final.


### <a name="what-are-the-next-steps"></a>Quais são as próximas etapas?

Quando a configuração técnica estiver em vigor, você deverá incorporar esses leads em suas vendas atuais & estratégia de marketing e processos operacionais. Estamos interessados em entender melhor seu processo de vendas geral e quero trabalhar junto com você para fornecer leads de alta qualidade e dados suficientes para que você tenha êxito. Agradecemos seus comentários sobre como é possível otimizar e aprimorar os leads que enviamos a você com dados adicionais para ajudar a tornar esses clientes bem-sucedidos. Informe-nos se você estiver interessado em fornecer comentários e sugestões para permitir que sua equipe de vendas seja mais bem-sucedida com leads do Marketplace.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Erros comuns de configuração de Lead durante a publicação no portal de parceiros de nuvem 

**Não foi possível salvar o cliente potencial no Dynamics CRM. Verifique as configurações de conta do Dynamics CRM. LastCRMError: Não é possível entrar no Dynamics CRM, LastCRMException:** 

> Se a autenticação do O365 tiver sido selecionada, verifique se a conta de usuário e a senha são válidas. Se o AAD foi selecionado, verifique se a ID do locatário, a ID do aplicativo e a chave secreta do aplicativo correspondem ao que foi configurado no AAD. Siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Se o nome de usuário/senha da conta for válido, verifique se ele tem acesso ao Dynamics 365 e tem uma licença atribuída (etapas 11-15 se estiver usando Azure Active Directory ou configurações de segurança se estiver usando um usuário do Office). 

 
**Não foi possível salvar o cliente potencial no Dynamics CRM. O usuário não tem permissões de criação para o atributo leadsourcecode na entidade de cliente potencial** 

> O aplicativo/usuário não tem funções de segurança para Microsoft Marketplace gravador de cliente potencial. Siga as etapas 11-15 se estiver usando Azure Active Directory ou configurações de segurança se estiver usando um usuário do Office [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: Locatário não encontrado. Essa instância pode ocorrer se não houver nenhuma assinatura ativa para o locatário.**  

> A ID de diretório fornecida na seção de gerenciamento de leads não é um diretório válido. Obtenha a ID do diretório com base nas instruções na etapa 2 (em Azure Active Directory [, daqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Não foi possível salvar o cliente potencial no Dynamics CRM. LastCRMError: SecLib:: RetrievePrivilegeForUser falhou-nenhuma função está atribuída ao usuário.**  

> Resolução: Atribua a função de segurança a Microsoft Marketplace gravador de cliente potencial. Siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) em configurações de segurança 

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: O aplicativo com identificador não foi encontrado no diretório** 

> A ID do aplicativo fornecida na seção de gerenciamento de leads não é um diretório válido. Obtenha a ID do diretório com base nas instruções na etapa 8 (em Azure Active Directory, daqui [).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: O identificador de locatário solicitado não é válido e não é um formato de domínio externo válido** 

> A ID de diretório fornecida na seção de gerenciamento de leads não é um diretório válido. Obtenha a ID do diretório com base nas instruções na etapa 2 (em Azure Active Directory, daqui [).](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Não foi possível salvar o cliente potencial no Dynamics CRM usando o AAD. Exceção:: Erro ao validar as credenciais.: Segredo do cliente inválido fornecido.** 

> Resolução: Entre no portal do Azure, verifique se a chave do aplicativo corresponde ao que está no Portal do Cloud Partner. Gere a senha com base na instrução na etapa 10 (em Azure Active Directory), a partir [daqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível salvar o cliente potencial no Dynamics CRM. LastCRMError: O canal de solicitação atingiu o tempo limite ao aguardar uma resposta após 00:02:00. Aumente o valor de tempo limite passado para a chamada para solicitação ou aumente o valor de SendTimeout na associação. O tempo alocado para esta operação pode ter sido uma parte de um tempo limite maior.**  

> Resolução: Entre no Portal do Cloud Partner, verifique os detalhes da vitrine > > destino de Lead > > URL, verifique se é uma instância dinâmica de CRM válida

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**O que são clientes potenciais e por que eles são importantes para mim como um Publicador no Marketplace?** 

Os leads são clientes que estão implantando seus produtos do Marketplace. Se seu produto estiver listado no [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) ou [AppSource](https://appsource.microsoft.com/), você poderá receber clientes potenciais de clientes interessados em seu produto se tiver configurado o destino de Lead em sua oferta.  


**Onde posso obter ajuda para configurar meu destino de Lead?** 

Você pode encontrar a documentação aqui: [Obtenha clientes potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ou envie um tíquete de suporte por meio de aka.ms/marketplacepublishersupport selecionar tipo de oferta e gerenciamento de Lead. 



**Eu preciso configurar um destino de cliente potencial para publicar uma oferta no Marketplace?**

Sim, se você estiver publicando um aplicativo SaaS de contato comigo ou serviços de consultoria.  
 


**Como posso confirmar se a configuração do cliente potencial está correta?**

Depois de configurar sua oferta, e destino do cliente potencial, publique sua oferta. Em etapa de validação de cliente potencial, o Marketplace enviará um cliente potencial de teste para o destino do cliente potencial configurado em sua oferta. 


**Como posso encontrar o Lead de teste?**


Pesquise "MSFT_TEST" em seu destino de Lead, aqui está um exemplo de dados de Lead de teste: 

company = MSFT_TEST_636573304831318844 

país = US 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844 | \<Nome da oferta > 

OID = 00Do0000000ZHog 

telefone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Tenho uma oferta ao vivo, mas não estou vendo clientes potenciais?**

Cada Lead terá dados passados em campos no destino do cliente potencial selecionado, os leads virão neste formato: **Origem-ação | Proporcionar** 

  *Causas*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Ações*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Ofereça*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Aqui estão dados de exemplo das informações do cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Saiba mais em [informações de Lead](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Configurei o BLOB do Azure como meu destino de Lead, por que não vejo o cliente potencial?** 

O cliente potencial só é escrito quando você seleciona o armazenamento de BLOBs do Azure como destino do cliente potencial. Alterne para a tabela do Azure para receber o lead time em tempo real 


**Recebi um email do Marketplace, por que não consigo encontrar o cliente potencial no meu CRM?**  

É possível que o domínio de email do usuário final seja de. edu. Por motivos de privacidade, não passamos dados de PII do domínio. edu. Enviar um tíquete de suporte por meio do aka.ms/marketplacepublishersupport 


 **Configurei o Azure Table/Azure BLOB como meu destino de Lead, como posso exibir os leads?** 

Você pode acessar o BLOB ou a tabela no portal do Azure ou pode baixar e instalar [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para exibir as tabelas/BLOBs da sua conta de armazenamento do Azure. 


**Configurei a tabela do Azure como meu destino de Lead, posso ser notificado sempre que um novo cliente potencial for enviado pelo Marketplace?** 

Sim, siga as instruções para configurar a função do Azure Table + na documentação [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Configurei o Salesforce como meu destino de Lead, por que não consigo encontrar os leads?** 

Verifique se o formulário da Web para Lead é um campo obrigatório com base em uma lista de seleção. Em caso afirmativo, alterne o campo para um campo de texto não obrigatório.  
 

**Ocorreu um problema com o meu destino de Lead e eu perdi alguns clientes potenciais. Posso tê-los enviados por email?** 

Devido a políticas PII (informações de identificação particular), não podemos compartilhar informações de Lead por emails não seguros. 



**Configurei o armazenamento do Azure (BLOB/tabela) como meu destino de Lead, quanto custará?** 

Os dados de Gen de Lead são baixos (< 1 GB para quase todos os Publicadores). O custo dependerá do número de clientes potenciais recebidos, se 1.000 clientes potenciais forem recebidos em um mês, custarão cerca de 50 centavos. 
