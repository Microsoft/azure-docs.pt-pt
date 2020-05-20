---
title: Visão geral da autenticação da conta Azure Automation
description: Este artigo fornece uma visão geral dos métodos de segurança e autenticação da Azure Automation para as contas da Automação.
keywords: segurança de automatização, automatização segura; autenticação de automatização
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: c9e3c2b15b45eb15b40782a5d0ecfe1e736fc013
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683514"
---
# <a name="azure-automation-account-authentication-overview"></a>Visão geral da autenticação da conta Azure Automation

A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços em nuvem, tais como os Amazon Web Services (AWS). Para que um livro de execução realize as suas ações necessárias, deve ter permissões para aceder de forma segura aos recursos com os direitos mínimos exigidos dentro da subscrição.

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automatização permitem-lhe a isolar os recursos de Automatização (runbooks, recursos, configurações) a partir dos recursos contidos noutras contas de Automatização. Pode utilizar contas de Automatização para separar recursos em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta para o desenvolvimento, outra para a produção e outra para o seu ambiente no local. Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure.

Os recursos de Automatização de cada conta de Automatização estão associados a uma única região do Azure, mas as contas de Automatização podem gerir todos os recursos na sua subscrição. A razão principal para criar contas de Automatização em diferentes regiões seria se tiver políticas que exigem que os dados e os recursos estejam isolados numa região específica.

Todas as tarefas que executa contra recursos utilizando o Azure Resource Manager e os cmdlets Azure na Azure Automation devem autenticar o Azure utilizando a autenticação de identidade baseada na identidade do Azure Ative (Azure AD). Executar Como contas na Azure Automation fornecem autenticação para gestão de recursos em Azure usando os cmdlets Azure. Ao criar uma conta Run As, cria um novo utilizador principal de serviço no Azure AD e atribui a função de Colaborador a este utilizador ao nível da subscrição. Para livros de corridas que utilizam trabalhadores híbridos em máquinas virtuais Azure, pode utilizar a autenticação do [livro de corridas com identidades geridas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez de Executar Como contas para autenticar os seus recursos Azure.

O diretor de serviço de uma Conta Não tem permissões para ler Azure AD por padrão. Se quiser adicionar permissões para ler ou gerir o Azure AD, terá de conceder as permissões no diretor de serviço sob **permissões DaPI**. Para saber mais, consulte [Adicionar permissões para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

O controlo de acesso baseado em funções está disponível no Azure Resource Manager para conceder ações permitidas a uma conta de utilizador do Azure AD e uma conta Run As e autenticar esse principal de serviço. Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.  

Os livros de execução que funcionam num Trabalhador de Runbook Híbrido no seu centro de dados ou contra serviços de computação em outros ambientes em nuvem como o AWS, não podem usar o mesmo método que é normalmente usado para aautenticação de livros de execução aos recursos Do Azure. Isto acontece porque esses recursos estão a ser executados fora do Azure e, como tal, precisam das suas próprias credenciais de segurança definidas na Automatização para autenticar em recursos que acedem localmente. Para obter mais informações sobre a autenticação do livro de corridas com trabalhadores do livro de corridas, consulte os livros de [execução Authenticate para Trabalhadores híbridos](automation-hrw-run-runbooks.md)do livro de corridas. 

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de Automação a partir do portal Azure.](automation-create-standalone-account.md)
* [Crie uma conta de Automação utilizando](automation-create-account-template.md)o modelo de Gestor de Recursos Azure .
* [Autenticar com a Amazon Web Services (AWS)](automation-config-aws-account.md).
