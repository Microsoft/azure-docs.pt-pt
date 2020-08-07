---
title: Acesso seguro a um cofre de chaves - Azure Key Vault / Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Cobre o modelo de autenticação e autorização do Key Vault e como proteger o cofre da chave.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: f9995b82c1dc437cdaa2f9f987abba3e9681454a
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926761"
---
# <a name="secure-access-to-a-key-vault"></a>Acesso seguro a um cofre de chaves

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. Como estes dados são sensíveis e críticos do negócio, é necessário garantir o acesso aos cofres das chaves, permitindo apenas aplicações e utilizadores autorizados. Este artigo fornece uma visão geral do modelo de acesso ao Cofre de Chaves. Explica a autenticação e a autorização e descreve como garantir o acesso aos cofres das chaves.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre-chave é controlado através de duas interfaces: o **plano de gestão** e o **plano de dados.** O avião de gestão é onde geres o Key Vault. As operações neste avião incluem a criação e eliminação de cofres chave, a recuperação de propriedades do Cofre-Chave e a atualização das políticas de acesso. O plano de dados é onde trabalha com os dados armazenados num cofre chave. Pode adicionar, excluir e modificar chaves, segredos e certificados.

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ter a autenticação e a autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais as operações que o chamador pode executar.

Ambos os aviões utilizam o Azure Ative Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza o controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso ao Cofre-Chave.

## <a name="active-directory-authentication"></a>Autenticação do Diretório Ativo

Quando cria um cofre chave numa subscrição do Azure, está automaticamente associado ao inquilino AD AD da subscrição. Todos os chamadores em ambos os aviões devem registar-se neste inquilino e autenticar para aceder ao cofre da chave. Em ambos os casos, as aplicações podem aceder ao Key Vault de duas formas:

- **Acesso à aplicação user plus**: A aplicação acede ao Key Vault em nome de um utilizador inscrito. Exemplos deste tipo de acesso incluem a Azure PowerShell e o portal Azure. O acesso ao utilizador é concedido de duas formas. Os utilizadores podem aceder ao Key Vault a partir de qualquer aplicação, ou devem utilizar uma aplicação específica (designada como _identidade composta)._
- **Acesso exclusivo à aplicação**: A aplicação funciona como um serviço de daemon ou trabalho de fundo. A identidade da aplicação tem acesso ao cofre da chave.

Para ambos os tipos de acesso, a aplicação autentica-se com Azure AD. A aplicação utiliza qualquer [método de autenticação suportado](../../active-directory/develop/authentication-scenarios.md) com base no tipo de aplicação. A aplicação adquire um símbolo para um recurso no avião para conceder acesso. O recurso é um ponto final na gestão ou plano de dados, baseado no ambiente Azure. A aplicação utiliza o token e envia um pedido de API REST para o Key Vault. Para saber mais, reveja todo o [fluxo de autenticação.](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

O modelo de um único mecanismo de autenticação para ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os cofres chave da sua organização.
- Se um utilizador sair, perdem instantaneamente o acesso a todos os cofres chave da organização.
- As organizações podem personalizar a autenticação utilizando as opções em Azure AD, tais como para permitir a autenticação de vários fatores para uma segurança adicional.

## <a name="resource-endpoints"></a>Pontos finais de recursos

As aplicações acedem aos aviões através de pontos finais. Os controlos de acesso dos dois aviões funcionam de forma independente. Para conceder a uma aplicação acesso a chaves de utilização num cofre de chaves, você concede acesso a um plano de dados usando uma política de acesso ao Cofre chave. Para conceder a um utilizador o acesso às propriedades e etiquetas do Key Vault, mas não ter acesso a dados (chaves, segredos ou certificados), concede acesso de avião de gestão com o RBAC.

A tabela seguinte mostra os pontos finais para os aviões de gestão e dados.

| Avião de acesso &nbsp; | Pontos finais de acesso | Operações | Mecanismo &nbsp; de controlo de acesso |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Governo Azure US:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e apagar cofres-chave<br><br>Definir políticas de acesso ao cofre de chaves<br><br>Definir tags de cofre de chaves | Azure RBAC |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Governo Azure US:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: desencriptar, encriptar,<br> desembrulhar, embrulhar, verificar, assinar,<br> obter, listar, atualizar, criar,<br> importar, eliminar, fazer backup, restaurar<br><br> Segredos: obter, listar, definir, excluir | Política de acesso ao Cofre chave |

## <a name="management-plane-and-rbac"></a>Avião de gestão e RBAC

No plano de gestão, você usa o controlo de acesso baseado em funções Azure (Azure RBAC) para autorizar as operações que um chamador pode executar. No modelo RBAC, cada subscrição do Azure tem um exemplo de Azure AD. Você concede acesso a utilizadores, grupos e aplicações deste diretório. O acesso é concedido para gerir recursos na subscrição Azure que utilizam o modelo de implementação do Gestor de Recursos Azure. Para conceder acesso, utilize o [portal Azure](https://portal.azure.com/), o [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [a Azure PowerShell,](/powershell/azure/)ou o [Azure Resource Manager REST APIs](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Cria-se um cofre chave num grupo de recursos e gere o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Você concede o acesso a um nível de âmbito específico, atribuindo as funções apropriadas de Azure. Para conceder acesso a um utilizador para gerir cofres-chave, atribui uma função predefinida `key vault Contributor` ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função Azure:

- **Subscrição**: Uma função Azure atribuída ao nível de subscrição aplica-se a todos os grupos de recursos e recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função Azure atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função Azure atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre-chave específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [o RBAC: Funções incorporadas.](../../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofre chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre de Chaves. Deve controlar bem quem tem `Contributor` acesso aos seus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Plano de dados e políticas de acesso

Você concede acesso a um plano de dados definindo as políticas de acesso do Key Vault para um cofre chave. Para definir estas políticas de acesso, um utilizador, grupo ou aplicação deve ter `Contributor` permissões para o plano de gestão para o cofre de chaves.

Você concede a um utilizador, grupo ou acesso de aplicação para executar operações específicas para chaves ou segredos em um cofre chave. O Key Vault suporta até 1.024 entradas na política de acesso para um cofre de chaves. Para conceder acesso de avião de dados a vários utilizadores, crie um grupo de segurança Azure AD e adicione utilizadores a esse grupo.

Pode ver a lista completa de operações secretas e cofre e compreender as operações permitidas quando configurar as políticas de acesso ao cofre chave, visualizando a seguinte referência. [Referência da operação do cofre de chaves](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a>As políticas de acesso ao Cofre chave concedem permissões separadamente a chaves, segredos e certificados. Pode conceder acesso a um utilizador apenas às chaves e não aos segredos. As permissões de acesso para chaves, segredos e certificados estão ao nível do cofre. As políticas de acesso ao Cofre não suportam permissões granulares ao nível de objetos como uma chave específica, segredo ou certificado. Para definir políticas de acesso para um cofre de chaves, utilize o [portal Azure,](https://portal.azure.com/)o [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [o Azure PowerShell](/powershell/azure/)ou as [APIs de Gestão de Cofres de Chaves](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> As principais políticas de acesso ao cofre aplicam-se ao nível do cofre. Quando um utilizador tem permissão para criar e eliminar chaves, pode efetuar essas operações em todas as teclas do cofre.
>

Pode restringir o acesso a um plano de dados utilizando [pontos finais de serviço de rede virtual para a Azure Key Vault](overview-vnet-service-endpoints.md)). Pode configurar [firewalls e regras de rede virtuais](network-security.md) para uma camada adicional de segurança.

## <a name="example"></a>Exemplo

Neste exemplo, estamos a desenvolver uma aplicação que utiliza um certificado para TLS/SSL, Azure Storage para armazenar dados, e uma chave RSA de 2.048 bits para operações de sinalização. A nossa aplicação funciona numa máquina virtual Azure (VM) (ou num conjunto de escala de máquina virtual). Podemos usar um cofre para guardar os segredos da aplicação. Podemos armazenar o certificado de bootstrap que é usado pela aplicação para autenticar com Azure AD.

Precisamos de acesso às seguintes chaves e segredos armazenados:
- **Certificado TLS/SSL**: Utilizado para TLS/SSL.
- **Chave de armazenamento**: Utilizado para aceder à conta de Armazenamento.
- **Tecla RSA 2.048-bit**: Utilizada para operações de sinalização.
- **Certificado de bootstrap**: Usado para autenticar com Azure AD. Após o acesso ser concedido, podemos pegar na chave de armazenamento e usar a chave RSA para a assinatura.

Precisamos definir as seguintes funções para especificar quem pode gerir, implementar e auditar a nossa aplicação:
- **Equipa de segurança**: Pessoal de TI do gabinete do CSO (Chief Security Officer) ou colaboradores semelhantes. A equipa de segurança é responsável pela proteção adequada dos segredos. Os segredos podem incluir certificados TLS/SSL, chaves RSA para assinatura, cordas de conexão e chaves de conta de armazenamento.
- **Desenvolvedores e operadores**: O pessoal que desenvolve a aplicação e a implementa em Azure. Os membros desta equipa não fazem parte da equipa de segurança. Não devem ter acesso a dados sensíveis como certificados TLS/SSL e chaves RSA. Apenas a aplicação que implementam deve ter acesso a dados sensíveis.
- **Auditores**: Este papel é para os contribuintes que não são membros do pessoal de desenvolvimento ou de TI geral. Revejam o uso e manutenção de certificados, chaves e segredos para garantir o cumprimento das normas de segurança.

Há outro papel que está fora do âmbito da nossa aplicação: o administrador de subscrição (ou grupo de recursos). O administrador de subscrição estabelece permissões de acesso iniciais para a equipa de segurança. Concedem acesso à equipa de segurança utilizando um grupo de recursos que tem os recursos exigidos pela aplicação.

Precisamos de autorizar as seguintes operações para as nossas funções:

**Equipa de segurança**
- Crie cofres chave.
- Ligue o registo do Cofre de Chaves.
- Adicione chaves e segredos.
- Crie cópias de segurança das chaves para a recuperação de desastres.
- Desemboe as políticas de acesso do Key Vault para conceder permissões aos utilizadores e aplicações para operações específicas.
- Rolar as chaves e os segredos periodicamente.

**Programadores e operadores**
- Obtenha referências da equipa de segurança para os certificados bootstrap e TLS/SSL (impressões digitais), chave de armazenamento (URI secreto) e chave RSA (key URI) para a assinatura.
- Desenvolva e implemente a aplicação para aceder a chaves e segredos programáticamente.

**Auditores**
- Reveja os registos do Cofre-Chave para confirmar o uso adequado de chaves e segredos e o cumprimento das normas de segurança de dados.

A tabela seguinte resume as permissões de acesso para as nossas funções e aplicação.

| Função | Permissões do plano de gestão | Permissões do plano de dados |
| --- | --- | --- |
| Equipa de segurança | Contribuidor do Cofre Chave | Chaves: criar cópia de segurança, criar, eliminar, obter, importar, listar, restaurar<br>Segredos: todas as operações |
| Desenvolvedores e &nbsp; operadores | Permissão de implantação do Cofre de Chaves<br><br> **Nota:** Esta permissão permite que os VM implantados tragam segredos de um cofre de chaves. | Nenhum |
| Auditores | Nenhum | Chaves: listar<br>Segredos: listar<br><br> **Nota:** Esta permissão permite que os auditores inspecionem atributos (tags, datas de ativação, datas de validade) para obter chaves e segredos não emitidos nos registos. |
| Aplicação | Nenhum | Chaves: assinar<br>Segredos: obter |

As três funções da equipa precisam de acesso a outros recursos, juntamente com permissões do Key Vault. Para implementar VMs (ou a funcionalidade de Aplicações Web do Azure App Service), os desenvolvedores e operadores precisam de `Contributor` acesso a esses tipos de recursos. Os auditores precisam de ler o acesso à conta de armazenamento onde os registos do Cofre de Chaves estão armazenados.

Para obter mais informações sobre como implementar certificados, chaves de acesso e segredos programáticamente, consulte estes recursos:
- Saiba como [implantar certificados em VMs a partir de um cofre-chave gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blog post).
- Veja as amostras do [cliente Azure Key Vault](https://docs.microsoft.com/samples/browse/?term=Key%20Vault). Este conteúdo ilustra como usar um certificado de bootstrap para autenticar a Azure AD para aceder a um cofre de chaves.

Pode conceder a maioria das permissões de acesso utilizando o portal Azure. Para conceder permissões granulares, pode utilizar a Azure PowerShell ou o Azure CLI.

Os snippets PowerShell nesta secção são construídos com os seguintes pressupostos:
- O administrador da AD Azure criou grupos de segurança para representar as três funções: Contoso Security Team, Contoso App DevOps e Contoso App Auditores. O administrador adicionou utilizadores aos respetivos grupos.
- Todos os recursos estão localizados no grupo de recursos **ContosoAppRG.**
- Os registos do Cofre-Chave estão armazenados na conta de armazenamento **de contosologstorage.**
- O cofre chave **ContosoKeyVault** e a conta de armazenamento **de contosologstorage** estão na mesma localização Azure.

O administrador de subscrição atribui os `key vault Contributor` papéis e `User Access Administrator` funções à equipa de segurança. Estas funções permitem à equipa de segurança gerir o acesso a outros recursos e cofres chave, ambos no grupo de recursos **ContosoAppRG.**

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A equipa de segurança cria um cofre chave e configura permissões de registo e acesso.

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

As nossas funções personalizadas definidas são atribuíveis apenas à subscrição onde é criado o grupo de recursos **ContosoAppRG.** Para utilizar um papel personalizado para outros projetos em outras subscrições, adicione outras subscrições à margem do papel.

Para a nossa equipa de DevOps, a atribuição de funções personalizadas para a permissão do cofre chave `deploy/action` é aparada para o grupo de recursos. Apenas os VMs criados no grupo de recursos **ContosoAppRG** têm acesso aos segredos (certificados TLS/SSL e bootstrap). Os VMs criados em outros grupos de recursos por um membro da DevOps não podem aceder a estes segredos, mesmo que o VM tenha os URIs secretos.

O nosso exemplo descreve um cenário simples. Cenários da vida real podem ser mais complexos. Pode ajustar permissões ao seu cofre com base nas suas necessidades. Assumimos que a equipa de segurança fornece as referências chave e secretas (URIs e impressões digitais), que são usadas pelo pessoal da DevOps nas suas aplicações. Os desenvolvedores e operadores não requerem acesso a um avião de dados. Concentrámo-nos em como proteger o cofre das chaves. Tenha a mesma consideração quando garantir [os seus VMs,](https://azure.microsoft.com/services/virtual-machines/security/)contas de armazenamento e [outros](../../storage/blobs/security-recommendations.md)recursos Azure.

> [!NOTE]
> Este exemplo mostra como o acesso ao Cofre chave é bloqueado na produção. Os desenvolvedores devem ter a sua própria subscrição ou grupo de recursos com permissões completas para gerir os seus cofres, VMs e a conta de armazenamento onde desenvolvem a aplicação.

Recomendamos que crie um acesso adicional e seguro ao cofre de chaves [configurando firewalls key Vault e redes virtuais.](network-security.md)

## <a name="resources"></a>Recursos

* [Azure RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md)

* [Compreender a implementação do Gestor de Recursos e a implantação clássica](../../azure-resource-manager/management/deployment-models.md)

* [Gerir o RBAC com a Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [Gerir o RBAC com a API REST](../../role-based-access-control/role-assignments-rest.md)

* [RBAC para Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Este vídeo de conferência da Microsoft Ignite de 2015 discute capacidades de gestão de acessos e reportagens em Azure. Também explora as melhores práticas para garantir o acesso às subscrições do Azure utilizando a Azure AD.

* [Autorizar o acesso a aplicações web utilizando a OAuth 2.0 e a Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [Principais APIs de Gestão de Abóbada de Cofre](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A referência para as APIs REST para gerir o seu cofre de chaves programáticamente, incluindo a definição da política de acesso ao Cofre de Chaves.

* [APIs de repouso de cofre de chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Controlo de acesso a chaves](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Controlo de acesso a segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Descoda](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) e [remova a](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) política de acesso ao Cofre da Chave utilizando o PowerShell.

## <a name="next-steps"></a>Passos seguintes

Configure [firewalls key vault e redes virtuais](network-security.md).

Para um tutorial de iniciação para um administrador, veja [o que é Azure Key Vault?](overview.md)

Para obter mais informações sobre o registo de utilização para o Key Vault, consulte [o registo do Cofre da Chave Azure).](logging.md)

Para obter mais informações sobre a utilização de chaves e segredos com o Azure Key Vault, consulte [Sobre chaves e segredos.](https://msdn.microsoft.com/library/azure/dn903623.aspx)

Se tiver dúvidas sobre o Key Vault, visite a [página de perguntas do Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
