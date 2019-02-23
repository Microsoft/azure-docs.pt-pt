---
title: Numa gestão para o marketplace da cloud | O Azure Marketplace e AppSource
description: Uma visão geral de vários tópicos relacionados ao publicar artefactos técnicos e ofertas no Azure Marketplace e AppSource
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/05/2018
ms.author: yijenj
ms.openlocfilehash: 810298fc45becf132da6f082df7ad33e7af828aa
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56735546"
---
# <a name="lead-management-for-cloud-marketplace"></a>Numa gestão para o marketplace da cloud


Os clientes são o Centro de qualquer bom negócio. A transformação, de aquisições de produto de hoje em dia, os comerciantes tem de se concentrar em contacto diretamente com clientes e criar uma relação. É por isso gerar oportunidades potenciais de alta qualidade é uma ferramenta essencial para o ciclo de vendas. Após a listagem a oferta na [Cloud Partner Portal](https://cloudpartner.azure.com/), existem ferramentas ativadas para a programaticamente receber informações de contacto do cliente imediatamente depois de um cliente expressa interesse ou implementa o seu produto no Marketplace. 



## <a name="what-are-leads-in-the-marketplace"></a>O que são é líder no mercado?

As oportunidades potenciais são de clientes que estejam interessados ou a implementação de seus produtos no Marketplace. Se o seu produto é listado no Azure Marketplace ou no AppSource, poderá receber oportunidades potenciais de clientes, uma vez que este está configurado corretamente no seu CRM para sua listing(s) no Portal de parceiros da Cloud 


## <a name="how-to-connect-your-crm-system-with-the-cloud-partner-portal"></a>Como ligar o seu sistema CRM com o portal de parceiros da cloud

Para iniciar a obtenção de oportunidades potenciais, o conector levar Management no Portal de parceiros de Cloud foi concebido para que ele pode ser facilmente ligado com suas informações de CRM para uma lista do sistema CRM disponível. Agora pode aproveitar facilmente as oportunidades potenciais geradas pelo marketplace sem um esforço significativo de engenharia para integrar com um sistema externo.

Seguem-se as instruções passo a passo sobre como ligar a cada um dos destinos líder de possíveis:

**Dynamics CRM Online** - [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) com as instruções sobre como configurar o Dynamics CRM Online para obter oportunidades potenciais.

**Marketo** - [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-marketo) com as instruções para configurar a configuração de levar Marketo para obter oportunidades potenciais.

**Salesforce** - [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-salesforce) para obter instruções sobre como configurar a instância do Salesforce obter oportunidades potenciais.

**Tabela do Azure** – [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table) para obter instruções para configurar a sua conta de armazenamento do Azure para obter oportunidades potenciais numa tabela do Azure.

**Ponto final de HTTPS** – [clique aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-https) com as instruções para configurar o ponto final de Https para obter oportunidades potenciais.

Assim que tiver configurado o destino da oportunidade potencial corretamente e ter atingido a funcionalidade Publicar na sua oferta, iremos validar a ligação e enviar-lhe um líder de teste. Quando está a visualizar a oferta antes de ir ao vivo, também pode testar a ligação de oportunidades potenciais tentando adquirir a oferta por conta própria no ambiente de pré-visualização. É importante certificar-se de que o mantenha-se do líder de definições atualizadas para que não perca quaisquer oportunidades potenciais, por isso, certifique-se de que atualiza essas conexões sempre que algo foi alterado de sua parte.


### <a name="what-are-the-next-steps"></a>Quais são os passos seguintes?

Assim que o técnico configurar estiver em vigor, deve incorporar destas oportunidades potenciais vendas atuais e estratégia de marketing e processos operacionais. Estamos interessados em compreender melhor o seu processo geral de venda e pretende trabalhar de perto com no fornecimento de oportunidades potenciais de alta qualidade e dados suficientes para torná-lo com êxito. Apreciamos os seus comentários sobre como podemos otimizar e melhorar as oportunidades potenciais que podemos enviar-lhe com dados adicionais para ajudar a tornar estes clientes com êxito. Queremos sabe se estiver interessado em fornecer comentários e sugestões para ativar a sua equipa de vendas ser mais bem sucedido com oportunidades potenciais do Marketplace.



## <a name="common-lead-configuration-errors-during-publishing-on-cloud-partner-portal"></a>Erros comuns de configuração da oportunidade potencial durante a publicação no portal de parceiros da cloud 

**Não foi possível guardar a liderança ao Dynamics CRM. Verifique as definições de conta do Dynamics CRM. LastCRMError: Não é possível iniciar sessão no Dynamics CRM, LastCRMException:** 

> Se tiver sido selecionada a autenticação do O365, verifique se a conta de utilizador e palavra-passe é válido. Se AAD tiver sido selecionado, verifique se o ID de inquilino, ID da aplicação e correspondências de chave secreta de aplicativo o que foi configurado no AAD. Siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics). Se o nome de utilizador/palavra-passe conta for válido, certifique-se de que tem acesso ao Dynamics 365 e tem uma licença atribuída (passos 11 a 15 se utilizar o Azure Active Directory ou as definições de segurança se utilizar um utilizador do Office). 

 
**Não foi possível guardar a liderança ao Dynamics CRM. Utilizador não tem permissões de criação para o atributo leadsourcecode na entidade oportunidade potencial** 

> O aplicativo/usuário está em falta as funções de segurança para o escritor de oportunidades potenciais do Microsoft Marketplace. Siga os passos 11 a 15 se utilizar o Azure Active Directory ou as definições de segurança se utilizar um utilizador do Office [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics).

**Não foi possível guardar a liderança ao Dynamics CRM com o AAD. Exceção:: Inquilino não foi encontrado. Esta instância pode acontecer se existirem não existem subscrições ativas para o inquilino.**  

> O Id de diretório na seção de gestão de oportunidades potenciais não é um diretório válido. Obtenha o Id de diretório com base nas instruções no passo 2 (em Azure Active Directory, de [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) 

**Não foi possível guardar a liderança ao Dynamics CRM. LastCRMError: SecLib::RetrievePrivilegeForUser falha - não existem funções são atribuídas ao utilizador.**  

> Resolução: Atribua a função de segurança para o escritor de oportunidades potenciais do Microsoft Marketplace. Siga as instruções [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics) em configurações de segurança 

**Não foi possível guardar a liderança ao Dynamics CRM com o AAD. Exceção:: Aplicação com o identificador não foi encontrada no diretório** 

> O Id da aplicação na seção de gestão de oportunidades potenciais não é um diretório válido. Obtenha o Id de diretório com base nas instruções no passo 8 (em Azure Active Directory, partir [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível guardar a liderança ao Dynamics CRM com o AAD. Exceção:: Identificador do inquilino pedida não é o formato de domínio externo válido e não é válido** 

> O Id de diretório na seção de gestão de oportunidades potenciais não é um diretório válido. Obtenha o Id de diretório com base nas instruções no passo 2 (em Azure Active Directory, partir [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível guardar a liderança ao Dynamics CRM com o AAD. Exceção:: Erro ao validar as credenciais.: É fornecido o segredo de cliente inválido.** 

> Resolução: Inicie sessão no Portal do Azure, verifique se a chave da aplicação corresponde a que há no Portal de parceiros de nuvem. Volte a gerar palavra-passe com base nas instruções no passo 10 (em Azure Active Directory), da [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-dynamics)). 

**Não foi possível guardar a liderança ao Dynamics CRM. LastCRMError: O canal de pedido excedido o tempo limite ao aguardar uma resposta após as 00:00 02:. Aumente o valor de tempo limite transmitido para a chamada a pedido ou aumente o valor de SendTimeout na associação. O tempo alocado para esta operação pode ter sido uma parte do limite de tempo mais longo.**  

> Resolução: Inicie sessão no Portal do Cloud Partner, verifique os detalhes da loja >> líder de destino >> URL, verifique se é uma instância válida do Dynamic CRM

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Quais são oportunidades potenciais e por que eles são importantes para mim como publicador no Marketplace?** 

Oportunidades potenciais são os clientes que estão a implementar seus produtos no Marketplace. Se o seu produto está listado no [do Azure Marketplace](https://azuremarketplace.microsoft.com/en-us) ou [AppSource](https://appsource.microsoft.com/), poderá receber oportunidades potenciais de clientes que estejam interessados em seu produto, se tiver configurado o destino de oportunidades potenciais na sua oferta.  


**Onde posso obter ajuda na configuração de meu destino líder?** 

Pode encontrar a documentação aqui: [Obter oportunidades potenciais](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) ou submeter um pedido de suporte através do tipo de oferta selecione aka.ms/marketplacepublishersupport e levar a gestão. 



**Sou obrigado a configurar um destino de oportunidades potenciais para poder publicar uma oferta no Marketplace?**

Sim, se está a publicar uma aplicação SaaS do contacto Me ou dos serviços de consultoria.  
 


**Como posso confirmar que a configuração de oportunidades potenciais está correta?**

Depois de configurar a oferta e o destino de oportunidades potenciais, publica a sua oferta. No passo de validação de oportunidades potenciais, Marketplace irá enviar um líder de teste para o destino de oportunidades potenciais configurado na sua oferta. 


**Como posso encontrar o líder de teste?**


Pesquisa para "MSFT_TEST" no destino da oportunidade potencial, aqui está um dados de oportunidades potenciais de teste de exemplo: 

company = MSFT_TEST_636573304831318844 

país = EUA 

description = MSFT_TEST_636573304831318844 

email = MSFT_TEST_636573304831318844@test.com

codificação = UTF-8 

codificação = UTF-8 

first_name = MSFT_TEST_636573304831318844 

last_name = MSFT_TEST_636573304831318844 

lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name> 

oid = 00Do0000000ZHog 

telefone = 1234567890 

title = MSFT_TEST_636573304831318844 

 

**Tenho uma oferta em direto, mas não estou a ver quaisquer oportunidades potenciais?**

Cada oportunidade potencial terá dados passados em campos no destino da oportunidade potencial selecionado, as oportunidades potenciais virão no seguinte formato: **Acção de origem | Oferta** 

  *Origens:*

    “AzureMarketplace”, 
    “AzurePortal”, 
    “TestDrive”,  
    “SPZA” (acronym for AppSource) 

  *Ações:*

    “INS” – Stands for Installation. This is on Azure Marketplace or AppSource whenever a customer hits the button to acquire your product. 
    “PLT” – Stands for Partner Led Trial. This is on AppSource whenever a customer hits the Contact me button. 

    “DNC” – Stands for Do Not Contact. This is on AppSource whenever a Partner who was cross listed on your app page gets requested to be contacted. We are sharing the heads up that this customer was cross listed on your app, but they do not need to be contacted. 

    “Create” – This is inside Azure Portal only and is whenever a customer purchases your offer to their account. 

    “StartTestDrive” – This is for Test Drives only and is whenever a customer starts their test drive. 


  *Oferece:*

    “checkpoint.check-point-r77-10sg-byol”, 
    “bitnami.openedxcypress”, 
    “docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145a” 

 

  *Segue-se os dados de exemplo das informações do cliente*

    { 

    "FirstName":"John", 

    "LastName":"Smith", 

    "Email":"jsmith@microsoft.com", 

    "Phone":"1234567890", 

    "Country":"US", 

    "Company":"Microsoft", 

    "Title":"CTO" 

    } 

Saiba mais em [levar informações](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads). 


**Configurei o BLOB do Azure como destino meu chefe, por que motivo não vejo o líder?** 

O líder de apenas é gravado quando selecionar o armazenamento de BLOBS do Azure como líder de destino. Mudar para uma tabela do Azure para receber o líder em tempo real 


**Recebi uma mensagem de e-mail do Marketplace, por que motivo não consigo encontrá a liderança no meu CRM?**  

É possível que o domínio de e-mail do utilizador final é de. edu. Por motivos de privacidade, não passar dados PII do domínio. edu. Submeter um pedido de suporte por meio de aka.ms/marketplacepublishersupport 


 **Configurei o BLOB de tabela/Azure do Azure como destino meu oportunidade potencial, como posso exibir as oportunidades potenciais?** 

Pode aceder a BLOBs ou tabelas a partir do Portal do Azure, ou pode transferir e instalar [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) gratuitamente para ver tabelas/blobs da sua conta de armazenamento do Azure. 


**Configurei tabelas do Azure como destino meu chefe, eu posso obter notificado sempre que uma nova oportunidade potencial é enviada pelo Marketplace?** 

Sim, siga as instruções para definir tabelas do Azure + a função de segurança sobre a documentação [aqui](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-lead-management-instructions-azure-table). 



**Configurei o Salesforce como destino meu chefe, por que não é possível localizar as oportunidades potenciais?** 

Verificar se a web para levar o formulário é um campo obrigatório, com base numa lista de opções. Se Sim, mude o campo para um campo de texto não obrigatórias.  
 

**Ocorreu um problema com meu destino de oportunidades potenciais, e eu me esquecesse de algumas oportunidades potenciais. Posso ter-lhes enviados-me por e-mail?** 

Devido a políticas PII (informações de identificação privada), nós não podem partilhar informações de oportunidades potenciais através do e-mail não segura. 



**Posso ter configurado o armazenamento do Azure (BLOBS/tabelas) como meu destino oportunidade potencial, quanto ele custará?** 

Fins levar dados são baixos (< 1 GB para quase todos os publicadores). O custo será dependem do número de oportunidades potenciais recebido, se 1.000 oportunidades são recebidas por mês, custa cerca de 50 centavos. 
