---
title: 'Azure Databricks: perguntas e ajuda comuns'
description: Obtenha respostas para perguntas comuns e informações de solução de problemas sobre Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8707aa55c1126af6a7fb2812d4c7314f031209d0
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597482"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Perguntas mais frequentes sobre o Azure Databricks

Este artigo lista as principais perguntas que você pode ter relacionadas a Azure Databricks. Ele também lista alguns problemas comuns que você pode ter ao usar o databricks. Para obter mais informações, consulte [o que é Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Posso usar Azure Key Vault para armazenar chaves/segredos a serem usados no Azure Databricks?
Sim. Você pode usar Azure Key Vault para armazenar chaves/segredos para uso com Azure Databricks. Para obter mais informações, consulte [escopos com suporte Azure Key Vault](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#akv-ss).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Posso usar redes virtuais do Azure com o databricks?
Sim. Você pode usar uma rede virtual do Azure (VNET) com Azure Databricks. Para obter mais informações, consulte [Implantando Azure Databricks em sua rede virtual do Azure](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Como fazer Azure Data Lake Storage de acesso de um bloco de anotações? 

Siga estes passos.
1. No Azure Active Directory (AD do Azure), Provisione uma entidade de serviço e registre sua chave.
1. Atribua as permissões necessárias à entidade de serviço no Data Lake Storage.
1. Para acessar um arquivo no Data Lake Storage, use as credenciais da entidade de serviço no bloco de anotações.

Para obter mais informações, consulte [usar Azure data Lake Storage com Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html).

## <a name="fix-common-problems"></a>Corrigir problemas comuns

Aqui estão alguns problemas que você pode encontrar com o databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: esta assinatura não está registrada para usar o namespace ' Microsoft. databricks '

#### <a name="error-message"></a>Mensagem de erro

"Esta assinatura não está registrada para usar o namespace ' Microsoft. databricks '. Consulte https://aka.ms/rps-not-found para saber como registrar assinaturas. (Código: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Solução

1. Aceda ao [Portal do Azure](https://portal.azure.com).
1. Selecione **assinaturas**, a assinatura que você está usando e, em seguida, **provedores de recursos**. 
1. Na lista de provedores de recursos, em relação a **Microsoft. databricks**, selecione **registrar**. Você deve ter a função colaborador ou proprietário na assinatura para registrar o provedor de recursos.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: sua conta {email} não tem a função proprietário ou colaborador no recurso de espaço de trabalho do databricks no portal do Azure

#### <a name="error-message"></a>Mensagem de erro

"Sua conta {email} não tem a função proprietário ou colaborador no recurso de espaço de trabalho do databricks no portal do Azure. Esse erro também pode ocorrer se você for um usuário convidado no locatário. Peça ao administrador para conceder acesso ou adicioná-lo como um usuário diretamente no espaço de trabalho do databricks. " 

#### <a name="solution"></a>Solução

A seguir estão algumas soluções para esse problema:

* Para inicializar o locatário, você deve estar conectado como um usuário normal do locatário, não como um usuário convidado. Você também deve ter uma função de colaborador no recurso de espaço de trabalho do databricks. Você pode conceder a um usuário acesso a partir da guia **controle de acesso (iam)** no seu espaço de trabalho do databricks no portal do Azure.

* Esse erro também pode ocorrer se o seu nome de domínio de email for atribuído a vários diretórios no Azure AD. Para contornar esse problema, crie um novo usuário no diretório que contém a assinatura com seu espaço de trabalho do databricks.

    a. Na portal do Azure, vá para Azure AD. Selecione **usuários e grupos**  > **Adicionar um usuário**.

    b. Adicione um usuário com um email `@<tenant_name>.onmicrosoft.com` em vez de `@<your_domain>` email. Você pode encontrar essa opção em **domínios personalizados**, no Azure AD na portal do Azure.
    
    c. Conceda a esse novo usuário a função de **colaborador** no recurso de espaço de trabalho do databricks.
    
    d. Entre no portal do Azure com o novo usuário e localize o espaço de trabalho do databricks.
    
    e. Inicie o espaço de trabalho do databricks como este usuário.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: sua conta {email} não foi registrada no databricks 

#### <a name="solution"></a>Solução

Se você não criou o espaço de trabalho e foi adicionado como um usuário, entre em contato com a pessoa que criou o espaço de trabalho. Peça a ela para adicioná-lo usando o console do administrador do Azure Databricks. Para obter instruções, consulte [adicionando e gerenciando usuários](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Se você criou o espaço de trabalho e ainda recebe esse erro, tente selecionar **inicializar espaço de trabalho** novamente no portal do Azure.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problema: falha ao iniciar o provedor de nuvem durante a configuração do cluster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Mensagem de erro

"Falha ao iniciar o provedor de nuvem: foi encontrado um erro no provedor de nuvem durante a configuração do cluster. Para obter mais informações, consulte o guia do databricks. Código de erro do Azure: PublicIPCountLimitReached. Mensagem de erro do Azure: não é possível criar mais de 60 endereços IP públicos para esta assinatura nesta região. "

#### <a name="solution"></a>Solução

Os clusters do Databricks utilizam um endereço IP público por nó. Se sua assinatura já tiver usado todos os seus IPs públicos, você deverá [solicitar o aumento da cota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Escolha **cota** como o **tipo de problema**e **rede: ARM** como o **tipo de cota**. Em **detalhes**, solicite um aumento de cota de endereço IP público. Por exemplo, se o limite for atualmente 60 e você quiser criar um cluster de 100 nós, solicite um aumento de limite para 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problema: um segundo tipo de falha de inicialização do provedor de nuvem durante a configuração do cluster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Mensagem de erro

"Falha ao iniciar o provedor de nuvem: foi encontrado um erro no provedor de nuvem durante a configuração do cluster. Para obter mais informações, consulte o guia do databricks.
Código de erro do Azure: mensagem de erro do MissingSubscriptionRegistration Azure: a assinatura não está registrada para usar o namespace ' Microsoft. Compute '. Consulte https://aka.ms/rps-not-found para saber como registrar assinaturas ".

#### <a name="solution"></a>Solução

1. Aceda ao [Portal do Azure](https://portal.azure.com).
1. Selecione **assinaturas**, a assinatura que você está usando e, em seguida, **provedores de recursos**. 
1. Na lista de provedores de recursos, em relação a **Microsoft. Compute**, selecione **registrar**. Você deve ter a função colaborador ou proprietário na assinatura para registrar o provedor de recursos.

Para obter instruções mais detalhadas, consulte [provedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problema: Azure Databricks precisa de permissões para acessar recursos em sua organização que apenas um administrador pode conceder.

#### <a name="background"></a>Segundo plano

O Azure Databricks é integrado ao Azure Active Directory. Você pode definir permissões dentro de Azure Databricks (por exemplo, em blocos de anotações ou clusters) especificando usuários do Azure AD. Para que Azure Databricks seja capaz de listar os nomes dos usuários do Azure AD, é necessário ter permissão de leitura para que as informações e o consentimento sejam fornecidos. Se o consentimento ainda não estiver disponível, você verá o erro.

#### <a name="solution"></a>Solução

Faça logon como um administrador global para a portal do Azure. Para Azure Active Directory, vá para a guia **configurações de usuário** e verifique se **os usuários podem dar consentimento a aplicativos que acessam dados da empresa em seu nome** está definido como **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Início rápido: introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [O que é Azure Databricks?](what-is-azure-databricks.md)

