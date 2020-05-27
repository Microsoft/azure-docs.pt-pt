---
title: Visão geral da autenticação da conta Azure Automation
description: Este artigo fornece uma visão geral da autenticação da conta Azure Automation.
keywords: segurança de automatização, automatização segura; autenticação de automatização
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2b9f705c73e667f34e46fdeed3c80af1e65fb12
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830111"
---
# <a name="automation-account-authentication-overview"></a>Descrição geral da autenticação da conta de Automatização

A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços em nuvem, tais como os Amazon Web Services (AWS). Pode utilizar livros de execução para automatizar as suas tarefas, ou um Trabalhador de Livro híbrido se tiver tarefas não Azure para gerir. Qualquer um dos ambientes requer permissões para aceder de forma segura aos recursos com os direitos mínimos exigidos dentro da subscrição do Azure.

Este artigo abrange cenários de autenticação suportados pela Azure Automation e diz como começar com base no ambiente ou ambientes que precisa de gerir.

## <a name="automation-account"></a>Conta de automatização 

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automação permitem isolar os seus recursos de Automação, livros de execução, ativos, configurações, a partir dos recursos de outras contas. Pode utilizar contas de Automatização para separar recursos em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta para o desenvolvimento, outra para a produção e outra para o seu ambiente no local. Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure. Para uma introdução à criação de uma conta De automação, consulte [Criar uma conta De Automação.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Recursos de automatização

Os recursos de Automação para cada conta Automation estão associados a uma única região azure, mas a conta pode gerir todos os recursos na sua subscrição Azure. A principal razão para criar contas de Automação em diferentes regiões é se você tem políticas que requerem dados e recursos para ser isolado para uma região específica.

Todas as tarefas que criar contra recursos utilizando o Azure Resource Manager e os cmdlets PowerShell na Azure Automation devem autenticar o Azure utilizando a autenticação de identidade baseada na identidade do Azure Ative Directory (Azure AD). 

## <a name="run-as-account"></a>Conta Run As

Executar Como contas na Azure Automation fornecem autenticação para gestão de recursos Azure usando cmdlets PowerShell. Ao criar uma conta Run As, cria um novo utilizador principal de serviço no Azure AD e atribui a função de Colaborador a este utilizador ao nível da subscrição. Para livros de corridas que utilizam trabalhadores de runbook híbridos em VMs Azure, pode utilizar a autenticação do [livro de corridas com identidades geridas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez de Executar Como contas para autenticar os seus recursos Azure.

## <a name="service-principal-for-run-as-account"></a>Diretor de serviço para Executar Como conta

O diretor de serviço de uma conta Run As conta não tem permissões para ler Azure AD por padrão. Se pretender adicionar permissões para ler ou gerir o Azure AD, deve conceder as permissões no diretor de serviço sob **permissões DaPI**. Para saber mais, consulte [Adicionar permissões para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O controlo de acesso baseado em funções está disponível com o Gestor de Recursos Azure para conceder ações permitidas a uma conta de utilizador da AD Azure e executar Como conta, e autenticar o diretor de serviço. Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.  

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticação do livro de corridas com trabalhador híbrido do livro de corridas 

Os livros de execução que funcionam num Trabalhador de Runbook Híbrido no seu datacenter ou contra serviços de computação em outros ambientes em nuvem como o AWS, não podem usar o mesmo método que é normalmente usado para aautenticação de livros de execução aos recursos Do Azure. Isto acontece porque esses recursos estão a ser executados fora do Azure e, como tal, precisam das suas próprias credenciais de segurança definidas na Automatização para autenticar em recursos que acedem localmente. Para obter mais informações sobre a autenticação do livro de corridas com trabalhadores do livro de corridas, consulte [run run run book on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md). 

## <a name="next-steps"></a>Passos seguintes

* Para criar uma conta De automação a partir do portal Azure, consulte Criar uma conta autónoma da [Automação Azure](automation-create-standalone-account.md).
* Se preferir criar a sua conta usando um modelo, consulte [Criar uma conta de Automação utilizando um modelo](automation-create-account-template.md)de Gestor de Recursos Azure .
* Para autenticação utilizando os Serviços Web da Amazon, consulte os livros de [execução Authenticate com os Serviços Web da Amazon](automation-config-aws-account.md).