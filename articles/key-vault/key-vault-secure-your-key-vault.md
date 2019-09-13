---
title: Proteger o acesso a um cofre de chaves-Azure Key Vault | Microsoft Docs
description: Gerencie permissões de acesso para Azure Key Vault, chaves e segredos. Aborda o modelo de autenticação e autorização para Key Vault e como proteger o cofre de chaves.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 4857cda7c3387e72be8837422469888adc5504d1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883104"
---
# <a name="secure-access-to-a-key-vault"></a>Proteger o acesso a um cofre de chaves

Azure Key Vault é um serviço de nuvem que protege chaves de criptografia e segredos como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e críticos para os negócios, você precisa proteger o acesso aos seus cofres de chaves, permitindo apenas aplicativos e usuários autorizados. Este artigo fornece uma visão geral do modelo de acesso Key Vault. Ele explica a autenticação e a autorização e descreve como proteger o acesso aos seus cofres de chaves.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre de chaves é controlado por meio de duas interfaces: o **plano de gerenciamento** e o **plano de dados**. O plano de gerenciamento é onde você gerencia Key Vault em si. As operações nesse plano incluem a criação e exclusão de cofres de chaves, a recuperação de Key Vault Propriedades e a atualização de políticas de acesso. O plano de dados é onde você trabalha com os dados armazenados em um cofre de chaves. Você pode adicionar, excluir e modificar chaves, segredos e certificados.

Para acessar um cofre de chaves em qualquer plano, todos os chamadores (usuários ou aplicativos) devem ter autenticação e autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais operações o chamador pode executar. 

Ambos os planos usam Azure Active Directory (AD do Azure) para autenticação. Para autorização, o plano de gerenciamento usa o RBAC (controle de acesso baseado em função) e o plano de dados usa uma política de acesso Key Vault.

## <a name="active-directory-authentication"></a>Autenticação do Active Directory

Quando você cria um cofre de chaves em uma assinatura do Azure, ele é automaticamente associado ao locatário do Azure AD da assinatura. Todos os chamadores em ambos os planos devem se registrar nesse locatário e autenticar para acessar o cofre de chaves. Em ambos os casos, os aplicativos podem acessar Key Vault de duas maneiras:

- **Usuário mais acesso ao aplicativo**: O aplicativo acessa Key Vault em nome de um usuário conectado. Exemplos desse tipo de acesso incluem Azure PowerShell e o portal do Azure. O acesso do usuário é concedido de duas maneiras. Os usuários podem acessar Key Vault de qualquer aplicativo ou devem usar um aplicativo específico (conhecido como _identidade composta_).
- **Acesso somente ao aplicativo**: O aplicativo é executado como um serviço daemon ou trabalho em segundo plano. A identidade do aplicativo recebe acesso ao cofre de chaves.

Para ambos os tipos de acesso, o aplicativo é autenticado com o Azure AD. O aplicativo usa qualquer [método de autenticação com suporte](../active-directory/develop/authentication-scenarios.md) com base no tipo de aplicativo. O aplicativo adquire um token para um recurso no plano para conceder acesso. O recurso é um ponto de extremidade no plano de gerenciamento ou de dados, com base no ambiente do Azure. O aplicativo usa o token e envia uma solicitação da API REST para Key Vault. Para saber mais, examine [todo o fluxo de autenticação](../active-directory/develop/v1-protocols-oauth-code.md).

O modelo de um único mecanismo de autenticação para ambos os planos tem vários benefícios:

- As organizações podem controlar o acesso centralmente a todos os cofres de chaves em sua organização.
- Se um usuário deixar, ele perderá instantaneamente o acesso a todos os cofres de chaves na organização.
- As organizações podem personalizar a autenticação usando as opções no Azure AD, como para habilitar a autenticação multifator para aumentar a segurança.

## <a name="resource-endpoints"></a>Pontos de extremidade de recurso

Os aplicativos acessam os planos por meio de pontos de extremidade. Os controles de acesso para os dois planos funcionam de forma independente. Para conceder a um aplicativo acesso para usar chaves em um cofre de chaves, você concede acesso ao plano de dados usando uma política de acesso de Key Vault. Para conceder a um usuário acesso de leitura a Key Vault Propriedades e marcas, mas não ao acesso a dados (chaves, segredos ou certificados), você concede acesso ao plano de gerenciamento com o RBAC.

A tabela a seguir mostra os pontos de extremidade para os planos de dados e de gerenciamento.

| Plano&nbsp;de acesso | Pontos finais de acesso | Operações | Mecanismo&nbsp;de controle de acesso |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br><br> **21Vianet do Azure na China:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e excluir cofres de chaves<br><br>Definir políticas de acesso de Key Vault<br><br>Definir marcas de Key Vault | Azure Resource Manager RBAC |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **21Vianet do Azure na China:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: descriptografar, criptografar,<br> desencapsular, encapsular, verificar, assinar,<br> obter, listar, atualizar, criar,<br> importação, exclusão, backup, restauração<br><br> Segredos: obter, listar, definir, excluir | Política de acesso de Key Vault |

## <a name="management-plane-and-rbac"></a>Plano de gerenciamento e RBAC

No plano de gerenciamento, você usa o RBAC (controle de acesso baseado em função) para autorizar as operações que um chamador pode executar. No modelo RBAC, cada assinatura do Azure tem uma instância do Azure AD. Você concede acesso a usuários, grupos e aplicativos desse diretório. O acesso é concedido para gerenciar recursos na assinatura do Azure que usam o modelo de implantação Azure Resource Manager. Para conceder acesso, use as [portal do Azure](https://portal.azure.com/), [CLI do Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)ou as [APIs REST Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Você cria um cofre de chaves em um grupo de recursos e gerencia o acesso usando o Azure AD. Você concede aos usuários ou grupos a capacidade de gerenciar os cofres de chaves em um grupo de recursos. Conceda o acesso em um nível de escopo específico atribuindo funções RBAC apropriadas. Para conceder acesso a um usuário para gerenciar cofres de chaves, você atribui uma `key vault Contributor` função predefinida para o usuário em um escopo específico. Os seguintes níveis de escopos podem ser atribuídos a uma função de RBAC:

- **Subscrição**: Uma função RBAC atribuída no nível de assinatura se aplica a todos os grupos de recursos e recursos dentro dessa assinatura.
- **Grupo de recursos**: Uma função RBAC atribuída no nível do grupo de recursos se aplica a todos os recursos nesse grupo de recursos.
- **Recurso específico**: Uma função RBAC atribuída a um recurso específico se aplica a esse recurso. Nesse caso, o recurso é um cofre de chaves específico.

Há várias funções predefinidas. Se uma função predefinida não atender às suas necessidades, você poderá definir sua própria função. Para obter mais informações, [consulte RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Se um usuário tiver `Contributor` permissões para um plano de gerenciamento do cofre de chaves, o usuário poderá conceder a si mesmo o acesso ao plano de dados definindo uma política de acesso de Key Vault. Você deve controlar rigidamente quem tem `Contributor` acesso de função para seus cofres de chaves. Certifique-se de que apenas pessoas autorizadas possam acessar e gerenciar seus cofres de chaves, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Plano de dados e políticas de acesso

Conceda acesso ao plano de dados definindo Key Vault políticas de acesso para um cofre de chaves. Para definir essas políticas de acesso, um usuário, grupo ou aplicativo deve ter `Contributor` permissões para o plano de gerenciamento para esse cofre de chaves.

Você concede acesso de usuário, de grupo ou de aplicativo para executar operações específicas para chaves ou segredos em um cofre de chaves. O Key Vault dá suporte a até 1.024 entradas de política de acesso para um cofre de chaves. Para conceder acesso ao plano de dados a vários usuários, crie um grupo de segurança do Azure AD e adicione usuários a esse grupo.

<a id="key-vault-access-policies"></a>Key Vault políticas de acesso concedem permissões separadamente a chaves, segredos e certificado. Você pode conceder a um usuário acesso apenas a chaves e não a segredos. As permissões de acesso para chaves, segredos e certificados estão no nível do cofre. Key Vault políticas de acesso não dão suporte a permissões granulares em nível de objeto, como uma chave, um segredo ou um certificado específico. Para definir políticas de acesso para um cofre de chaves, use o [portal do Azure](https://portal.azure.com/), o [CLI do Azure](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs)ou as [APIs REST de gerenciamento de Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Key Vault políticas de acesso se aplicam no nível do cofre. Quando um usuário recebe permissão para criar e excluir chaves, ele pode executar essas operações em todas as chaves nesse cofre de chaves.
>

Você pode restringir o acesso ao plano de dados usando [pontos de extremidade de serviço de rede virtual para Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Você pode configurar [firewalls e regras de rede virtual](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="example"></a>Exemplo

Neste exemplo, estamos desenvolvendo um aplicativo que usa um certificado para SSL, armazenamento do Azure para armazenar dados e uma chave RSA de 2.048 bits para operações de entrada. Nosso aplicativo é executado em uma VM (máquina virtual) do Azure (ou em um conjunto de dimensionamento de máquinas virtuais). Podemos usar um cofre de chaves para armazenar os segredos do aplicativo. Podemos armazenar o certificado de bootstrap que é usado pelo aplicativo para autenticar com o Azure AD.

Precisamos de acesso às seguintes chaves e segredos armazenados:
- **Certificado SSL**: Usado para SSL.
- **Chave de armazenamento**: Usado para acessar a conta de armazenamento.
- **Chave RSA de 2.048 bits**: Usado para operações de entrada.
- **Certificado de inicialização**: Usado para autenticar com o Azure AD. Depois que o acesso é concedido, podemos buscar a chave de armazenamento e usar a chave RSA para assinatura.

Precisamos definir as seguintes funções para especificar quem pode gerenciar, implantar e auditar nosso aplicativo:
- **Equipe de segurança**: A equipe de ti do escritório do CSO (diretor de segurança) ou colaboradores semelhantes. A equipe de segurança é responsável pela proteção adequada dos segredos. Os segredos podem incluir certificados SSL, chaves RSA para assinatura, cadeias de conexão e chaves de conta de armazenamento.
- **Desenvolvedores e operadores**: A equipe que desenvolve o aplicativo e o implanta no Azure. Os membros dessa equipe não fazem parte da equipe de segurança. Eles não devem ter acesso a dados confidenciais, como certificados SSL e chaves RSA. Somente o aplicativo que eles implantam deve ter acesso a dados confidenciais.
- **Auditores**: Essa função destina-se a colaboradores que não são membros do desenvolvimento ou da equipe de ti geral. Eles revisam o uso e a manutenção de certificados, chaves e segredos para garantir a conformidade com os padrões de segurança. 

Há outra função que está fora do escopo do nosso aplicativo: o administrador da assinatura (ou grupo de recursos). O administrador da assinatura configura as permissões de acesso inicial para a equipe de segurança. Eles concedem acesso à equipe de segurança usando um grupo de recursos que tem os recursos exigidos pelo aplicativo.

Precisamos autorizar as seguintes operações para nossas funções:

**Equipa de segurança**
- Crie cofres de chaves.
- Ative o log de Key Vault.
- Adicionar chaves e segredos.
- Crie backups de chaves para recuperação de desastres.
- Defina Key Vault políticas de acesso para conceder permissões a usuários e aplicativos para operações específicas.
- Role as chaves e os segredos periodicamente.

**Desenvolvedores e operadores**
- Obtenha referências da equipe de segurança para os certificados Bootstrap e SSL (impressões digitais), a chave de armazenamento (URI secreto) e a chave RSA (URI de chave) para assinatura.
- Desenvolva e implante o aplicativo para acessar chaves e segredos de forma programática.

**Auditores**
- Examine os logs de Key Vault para confirmar o uso adequado de chaves e segredos e a conformidade com os padrões de segurança de dados.

A tabela a seguir resume as permissões de acesso para nossas funções e aplicativos. 

| Role | Permissões do plano de gestão | Permissões do plano de dados |
| --- | --- | --- |
| Equipa de segurança | Contribuidor do Key Vault | Chaves: criar cópia de segurança, criar, eliminar, obter, importar, listar, restaurar<br>Segredos: todas as operações |
| Desenvolvedores e&nbsp;operadores | Permissão de implantação de Key Vault<br><br> **Nota**: Essa permissão permite que as VMs implantadas busquem segredos de um cofre de chaves. | Nenhum |
| Auditores | Nenhum | Chaves: listar<br>Segredos: listar<br><br> **Nota**: Essa permissão permite que auditores inspecionem atributos (marcas, datas de ativação, datas de expiração) para chaves e segredos não emitidos nos logs. |
| Aplicação | Nenhum | Chaves: assinar<br>Segredos: obter |

As três funções de equipe precisam de acesso a outros recursos, juntamente com as permissões de Key Vault. Para implantar VMS (ou o recurso aplicativos Web do serviço Azure app), os desenvolvedores e operadores `Contributor` precisam acessar esses tipos de recursos. Os auditores precisam de acesso de leitura à conta de armazenamento em que os logs de Key Vault são armazenados.

Para obter mais informações sobre como implantar certificados, chaves de acesso e segredos de forma programática, consulte estes recursos:
- Saiba como [implantar certificados em VMs de um cofre de chaves gerenciado pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (postagem de blog).
- Baixe os [exemplos de cliente Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Este conteúdo ilustra como usar um certificado de inicialização para se autenticar no Azure AD para acessar um cofre de chaves.

Você pode conceder a maioria das permissões de acesso usando o portal do Azure. Para conceder permissões granulares, você pode usar Azure PowerShell ou o CLI do Azure.

Os trechos de código do PowerShell nesta seção são criados com as seguintes suposições:
- O administrador do Azure AD criou grupos de segurança para representar as três funções: Equipe de segurança da Contoso, contoso app DevOps e auditores de aplicativos da contoso. O administrador adicionou usuários a seus respectivos grupos.
- Todos os recursos estão localizados no grupo de recursos **ContosoAppRG** .
- Os logs de Key Vault são armazenados na conta de armazenamento **contosologstorage** . 
- O cofre de chaves **ContosoKeyVault** e a conta de armazenamento **contosologstorage** estão no mesmo local do Azure.

O administrador da assinatura atribui as `key vault Contributor` funções e `User Access Administrator` à equipe de segurança. Essas funções permitem que a equipe de segurança gerencie o acesso a outros recursos e cofres de chaves, ambos no grupo de recursos **ContosoAppRG** .

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A equipe de segurança cria um cofre de chaves e configura as permissões de registro e acesso. Para obter detalhes sobre as permissões de política de acesso Key Vault, consulte [sobre Azure Key Vault chaves, segredos e certificados](about-keys-secrets-and-certificates.md).

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

Nossas funções personalizadas definidas são atribuíveis somente à assinatura em que o grupo de recursos **ContosoAppRG** é criado. Para usar uma função personalizada para outros projetos em outras assinaturas, adicione outras assinaturas ao escopo para a função.

Para nossa equipe do DevOps, a atribuição de função personalizada para a `deploy/action` permissão do cofre de chaves tem como escopo o grupo de recursos. Somente as VMs criadas no grupo de recursos **ContosoAppRG** têm permissão para acessar os segredos (SSL e certificados de Bootstrap). As VMs criadas em outros grupos de recursos por um membro do DevOps não podem acessar esses segredos, mesmo que a VM tenha os URIs secretos.

Nosso exemplo descreve um cenário simples. Cenários de vida real podem ser mais complexos. Você pode ajustar as permissões para o cofre de chaves com base em suas necessidades. Assumimos que a equipe de segurança fornece as referências de chave e segredo (URIs e impressões digitais), que são usadas pela equipe de DevOps em seus aplicativos. Os desenvolvedores e operadores não exigem nenhum acesso ao plano de dados. Nos concentramos em como proteger seu cofre de chaves. Dê uma consideração semelhante ao proteger [suas VMs](https://azure.microsoft.com/services/virtual-machines/security/), [contas de armazenamento](../storage/common/storage-security-guide.md)e outros recursos do Azure.

> [!NOTE]
> Este exemplo mostra como Key Vault acesso é bloqueado na produção. Os desenvolvedores devem ter sua própria assinatura ou grupo de recursos com permissões totais para gerenciar seus cofres, VMs e a conta de armazenamento onde desenvolvem o aplicativo.

Recomendamos que você configure o acesso seguro adicional ao cofre de chaves [configurando Key Vault firewalls e redes virtuais](key-vault-network-security.md).

## <a name="resources"></a>Recursos

* [RBAC do Azure AD](../role-based-access-control/role-assignments-portal.md)

* [RBAC Funções internas](../role-based-access-control/built-in-roles.md)

* [Entender a implantação do Resource Manager e a implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Gerenciar RBAC com Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Gerenciar o RBAC com a API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC para Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Este vídeo 2015 do Microsoft Ignite Conference discute os recursos de gerenciamento e relatórios de acesso no Azure. Ele também explora as práticas recomendadas para proteger o acesso às assinaturas do Azure usando o Azure AD.

* [Autorizar o acesso a aplicativos Web usando o OAuth 2,0 e o Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)

* [APIs REST de gerenciamento de Key Vault](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A referência para as APIs REST para gerenciar o cofre de chaves programaticamente, incluindo a configuração Key Vault política de acesso.

* [Key Vault APIs REST](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Controlo de acesso a chaves](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Controlo de acesso a segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Definir](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) e [remover](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) Key Vault política de acesso usando o PowerShell.
  
## <a name="next-steps"></a>Passos seguintes

Configure [Key Vault firewalls e redes virtuais](key-vault-network-security.md).

Para obter um tutorial de introdução para um administrador, consulte [o que é Azure Key Vault?](key-vault-overview.md).

Para obter mais informações sobre o registo de utilização do Key Vault, veja [Registo do Azure Key Vault](key-vault-logging.md).

Para obter mais informações sobre como usar chaves e segredos com Azure Key Vault, consulte [sobre chaves e segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Se você tiver dúvidas sobre Key Vault, visite os [fóruns](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
