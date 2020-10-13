---
title: Visão geral da autenticação da conta Azure Automation
description: Este artigo fornece uma visão geral da autenticação da conta Azure Automation.
keywords: segurança de automatização, automatização segura; autenticação de automatização
services: automation
ms.subservice: process-automation
ms.date: 09/28/2020
ms.topic: conceptual
ms.openlocfilehash: bcb5f61c93bd4c3ff7c0f81ae808807f7deb71df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91766091"
---
# <a name="automation-account-authentication-overview"></a>Descrição geral da autenticação da conta de Automatização

A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços em nuvem, tais como os Amazon Web Services (AWS). Pode utilizar livros para automatizar as suas tarefas, ou um Trabalhador de Runbook Híbrido se tiver processos empresariais ou operacionais para gerir fora de Azure. Trabalhar em qualquer um destes ambientes requer permissões para aceder de forma segura aos recursos com os direitos mínimos exigidos.

Este artigo abrange cenários de autenticação suportados pela Azure Automation e diz como começar com base no ambiente ou ambientes que precisa de gerir.

## <a name="automation-account"></a>Conta de automatização

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automação permitem isolar os seus recursos de Automação, runbooks, ativos, configurações, a partir dos recursos de outras contas. Pode utilizar contas de Automatização para separar recursos em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta para o desenvolvimento, outra para a produção e outra para o seu ambiente no local. Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure. Para uma introdução à criação de uma conta Demôm automação, consulte [Criar uma conta Demôm automação.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Recursos de automatização

Os recursos de Automação para cada conta Automation estão associados a uma única região do Azure, mas a conta pode gerir todos os recursos na sua subscrição do Azure. A principal razão para criar contas de Automação em diferentes regiões é se tiver políticas que exijam que os dados e recursos sejam isolados para uma região específica.

Todas as tarefas que cria contra recursos utilizando o Azure Resource Manager e os cmdlets PowerShell na Azure Automation devem autenticar para a Azure utilizando a autenticação baseada na credencial de identidade organizacional Azure Ative..

## <a name="run-as-accounts"></a>Contas Run As

Executar Como as contas na Azure Automation fornecem autenticação para gerir recursos ou recursos do Azure Resource Manager ou recursos implantados no modelo de implementação clássico. Existem dois tipos de contas Run As na Azure Automation:

* Conta Run As do Azure
* Azure Classic Run Como conta

Para saber mais sobre estes dois modelos de implementação, consulte [o Gestor de Recursos e a implementação clássica.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>As assinaturas Azure Cloud Solution Provider (CSP) suportam apenas o modelo Azure Resource Manager. Os serviços não-Azure Resource Manager não estão disponíveis no programa. Quando está a utilizar uma subscrição CSP, a conta Azure Classic Run As não é criada, mas a conta Azure Run As é criada. Para saber mais sobre as subscrições da CSP, consulte [os serviços disponíveis em subscrições CSP.](/azure/cloud-solution-provider/overview/azure-csp-available-services)

### <a name="run-as-account"></a>Conta Run As

A conta Azure Run As gere os recursos da Azure com base no serviço de implementação e gestão do Azure Resource Manager para a Azure.

Quando cria uma conta Run As, executa as seguintes tarefas:

* Cria um pedido AD Azure com um certificado auto-assinado, cria uma conta principal de serviço para a aplicação em Azure AD, e atribui o papel [de Contribuinte](../role-based-access-control/built-in-roles.md#contributor) para a conta na sua subscrição atual. Pode alterar a definição de certificado para Proprietário ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).

* Cria um certificado de automação nomeado `AzureRunAsCertificate` na conta de Automação especificada. O certificado detém a chave privada de certificado que a aplicação Azure AD utiliza.

* Cria um ativo de ligação Automation named `AzureRunAsConnection` in the specific Automation account. O ativo de ligação detém o ID da aplicação, iD do inquilino, ID de assinatura e impressão digital de certificado.

### <a name="azure-classic-run-as-account"></a>Conta Run As Clássica do Azure

A conta Azure Classic Run Como gere os recursos clássicos da Azure com base no modelo de implementação Clássico. Deve ser coadministrador na subscrição para criar ou renovar este tipo de conta Run As.

Quando cria uma conta Azure Classic Run Como, executa as seguintes tarefas.

* Cria um certificado de gestão na subscrição.

* Cria um certificado de automação nomeado `AzureClassicRunAsCertificate` na conta de Automação especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.

* Cria um ativo de ligação Automation named `AzureClassicRunAsConnection` in the specific Automation account. O ativo de ligação detém o nome de subscrição, iD de subscrição e nome do ativo do certificado.

>[!NOTE]
>A azure Classic Run Como a conta não é criada por padrão ao mesmo tempo que cria uma conta Automation. Esta conta é criada individualmente seguindo os passos descritos no artigo de [conta Manage As.](manage-runas-account.md#create-a-run-as-account-in-azure-portal)

## <a name="service-principal-for-run-as-account"></a>Diretor de serviço para executar como conta

O titular do serviço para uma conta Run Como não tem permissões para ler Azure AD por padrão. Se pretender adicionar permissões para ler ou gerir a Azure AD, deve conceder as permissões ao principal do serviço sob **permissões da API**. Para saber mais, consulte [Adicionar permissões para aceder à sua API web.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O controlo de acesso baseado em funções está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de utilizador AZure E Executar Como, e autenticar o principal do serviço. Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticação de runbook com trabalhador de runbook híbrido

Os runbooks que funcionam num Trabalhador De Runbook Híbrido no seu datacenter ou contra serviços de computação em outros ambientes em nuvem como o AWS, não podem usar o mesmo método que é normalmente usado para livros de execução autenticando recursos Azure. Isto acontece porque esses recursos estão a ser executados fora do Azure e, como tal, precisam das suas próprias credenciais de segurança definidas na Automatização para autenticar em recursos que acedem localmente. Para obter mais informações sobre a autenticação de runbook com trabalhadores de runbook, consulte [runbooks runbooks em um Trabalhador de Runbook Híbrido.](automation-hrw-run-runbooks.md)

Para os runbooks que utilizam trabalhadores de runbook híbridos em VMs Azure, pode utilizar [a autenticação de runbook com identidades geridas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez de executar como contas para autenticar os seus recursos Azure.

## <a name="next-steps"></a>Passos seguintes

* Para criar uma conta Automation a partir do portal Azure, consulte [Criar uma conta Azure Automation autónoma.](automation-create-standalone-account.md)
* Se preferir criar a sua conta utilizando um modelo, consulte [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure.](quickstart-create-automation-account-template.md)
* Para autenticação utilizando os Serviços Web da Amazon, consulte [os livros de autenticação com os Serviços Web da Amazon.](automation-config-aws-account.md)