---
title: 'Azure Databricks: Questões comuns e ajuda'
description: Obtenha respostas a perguntas comuns e informações sobre os Databricks Azure.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671570"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Perguntas frequentes sobre os Tijolos de Dados Azure

Este artigo lista as principais questões que pode ter relacionadas com os Bricks Azure. Também enumera alguns problemas comuns que pode ter ao usar databricks. Para mais informações, consulte [o que é Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Posso usar o Cofre chave Azure para armazenar chaves/segredos para serem usados em Tijolos de Dados Azure?
Sim. Pode utilizar o Cofre de Chaves Azure para armazenar chaves/segredos para uso com tijolos de dados Azure. Para mais informações, consulte [os âmbitos apoiados pelo Cofre de Chaves do Azure](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Posso usar redes virtuais Azure com databricks?
Sim. Pode utilizar uma Rede Virtual Azure (VNET) com tijolos de dados Azure. Para mais informações, consulte a implementação de tijolos de [dados Azure na sua Rede Virtual Azure](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Como posso aceder ao Armazenamento do Lago De Dados Azure a partir de um caderno? 

Siga estes passos.
1. No Azure Ative Directory (Azure AD), prestaum diretor de serviço, e regista a sua chave.
1. Atribua as permissões necessárias ao diretor de serviço no Armazenamento de Data Lake.
1. Para aceder a um ficheiro no Data Lake Storage, utilize as credenciais principais de serviço no Notebook.

Para mais informações, consulte Utilize o Armazenamento do [Lago De Dados Azure com tijolos](/azure/databricks/data/data-sources/azure/azure-datalake)de dados Azure .

## <a name="fix-common-problems"></a>Corrigir problemas comuns

Aqui estão alguns problemas que pode encontrar com databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: Esta subscrição não está registada para utilizar o espaço de nome 'Microsoft.Databricks'

#### <a name="error-message"></a>Mensagem de erro

"Esta subscrição não está registada para usar o espaço de nome 'Microsoft.Databricks'. Veja https://aka.ms/rps-not-found como registar subscrições. (Código: Falta de registo de subscrições)"

#### <a name="solution"></a>Solução

1. Vá ao [portal Azure.](https://portal.azure.com)
1. Selecione **Subscrições,** a subscrição que está a utilizar e, em seguida, **os fornecedores**de Recursos. 
1. Na lista de fornecedores de recursos, contra **microsoft.Databricks,** **selecione Register**. Deve ter a função de contribuinte ou proprietário na subscrição para registar o fornecedor de recursos.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: A sua conta {email} não tem o papel do proprietário ou colaborador no recurso espaço de trabalho Databricks no portal Azure

#### <a name="error-message"></a>Mensagem de erro

"A sua conta {email} não tem função de Proprietário ou Colaborador no recurso espaço de trabalho databricks no portal Azure. Este erro também pode ocorrer se for um utilizador convidado no inquilino. Peça ao seu administrador que lhe conceda acesso ou adicione-o diretamente no espaço de trabalho dos Databricks." 

#### <a name="solution"></a>Solução

Seguem-se algumas soluções para esta questão:

* Para inicializar o inquilino, deve ser inscrito como utilizador regular do inquilino, e não como utilizador convidado. Deve também ter um papel contributivo no recurso espaço de trabalho databricks. Pode conceder a um utilizador acesso a partir do separador Controlo de **Acesso (IAM)** dentro do seu espaço de trabalho Databricks no portal Azure.

* Este erro também pode ocorrer se o seu nome de domínio de e-mail for atribuído a vários diretórios em Azure AD. Para resolver este problema, crie um novo utilizador no diretório que contenha a subscrição com o seu espaço de trabalho Databricks.

    a. No portal Azure, vá ao Azure AD. Selecione **Utilizadores e Grupos** > **Adicionar um utilizador**.

    b. Adicione um utilizador `@<tenant_name>.onmicrosoft.com` com `@<your_domain>` um e-mail em vez de e-mail. Pode encontrar esta opção em **Domínios Personalizados,** em Azure AD no portal Azure.
    
    c. Conceda a este novo utilizador o papel de **Colaborador** no recurso espaço de trabalho Databricks.
    
    d. Inscreva-se no portal Azure com o novo utilizador e encontre o espaço de trabalho databricks.
    
    e. Lance o espaço de trabalho databricks como este utilizador.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: A sua conta {email} não foi registada em Databricks 

#### <a name="solution"></a>Solução

Se não criou o espaço de trabalho, e é adicionado como utilizador, contacte a pessoa que criou o espaço de trabalho. Faça com que essa pessoa o adicione utilizando a Consola de Administração de Tijolos De Dados Azure. Para obter instruções, consulte [Adicionar e gerir os utilizadores](/azure/databricks/administration-guide/users-groups/users). Se criou o espaço de trabalho e ainda obtém este erro, tente selecionar o Espaço de **Trabalho Inicialize** novamente a partir do portal Azure.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problema: Falha de lançamento do fornecedor de nuvem ao configurar o cluster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Mensagem de erro

"Falha de lançamento do fornecedor de nuvem: Foi encontrado um erro do fornecedor de nuvem durante a instalação do cluster. Para mais informações, consulte o guia Databricks. Código de erro azure: PublicIPCountLimitReached. Mensagem de erro do Azure: Não pode criar mais de 10 endereços IP públicos para esta subscrição nesta região."

#### <a name="background"></a>Segundo plano

Os clusters de tijolos de dados utilizam um endereço IP público por nó (incluindo o nó do condutor). As assinaturas Azure têm [limites de endereços IP públicos](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) por região. Assim, a criação de clusters e as operações de escala podem falhar se fizerem com que o número de endereços IP públicos atribuídos a essa subscrição nessa região exceda o limite. Este limite também inclui endereços IP públicos atribuídos para uso não databricks, tais como VMs definidos pelo utilizador personalizados.

Em geral, os clusters consomem apenas endereços IP públicos enquanto estão ativos. No `PublicIPCountLimitReached` entanto, os erros podem continuar a ocorrer por um curto período de tempo, mesmo após o fim de outros clusters. Isto porque os Databricks caches temporariamente os recursos Azure quando um cluster é terminado. O caching de recursos é por design, uma vez que reduz significativamente a latência do arranque do cluster e autoscalcificação em muitos cenários comuns.

#### <a name="solution"></a>Solução

Se a sua subscrição já atingiu o seu limite de endereço IP público para uma determinada região, então deve fazer um ou outro dos seguintes.

- Crie novos clusters num espaço de trabalho diferente dos Databricks. O outro espaço de trabalho deve estar localizado numa região em que não tenha atingido o limite de endereço IP público da sua subscrição.
- [Pedido para aumentar o seu limite de endereço IP público](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Escolha **quota** como tipo de **emissão**e **Networking: ARM** como o Tipo de **Quota**. Em **Detalhes,** solicite um aumento da quota de endereço IP público. Por exemplo, se o seu limite for atualmente de 60, e quiser criar um cluster de 100 nós, solicite um aumento limite para 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problema: Um segundo tipo de falha de lançamento do fornecedor de nuvem ao configurar o cluster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Mensagem de erro

"Falha de lançamento do fornecedor de nuvem: Foi encontrado um erro do fornecedor de nuvem durante a instalação do cluster. Para mais informações, consulte o guia Databricks.
Código de erro Azure: Mensagem de erro Do Registo de Registo sem querer: A subscrição não está registada para utilizar o espaço de nome 'Microsoft.Compute'. Veja https://aka.ms/rps-not-found como registar assinaturas."

#### <a name="solution"></a>Solução

1. Vá ao [portal Azure.](https://portal.azure.com)
1. Selecione **Subscrições,** a subscrição que está a utilizar e, em seguida, **os fornecedores**de Recursos. 
1. Na lista de fornecedores de recursos, contra **microsoft.Compute,** **selecione Register**. Deve ter a função de contribuinte ou proprietário na subscrição para registar o fornecedor de recursos.

Para obter instruções mais detalhadas, consulte [os fornecedores e tipos](../azure-resource-manager/management/resource-providers-and-types.md)de recursos.

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Emissão: A Azure Databricks necessita de permissões para aceder a recursos na sua organização que só um administrador pode conceder.

#### <a name="background"></a>Segundo plano

A Azure Databricks está integrada com o Azure Ative Directory. Pode definir permissões dentro de Bricks Azure (por exemplo, em cadernos ou clusters) especificando os utilizadores da Azure AD. Para que os Tijolos de Dados Da Azure possam listar os nomes dos utilizadores do seu Anúncio Azure, requer que seja dada permissão para essa informação e consentimento. Se o consentimento ainda não estiver disponível, verá o erro.

#### <a name="solution"></a>Solução

Inicie sessão como administrador global do portal Azure. Para o Diretório Ativo do Azure, aceda ao separador **Definições** do Utilizador e certifique-se de que **os Utilizadores podem consentir que o acesso a aplicações que acedam aos dados** da empresa em seu nome está definido para **Sim**.

## <a name="next-steps"></a>Passos seguintes

- [Quickstart: Comece com os Tijolos de Dados Azure](quickstart-create-databricks-workspace-portal.md)
- [O que é o Azure Databricks?](what-is-azure-databricks.md)
