---
title: Proteger o acesso a um cofre de chaves - Azure Key Vault | Documentos da Microsoft
description: Gerir permissões de acesso para o Azure Key Vault, chaves e segredos. Abrange o modelo de autenticação e autorização para o Cofre de chaves e como proteger o seu Cofre de chaves.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 3b302c60aefec1c4cd37a7dde82a2f11a9eeed33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57862867"
---
# <a name="secure-access-to-a-key-vault"></a>Proteger o acesso a um cofre de chaves

O Azure Key Vault é um serviço cloud que salvaguarda as chaves de encriptação e os segredos, como certificados, cadeias de ligação e palavras-passe. Uma vez que estes dados são confidenciais e crítico para a empresa, terá de proteger o acesso aos seus cofres de chaves ao permitir que apenas aplicativos autorizados e utilizadores. Este artigo fornece uma descrição geral do modelo de acesso do Cofre de chaves. Ele explica a autenticação e autorização e descreve como proteger o acesso aos seus cofres de chaves.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

Acesso a um cofre de chaves é controlado através de duas interfaces: os **plano de gestão** e o **plano de dados**. O plano de gestão é onde pode gerir o Key Vault em si. As operações nesse plano incluem criar e eliminar cofres de chaves, obter propriedades do Cofre de chaves e a atualizar as políticas de acesso. O plano de dados é onde trabalha com os dados armazenados num cofre de chaves. Pode adicionar, eliminar e modificar as chaves, segredos e certificados.

Para aceder a um cofre de chaves num plano, todos os chamadores (utilizadores ou aplicações) tem de ter autenticação e autorização adequadas. A autenticação estabelece a identidade do chamador. Autorização determina as operações que pode executar o autor da chamada. 

Ambos os planos utilizam o Azure Active Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza o controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso do Cofre de chaves.

## <a name="active-directory-authentication"></a>Autenticação do Active Directory

Quando cria um cofre de chaves numa subscrição do Azure, é automaticamente associado com o inquilino do Azure AD da subscrição. Todos os chamadores em ambos os planos tem de registar neste inquilino e autenticar para aceder ao Cofre de chave. Em ambos os casos, aplicações podem aceder ao Cofre de chaves de duas formas:

- **Acesso de utilizador e a aplicação**: O aplicativo acessa o Cofre de chaves em nome de um utilizador com sessão iniciada. Os exemplos deste tipo de acesso incluem o Azure PowerShell e o portal do Azure. Acesso de utilizador é concedido de duas formas. Os usuários podem acessar o Key Vault a partir de qualquer aplicação ou tem de utilizar uma aplicação específica (denominados _composta identidade_).
- **Acesso só de aplicação**: A aplicação é executada como uma tarefa em segundo plano ou de serviço de daemon. A identidade da aplicação é concedida acesso ao Cofre de chaves.

Para ambos os tipos de acesso, a aplicação autentica-se com o Azure AD. A aplicação utiliza qualquer [suportado o método de autenticação](../active-directory/develop/authentication-scenarios.md) com base no tipo de aplicação. A aplicação recebe um token para um recurso no plano para conceder acesso. O recurso é um ponto final no painel de gestão ou de dados, com base no ambiente do Azure. A aplicação utiliza o token e envia um pedido de REST API para o Key Vault. Para obter mais informações, reveja os [fluxo de autenticação completo](../active-directory/develop/v1-protocols-oauth-code.md).

O modelo de um mecanismo único para a autenticação para ambos os planos tem várias vantagens:

- As organizações podem controlar o acesso centralmente a todos os cofres de chaves na sua organização.
- Se um utilizador sai, perde imediatamente o acesso a todos os cofres da organização.
- As organizações podem personalizar a autenticação utilizando as opções no Azure AD, tal como para ativar a autenticação multifator para aumentar a segurança.

## <a name="resource-endpoints"></a>Pontos finais do recurso

Aplicações de acesso os planos através de pontos finais. Os controlos de acesso para os dois planos trabalham de forma independente. Para conceder um acesso de aplicação para utilizar as chaves num cofre de chaves, pode conceder acesso do plano de dados com uma política de acesso do Cofre de chaves. Para conceder um acesso de leitura do utilizador para etiquetas e propriedades do Cofre de chaves, mas não aceder aos dados (chaves, segredos ou certificados), pode conceder acesso do plano de gestão com o RBAC.

A tabela seguinte mostra os pontos finais para o gerenciamento e planos de dados.

| Acesso&nbsp;plano | Pontos finais de acesso | Operações | Acesso&nbsp;mecanismo de controlo |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e eliminar cofres de chaves<br><br>Definir políticas de acesso do Cofre de chaves<br><br>Definir etiquetas do Cofre de chaves | Azure Resource Manager RBAC |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: desencriptar, encriptar,<br> anular a moldagem, moldar, certifique-se, iniciarem sessão,<br> obter, listar, atualizar, criar,<br> importar, eliminar, restauro de cópia de segurança,<br><br> Segredos: obter, listar, definir, eliminar | Política de acesso do Cofre de chaves |

## <a name="management-plane-and-rbac"></a>Plano de gestão e o RBAC

O plano de gestão, vai utilizar o RBAC (controlo de acesso de com base em funções) para autorizar as operações que um chamador pode executar. No modelo de RBAC, cada subscrição do Azure tem uma instância do Azure AD. Conceder acesso a utilizadores, grupos e aplicações deste diretório. O acesso é concedido para gerir os recursos na subscrição do Azure que utilizam o modelo de implementação Azure Resource Manager. Para conceder acesso, utilize o [portal do Azure](https://portal.azure.com/), o [CLI do Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs), ou o [APIs de REST do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Criar um cofre de chaves num grupo de recursos e gerir o acesso ao utilizar o Azure AD. Conceder aos utilizadores ou grupos a capacidade de gerir cofres de chaves num grupo de recursos. Conceder o acesso a um nível de âmbito específico através da atribuição de funções RBAC adequadas. Para conceder acesso a um utilizador para gerir cofres de chaves, atribui um predefinidos `key vault Contributor` função ao utilizador com um âmbito específico. Os seguintes níveis de âmbitos podem ser atribuídos a uma função RBAC:

- **Subscrição**: Uma função RBAC atribuída ao nível da subscrição se aplica a todos os grupos de recursos e recursos nessa subscrição.
- **Grupo de recursos**: Uma função RBAC atribuída ao nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: Uma função RBAC atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre de chaves específico.

Existem várias funções predefinidas. Se uma função predefinida não atender a suas necessidades, pode definir sua própria função. Para obter mais informações, consulte [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um plano de gestão do Cofre de chaves, o utilizador pode conceder próprios acesso para o plano de dados ao definir uma política de acesso do Cofre de chaves. Deve controlar rigorosamente quem tem `Contributor` acesso à função para seus cofres de chaves. Certifique-se de que só pessoas autorizadas podem aceder e gerir cofres de chaves, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Políticas de acesso e de plano de dados

Conceder acesso do plano de dados através da definição de políticas de acesso do Cofre de chaves para um cofre de chaves. Para definir estas políticas de acesso, um utilizador, grupo ou aplicação tem de ter `Contributor` permissões para o plano de gestão desse Cofre de chaves.

Pode conceder um utilizador, grupo ou acesso a aplicações para executar operações específicas de chaves ou segredos num cofre de chaves. Key Vault suporta até 1024 entradas de política de acesso para um cofre de chaves. Para conceder acesso do plano de dados a vários utilizadores, crie um grupo de segurança do Azure AD e adicionar utilizadores a esse grupo.

<a id="key-vault-access-policies"></a> Políticas de acesso do Cofre de chaves concedem permissões em separado para chaves, segredos e certificados. Pode conceder um acesso de utilizadores apenas a chaves e não a segredos. Permissões de acesso para chaves, segredos e certificados são ao nível do cofre. Políticas de acesso do Cofre de chaves não suportam permissões granulares, ao nível do objeto, como uma chave específica, o segredo ou o certificado. Para definir políticas de acesso para um cofre de chaves, utilize o [portal do Azure](https://portal.azure.com/), o [CLI do Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs), ou o [APIs de REST de gestão do Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Políticas de acesso do Key Vault aplicam-se ao nível do cofre. Quando um utilizador tem permissão para criar e eliminar chaves, eles podem executar essas operações em todas as chaves no Cofre de chaves.
>

Pode restringir o acesso do plano de dados usando [pontos finais de serviço de rede virtual para o Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Pode configurar [firewalls e regras de rede virtual](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="example"></a>Exemplo

Neste exemplo, estamos a desenvolver uma aplicação que utiliza um certificado para SSL, o armazenamento do Azure para armazenar dados e uma chave RSA 2.048 bits para operações de início de sessão. Nosso aplicativo é executado numa máquina virtual do Azure (VM) (ou um conjunto de dimensionamento de máquinas virtuais). Podemos usar um cofre de chaves para armazenar os segredos da aplicação. Podemos pode armazenar o certificado de arranque de configuração que é utilizado pela aplicação para autenticar com o Azure AD.

Precisamos de acesso aos seguintes armazenadas chaves e segredos:
- **Certificado SSL**: Utilizado para SSL.
- **Chave de armazenamento**: Utilizado para aceder à conta de armazenamento.
- **Chave RSA de 2.048 bits**: Utilizado para operações de início de sessão.
- **Certificado de arranque de configuração**: Utilizado para autenticar com o Azure AD. Depois do acesso é concedido, podemos obter a chave de armazenamento e utilizar a chave RSA para a assinatura.

É preciso definir as seguintes funções para especificar quem pode gerir, implementar e nosso aplicativo de auditoria:
- **Equipe de segurança**: Equipe de TI do office do CSO (Chief Security Officer) ou contribuidores semelhante. A equipe de segurança é responsável por proteger adequadamente dos segredos. Os segredos podem incluir certificados SSL, chaves RSA para iniciar sessão, as cadeias de ligação e chaves de conta de armazenamento.
- **Os desenvolvedores e operadores**: A equipe que desenvolveram a aplicação e implementá-la no Azure. Os membros da Equipe não fazem parte da equipe de segurança. Eles não devem ter acesso a dados confidenciais, como certificados SSL e chaves RSA. Apenas o aplicativo que implementam deve ter acesso a dados confidenciais.
- **Auditores**: Esta função é para os contribuidores que não são membros da equipa de TI geral ou desenvolvimento. Ele Revise o uso e a manutenção de certificados, chaves e segredos para garantir a conformidade com normas de segurança. 

Existe outra função que está fora do escopo do nosso aplicativo: a subscrição (ou grupo de recursos) administrador. O administrador da subscrição configura as permissões de acesso inicial para a equipe de segurança. Eles concedem acesso à equipa de segurança ao utilizar um grupo de recursos que tem os recursos exigidos pelo aplicativo.

É preciso autorizar as seguintes operações para nossas funções:

**Equipa de segurança**
- Crie cofres de chaves.
- Ative o registo do Cofre de chaves.
- Adicione chaves e segredos.
- Crie cópias de segurança de chaves para recuperação após desastre.
- Definir políticas de acesso do Key Vault para conceder permissões a utilizadores e aplicações para operações específicas.
- Distribuir as chaves e segredos periodicamente.

**Os desenvolvedores e operadores**
- Obter referências da equipe de segurança para o arranque e certificados SSL (thumbprints), a chave de armazenamento (URI secreta) e chave RSA (URI da chave) para iniciar sessão.
- Desenvolver e implementar a aplicação para aceder a chaves e segredos programaticamente.

**Auditores**
- Reveja os registos do Key Vault para confirmar a utilização adequada de chaves e segredos e conformidade com as normas de segurança de dados.

A tabela seguinte resume as permissões de acesso para nosso aplicativo e as funções. 

| Função | Permissões do plano de gestão | Permissões do plano de dados |
| --- | --- | --- |
| Equipa de segurança | Contribuidor do Key Vault | Chaves: criar cópias de segurança, criar, eliminar, obter, importar, listar, restaurar<br>Segredos: todas as operações |
| Os desenvolvedores e&nbsp;operadores | Permissão de implementação do Key Vault<br><br> **Nota**: Esta permissão permite que as VMs implementadas obter segredos a partir de um cofre de chaves. | Nenhuma |
| Auditores | Nenhuma | Chaves: listar<br>Segredos: listar<br><br> **Nota**: Esta permissão permite auditores inspecionar atributos (etiquetas, datas de ativação, datas de expiração) para chaves e segredos não são emitidos nos registos. |
| Aplicação | Nenhuma | Chaves: assinar<br>Segredos: obter |

As três funções de equipe precisam de acesso a outros recursos, juntamente com as permissões do Cofre de chaves. Para implementar VMs (ou a funcionalidade de aplicações Web do serviço de aplicações do Azure), os desenvolvedores e operadores precisam `Contributor` acesso a esses tipos de recursos. Os auditores requerem acesso de leitura à conta de armazenamento onde são armazenados os registos do Key Vault.

Para obter mais informações sobre como implementar certificados, chaves de acesso e segredos programaticamente, consulte estes recursos:
- Saiba como [implementar certificados para VMs a partir de um cofre de chaves geridas pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (mensagem de blogue).
- Transfira o [exemplos de cliente do Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Este conteúdo ilustra como utilizar um certificado de arranque de configuração para autenticar para o Azure AD para aceder a um cofre de chaves.

Pode conceder a maioria das permissões de acesso com o portal do Azure. Para conceder permissões granulares, que pode utilizar o Azure PowerShell ou a CLI do Azure.

Os fragmentos de PowerShell nesta secção baseiam-se com as seguintes suposições:
- O administrador do Azure AD criar grupos de segurança para representar as três funções: Equipa de segurança da Contoso, DevOps de aplicação da Contoso e auditores de aplicações da Contoso. O administrador tem de adicionar utilizadores para os respetivos grupos.
- Todos os recursos estão localizados no **ContosoAppRG** grupo de recursos.
- Os registos do Cofre de chaves são armazenados no **contosologstorage** conta de armazenamento. 
- O **ContosoKeyVault** Cofre de chaves e o **contosologstorage** conta de armazenamento estão na mesma localização do Azure.

O administrador da subscrição atribui as `key vault Contributor` e `User Access Administrator` funções para a equipe de segurança. Estas funções de permitir que a equipe de segurança gerir o acesso a outros recursos e os cofres de chaves, que, em ambos os **ContosoAppRG** grupo de recursos.

```PowerShell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A equipe de segurança cria um cofre de chaves e configura as permissões de acesso e de registo. Para obter detalhes sobre as permissões de política de acesso do Cofre de chaves, consulte [chaves de sobre o Azure Key Vault, segredos e certificados](about-keys-secrets-and-certificates.md).

```PowerShell
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

Nossas funções personalizadas definidas são atribuíveis apenas para a subscrição em que o **ContosoAppRG** é criado o grupo de recursos. Para utilizar uma função personalizada para outros projetos de outras subscrições, adicione outras assinaturas ao âmbito para a função.

Para a nossa equipa de DevOps, a atribuição de função personalizada para o Cofre de chaves `deploy/action` permissão tem um âmbito para o grupo de recursos. Apenas as VMs criadas no **ContosoAppRG** grupo de recursos têm acesso a segredos (SSL e certificados de arranque do sistema). As VMs criadas em outros grupos de recursos por um membro de DevOps não é possível aceder estes segredos, mesmo que a VM tem os respetivos URIs.

Nosso exemplo descreve um cenário simples. Cenários da vida real podem ser mais complexos. Pode ajustar as permissões para o seu Cofre de chaves com base nas suas necessidades. Assumimos que a equipe de segurança fornece a chave e referências segredas (URIs e thumbprints), o que são utilizadas pela equipa do DevOps nas respetivas aplicações. Os desenvolvedores e operadores não necessitam de qualquer acesso do plano de dados. Nos concentramos em como proteger o seu Cofre de chaves. Considere semelhante quando protege [as suas VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamento](../storage/common/storage-security-guide.md)e outros recursos do Azure.

> [!NOTE]
> Este exemplo mostra como o acesso do Key Vault está bloqueado na produção. Os desenvolvedores devem ter sua própria subscrição ou grupo de recursos com todas as permissões para gerir os cofres, VMs e a conta de armazenamento em que desenvolveram o aplicativo.

Recomendamos que configure adicional acesso seguro ao seu Cofre de chaves por [configurar o Cofre de chaves firewalls e redes virtuais](key-vault-network-security.md).

## <a name="resources"></a>Recursos

* [RBAC do Azure AD](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md)

* [Compreender a implementação do Resource Manager e a implementação clássica](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Gerir o RBAC com o Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Gerir o RBAC com a API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC do Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Este 2015 Microsoft Ignite aborda de vídeo de conferência gestão de acesso e capacidades de relatórios no Azure. Também explora as práticas recomendadas para proteger o acesso a subscrições do Azure ao utilizar o Azure AD.

* [Autorizar o acesso às aplicações web com o OAuth 2.0 e o Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)

* [REST APIs de gestão do Cofre de chaves](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A referência para as APIs REST para gerir a sua chave de cofre por meio de programação, incluindo a definição de política de acesso do Cofre de chaves.

* [APIs REST do Cofre de chaves](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Controlo de acesso a chaves](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Controlo de acesso a segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Definir](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) e [remover](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) política de acesso do Key Vault com o PowerShell.
  
## <a name="next-steps"></a>Passos Seguintes

Configurar [Key Vault firewalls e redes virtuais](key-vault-network-security.md).

Para obter um tutorial de introdução de mensagens em fila para um administrador, consulte [o que é o Azure Key Vault?](key-vault-overview.md).

Para obter mais informações sobre o registo de utilização do Key Vault, veja [Registo do Azure Key Vault](key-vault-logging.md).

Para obter mais informações sobre a utilização de chaves e segredos com o Azure Key Vault, consulte [sobre chaves e segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se tiver dúvidas sobre o Key Vault, visite o [fóruns](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
