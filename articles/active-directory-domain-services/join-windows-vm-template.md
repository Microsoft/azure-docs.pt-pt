---
title: Use um modelo para juntar um Windows VM a Azure AD DS Microsoft Docs
description: Aprenda a usar os modelos do Gestor de Recursos Azure para se juntar a um novo ou existente Windows Server VM a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 4eabfd8e-5509-4acd-86b5-1318147fddb5
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 988f009527f26a9f2be965b635d57f0bc38913c2
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960699"
---
# <a name="join-a-windows-server-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain-using-a-resource-manager-template"></a>Junte uma máquina virtual do Windows Server a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure utilizando um modelo de Gestor de Recursos

Para automatizar a implementação e configuração de máquinas virtuais Azure (VMs), pode utilizar um modelo de Gestor de Recursos. Estes modelos permitem criar sempre implementações consistentes. As extensões também podem ser incluídas em modelos para configurar automaticamente um VM como parte da implementação. Uma extensão útil junta VMs a um domínio, que pode ser usado com domínios geridos Azure Ative Directory Domain Services (Azure AD DS).

Este artigo mostra-lhe como criar e juntar um VM do Windows Server a um domínio gerido AD DS Azure usando modelos de Gestor de Recursos. Também aprende a juntar um VM do Servidor do Windows existente a um domínio AD DS Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, o primeiro tutorial cria e configura um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido.

## <a name="azure-resource-manager-template-overview"></a>Visão geral do modelo do gestor de recursos Azure

Os modelos do Gestor de Recursos permitem definir a infraestrutura Azure em código. Os recursos necessários, ligações de rede ou configuração de VMs podem ser definidos num modelo. Estes modelos criam implementações consistentes e reprodutíveis de cada vez, e podem ser versados à medida que estão a fazer alterações. Para obter mais informações, consulte [a visão geral do Azure Resource Manager][template-overview].

Cada recurso é definido num modelo utilizando a Notação de Objetos JavaScript (JSON). O exemplo JSON a seguir utiliza o tipo de recurso *Microsoft.Compute/virtualMachines/extensões* para instalar a extensão de união do domínio Ative Directory. São utilizados parâmetros que especifica na hora de implantação. Quando a extensão é implantada, o VM é associado ao domínio gerido especificado.

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

* [Crie um VM do Servidor do Windows e junte-se a um domínio gerido](#create-a-windows-server-vm-and-join-to-a-managed-domain)
* [Junte-se a um VM do Servidor do Windows existente para um domínio gerido](#join-an-existing-windows-server-vm-to-a-managed-domain)

## <a name="create-a-windows-server-vm-and-join-to-a-managed-domain"></a>Crie um VM do Servidor do Windows e junte-se a um domínio gerido

Se precisar de um VM do Servidor do Windows, pode criar e configurar um utilizando um modelo de Gestor de Recursos. Quando o VM é implantado, uma extensão é então instalada para unir o VM a um domínio gerido. Se já tiver um VM que deseja aderir a um domínio gerido, salte para [Juntar um VM do Servidor do Windows existente a um domínio gerido](#join-an-existing-windows-server-vm-to-a-managed-domain).

Para criar um VM do Servidor do Windows, junte-o a um domínio gerido, complete os seguintes passos:

1. Navegue pelo [modelo de arranque rápido.](https://azure.microsoft.com/resources/templates/201-vm-domain-join/) Selecione a opção **de Implantação para Azure**.
1. Na página **de implementação personalizada,** introduza as seguintes informações para criar e juntar um VM do Windows Server ao domínio gerido:

    | Definição                   | Valor |
    |---------------------------|-------|
    | Subscrição              | Escolha a mesma subscrição Azure na qual ativou os Serviços de Domínio Azure AD. |
    | Grupo de recursos            | Escolha o grupo de recursos para o seu VM. |
    | Localização                  | Selecione a localização do seu VM. |
    | Nome VNET existente        | O nome da rede virtual existente para ligar o VM a, como *o myVnet*. |
    | Nome da sub-rede existente      | O nome da sub-rede virtual existente, como *Workloads*. |
    | Prefixo da etiqueta do rótulo de DNS          | Introduza um nome DNS para usar para o VM, como *o myvm*. |
    | Tamanho da VM                   | Especificar um tamanho VM, como *Standard_DS2_v2*. |
    | Domínio para aderir            | O nome DNS de domínio gerido, como *aaddscontoso.com*. |
    | Nome de utilizador de domínio           | A conta de utilizador no domínio gerido que deve ser usada para juntar o VM ao domínio gerido, como `contosoadmin@aaddscontoso.com` . Esta conta deve fazer parte do domínio gerido. |
    | Senha de domínio           | A palavra-passe da conta de utilizador especificada na definição anterior. |
    | Caminho opcional da UA          | O ou personalizado em que adicionar o VM. Se não especificar um valor para este parâmetro, o VM é adicionado ao *AAD DC Computers* OU predefinido. |
    | VM Nome de Utilizador         | Especifique uma conta de administrador local para criar no VM. |
    | Senha de administração VM         | Especifique uma senha de administrador local para o VM. Crie uma senha de administrador local forte para proteger contra ataques de força bruta de palavra-passe. |

1. Reveja os termos e condições e, em seguida, verifique a caixa pois **concordo com os termos e condições acima indicados.** Quando estiver pronto, selecione **Comprar** para criar e juntar o VM ao domínio gerido.

> [!WARNING]
> **Manuseie as palavras-passe com cuidado.**
> O ficheiro de parâmetro do modelo solicita a palavra-passe para uma conta de utilizador que faz parte do domínio gerido. Não introduza manualmente valores neste ficheiro e deixe-o acessível em partilhas de ficheiros ou em outras localizações partilhadas.

Leva alguns minutos para que a implantação termine com sucesso. Quando terminado, o Windows VM é criado e unido ao domínio gerido. O VM pode ser gerido ou assinado através de contas de domínio.

## <a name="join-an-existing-windows-server-vm-to-a-managed-domain"></a>Junte-se a um VM do Servidor do Windows existente para um domínio gerido

Se tiver um VM ou grupo de VM existentes que deseje aderir a um domínio gerido, pode utilizar um modelo de Gestor de Recursos para apenas implementar a extensão VM.

Para juntar um VM do Servidor do Windows existente a um domínio gerido, complete os seguintes passos:

1. Navegue pelo [modelo de arranque rápido.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/) Selecione a opção **de Implantação para Azure**.
1. Na página **de implementação personalizada,** insira as seguintes informações para juntar o VM ao domínio gerido:

    | Definição                   | Valor |
    |---------------------------|-------|
    | Subscrição              | Escolha a mesma subscrição Azure na qual ativou os Serviços de Domínio Azure AD. |
    | Grupo de recursos            | Escolha o grupo de recursos com o seu VM existente. |
    | Localização                  | Selecione a localização do seu VM existente. |
    | Lista VM                   | Introduza a lista separada em vírgula dos VM(s) existentes para se juntar ao domínio gerido, como *o myVM1,myVM2*. |
    | Domínio juntar nome de utilizador     | A conta de utilizador no domínio gerido que deve ser usada para juntar o VM ao domínio gerido, como `contosoadmin@aaddscontoso.com` . Esta conta deve fazer parte do domínio gerido. |
    | Domínio Junte-se à senha do utilizador | A palavra-passe da conta de utilizador especificada na definição anterior. |
    | Caminho opcional da UA          | O ou personalizado em que adicionar o VM. Se não especificar um valor para este parâmetro, o VM é adicionado ao *AAD DC Computers* OU predefinido. |

1. Reveja os termos e condições e, em seguida, verifique a caixa pois **concordo com os termos e condições acima indicados.** Quando estiver pronto, **selecione Comprar** para se juntar ao VM ao domínio gerido.

> [!WARNING]
> **Manuseie as palavras-passe com cuidado.**
> O ficheiro de parâmetro do modelo solicita a palavra-passe para uma conta de utilizador que faz parte do domínio gerido. Não introduza manualmente valores neste ficheiro e deixe-o acessível em partilhas de ficheiros ou em outras localizações partilhadas.

Leva alguns momentos para que a implantação termine com sucesso. Quando terminados, os VMs do Windows especificados são unidos ao domínio gerido e podem ser geridos ou assinados através de contas de domínio.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você usou o portal Azure para configurar e implementar recursos usando modelos. Também pode implementar recursos com modelos de Gestor de Recursos utilizando [a Azure PowerShell][deploy-powershell] ou o [Azure CLI][deploy-cli].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[template-overview]: ../azure-resource-manager/templates/overview.md
[deploy-powershell]: ../azure-resource-manager/templates/deploy-powershell.md
[deploy-cli]: ../azure-resource-manager/templates/deploy-cli.md
