---
title: Introdução à autenticação na Automação Azure
description: Este artigo fornece uma descrição geral da segurança de Automatização e os métodos de autenticação diferentes disponíveis para as Contas de Automatização na Automatização do Azure.
keywords: segurança de automatização, automatização segura; autenticação de automatização
services: automation
ms.subservice: process-automation
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 109bb6dd29ea9c4239e0abcfc668f1185f7e9783
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114535"
---
# <a name="introduction-to-authentication-in-azure-automation"></a>Introdução à Autenticação na Automatização do Azure

A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços em nuvem, tais como os Amazon Web Services (AWS). Para um runbook efetuar as ações necessárias, tem de ter permissões para aceder de forma segura aos recursos com os direitos mínimos necessários dentro da subscrição.

Este artigo abrangerá os vários cenários de autenticação suportados pela Azure Automation e como começar com base no ambiente ou nos ambientes que precisa de gerir.  

## <a name="automation-account-overview"></a>Descrição Geral da Conta de Automatização

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automatização permitem-lhe a isolar os recursos de Automatização (runbooks, recursos, configurações) a partir dos recursos contidos noutras contas de Automatização. Pode utilizar contas de Automatização para separar recursos em ambientes lógicos separados. Por exemplo, poderá utilizar uma conta para o desenvolvimento, outra para a produção e outra para o seu ambiente no local. Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure.

Os recursos de Automatização de cada conta de Automatização estão associados a uma única região do Azure, mas as contas de Automatização podem gerir todos os recursos na sua subscrição. A razão principal para criar contas de Automatização em diferentes regiões seria se tiver políticas que exigem que os dados e os recursos estejam isolados numa região específica.

Todas as tarefas que executa relativamente aos recursos que utilizam o Azure Resource Manager e os cmdlets do Azure na Automatização do Azure têm de ser autenticados no Azure utilizando a autenticação com base em credenciais de identidade organizacional do Azure Active Directory. Executar Como contas na Azure Automation fornecem autenticação para gestão de recursos em Azure usando os cmdlets Azure. Ao criar uma conta Run As, cria um novo utilizador principal de serviço no Azure Ative Directory (AD) e atribui a função de Colaborador a este utilizador ao nível da subscrição. Para livros de corridas que utilizam trabalhadores híbridos em máquinas virtuais Azure, pode utilizar [identidades geridas para recursos Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) em vez de Run As contas para autenticar os seus recursos Azure.

O diretor de serviço de uma Conta Não tem permissões para ler Azure AD por padrão. Se quiser adicionar permissões para ler ou gerir o Azure AD, terá de conceder as permissões no diretor de serviço sob **permissões DaPI**. Para saber mais, consulte [Adicionar permissões para aceder a APIs web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

O controlo de acesso baseado em funções está disponível no Azure Resource Manager para conceder ações permitidas a uma conta de utilizador do Azure AD e uma conta Run As e autenticar esse principal de serviço. Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.  

Os livros de execução que funcionam num Trabalhador de Runbook Híbrido no seu centro de dados ou contra serviços de computação em outros ambientes em nuvem como o AWS, não podem usar o mesmo método que é normalmente usado para aautenticação de livros de execução aos recursos Do Azure. Isto acontece porque esses recursos estão a ser executados fora do Azure e, como tal, precisam das suas próprias credenciais de segurança definidas na Automatização para autenticar em recursos que acedem localmente. Para obter mais informações sobre a autenticação do livro de corridas com trabalhadores do livro de corridas, consulte os livros de [execução Authenticate para Trabalhadores híbridos](automation-hrw-run-runbooks.md)do livro de corridas. 

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta de Automação a partir do portal Azure.](automation-create-standalone-account.md)

* [Crie uma conta de Automação utilizando](automation-create-account-template.md)o modelo de Gestor de Recursos Azure .

* [Autenticar com a Amazon Web Services (AWS)](automation-config-aws-account.md).
