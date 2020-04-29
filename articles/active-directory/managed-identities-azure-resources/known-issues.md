---
title: FAQs e questões conhecidas com identidades geridas - Azure AD
description: Questões conhecidas com identidades geridas para recursos Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79266536"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>FaQs e questões conhecidas com identidades geridas para recursos Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (Perguntas)

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>As identidades geridas para os recursos azure funcionam com a Azure Cloud Services?

Não, não há planos para apoiar identidades geridas para os recursos Azure nos Serviços Azure Cloud.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>As identidades geridas para os recursos do Azure funcionam com a Biblioteca de Autenticação do Diretório Ativo (ADAL) ou com a Biblioteca de Autenticação da Microsoft (MSAL)?

Não, as identidades geridas para os recursos Azure ainda não estão integradas com a ADAL ou a MSAL. Para obter mais detalhes sobre a aquisição de um símbolo para identidades geridas para os recursos Azure utilizando o ponto final do REST, consulte [Como utilizar identidades geridas para os recursos Azure num Azure VM para adquirir um sinal de acesso.](how-to-use-vm-token.md)

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Qual é o limite de segurança das identidades geridas para os recursos do Azure?

O limite de segurança da identidade é o recurso a que está ligado. Por exemplo, o limite de segurança de uma Máquina Virtual com identidades geridas para os recursos Azure ativados, é a Máquina Virtual. Qualquer código em execução nesse VM, é capaz de ligar para as identidades geridas para o ponto final dos recursos Azure e solicitar fichas. É a experiência semelhante com outros recursos que suportam identidades geridas para os recursos do Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Qual a identidade a que o IMDS irá desacarinado se não especificar a identidade no pedido?

- Se a identidade gerida atribuída ao sistema for ativada e não for especificada qualquer identidade no pedido, o IMDS irá desativar-se no sistema atribuído à identidade gerida atribuída.
- Se o sistema atribuído à identidade gerida não estiver ativado e apenas existir uma identidade gerida atribuída ao utilizador, o IMDS irá desempendê-lo a essa identidade gerida atribuída ao único utilizador. 
- Se o sistema atribuído à identidade gerida não estiver ativado e existirem múltiplas identidades geridas por utilizador, é necessário especificar uma identidade gerida no pedido.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Devo utilizar as identidades geridas para os recursos Azure IMDS endpoint ou o ponto final de extensão VM?

Ao utilizar identidades geridas para recursos Azure com VMs, recomendamos a utilização do ponto final do IMDS. O Serviço de Metadados de Instância Seletiva Azure é um ponto final REST acessível a todos os VMs IaaS criados através do Gestor de Recursos Azure. 

Alguns dos benefícios da utilização de identidades geridas para os recursos do Azure sobre o IMDS são:
- Todos os sistemas operativos apoiados pelo Azure IaaS podem utilizar identidades geridas para recursos Azure em vez do IMDS.
- Já não é necessário instalar uma extensão no seu VM para permitir identidades geridas para os recursos Azure. 
- Os certificados utilizados por identidades geridas para os recursos Azure já não estão presentes no VM.
- O ponto final do IMDS é um conhecido endereço IP não resaída, disponível apenas a partir do VM.
- 1000 identidades geridas atribuídas ao utilizador podem ser atribuídas a um único VM. 

As identidades geridas para a extensão VM de recursos Azure ainda estão disponíveis; no entanto, já não estamos a desenvolver novas funcionalidades sobre o mesmo. Recomendamos que se altere para utilizar o ponto final do IMDS. 

Algumas das limitações da utilização do ponto final da extensão VM são:
- Suporte limitado para distribuição de Linux: CoreOS Estável, CentOS 7.1, Chapéu Vermelho 7.2, Ubuntu 15.04, Ubuntu 16.04
- Apenas 32 identidades geridas atribuídas ao utilizador podem ser atribuídas ao VM.


Nota: As identidades geridas para a extensão vm de recursos Azure estarão fora de apoio em janeiro de 2019. 

Para mais informações sobre o Serviço de Metadados de Caso Sino Do Azure, consulte a [documentação do IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>As identidades geridas serão recriadas automaticamente se eu mover uma subscrição para outro diretório?

Não. Se transferir uma subscrição para outro diretório, terá de recriá-las manualmente e conceder atribuições de funções ao Azure RBAC novamente.
- Para o sistema atribuído identidades geridas: desativar e reativar. 
- Para as identidades geridas atribuídas ao utilizador: eliminar, recriar e anexá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Posso usar uma identidade gerida para aceder a um recurso num diretório/inquilino diferente?

Não. As identidades geridas não suportam atualmente cenários transversais. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Que permissões Azure RBAC são necessárias para gerir a identidade num recurso? 

- Identidade gerida atribuída pelo sistema: Precisa de permissões de escrita sobre o recurso. Por exemplo, para máquinas virtuais, precisa de Microsoft.Compute/virtualMachines/write. Esta ação está incluída em funções específicas de recursos incorporados como [o Virtual Machine Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).
- Identidade gerida atribuída ao utilizador: Precisa de permissões de escrita sobre o recurso. Por exemplo, para máquinas virtuais, precisa de Microsoft.Compute/virtualMachines/write. Para além da atribuição de funções de [Operador de Identidade Gerida](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) sobre a identidade gerida.

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Como reiniciar as identidades geridas para a extensão de recursos do Azure?
No Windows e em certas versões do Linux, se a extensão parar, o seguinte cmdlet pode ser utilizado para reiniciá-lo manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- Nome e tipo de extensão para Windows é: ManagedIdentityExtensionForWindows
- Nome e tipo de extensão para Linux é: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Roteiro de automação" falha ao tentar exportar esquemas para identidades geridas para extensão de recursos do Azure

Quando as identidades geridas para os recursos Azure são ativadas num VM, o seguinte erro é demonstrado ao tentar utilizar a função "Automação script" para o VM, ou o seu grupo de recursos:

![Identidades geridas para erro de exportação de script de automação de recursos Do Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades geridas para a extensão vm de recursos Azure (prevista para a depreciação em janeiro de 2019) não suportam atualmente a capacidade de exportar o seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra parâmetros de configuração para permitir identidades geridas para recursos Azure no recurso. Estas secções podem ser adicionadas manualmente seguindo os exemplos em [Identidades geridas pela Configure para os recursos Azure num VM Azure utilizando um modelo](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquemas estiver disponível para as identidades geridas para a extensão VM dos recursos Azure (prevista para a depreciação em janeiro de 2019), será listada em Grupos de [Recursos Exportadores que contenham extensões VM.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM não começa depois de ser movido do grupo de recursos ou subscrição

Se mover um VM no estado de corrida, ele continua a funcionar durante a mudança. No entanto, após a mudança, se o VM for parado e reiniciado, não terá início. Esta questão acontece porque o VM não está a atualizar a referência às identidades geridas para a identidade dos recursos do Azure e continua a apontá-la no antigo grupo de recursos.

**Supor** 
 
Desencadear uma atualização sobre o VM para que possa obter valores corretos para as identidades geridas para os recursos Azure. Pode fazer uma alteração de propriedade VM para atualizar a referência às identidades geridas para a identidade dos recursos Azure. Por exemplo, pode definir um novo valor de etiqueta no VM com o seguinte comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova etiqueta "fixVM" com um valor de 1 no VM. 
 
Ao definir esta propriedade, o VM atualiza com as identidades geridas corretas para o recurso de recursos Azure URI, e então você deve ser capaz de iniciar o VM. 
 
Uma vez iniciado o VM, a etiqueta pode ser removida utilizando o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Falha no fornecimento de extensão VM

O fornecimento da extensão VM pode falhar devido a falhas de procura do DNS. Reinicie o VM e tente de novo.
 
> [!NOTE]
> A extensão vm está prevista para ser depreciada até janeiro de 2019. Recomendamos que se mova para a utilização do ponto final do IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferência de uma subscrição entre diretórios da AD Azure

As identidades geridas não são atualizadas quando uma subscrição é transferida/transferida para outro diretório. Como resultado, quaisquer identidades geridas atribuídas pelo sistema ou atribuídas ao utilizador serão quebradas. 

Seleção para identidades geridas numa subscrição que foi transferida para outro diretório:

 - Para o sistema atribuído identidades geridas: desativar e reativar. 
 - Para as identidades geridas atribuídas ao utilizador: eliminar, recriar e anexá-las novamente aos recursos necessários (por exemplo, máquinas virtuais)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Mover uma identidade gerida atribuída ao utilizador para um grupo/subscrição diferente

Mover uma identidade gerida atribuída ao utilizador para um grupo de recursos diferente fará com que a identidade se rompa. Como resultado, os recursos (por exemplo, VM) que utilizam essa identidade não poderão solicitar fichas para a sua identidade. 
