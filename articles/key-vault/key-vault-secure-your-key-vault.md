---
title: Acesso seguro a um cofre chave - Cofre chave Azure / Microsoft Docs
description: Gerencie permissões de acesso para cofre de chaves Azure, chaves e segredos. Cobre o modelo de autenticação e autorização para o Cofre chave e como proteger o cofre da chave.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: eac3850cfa0684bd1751cf7b88b4ff8e92667293
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197441"
---
# <a name="secure-access-to-a-key-vault"></a>Acesso seguro a um cofre chave

Azure Key Vault é um serviço na nuvem que salvaguarda chaves de encriptação e segredos como certificados, cordas de ligação e senhas. Como estes dados são sensíveis e críticos de negócios, é necessário garantir o acesso aos seus cofres chave, permitindo apenas aplicações e utilizadores autorizados. Este artigo fornece uma visão geral do modelo de acesso ao Cofre Chave. Explica a autenticação e a autorização e descreve como garantir o acesso aos seus cofres chave.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Visão geral do modelo de acesso

O acesso a um cofre chave é controlado através de duas interfaces: o plano de **gestão** e o plano de **dados.** O avião de gestão é onde geres o Cofre Chave. As operações neste avião incluem a criação e a eliminar cofres chave, recuperar propriedades do Key Vault e atualizar as políticas de acesso. O avião de dados é onde trabalha com os dados armazenados num cofre chave. Pode adicionar, eliminar e modificar chaves, segredos e certificados.

Para aceder a um cofre chave em qualquer um dos planos, todos os chamadores (utilizadores ou aplicações) devem ter autenticação e autorização adequadas. A autenticação estabelece a identidade do chamador. A autorização determina quais as operações que o chamador pode executar.

Ambos os aviões utilizam o Azure Ative Directory (Azure AD) para autenticação. Para autorização, o plano de gestão utiliza controlo de acesso baseado em funções (RBAC) e o plano de dados utiliza uma política de acesso ao Cofre chave.

## <a name="active-directory-authentication"></a>Autenticação ativa do Diretório

Quando se cria um cofre chave numa subscrição Azure, está automaticamente associado ao inquilino da AD Azure da subscrição. Todos os chamadores em ambos os aviões devem registar-se neste inquilino e autenticar para aceder ao cofre chave. Em ambos os casos, as aplicações podem aceder ao Key Vault de duas formas:

- **Acesso ao utilizador mais aplicação**: A aplicação acede ao Key Vault em nome de um utilizador inscrito. Exemplos deste tipo de acesso incluem o Azure PowerShell e o portal Azure. O acesso ao utilizador é concedido de duas formas. Os utilizadores podem aceder ao Key Vault a partir de qualquer aplicação, ou devem utilizar uma aplicação específica (referida como _identidade composta)._
- **Acesso apenas à aplicação**: A aplicação funciona como um serviço de daemon ou trabalho de fundo. A identidade da aplicação tem acesso ao cofre chave.

Para ambos os tipos de acesso, a aplicação autentica-se com AD Azure. A aplicação utiliza qualquer método de [autenticação suportado](../active-directory/develop/authentication-scenarios.md) com base no tipo de aplicação. A aplicação adquire um símbolo para um recurso no avião para conceder acesso. O recurso é um ponto final na gestão ou plano de dados, com base no ambiente Azure. A aplicação utiliza o símbolo e envia um pedido de API REST para o Cofre chave. Para saber mais, reveja todo o fluxo de [autenticação.](../active-directory/develop/v2-oauth2-auth-code-flow.md)

O modelo de um único mecanismo de autenticação a ambos os aviões tem vários benefícios:

- As organizações podem controlar o acesso central a todos os cofres chave da sua organização.
- Se um utilizador sair, perde instantaneamente o acesso a todos os cofres chave da organização.
- As organizações podem personalizar a autenticação utilizando as opções em AD Azure, de modo a permitir a autenticação de vários fatores para uma maior segurança.

## <a name="resource-endpoints"></a>Pontos finais de recursos

As aplicações acedem aos aviões através de pontos finais. Os controlos de acesso dos dois aviões funcionam de forma independente. Para conceder acesso a uma aplicação para usar chaves num cofre chave, você concede acesso de avião de dados usando uma política de acesso chave vault. Para conceder a um utilizador o acesso às propriedades e etiquetas key Vault, mas não acesso a dados (chaves, segredos ou certificados), concede acesso de avião de gestão com RBAC.

A tabela que se segue mostra os pontos finais para os planos de gestão e dados.

| Aviões&nbsp;de acesso | Pontos finais de acesso | Operações | Mecanismo de controlo&nbsp;de acesso |
| --- | --- | --- | --- |
| Plano de gestão | **Global:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> management.microsoftazure.de:443 | Criar, ler, atualizar e eliminar cofres chave<br><br>Definir políticas de acesso ao cofre chave<br><br>Definir etiquetas de cofre de chave | Gestor de Recursos Azure RBAC |
| Plano de dados | **Global:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Alemanha:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Chaves: desencriptar, encriptar,<br> desembrulhar, embrulhar, verificar, assinar,<br> obter, lista, atualizar, criar,<br> importar, eliminar, backup, restaurar<br><br> Segredos: obter, listar, definir, excluir | Política de acesso ao cofre chave |

## <a name="management-plane-and-rbac"></a>Plano de gestão e RBAC

No plano de gestão, utiliza o RBAC (Controlo de Acesso Baseado em Funções) para autorizar as operações que um chamador pode executar. No modelo RBAC, cada subscrição do Azure tem uma instância de Azure AD. Concede acesso a utilizadores, grupos e aplicações deste diretório. É concedido acesso para gerir recursos na subscrição azure que utilizam o modelo de implementação do Gestor de Recursos Azure. Para conceder acesso, utilize o [portal Azure,](https://portal.azure.com/)o [Azure CLI,](../cli-install-nodejs.md) [o Azure PowerShell](/powershell/azureps-cmdlets-docs)ou o Gestor de [Recursos Azure REST APIs](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Cria-se um cofre chave num grupo de recursos e gere-se o acesso utilizando o Azure AD. Você concede aos utilizadores ou grupos a capacidade de gerir os cofres chave em um grupo de recursos. Concede o acesso a um nível específico de âmbito, atribuindo funções rBAC adequadas. Para conceder acesso a um utilizador para gerir cofres chave, atribui um papel `key vault Contributor` predefinido ao utilizador num âmbito específico. Os seguintes níveis de âmbito podem ser atribuídos a uma função RBAC:

- **Subscrição**: Uma função RBAC atribuída ao nível de subscrição aplica-se a todos os grupos e recursos de recursos dentro dessa subscrição.
- **Grupo de recursos**: Uma função RBAC atribuída ao nível do grupo de recursos aplica-se a todos os recursos desse grupo de recursos.
- **Recurso específico**: Uma função RBAC atribuída a um recurso específico aplica-se a esse recurso. Neste caso, o recurso é um cofre específico.

Há vários papéis predefinidos. Se um papel predefinido não se adequa às suas necessidades, pode definir o seu próprio papel. Para mais informações, consulte [As funções RBAC: Incorporadas.](../role-based-access-control/built-in-roles.md)

> [!IMPORTANT]
> Se um utilizador tiver `Contributor` permissões para um avião de gestão de cofres chave, o utilizador pode conceder-se acesso ao plano de dados definindo uma política de acesso ao Cofre chave. Devias controlar bem quem tem acesso `Contributor` papel aos teus cofres chave. Certifique-se de que apenas pessoas autorizadas podem aceder e gerir os seus cofres chave, chaves, segredos e certificados.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Políticas de plano de dados e de acesso

Concede-se acesso a planos de dados, definindo as políticas de acesso ao Cofre chave para um cofre chave. Para definir estas políticas de acesso, um utilizador, grupo ou aplicação deve ter permissões `Contributor` para o plano de gestão para esse cofre chave.

Concede a um utilizador, grupo ou aplicação acesso para executar operações específicas para chaves ou segredos num cofre chave. O Cofre Chave suporta até 1.024 entradas de política de acesso para um cofre chave. Para conceder acesso de avião de dados a vários utilizadores, crie um grupo de segurança Azure AD e adicione utilizadores a esse grupo.

<a id="key-vault-access-policies"></a>As políticas de acesso ao Cofre Chave concedem permissões separadamente a chaves, segredos e certificados. Só pode conceder acesso a um utilizador a chaves e não a segredos. Permissões de acesso a chaves, segredos e certificados estão ao nível do cofre. As políticas de acesso ao Cofre Chave não suportam permissões granulares, ao nível do objeto, como uma chave específica, segredo ou certificado. Para definir as políticas de acesso para um cofre chave, utilize o [portal Azure,](https://portal.azure.com/)o [Azure CLI,](../cli-install-nodejs.md) [o Azure PowerShell](/powershell/azureps-cmdlets-docs)ou o [Key Vault Management REST APIs](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> As políticas de acesso ao cofre aplicam-se ao nível do cofre. Quando um utilizador tem permissão para criar e apagar chaves, pode efetuar essas operações em todas as teclas do cofre da chave.
>

Pode restringir o acesso ao avião de dados utilizando [pontos finais](key-vault-overview-vnet-service-endpoints.md)de serviço de rede virtual para o Cofre de Chaves Azure . Pode configurar [firewalls e regras de rede virtuais](key-vault-network-security.md) para uma camada adicional de segurança.

## <a name="example"></a>Exemplo

Neste exemplo, estamos a desenvolver uma aplicação que utiliza um certificado para TLS/SSL, Armazenamento Azure para armazenar dados, e uma chave RSA de 2.048 bits para operações de sinalização. A nossa aplicação funciona numa máquina virtual Azure (VM) (ou num conjunto de escala de máquina virtual). Podemos usar um cofre chave para guardar os segredos da aplicação. Podemos armazenar o certificado de bootstrap que é usado pela aplicação para autenticar com a Azure AD.

Precisamos de acesso às seguintes chaves e segredos armazenados:
- **Certificado TLS/SSL**: Utilizado para TLS/SSL.
- **Chave de armazenamento**: Utilizado para aceder à conta de armazenamento.
- **Chave RSA 2.048 bits**: Utilizada para operações de sinalização.
- **Certificado de armadilha**de botas : Usado para autenticar com Azure AD. Após o acesso concedido, podemos pegar na chave de armazenamento e usar a chave RSA para assinar.

Precisamos definir as seguintes funções para especificar quem pode gerir, implementar e auditar a nossa aplicação:
- **Equipa de segurança**: Pessoal de TI do escritório do CSO (Chief Security Officer) ou colaboradores semelhantes. A equipa de segurança é responsável pela segurança dos segredos. Os segredos podem incluir certificados TLS/SSL, chaves RSA para assinatura, cordas de ligação e chaves de conta de armazenamento.
- **Desenvolvedores e operadores**: O pessoal que desenvolve a aplicação e a implanta no Azure. Os membros desta equipa não fazem parte do pessoal de segurança. Não devem ter acesso a dados sensíveis como certificados TLS/SSL e chaves RSA. Só a aplicação que implementam deve ter acesso a dados sensíveis.
- **Auditores**: Este papel é para contribuintes que não são membros do desenvolvimento ou do pessoal geral de TI. Eles revêem a utilização e manutenção de certificados, chaves e segredos para garantir o cumprimento das normas de segurança.

Há outra função que está fora do âmbito da nossa aplicação: o administrador de subscrição (ou grupo de recursos). O administrador de subscrição configura as permissões de acesso iniciais para a equipa de segurança. Concedem acesso à equipa de segurança utilizando um grupo de recursos que dispõe dos recursos exigidos pela aplicação.

Temos de autorizar as seguintes operações para as nossas funções:

**Equipa de segurança**
- Crie cofres chave.
- Ligue a exploração do cofre chave.
- Adicione chaves e segredos.
- Crie cópias de segurança das chaves para a recuperação de desastres.
- Detete as políticas de acesso ao Cofre Chave para conceder permissões aos utilizadores e aplicações para operações específicas.
- Enrole as chaves e segredos periodicamente.

**Desenvolvedores e operadores**
- Obtenha referências da equipa de segurança para as botas e certificados TLS/SSL (impressões digitais), chave de armazenamento (URI secreta) e chave RSA (chave URI) para a assinatura.
- Desenvolver e implementar a aplicação para aceder programaticamente a chaves e segredos.

**Auditores**
- Reveja os registos do Cofre chave para confirmar o uso adequado de chaves e segredos e o cumprimento das normas de segurança de dados.

A tabela seguinte resume as permissões de acesso para as nossas funções e aplicação.

| Função | Permissões do plano de gestão | Permissões do plano de dados |
| --- | --- | --- |
| Equipa de segurança | Contribuinte chave do cofre | Chaves: criar cópia de segurança, criar, eliminar, obter, importar, listar, restaurar<br>Segredos: todas as operações |
| Desenvolvedores e operadores de&nbsp; | Autorização de implantação do cofre chave<br><br> **Nota:** Esta permissão permite que os VMs implantados apanhem segredos de um cofre chave. | Nenhum |
| Auditores | Nenhum | Chaves: listar<br>Segredos: listar<br><br> **Nota:** Esta permissão permite que os auditores inspecionem atributos (tags, datas de ativação, datas de validade) para chaves e segredos não emitidos nos registos. |
| Aplicação | Nenhum | Chaves: assinar<br>Segredos: obter |

As três funções de equipa precisam de acesso a outros recursos, juntamente com permissões do Cofre Chave. Para implementar VMs (ou a funcionalidade de Aplicações Web do Azure App Service), os desenvolvedores e operadores precisam `Contributor` acesso a esses tipos de recursos. Os auditores precisam de ler o acesso à conta de armazenamento onde os registos do Cofre chave estão armazenados.

Para obter mais informações sobre como implementar certificados, chaves de acesso e segredos programáticamente, consulte estes recursos:
- Saiba como [implementar certificados para VMs a partir de um cofre de chaves gerido pelo cliente](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (post de blog).
- Descarregue as amostras do cliente do [Cofre chave Azure.](https://www.microsoft.com/download/details.aspx?id=45343) Este conteúdo ilustra como usar um certificado de bootstrap para autenticar a Azure AD para aceder a um cofre chave.

Pode conceder a maioria das permissões de acesso utilizando o portal Azure. Para conceder permissões granulares, pode utilizar o Azure PowerShell ou o Azure CLI.

Os cortes PowerShell nesta secção são construídos com as seguintes suposições:
- O administrador da AD Azure criou grupos de segurança para representar as três funções: Equipa de Segurança Contoso, Contoso App DevOps e Auditores de Aplicações Contoso. O administrador adicionou utilizadores aos respetivos grupos.
- Todos os recursos estão localizados no grupo de recursos **ContosoAppRG.**
- Os registos key vault são armazenados na conta de armazenamento de **contosolog.**
- O cofre chave **ContosoKeyVault** e a conta de armazenamento de **armazenamento de contosolog** estão na mesma localização Azure.

O administrador de subscrição atribui as funções de `key vault Contributor` e `User Access Administrator` à equipa de segurança. Estas funções permitem à equipa de segurança gerir o acesso a outros recursos e cofres chave, ambos no grupo de recursos **ContosoAppRG.**

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

A equipa de segurança cria um cofre chave e configura permissões de registo e acesso. Para mais detalhes sobre as permissões da política de acesso ao Cofre chave, consulte [as chaves, segredos e certificados do Cofre chave Azure.](about-keys-secrets-and-certificates.md)

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

As nossas funções personalizadas definidas são atribuídas apenas à subscrição onde o grupo de recursos **ContosoAppRG** é criado. Para utilizar uma função personalizada para outros projetos noutras subscrições, adicione outras subscrições ao âmbito para o papel.

Para a nossa equipa de DevOps, a atribuição de funções personalizadas para o cofre chave `deploy/action` a permissão é consultada ao grupo de recursos. Apenas vMs criados no grupo de recursos **ContosoAppRG** têm acesso aos segredos (TLS/SSL e certificados de bootstrap). VMs criados em outros grupos de recursos por um membro da DevOps não podem aceder a estes segredos, mesmo que o VM tenha os URIs secretos.

O nosso exemplo descreve um cenário simples. Cenários da vida real podem ser mais complexos. Pode ajustar permissões no seu cofre com base nas suas necessidades. Assumimos que a equipa de segurança fornece as referências secretas e chave (URIs e impressões digitais), que são usadas pelo pessoal da DevOps nas suas aplicações. Os desenvolvedores e operadores não requerem acesso a planos de dados. Concentrámo-nos em como proteger o cofre da chave. Considere o mesmo quando protege [os seus VMs,](https://azure.microsoft.com/services/virtual-machines/security/) [contas de armazenamento](../storage/blobs/security-recommendations.md)e outros recursos Azure.

> [!NOTE]
> Este exemplo mostra como o acesso ao Cofre Chave está bloqueado na produção. Os desenvolvedores devem ter a sua própria subscrição ou grupo de recursos com permissões completas para gerir os seus cofres, VMs e a conta de armazenamento onde desenvolvem a aplicação.

Recomendamos que configure um acesso adicional seguro ao seu cofre chave [configurando firewalls key vault e redes virtuais](key-vault-network-security.md).

## <a name="resources"></a>Recursos

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Funções incorporadas](../role-based-access-control/built-in-roles.md)

* [Compreender a implementação do Gestor de Recursos e a implantação clássica](../azure-resource-manager/management/deployment-models.md)

* [Gerir o RBAC com a Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)

* [Gerir o RBAC com a API REST](../role-based-access-control/role-assignments-rest.md)

* [RBAC para Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Este vídeo da conferência Microsoft Ignite de 2015 discute capacidades de gestão de acesso e reporte no Azure. Também explora as melhores práticas para garantir o acesso às subscrições do Azure utilizando o Azure AD.

* [Autorizar acesso a aplicações web utilizando AAuth 2.0 e Azure AD](../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [APIs de gestão do cofre chave](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    A referência para as APIs rest para gerir o seu cofre chave programáticamente, incluindo definir a política de acesso ao Cofre Chave.

* [Cofre chave REST APIs](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Controlo de acesso a chaves](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Controlo de acesso a segredos](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Desmontar](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) e [remover](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) a política de acesso ao Cofre chave utilizando o PowerShell.

## <a name="next-steps"></a>Passos seguintes

Configure [firewalls de cofre](key-vault-network-security.md)chave e redes virtuais .

Para um tutorial de início para um administrador, veja [o que é o Cofre chave Azure?](key-vault-overview.md)

Para obter mais informações sobre o registo de utilização do Key Vault, veja [Registo do Azure Key Vault](key-vault-logging.md).

Para mais informações sobre o uso de chaves e segredos com o Cofre de Chaves Azure, consulte [chaves e segredos.](https://msdn.microsoft.com/library/azure/dn903623.aspx)

Se tiver dúvidas sobre o Cofre chave, visite os [fóruns.](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)
