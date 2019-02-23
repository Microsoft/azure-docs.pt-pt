---
title: FAQ e problemas conhecidos com identidades geridas para recursos do Azure
description: Problemas conhecidos com identidades geridas para recursos do Azure.
services: active-directory
documentationcenter: ''
author: priyamohanram
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
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1162eb7964c8ec40f2b342e33044b60385cbd5f6
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56727490"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>FAQ e problemas conhecidos com identidades geridas para recursos do Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Perguntas Mais Frequentes (FAQs)

> [!NOTE]
> Identidades geridas para recursos do Azure é o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerida (MSI).

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Identidades geridas para recursos do Azure funciona com serviços Cloud do Azure?

Não, não existem não existem planos para oferecer suporte a identidades geridas para recursos do Azure nos serviços de Cloud do Azure.

### <a name="does-managed-identities-for-azure-resources-work-with-the-active-directory-authentication-library-adal-or-the-microsoft-authentication-library-msal"></a>Identidades geridas para recursos do Azure funciona com o Active Directory Authentication Library (ADAL) ou o Microsoft Authentication Library (MSAL)?

Não, geridos identidades para recursos do Azure ainda não está integrado com o ADAL ou MSAL. Para obter detalhes sobre como adquirir um token para identidades geridas para recursos do Azure com o ponto final REST, veja [como utilizar identidades geridas para recursos do Azure numa VM do Azure para adquirir um token de acesso ](how-to-use-vm-token.md).

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>O que é o limite de segurança de identidades geridas para recursos do Azure?

O limite de segurança da identidade é o recurso ao qual está ligado a. Por exemplo, o limite de segurança para uma Máquina Virtual com identidades geridas para recursos do Azure ativado, é a Máquina Virtual. Qualquer código em execução nessa VM, é capaz de chamar as identidades geridas para recursos do Azure, tokens de ponto final e a pedido. É a experiência de semelhante com outros recursos que oferecem suporte a identidades geridas para recursos do Azure.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Identidade que será IMDS assumem como padrão se não especificar a identidade no pedido?

- Se o sistema atribuído a identidade gerida está ativado e nenhuma identidade é especificada no pedido, IMDS será predefinido para o sistema de identidade gerida atribuído.
- Se o sistema atribuído a identidade gerida não está ativado e existe apenas um utilizador atribuído a identidade gerida, IMDS será predefinido para esse utilizador único atribuído a identidade gerida. 
- Se o sistema atribuído a identidade gerida não está ativado e o utilizador várias identidades geridas de atribuído existe, em seguida, especificar uma identidade gerida no pedido é necessário.

### <a name="should-i-use-the-managed-identities-for-azure-resources-imds-endpoint-or-the-vm-extension-endpoint"></a>Deve utilizar as identidades geridas para o ponto final IMDS de recursos do Azure ou o ponto de final de extensão VM?

Quando utiliza identidades geridas para recursos do Azure com VMs, recomendamos que utilize o ponto de extremidade IMDS. O serviço de metadados de instância do Azure é um ponto final REST acessíveis a todas as VMs de IaaS criadas através do Azure Resource Manager. 

Algumas das vantagens da utilização de identidades geridas para recursos do Azure através de IMDS são:
    - Todos os sistemas de operativos de IaaS do Azure suportada pode utilizar identidades geridas para recursos do Azure através de IMDS.
    - Já não é necessário instalar uma extensão na sua VM para ativar identidades geridas para recursos do Azure. 
    - Os certificados utilizados por identidades geridas para recursos do Azure já não estão presentes na VM.
    - O ponto de extremidade IMDS é um endereço IP bem conhecido não encaminháveis internos, apenas disponível a partir da VM.
    - 1000 atribuído ao utilizador identidades geridas podem ser atribuídas a uma única VM. 

As identidades geridas para a extensão VM de recursos do Azure está ainda disponível. No entanto, estamos já não está desenvolvendo nova funcionalidade no mesmo. É recomendável mudar para utilizar o ponto final IMDS. 

Algumas das limitações da utilização do ponto de final de extensão da VM são:
    - Suporte limitado para distribuições do Linux: CoreOS Stable, CentOS 7.1, Red Hat 7.2, Ubuntu 15.04, Ubuntu 16.04
    - Apenas 32 atribuído ao utilizador identidades geridas podem ser atribuídas à VM.


Nota: As identidades geridas para a extensão VM de recursos do Azure será o suporte termina dentro de Janeiro de 2019. 

Para obter mais informações sobre o serviço de metadados de instância do Azure, consulte [documentação IMDS](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Serão identidades geridas recriadas automaticamente se mover uma subscrição para outro diretório?

Não. Se mover uma subscrição para outro diretório, terá de recriar manualmente-los e conceder novamente o atribuições de funções do RBAC do Azure.
    - Para sistema atribuídos identidades geridas: desative e volte a ativar. 
    - Para o utilizador atribuído identidades geridas: eliminar, voltar a criar e anexe-os novamente para os recursos necessários (por exemplo, as máquinas virtuais)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Pode utilizar uma identidade gerida para aceder a um recurso num diretório diferente/inquilino?

Não. Identidades geridas não suportam atualmente directory em vários cenários. 

### <a name="how-do-you-restart-the-managed-identities-for-azure-resources-extension"></a>Como reiniciar as identidades geridas para a extensão de recursos do Azure?
No Windows e determinadas versões do Linux, se a extensão de parar, o cmdlet seguinte pode ser usado para reiniciar manualmente:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Em que: 
- Nome de extensão e tipo para Windows é: ManagedIdentityExtensionForWindows
- Nome de extensão e o tipo para Linux é: ManagedIdentityExtensionForLinux

## <a name="known-issues"></a>Problemas conhecidos

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>"Script de automação de" Falha ao tentar exportar de esquema para identidades geridas para a extensão de recursos do Azure

Quando as identidades geridas para recursos do Azure é ativada numa VM, o seguinte erro é apresentado durante a tentativa de utilizar a funcionalidade de "Script de automatização" para a VM ou o respetivo grupo de recursos:

![Erro de exportação de identidades geridas para o script de automação de recursos do Azure](./media/msi-known-issues/automation-script-export-error.png)

As identidades geridas para recursos do Azure a extensão VM (planeada para preterição em Janeiro de 2019) faz atualmente não suportam a capacidade de exportar o seu esquema para um modelo de grupo de recursos. Como resultado, o modelo gerado não mostra os parâmetros de configuração para ativar identidades geridas para recursos do Azure no recurso. Estas secções podem ser adicionadas manualmente ao seguir os exemplos [configurar geridos identidades para recursos do Azure na VM do Azure com um modelo de](qs-configure-template-windows-vm.md).

Quando a funcionalidade de exportação de esquema fica disponível para as identidades geridas para a extensão VM de recursos do Azure (planeada para preterição em Janeiro de 2019), serão listado na [e grupos de recursos de exportação, que contêm as extensões de VM](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>VM falha a iniciação após a ser movidos do grupo de recursos ou subscrição

Se mover uma VM em execução, ele continua a ser executado durante a mudança. No entanto, após a mudança, se a VM é parada e reiniciada, ele irá falhar iniciar. Este problema ocorre porque a VM não está a ser atualizada a referência para as identidades geridas para a identidade de recursos do Azure e continua para apontá-lo no grupo de recursos antigos.

**Solução** 
 
Acione uma atualização na VM para que ele pode obter valores corretos para as identidades geridas para recursos do Azure. Pode fazer uma alteração de propriedade VM para atualizar a referência para as identidades geridas para a identidade de recursos do Azure. Por exemplo, pode definir um novo valor de etiqueta na VM com o seguinte comando:

```azurecli-interactive
 az  vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Este comando define uma nova etiqueta "fixVM" com um valor de 1 na VM. 
 
Definindo essa propriedade, a VM atualiza com as identidades geridas corretas para o URI do recurso de recursos do Azure e, em seguida, deve ser capaz de iniciar a VM. 
 
Assim que a VM é iniciada, a etiqueta pode ser removida ao utilizar o seguinte comando:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="vm-extension-provisioning-fails"></a>Falha de aprovisionamento de extensão de VM

Aprovisionamento da extensão VM poderá falhar devido a falhas de pesquisa DNS. Reinicie a VM e tente novamente.
 
> [!NOTE]
> A extensão de VM está prevista para preterição até Janeiro de 2019. Recomendamos que passar a usar o ponto de extremidade IMDS.

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Transferindo uma assinatura entre diretórios do Azure AD

Identidades geridas for atualizadas quando a subscrição for movido/transferidos para outro diretório. Como resultado, qualquer existente atribuído de sistema ou identidades geridas atribuído ao utilizador será quebradas. 

Solução de identidades geridas numa subscrição que foi movida para outro diretório:

 - Para sistema atribuídos identidades geridas: desative e volte a ativar. 
 - Para o utilizador atribuído identidades geridas: eliminar, voltar a criar e anexe-os novamente para os recursos necessários (por exemplo, as máquinas virtuais)

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Mover uma identidade gerida atribuído ao utilizador para uma subscrição/grupo de recursos diferente

Mover uma identidade gerida atribuído ao utilizador para um grupo de recursos diferente, fará com que a identidade de quebrar. Como resultado, recursos (por exemplo, VM) com essa identidade não será capazes de pedir tokens para o mesmo. 
