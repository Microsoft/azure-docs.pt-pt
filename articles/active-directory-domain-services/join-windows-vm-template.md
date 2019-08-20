---
title: Ingresse em uma VM do Windows Server para Azure Active Directory Domain Services | Microsoft Docs
description: Ingresse uma máquina virtual do Windows Server em um domínio gerenciado usando modelos de Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 599d474b7c45274c87878c622149a86bc93af318
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612265"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Ingressar uma máquina virtual do Windows Server em um domínio gerenciado usando um modelo do Resource Manager
Este artigo mostra como unir uma máquina virtual do Windows Server a um Azure AD Domain Services domínio gerenciado usando modelos do Resource Manager.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:
1. Uma **assinatura válida do Azure**.
2. Um **diretório do Azure ad** -seja sincronizado com um diretório local ou um diretório somente na nuvem.
3. **Azure AD Domain Services** deve ser habilitado para o diretório do Azure AD. Se você ainda não fez isso, siga todas as tarefas descritas no [Guia de introdução](tutorial-create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Conclua as etapas necessárias para [sincronizar as senhas para seu Azure AD Domain Services domínio gerenciado](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="install-and-configure-required-tools"></a>Instalar e configurar as ferramentas necessárias
Você pode usar qualquer uma das seguintes opções para executar as etapas descritas neste documento:
* **Azure PowerShell**: [Instalar e configurar](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **CLI do Azure**: [Instalar e configurar](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opção 1: Provisionar uma nova VM do Windows Server e associá-la a um domínio gerenciado
**nome do modelo de início rápido**: [201-VM-domínio-ingressar](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Para implantar uma máquina virtual do Windows Server e associá-la a um domínio gerenciado, execute as seguintes etapas:
1. Navegue até o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Clique em **Implementar no Azure**.
3. Na página **implantação personalizada** , forneça as informações necessárias para provisionar a máquina virtual.
4. Selecione a **assinatura do Azure** na qual provisionar a máquina virtual. Escolha a mesma assinatura do Azure na qual você habilitou Azure AD Domain Services.
5. Escolha um **grupo de recursos** existente ou crie um novo.
6. Escolha um **local** no qual implantar a nova máquina virtual.
7. Em **nome da VNET existente**, especifique a rede virtual na qual você implantou seu Azure AD Domain Services domínio gerenciado.
8. Em **nome da sub-rede existente**, especifique a sub-rede na rede virtual em que você deseja implantar esta máquina virtual. Não selecione a sub-rede de gateway na rede virtual. Além disso, não selecione a sub-rede dedicada na qual seu domínio gerenciado é implantado.
9. Em **prefixo do rótulo DNS**, especifique o nome do host para a máquina virtual que está sendo provisionada. Por exemplo, ' contoso-Win '.
10. Selecione o **tamanho de VM** apropriado para a máquina virtual.
11. Em **domínio para ingressar**, especifique o nome de domínio DNS do seu domínio gerenciado.
12. Em **nome**de usuário do domínio, especifique o nome da conta de usuário no domínio gerenciado que deve ser usado para ingressar a VM no domínio gerenciado.
13. Em **senha do domínio**, especifique a senha da conta de usuário do domínio referida pelo parâmetro ' domainUsername '.
14. Opcional: Você pode especificar um **caminho de UO** para uma UO personalizada, na qual adicionar a máquina virtual. Se você não especificar um valor para esse parâmetro, a máquina virtual será adicionada à UO **computadores DC do AAD** padrão no domínio gerenciado.
15. No campo **nome de usuário do administrador da VM** , especifique um nome de conta de administrador local para a máquina virtual.
16. No campo **senha de administrador da VM** , especifique uma senha de administrador local para a máquina virtual. Forneça uma senha forte de administrador local para que a máquina virtual proteja contra ataques de força bruta de senha.
17. Clique em **concordo com os termos e condições declarados acima**.
18. Clique em **comprar** para provisionar a máquina virtual.

> [!WARNING]
> **Manipule senhas com cuidado.**
> O arquivo de parâmetro de modelo contém senhas para contas de domínio, bem como senhas de administrador local para a máquina virtual. Certifique-se de não deixar esse arquivo em relação a compartilhamentos de arquivos ou outros locais compartilhados. Recomendamos que você descarte esse arquivo depois de concluir a implantação de suas máquinas virtuais.
>

Depois que a implantação for concluída com êxito, a máquina virtual do Windows recentemente provisionada será unida ao domínio gerenciado.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opção 2: Ingressar uma VM do Windows Server existente em um domínio gerenciado
**modelo de início rápido**: [201-VM-domínio-Join-existente](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Para ingressar uma máquina virtual do Windows Server existente em um domínio gerenciado, execute as seguintes etapas:
1. Navegue até o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Clique em **Implementar no Azure**.
3. Na página **implantação personalizada** , forneça as informações necessárias para provisionar a máquina virtual.
4. Selecione a **assinatura do Azure** na qual provisionar a máquina virtual. Escolha a mesma assinatura do Azure na qual você habilitou Azure AD Domain Services.
5. Escolha um **grupo de recursos** existente ou crie um novo.
6. Escolha um **local** no qual implantar a nova máquina virtual.
7. No campo **lista de VMs** , especifique os nomes das máquinas virtuais existentes a serem unidas ao domínio gerenciado. Use uma vírgula para separar nomes de VM individuais. Por exemplo, **contoso-Web, contoso-API**.
8. Em **nome de usuário de ingresso no domínio**, especifique o nome da conta de usuário em seu domínio gerenciado que deve ser usado para ingressar a VM no domínio gerenciado.
9. Em **senha de usuário de ingresso no domínio**, especifique a senha da conta de usuário do domínio referida pelo parâmetro ' domainUsername '.
10. Em **FQDN do domínio**, especifique o nome de domínio DNS do seu domínio gerenciado.
11. Opcional: Você pode especificar um **caminho de UO** para uma UO personalizada, na qual adicionar a máquina virtual. Se você não especificar um valor para esse parâmetro, a máquina virtual será adicionada à UO **computadores DC do AAD** padrão no domínio gerenciado.
12. Clique em **concordo com os termos e condições declarados acima**.
13. Clique em **comprar** para provisionar a máquina virtual.

> [!WARNING]
> **Manipule senhas com cuidado.**
> O arquivo de parâmetro de modelo contém senhas para contas de domínio, bem como senhas de administrador local para a máquina virtual. Certifique-se de não deixar esse arquivo em relação a compartilhamentos de arquivos ou outros locais compartilhados. Recomendamos que você descarte esse arquivo depois de concluir a implantação de suas máquinas virtuais.
>

Depois que a implantação for concluída com êxito, as máquinas virtuais do Windows especificadas serão Unidas ao domínio gerenciado.


## <a name="related-content"></a>Conteúdo relacionado
* [Overview of Azure PowerShell](/powershell/azure/overview)
* [Modelo de início rápido do Azure – ingressar no domínio em uma nova VM](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Modelo de início rápido do Azure – ingressar no domínio VMs existentes](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
