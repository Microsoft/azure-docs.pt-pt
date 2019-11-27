---
title: Perguntas frequentes e problemas conhecidos com identidades gerenciadas-Azure AD
description: Problemas conhecidos com identidades gerenciadas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f0c678f2426d9de58d2ab337c56243394b4d0f
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74183876"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Perguntas frequentes e problemas conhecidos com identidades gerenciadas para recursos do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas Mais Frequentes (FAQs)

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades gerenciadas para recursos do Azure funcionam com os serviços de nuvem do Azure?

Não, não há planos para dar suporte a identidades gerenciadas para recursos do Azure nos serviços de nuvem do Azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>As identidades gerenciadas para recursos do Azure funcionam com a ADAL (Biblioteca de Autenticação do Active Directory) ou a biblioteca de autenticação da Microsoft (MSAL)?

Não, as identidades gerenciadas dos recursos do Azure ainda não estão integradas ao ADAL ou MSAL. Para obter detalhes sobre como adquirir um token para identidades gerenciadas para recursos do Azure usando o ponto de extremidade REST, consulte [como usar identidades gerenciadas para recursos do Azure em uma VM do Azure para adquirir um token de acesso](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Qual é o limite de segurança de identidades gerenciadas para recursos do Azure?

O limite de segurança da identidade é o recurso ao qual ele está anexado. Por exemplo, o limite de segurança para uma máquina virtual com identidades gerenciadas para recursos do Azure habilitados é a máquina virtual. Qualquer código em execução nessa VM, é capaz de chamar as identidades gerenciadas para o ponto de extremidade de recursos do Azure e solicitar tokens. É a experiência semelhante com outros recursos que dão suporte a identidades gerenciadas para recursos do Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Qual identidade será IMDSda por padrão se não especificar a identidade na solicitação?

- Se a identidade gerenciada atribuída pelo sistema estiver habilitada e nenhuma identidade for especificada na solicitação, IMDS usará como padrão a identidade gerenciada atribuída pelo sistema.
- Se a identidade gerenciada atribuída pelo sistema não estiver habilitada e apenas uma identidade gerenciada atribuída pelo usuário existir, o padrão IMDS será atribuído a essa identidade gerenciada atribuída por usuário único. 
- Se a identidade gerenciada atribuída pelo sistema não estiver habilitada, e várias identidades gerenciadas atribuídas ao usuário existirem, a especificação de uma identidade gerenciada na solicitação será necessária.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Devo usar as identidades gerenciadas para o ponto de extremidade IMDS de recursos do Azure ou o ponto de extremidade de extensão de VM?

Ao usar identidades gerenciadas para recursos do Azure com VMs, é recomendável usar o ponto de extremidade IMDS. O serviço de metadados de instância do Azure é um ponto de extremidade REST acessível a todas as VMs de IaaS criadas por meio do Azure Resource Manager. 

Alguns dos benefícios de usar identidades gerenciadas para recursos do Azure em IMDS são:
- Todos os sistemas operacionais com suporte do Azure IaaS podem usar identidades gerenciadas para recursos do Azure em IMDS.
- Não é mais necessário instalar uma extensão em sua VM para habilitar identidades gerenciadas para recursos do Azure. 
- Os certificados usados por identidades gerenciadas para os recursos do Azure não estão mais presentes na VM.
- O ponto de extremidade IMDS é um endereço IP não roteável bem conhecido, disponível somente de dentro da VM.
- 1000 identidades gerenciadas atribuídas pelo usuário podem ser atribuídas a uma única VM. 

As identidades gerenciadas para extensão de VM de recursos do Azure ainda estão disponíveis; no entanto, não estamos mais desenvolvendo novas funcionalidades nele. É recomendável alternar para usar o ponto de extremidade IMDS. 

Algumas das limitações do uso do ponto de extremidade de extensão de VM são:
- Suporte limitado para distribuições do Linux: CoreOS estável, CentOS 7,1, Red Hat 7,2, Ubuntu 15, 4, Ubuntu 16, 4
- Apenas 32 identidades gerenciadas atribuídas pelo usuário podem ser atribuídas à VM.


Observação: as identidades gerenciadas para extensão de VM de recursos do Azure ficarão sem suporte em janeiro de 2019. 

Para obter mais informações sobre o serviço de metadados de instância do Azure, consulte a [documentação do IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>As identidades gerenciadas serão recriadas automaticamente se eu mover uma assinatura para outro diretório?

Não. Se você mover uma assinatura para outro diretório, precisará recriá-las manualmente e conceder as atribuições de função do RBAC do Azure novamente.
- Para identidades gerenciadas atribuídas pelo sistema: Desabilite e habilite novamente. 
- Para identidades gerenciadas atribuídas pelo usuário: excluir, recriá-las e anexá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Posso usar uma identidade gerenciada para acessar um recurso em um diretório/locatário diferente?

Não. Atualmente, as identidades gerenciadas não dão suporte a cenários entre diretórios. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quais permissões do RBAC do Azure são necessárias para a identidade gerenciada em um recurso? 

- Identidade gerenciada atribuída pelo sistema: você precisa de permissões de gravação sobre o recurso. Por exemplo, para máquinas virtuais, você precisa do Microsoft. Compute/virtualMachines/Write. Essa ação é incluída em funções internas específicas do recurso, como [colaborador da máquina virtual](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Identidade gerenciada atribuída pelo usuário: você precisa de permissões de gravação sobre o recurso. Por exemplo, para máquinas virtuais, você precisa do Microsoft. Compute/virtualMachines/Write. Além da atribuição de função de [operador de identidade gerenciada](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) sobre a identidade gerenciada.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Como reiniciar as identidades gerenciadas para a extensão de recursos do Azure?
No Windows e em determinadas versões do Linux, se a extensão for interrompida, o cmdlet a seguir poderá ser usado para reiniciá-lo manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- O nome e o tipo da extensão para o Windows é: ManagedIdentityExtensionForWindows
- O nome e o tipo da extensão para o Linux é: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação" falha ao tentar exportar o esquema para identidades gerenciadas para extensão de recursos do Azure

Quando identidades gerenciadas para recursos do Azure são habilitadas em uma VM, o seguinte erro é mostrado ao tentar usar o recurso "script de automação" para a VM ou seu grupo de recursos:

![Identidades gerenciadas para erro de exportação de script de automação de recursos do Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades gerenciadas para extensão de VM de recursos do Azure (planejada para substituição em janeiro de 2019) atualmente não dão suporte à capacidade de exportar seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para habilitar identidades gerenciadas para recursos do Azure no recurso. Essas seções podem ser adicionadas manualmente seguindo os exemplos em [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando modelos](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema se tornar disponível para as identidades gerenciadas para a extensão de VM dos recursos do Azure (planejada para substituição em janeiro de 2019), ela será listada na [exportação dos grupos de recursos que contêm extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A VM não é iniciada após ser movida do grupo de recursos ou da assinatura

Se você mover uma VM no estado de execução, ela continuará sendo executada durante a movimentação. No entanto, após a movimentação, se a VM for interrompida e reiniciada, ela não será iniciada. Esse problema ocorre porque a VM não está atualizando a referência para as identidades gerenciadas para o Azure Resources Identity e continua a apontar para ele no grupo de recursos antigo.

**Solução** 
 
Dispare uma atualização na VM para que ela possa obter os valores corretos para as identidades gerenciadas dos recursos do Azure. Você pode fazer uma alteração de propriedade de VM para atualizar a referência para a identidade gerenciada de recursos do Azure. Por exemplo, você pode definir um novo valor de marca na VM com o seguinte comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Esse comando define uma nova marca "fixVM" com um valor de 1 na VM. 
 
Ao definir essa propriedade, a VM é atualizada com as identidades gerenciadas corretas para o URI de recurso de recursos do Azure e, em seguida, você deve ser capaz de iniciar a VM. 
 
Depois que a VM for iniciada, a marca poderá ser removida usando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Falha no provisionamento da extensão da VM

O provisionamento da extensão de VM pode falhar devido a falhas de pesquisa de DNS. Reinicie a VM e tente novamente.
 
> [!NOTE]
> A extensão de VM é planejada para substituição em janeiro de 2019. Recomendamos que você mova para o usando o ponto de extremidade IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferindo uma assinatura entre diretórios do Azure AD

Identidades gerenciadas não são atualizadas quando uma assinatura é movida/transferida para outro diretório. Como resultado, qualquer identidade gerenciada atribuída pelo sistema ou pelo usuário existente será interrompida. 

Solução alternativa para identidades gerenciadas em uma assinatura que foi movida para outro diretório:

 - Para identidades gerenciadas atribuídas pelo sistema: Desabilite e habilite novamente. 
 - Para identidades gerenciadas atribuídas pelo usuário: excluir, recriá-las e anexá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Movendo uma identidade gerenciada atribuída pelo usuário para um grupo de recursos/assinatura diferente

Mover uma identidade gerenciada atribuída pelo usuário para um grupo de recursos diferente fará com que a identidade seja interrompida. Como resultado, os recursos (por exemplo, VM) usando essa identidade não poderão solicitar tokens para ele. 
