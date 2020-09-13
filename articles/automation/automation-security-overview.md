---
title: Visão geral da autenticação da conta Azure Automation
description: Este artigo fornece uma visão geral da autenticação da conta Azure Automation.
keywords: segurança de automatização, automatização segura; autenticação de automatização
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 8068d6ebe67dee1408420441aacd83726a1986df
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2020
ms.locfileid: "89434270"
---
# <a name="automation-account-authentication-overview"></a>Descrição geral da autenticação da conta de Automatização

A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços em nuvem, tais como os Amazon Web Services (AWS). Pode utilizar livros para automatizar as suas tarefas, ou um Trabalhador de Runbook Híbrido se tiver tarefas não-Azure para gerir. Qualquer um dos ambientes requer permissões para aceder de forma segura aos recursos com os direitos mínimos exigidos dentro da subscrição do Azure.

Este artigo abrange cenários de autenticação suportados pela Azure Automation e diz como começar com base no ambiente ou ambientes que precisa de gerir.

## <a name="automation-account"></a>Conta de automatização

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automação permitem isolar os seus recursos de Automação, runbooks, ativos, configurações, a partir dos recursos de outras contas. Pode utilizar contas de Automatização para separar recursos em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta para o desenvolvimento, outra para a produção e outra para o seu ambiente no local. Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure. Para uma introdução à criação de uma conta Demôm automação, consulte [Criar uma conta Demôm automação.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Recursos de automatização

Os recursos de Automação para cada conta Automation estão associados a uma única região do Azure, mas a conta pode gerir todos os recursos na sua subscrição do Azure. A principal razão para criar contas de Automação em diferentes regiões é se tiver políticas que exijam que os dados e recursos sejam isolados para uma região específica.

Todas as tarefas que cria contra recursos utilizando o Azure Resource Manager e os cmdlets PowerShell na Azure Automation devem autenticar para a Azure utilizando a autenticação baseada na credencial de identidade organizacional Azure Ative..

## <a name="run-as-account"></a>Conta Run As

Executar Como as contas na Azure Automation fornecem autenticação para a gestão dos recursos da Azure utilizando cmdlets PowerShell. Quando cria uma conta Run As, cria um novo utilizador principal de serviço em AZure AD e atribui a função Contribuinte a este utilizador ao nível da subscrição. Para os runbooks que utilizam trabalhadores de runbook híbridos em VMs Azure, pode utilizar [a autenticação de runbook com identidades geridas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez de executar como contas para autenticar os seus recursos Azure.

## <a name="service-principal-for-run-as-account"></a>Diretor de serviço para executar como conta

O titular do serviço para uma conta Run Como não tem permissões para ler Azure AD por padrão. Se pretender adicionar permissões para ler ou gerir a Azure AD, deve conceder as permissões ao principal do serviço sob **permissões da API**. Para saber mais, consulte [Adicionar permissões para aceder à sua API web.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O controlo de acesso baseado em funções está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de utilizador AZure E Executar Como, e autenticar o principal do serviço. Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticação de runbook com trabalhador de runbook híbrido

Os runbooks que funcionam num Trabalhador De Runbook Híbrido no seu datacenter ou contra serviços de computação em outros ambientes em nuvem como o AWS, não podem usar o mesmo método que é normalmente usado para livros de execução autenticando recursos Azure. Isto acontece porque esses recursos estão a ser executados fora do Azure e, como tal, precisam das suas próprias credenciais de segurança definidas na Automatização para autenticar em recursos que acedem localmente. Para obter mais informações sobre a autenticação de runbook com trabalhadores de runbook, consulte [runbooks runbooks em um Trabalhador de Runbook Híbrido.](automation-hrw-run-runbooks.md)

## <a name="next-steps"></a>Próximos passos

* Para criar uma conta Automation a partir do portal Azure, consulte [Criar uma conta Azure Automation autónoma.](automation-create-standalone-account.md)
* Se preferir criar a sua conta utilizando um modelo, consulte [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure.](quickstart-create-automation-account-template.md)
* Para autenticação utilizando os Serviços Web da Amazon, consulte [os livros de autenticação com os Serviços Web da Amazon.](automation-config-aws-account.md)