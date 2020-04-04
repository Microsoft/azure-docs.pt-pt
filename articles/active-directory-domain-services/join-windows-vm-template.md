---
title: Use um modelo para se juntar a um VM Windows para Azure AD DS [ Microsoft Docs
description: Aprenda a usar os modelos do Gestor de Recursos Azure para se juntar a um VM de servidor windows novo ou existente para um domínio gerido pelo Azure Ative Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: d2108b4c6b81675e2df6789d412dbd7d36f58a4d
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655115"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Junte-se a uma máquina virtual do Windows Server para um domínio gerido pelo Azure Ative Directory Services usando um modelo de Gestor de Recursos

Para automatizar a implementação e configuração de máquinas virtuais Azure (VMs), pode utilizar um modelo de Gestor de Recursos. Estes modelos permitem-lhe criar implementações consistentes de cada vez. As extensões também podem ser incluídas em modelos para configurar automaticamente um VM como parte da implementação. Uma extensão útil une VMs a um domínio, que pode ser usado com domínios geridos por Azure Ative Directory (Azure AD DS).

Este artigo mostra-lhe como criar e juntar um VM do Windows Server a um domínio gerido pelo Azure AD DS utilizando modelos de Gestor de Recursos. Também aprende a juntar um VM de Servidor Windows existente a um domínio Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, o primeiro tutorial cria e configura uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS.

## <a name="azure-resource-manager-template-overview"></a>Visão geral do modelo do Gestor de Recursos Azure

Os modelos do Gestor de Recursos permitem definir a infraestrutura Azure em código. Os recursos necessários, ligações de rede ou configuração de VMs podem ser definidos num modelo. Estes modelos criam implementações consistentes e reprodutíveis de cada vez, e podem ser versonizados à medida que faz alterações. Para mais informações, consulte a visão geral dos [modelos do Gestor de Recursos do Azure][template-overview].

Cada recurso é definido num modelo utilizando a Notação de Objetos JavaScript (JSON). O exemplo JSON seguinte utiliza o tipo de recurso *Microsoft.Compute/virtualMachines/extensions* para instalar a extensão de adesão ao domínio Ative Directory. São utilizados parâmetros que especifica no momento da implantação. Quando a extensão é implantada, o VM é unido ao domínio gerido pelo Azure AD DS especificado.

```json
 {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('dnsLabelPrefix'),'/joindomain')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('dnsLabelPrefix'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Compute",
        "type": "JsonADDomainExtension",
        "typeHandlerVersion": "1.3",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "Name": "[parameters('domainToJoin')]",
          "OUPath": "[parameters('ouPath')]",
          "User": "[concat(parameters('domainToJoin'), '\\', parameters('domainUsername'))]",
          "Restart": "true",
          "Options": "[parameters('domainJoinOptions')]"
        },
        "protectedSettings": {
          "Password": "[parameters('domainPassword')]"
        }
      }
    }
```

Esta extensão VM pode ser implantada mesmo que não crie um VM no mesmo modelo. Os exemplos deste artigo mostram ambas as seguintes abordagens:

* [Crie um VM do Windows Server e junte-se a um domínio gerido](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Junte-se a um VM de servidor windows existente para um domínio gerido](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Crie um VM do Windows Server e junte-se a um domínio gerido

Se precisar de um VM do Servidor do Windows, pode criar e configurar um utilizando um modelo de Gestor de Recursos. Quando o VM é implantado, é então instalada uma extensão para juntar o VM a um domínio gerido por Azure AD DS. Se já tem um VM que deseja aderir a um domínio gerido pelo Azure AD DS, salte para [Join a um VM](#join-an-existing-windows-server-vm-to-a-managed-domain)de servidor windows existente para um domínio gerido .

Para criar um VM do Servidor windows, junte-o a um domínio gerido por AD DS azure, complete os seguintes passos:

1. Navegue no [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join/). Selecione a opção de **implantação para Azure**.
1. Na página de **implementação Personalizada,** introduza as seguintes informações para criar e junte-se a um VM do Windows Server para o domínio gerido pelo Azure AD DS:

    | Definição                   | Valor |
    |---------------------------|-------|
    | Subscrição              | Escolha a mesma subscrição Azure na qual ativou os Serviços de Domínio Azure AD. |
    | Grupo de recursos            | Escolha o grupo de recursos para o seu VM. |
    | Localização                  | Selecione a localização para o seu VM. |
    | Nome VNET existente        | O nome da rede virtual existente para ligar o VM, como o *myVnet.* |
    | Nome sub-rede existente      | O nome da subnet de rede virtual existente, como cargas de *trabalho.* |
    | Prefixo de etiqueta DNS          | Introduza um nome DNS para utilizar para o VM, como *o myvm*. |
    | Tamanho da VM                   | Especifique um tamanho VM, como *Standard_DS2_v2*. |
    | Domínio para aderir            | O nome DNS de domínio gerido pelo Azure AD DS, como *aaddscontoso.com*. |
    | Nome de utilizador de domínio           | A conta de utilizador no domínio gerido pelo Azure AD DS deve ser utilizada `contosoadmin@aaddscontoso.com`para aderir ao VM ao domínio gerido, como . Esta conta deve fazer parte do domínio gerido pela AD DS azure. |
    | Palavra-passe de domínio           | A palavra-passe para a conta de utilizador especificada na definição anterior. |
    | Caminho opcional da OU          | O OU personalizado para adicionar o VM. Se não especificar um valor para este parâmetro, o VM é adicionado ao *AAD DC Computers* OU padrão. |
    | Nome de utilizador de administrador vm         | Especifique uma conta de administrador local para criar no VM. |
    | Senha de Administrador VM         | Especifique uma palavra-passe do administrador local para o VM. Crie uma senha de administrador local forte para proteger contra ataques de força bruta de senha. |

1. Reveja os termos e condições e, em seguida, verifique a caixa se **eu concordar com os termos e condições acima indicados**. Quando estiver pronto, selecione **Comprar** para criar e juntar o VM ao domínio gerido pelo Azure AD DS.

> [!WARNING]
> **Manuseie as palavras-passe com cuidado.**
> O ficheiro de parâmetro de modelo solicita a palavra-passe para uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS. Não introduza manualmente valores neste ficheiro e deixe-o acessível em partilhas de ficheiros ou outros locais partilhados.

Leva alguns minutos para que a implantação termine com sucesso. Quando terminado, o Windows VM é criado e se juntou ao domínio gerido pelo Azure AD DS. O VM pode ser gerido ou assinado na utilização de contas de domínio.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Junte-se a um VM de servidor windows existente para um domínio gerido

Se tiver um VM existente, ou grupo de VMs, que deseja aderir a um domínio gerido pelo Azure AD DS, pode utilizar um modelo de Gestor de Recursos para apenas implementar a extensão VM.

Para se juntar a um VM de servidor windows existente a um domínio gerido por AD DS Azure, complete os seguintes passos:

1. Navegue no [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/). Selecione a opção de **implantação para Azure**.
1. Na página de **implementação Personalizada,** introduza as seguintes informações para se juntar ao VM ao domínio gerido pelo Azure AD DS:

    | Definição                   | Valor |
    |---------------------------|-------|
    | Subscrição              | Escolha a mesma subscrição Azure na qual ativou os Serviços de Domínio Azure AD. |
    | Grupo de recursos            | Escolha o grupo de recursos com o seu VM existente. |
    | Localização                  | Selecione a localização do seu VM existente. |
    | Lista VM                   | Insira a lista separada da vírce dos VM(s) existentes para se juntar ao domínio gerido pela AD DS Azure, como o *myVM1,myVM2 .* |
    | Nome de utilizador de adesão ao domínio     | A conta de utilizador no domínio gerido pelo Azure AD DS deve ser utilizada `contosoadmin@aaddscontoso.com`para aderir ao VM ao domínio gerido, como . Esta conta deve fazer parte do domínio gerido pela AD DS azure. |
    | Domain Join User Password | A palavra-passe para a conta de utilizador especificada na definição anterior. |
    | Caminho opcional da OU          | O OU personalizado para adicionar o VM. Se não especificar um valor para este parâmetro, o VM é adicionado ao *AAD DC Computers* OU padrão. |

1. Reveja os termos e condições e, em seguida, verifique a caixa se **eu concordar com os termos e condições acima indicados**. Quando estiver pronto, selecione **Comprar** para aderir ao VM ao domínio gerido pelo Azure AD DS.

> [!WARNING]
> **Manuseie as palavras-passe com cuidado.**
> O ficheiro de parâmetro de modelo solicita a palavra-passe para uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS. Não introduza manualmente valores neste ficheiro e deixe-o acessível em partilhas de ficheiros ou outros locais partilhados.

Leva alguns momentos para que a implantação termine com sucesso. Quando terminados, os VMs do Windows especificados são unidos ao domínio gerido pelo Azure AD DS e podem ser geridos ou assinados na utilização de contas de domínio.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, utilizou o portal Azure para configurar e implementar recursos utilizando modelos. Também pode implantar recursos com modelos de Gestor de Recursos utilizando o [Azure PowerShell][deploy-powershell] ou o [Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
