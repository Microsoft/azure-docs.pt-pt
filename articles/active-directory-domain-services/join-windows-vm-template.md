---
title: Junte-se a uma VM do Windows Server para o Azure Active Directory Domain Services | Documentos da Microsoft
description: Junte-se uma máquina virtual do Windows Server a um domínio gerido utilizando modelos Azure Resource Manager.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: e4ca613059e10755056616b964cc500625fef187
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66245958"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-a-resource-manager-template"></a>Junte-se a uma máquina virtual do Windows Server a um domínio gerido com um modelo do Resource Manager
Este artigo mostra-lhe como associar uma máquina virtual do Windows Server a um domínio gerido do Azure AD Domain Services com modelos do Resource Manager.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:
1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](create-instance.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe ao seu domínio gerido do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="install-and-configure-required-tools"></a>Instalar e configurar as ferramentas necessárias
Pode utilizar qualquer uma das seguintes opções para executar os passos descritos neste documento:
* **Azure PowerShell**: [Instalar e configurar](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)
* **CLI do Azure**: [Instalar e configurar](https://azure.microsoft.com/documentation/articles/xplat-cli-install/)


## <a name="option-1-provision-a-new-windows-server-vm-and-join-it-to-a-managed-domain"></a>Opção 1: Aprovisionar uma nova VM do Windows Server e associe-a um domínio gerido
**Nome do modelo de início rápido**: [201-vm-domain-join](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)

Para implementar uma máquina virtual do Windows Server e associe-a um domínio gerido, execute os seguintes passos:
1. Navegue para o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/).
2. Clique em **Implementar no Azure**.
3. Na **implementação personalizada** , indique as informações necessárias para aprovisionar a máquina virtual.
4. Selecione o **subscrição do Azure** no qual aprovisionar a máquina virtual. Escolha a mesma subscrição do Azure em que tiver ativado o Azure AD Domain Services.
5. Selecione um existente **grupo de recursos** ou criar um novo.
6. Escolher uma **localização** na qual implementar a nova máquina virtual.
7. Na **nome da VNET existente**, especifique a rede virtual no qual implementou o seu domínio gerido do Azure AD Domain Services.
8. Na **nome da sub-rede existente**, especifique a sub-rede na rede virtual onde pretende implementar esta máquina virtual. Não selecione a sub-rede do gateway na rede virtual. Além disso, não selecione a sub-rede dedicada no qual o seu domínio gerido é implementado.
9. Na **prefixo de etiqueta de DNS**, especifique o nome do anfitrião da máquina virtual a ser aprovisionada. Por exemplo, "contoso-win".
10. Selecione o adequado **tamanho da VM** para a máquina virtual.
11. Na **a associação a um domínio**, especifique o nome de domínio DNS do seu domínio gerido.
12. Na **nome de utilizador de domínio**, especifique o nome de conta de utilizador no seu domínio gerido, que deve ser utilizado para associar a VM ao domínio gerido.
13. Na **palavra-passe do domínio**, especifique a palavra-passe da conta de utilizador de domínio referenciada pelo parâmetro 'domainUsername'.
14. Opcional: Pode especificar uma **UO caminho** uma UO personalizado, na qual pretende adicionar a máquina virtual. Se não especificar um valor para este parâmetro, a máquina virtual é adicionada para a predefinição **computadores do AAD DC** UO no domínio gerido.
15. Na **nome de utilizador de administrador de VM** campo, especifique um nome de conta de administrador local para a máquina virtual.
16. Na **palavra-passe de administrador de VM** campo, especifique uma palavra-passe de administrador local para a máquina virtual. Forneça uma palavra-passe de administrador local forte para a máquina virtual proteger contra ataques de força bruta de palavra-passe.
17. Clique em **concordo com os termos e condições indicados acima**.
18. Clique em **Compra** para aprovisionar a máquina virtual.

> [!WARNING]
> **Lidar com palavras-passe com cuidado.**
> O ficheiro de parâmetros de modelo contém palavras-passe para contas de domínio, bem como as palavras-passe de administrador local para a máquina virtual. Certifique-se de que não deixe este ficheiro afirmamos em partilhas de ficheiros ou em outros locais partilhados. Recomendamos que descartar este ficheiro depois de concluída a implementação de suas máquinas virtuais.
>

Depois de concluída a implementação com êxito, a sua máquina de virtual de Windows recentemente aprovisionada está associada ao domínio gerido.


## <a name="option-2-join-an-existing-windows-server-vm-to-a-managed-domain"></a>Opção 2: Junte-se a uma VM do Windows Server existente a um domínio gerido
**Modelo de início rápido**: [201-vm-domain-join-existing](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

Para associar uma máquina de virtual do Windows Server existente a um domínio gerido, execute os seguintes passos:
1. Navegue para o [modelo de início rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
2. Clique em **Implementar no Azure**.
3. Na **implementação personalizada** , indique as informações necessárias para aprovisionar a máquina virtual.
4. Selecione o **subscrição do Azure** no qual aprovisionar a máquina virtual. Escolha a mesma subscrição do Azure em que tiver ativado o Azure AD Domain Services.
5. Selecione um existente **grupo de recursos** ou criar um novo.
6. Escolher uma **localização** na qual implementar a nova máquina virtual.
7. Na **lista de VMS** campo, especifique os nomes das máquinas virtuais existentes para ser associado ao domínio gerido. Utilize uma vírgula para separar os nomes VM individuais. Por exemplo, **contoso-web, contoso api**.
8. Na **nome de utilizador de adesão ao domínio**, especifique o nome de conta de utilizador no seu domínio gerido, que deve ser utilizado para associar a VM ao domínio gerido.
9. Na **senha de usuário de associação de domínio**, especifique a palavra-passe da conta de utilizador de domínio referenciada pelo parâmetro 'domainUsername'.
10. Na **FQDN do domínio**, especifique o nome de domínio DNS do seu domínio gerido.
11. Opcional: Pode especificar uma **UO caminho** uma UO personalizado, na qual pretende adicionar a máquina virtual. Se não especificar um valor para este parâmetro, a máquina virtual é adicionada para a predefinição **computadores do AAD DC** UO no domínio gerido.
12. Clique em **concordo com os termos e condições indicados acima**.
13. Clique em **Compra** para aprovisionar a máquina virtual.

> [!WARNING]
> **Lidar com palavras-passe com cuidado.**
> O ficheiro de parâmetros de modelo contém palavras-passe para contas de domínio, bem como as palavras-passe de administrador local para a máquina virtual. Certifique-se de que não deixe este ficheiro afirmamos em partilhas de ficheiros ou em outros locais partilhados. Recomendamos que descartar este ficheiro depois de concluída a implementação de suas máquinas virtuais.
>

Depois de concluída a implementação com êxito, as máquinas de virtuais do Windows especificadas estão associadas ao domínio gerido.


## <a name="related-content"></a>Conteúdo relacionado
* [Overview of Azure PowerShell](/powershell/azure/overview)
* [Modelo de início rápido do Azure - associação de domínio uma VM nova](https://azure.microsoft.com/resources/templates/201-vm-domain-join/)
* [Modelo de início rápido do Azure - VMs existentes de associação de domínio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)
* [Implementar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
