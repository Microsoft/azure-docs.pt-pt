---
title: Visão geral da autenticação da conta Azure Automation
description: Este artigo fornece uma visão geral da autenticação da conta Azure Automation.
keywords: segurança de automatização, automatização segura; autenticação de automatização
services: automation
ms.subservice: process-automation
ms.date: 04/08/2021
ms.topic: conceptual
ms.openlocfilehash: b52fa3083dc5c42fa71e720e9a3991cb7aa5afec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501574"
---
# <a name="azure-automation-account-authentication-overview"></a>Visão geral da autenticação da conta Azure Automation

A Automatização do Azure permite-lhe automatizar tarefas relativamente aos recursos no Azure, no local e outros fornecedores de serviços em nuvem, tais como os Amazon Web Services (AWS). Pode utilizar livros para automatizar as suas tarefas, ou um Trabalhador de Runbook Híbrido se tiver processos empresariais ou operacionais para gerir fora de Azure. Trabalhar em qualquer um destes ambientes requer permissões para aceder de forma segura aos recursos com os direitos mínimos exigidos.

Este artigo abrange cenários de autenticação suportados pela Azure Automation e diz como começar com base no ambiente ou ambientes que precisa de gerir.

## <a name="automation-account"></a>Conta de automatização

Quando inicia a Automatização do Azure pela primeira vez, tem de criar pelo menos uma conta de Automatização. As contas de automação permitem isolar os seus recursos de Automação, runbooks, ativos e configurações dos recursos de outras contas. Pode utilizar as contas de Automação para separar recursos em ambientes lógicos separados ou responsabilidades delegadas. Por exemplo, poderá utilizar uma conta para o desenvolvimento, outra para a produção e outra para o seu ambiente no local. Ou pode dedicar uma conta de Automação para gerir atualizações do sistema operativo em todas as suas máquinas com [a Update Management](update-management/overview.md). 

Uma conta de automatização do Azure é diferente da sua conta Microsoft ou contas criadas na sua subscrição do Azure. Para uma introdução à criação de uma conta Demôm automação, consulte [Criar uma conta Demôm automação.](automation-quickstart-create-account.md)

## <a name="automation-resources"></a>Recursos de automatização

Os recursos de Automação para cada conta Automation estão associados a uma única região do Azure, mas a conta pode gerir todos os recursos na sua subscrição do Azure. A principal razão para criar contas de Automação em diferentes regiões é se tiver políticas que exijam que os dados e recursos sejam isolados para uma região específica.

Todas as tarefas que cria contra recursos utilizando o Azure Resource Manager e os cmdlets PowerShell na Azure Automation devem autenticar para a Azure utilizando a autenticação baseada na credencial de identidade organizacional Azure Ative..

## <a name="managed-identities-preview"></a>Identidades geridas (Pré-visualização)

Uma identidade gerida a partir do Azure Ative Directory (Azure AD) permite que o seu runbook aceda facilmente a outros recursos protegidos por Azure AD. A identidade é gerida pela plataforma Azure e não requer que forneça ou rode quaisquer segredos. Para obter mais informações sobre identidades geridas em Azure AD, consulte [identidades geridas para recursos Azure](/azure/active-directory/managed-identities-azure-resources/overview).

Eis alguns dos benefícios da utilização de identidades geridas:

- Pode utilizar identidades geridas para autenticar qualquer serviço Azure que suporte a autenticação AD do Azure.

- As identidades geridas podem ser utilizadas sem qualquer custo adicional.

- Não é preciso renovar o certificado utilizado pela conta Automation Run As.

- Não tem de especificar o objeto de ligação Run As no seu código de execução. Pode aceder a recursos utilizando a identidade gerida da sua conta Automation a partir de um livro de contas sem criar certificados, conexões, executar como contas, etc.

Uma conta de automação pode ser concedida dois tipos de identidades:

- Uma identidade atribuída ao sistema está ligada à sua aplicação e é eliminada se a sua aplicação for eliminada. Uma aplicação só pode ter uma identidade atribuída ao sistema.

- Uma identidade atribuída ao utilizador é um recurso autónomo da Azure que pode ser atribuído à sua aplicação. Uma aplicação pode ter várias identidades atribuídas ao utilizador.

>[!NOTE]
> As identidades atribuídas aos utilizadores ainda não são suportadas.

Para obter mais informações sobre a utilização de identidades geridas, consulte [Ativar a identidade gerida para a Azure Automation (Preview)](enable-managed-identity-for-automation.md).

## <a name="run-as-accounts"></a>Contas Run As

Executar Como as contas na Azure Automation fornecem autenticação para gerir recursos ou recursos do Azure Resource Manager ou recursos implantados no modelo de implementação clássico. Existem dois tipos de contas Run As na Azure Automation:

* Azure Run As account: Permite-lhe gerir os recursos da Azure com base no serviço de implementação e gestão do Azure Resource Manager para a Azure.
* Azure Classic Run Como conta: Permite-lhe gerir recursos clássicos do Azure com base no modelo de implementação Clássico.

Para saber mais sobre o Gestor de Recursos Azure e os modelos de implementação clássicos, consulte [o Gestor de Recursos e a implementação clássica.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>As assinaturas Azure Cloud Solution Provider (CSP) suportam apenas o modelo Azure Resource Manager. Os serviços não-Azure Resource Manager não estão disponíveis no programa. Quando está a utilizar uma subscrição CSP, a conta Azure Classic Run As não é criada, mas a conta Azure Run As é criada. Para saber mais sobre as subscrições da CSP, consulte [os serviços disponíveis em subscrições CSP.](/azure/cloud-solution-provider/overview/azure-csp-available-services)

Quando cria uma conta Desmômes, a conta Executar Como é criada por padrão ao mesmo tempo. Se optar por não criá-la juntamente com a conta Automation, pode ser criada individualmente posteriormente. Um Azure Classic Run As Account é opcional e é criado separadamente se precisar de gerir recursos clássicos.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>Conta Run As

Quando cria uma conta Run As, executa as seguintes tarefas:

* Cria um pedido AD Azure com um certificado auto-assinado, cria uma conta principal de serviço para a aplicação em Azure AD, e atribui o papel [de Contribuinte](../role-based-access-control/built-in-roles.md#contributor) para a conta na sua subscrição atual. Pode alterar a definição de certificado para [Reader](../role-based-access-control/built-in-roles.md#reader) ou qualquer outra função. Para obter mais informações, veja [Controlo de acesso baseado em funções na Automatização do Azure](automation-role-based-access-control.md).

* Cria um certificado de automação nomeado `AzureRunAsCertificate` na conta de Automação especificada. O certificado detém a chave privada de certificado que a aplicação Azure AD utiliza.

* Cria um ativo de ligação Automation named `AzureRunAsConnection` in the specific Automation account. O ativo de ligação detém o ID da aplicação, iD do inquilino, ID de assinatura e impressão digital de certificado.

### <a name="azure-classic-run-as-account"></a>Azure Classic Run Como conta

Quando cria uma conta Azure Classic Run Como, executa as seguintes tarefas:

> [!NOTE]
> Deve ser coadministrador na subscrição para criar ou renovar este tipo de conta Run As.

* Cria um certificado de gestão na subscrição.

* Cria um certificado de automação nomeado `AzureClassicRunAsCertificate` na conta de Automação especificada. O recurso do certificado contém a chave privada do certificado que o certificado de gestão utiliza.

* Cria um ativo de ligação Automation named `AzureClassicRunAsConnection` in the specific Automation account. O ativo de ligação detém o nome de subscrição, iD de subscrição e nome do ativo do certificado.

## <a name="service-principal-for-run-as-account"></a>Diretor de serviço para executar como conta

O titular do serviço para uma conta Run Como não tem permissões para ler Azure AD por padrão. Se pretender adicionar permissões para ler ou gerir a Azure AD, deve conceder as permissões ao principal do serviço sob **permissões da API**. Para saber mais, consulte [Adicionar permissões para aceder à sua API web.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Executar como permissões de conta

Esta secção define permissões tanto para contas regulares como para contas Desafinada como para a Execução Clássica como para as contas.

* Para criar ou atualizar uma conta Run As, um administrador de aplicação no Diretório Ativo Azure e um Proprietário na subscrição podem completar todas as tarefas.
* Para configurar ou renovar a Classic Run As contas, tem de ter a função de Coadministrador ao nível da subscrição. Para saber mais sobre permissões clássicas de subscrição, consulte os [administradores de subscrição clássicos da Azure.](../role-based-access-control/classic-administrators.md#add-a-co-administrator)

Numa situação em que se tem separação de deveres, o quadro a seguir apresenta uma lista das tarefas, o cmdlet equivalente, e permissões necessárias:

|Tarefa|Cmdlet  |Permissões Mínimas  |Onde define as permissões|
|---|---------|---------|---|
|Criar aplicação Azure Ad|[Aplicação New-AzAD](/powershell/module/az.resources/new-azadapplication)     | Função de Desenvolvedor de Aplicações<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações |
|Adicione uma credencial à aplicação.|[Novo-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações|
|Criar e obter um diretor de serviço AZure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Administrador de Aplicação ou Administrador Global<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registos de Aplicações|
|Atribuir ou obter o papel de Azure para o principal especificado|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrador de acesso ao utilizador ou proprietário, ou tenha as seguintes permissões:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subscrição](../role-based-access-control/role-assignments-portal.md)</br>Subscrições > > \<subscription name\> - Controlo de Acesso (IAM)|
|Criar ou remover um certificado de Automação|[Novo AzAutomationCertificato](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remover-AzAutomationCertificato](/powershell/module/az.automation/remove-azautomationcertificate)     | Contribuinte no grupo de recursos         |Grupo de recursos de conta de automação|
|Criar ou remover uma ligação de Automação|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remover-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Contribuinte no grupo de recursos |Grupo de recursos de conta de automação|

<sup>1</sup> Os utilizadores não administrador do seu inquilino AZure AD podem [registar aplicações AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) se os Utilizadores do inquilino AZURE **AD puderem registar** a opção de aplicações na página **de definições** do Utilizador está definida como **Sim**. Se a definição de registo de pedido for **Nº,** o utilizador que executa esta ação deve ser definido nesta tabela.

Se não for membro da instância ative do Diretório da subscrição antes de ser adicionado ao papel de Administrador Global da subscrição, é adicionado como convidado. Nesta situação, recebe um `You do not have permissions to create…` aviso na página da conta Add **Automation.**

Para verificar se a situação que produz a mensagem de erro foi corrigida:

1. A partir do painel de diretório ativo Azure no portal Azure, selecione **Utilizadores e grupos**.
2. Selecione **Todos os utilizadores**.
3. Escolha o seu nome e, em seguida, selecione **Perfil**.
4. Certifique-se de que o valor do atributo **do tipo Utilizador** no perfil do utilizador não está definido para o **Convidado**.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

O controlo de acesso baseado em funções está disponível com o Azure Resource Manager para conceder ações permitidas a uma conta de utilizador AZure E Executar Como, e autenticar o principal do serviço. Leia [o artigo Role-based access control in Azure Automation (Controlo de acesso baseado em funções na Automatização do Azure)](automation-role-based-access-control.md) para obter mais informações ajudar a desenvolver o seu modelo para a gestão de permissões de Automatização.

Se tiver rigorosos controlos de segurança para a atribuição de permissão em grupos de recursos, tem de atribuir a conta Run As à função **contribuinte** no grupo de recursos.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticação de runbook com trabalhador de runbook híbrido

Os runbooks que funcionam num Trabalhador De Runbook Híbrido no seu datacenter ou contra serviços de computação em outros ambientes em nuvem como o AWS, não podem usar o mesmo método que é normalmente usado para livros de execução autenticando recursos Azure. Isto acontece porque esses recursos estão a ser executados fora do Azure e, como tal, precisam das suas próprias credenciais de segurança definidas na Automatização para autenticar em recursos que acedem localmente. Para obter mais informações sobre a autenticação de runbook com trabalhadores de runbook, consulte [runbooks runbooks em um Trabalhador de Runbook Híbrido.](automation-hrw-run-runbooks.md)

Para os runbooks que utilizam trabalhadores de runbook híbridos em VMs Azure, pode utilizar [a autenticação de runbook com identidades geridas](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) em vez de executar como contas para autenticar os seus recursos Azure.

## <a name="next-steps"></a>Passos seguintes

* Para criar uma conta Automation a partir do portal Azure, consulte [Criar uma conta Azure Automation autónoma.](automation-create-standalone-account.md)
* Se preferir criar a sua conta utilizando um modelo, consulte [Criar uma conta de Automação utilizando um modelo de Gestor de Recursos Azure.](quickstart-create-automation-account-template.md)
* Para autenticação utilizando os Serviços Web da Amazon, consulte [os livros de autenticação com os Serviços Web da Amazon.](automation-config-aws-account.md)
* Para obter uma lista dos serviços do Azure que suportam a funcionalidade de identidades geridas para recursos do Azure, veja [Serviços que suportam as identidades geridas para recursos do Azure](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).
