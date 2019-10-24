---
title: Ingresse em uma VM CentOS para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e unir uma máquina virtual CentOS Linux a um Azure AD Domain Services domínio gerenciado.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 24d9bb72e52fba9bb4e4dc3256e650cf68e3963f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755737"
---
# <a name="join-a-centos-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Unir uma máquina virtual CentOS Linux a um Azure AD Domain Services domínio gerenciado

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode ingressar VMs em um domínio gerenciado Azure Active Directory Domain Services (AD DS). Quando você une uma VM a um domínio gerenciado AD DS do Azure, as contas de usuário e as credenciais do domínio podem ser usadas para entrar e gerenciar servidores. As associações de grupo do domínio gerenciado do AD DS do Azure também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como unir uma VM do CentOS Linux a um domínio gerenciado AD DS do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.

## <a name="create-and-connect-to-a-centos-linux-vm"></a>Criar e conectar-se a uma VM CentOS Linux

Se você tiver uma VM CentOS Linux existente no Azure, conecte-se a ela usando o SSH e continue na próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar uma VM CentOS Linux ou desejar criar uma VM de teste para uso com este artigo, você pode usar um dos seguintes métodos:

* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [O Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Ao criar a VM, preste atenção às configurações de rede virtual para garantir que a VM possa se comunicar com o domínio gerenciado AD DS do Azure:

* Implante a VM na mesma rede virtual, ou emparelhada, na qual você habilitou Azure AD Domain Services.
* Implante a VM em uma sub-rede diferente da instância do Azure AD Domain Services.

Depois que a VM for implantada, siga as etapas para se conectar à VM usando SSH.

## <a name="configure-the-hosts-file"></a>Configurar o arquivo de hosts

Para certificar-se de que o nome do host da VM esteja configurado corretamente para o domínio gerenciado, edite o arquivo */etc/hosts* e defina o nome do host:

```console
sudo vi /etc/hosts
```

No arquivo *hosts* , atualize o endereço *localhost* . No exemplo a seguir:

* *contoso.com* é o nome de domínio DNS do seu domínio gerenciado AD DS do Azure.
* *CentOS* é o nome do host da sua VM CentOS que você está unindo ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 centos.contoso.com centos
```

Quando terminar, salve e saia do arquivo de *hosts* usando o comando `:wq` do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

A VM precisa de alguns pacotes adicionais para ingressar a VM no domínio gerenciado AD DS do Azure. Para instalar e configurar esses pacotes, atualize e instale as ferramentas de ingresso no domínio usando `yum`:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

## <a name="join-vm-to-the-managed-domain"></a>Ingressar VM no domínio gerenciado

Agora que os pacotes necessários estão instalados na VM, ingresse a VM no domínio gerenciado AD DS do Azure.

1. Use o comando `realm discover` para descobrir o domínio gerenciado do AD DS do Azure. O exemplo a seguir descobre o realm *contoso.com*. Especifique seu próprio nome de domínio gerenciado AD DS do Azure em letras MAIÚSCULAs:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   Se o comando `realm discover` não conseguir localizar seu domínio gerenciado do Azure AD DS, examine as seguintes etapas de solução de problemas:

    * Verifique se o domínio está acessível da VM. Tente `ping contoso.com` para ver se uma resposta positiva é retornada.
    * Verifique se a VM está implantada no mesmo ou em uma rede virtual emparelhada na qual o domínio gerenciado do Azure AD DS está disponível.
    * Confirme se as configurações do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerenciado AD DS do Azure.

1. Agora, inicialize o Kerberos usando o comando `kinit`. Especifique um usuário que pertença ao grupo de *Administradores de DC do AAD* . Se necessário, [adicione uma conta de usuário a um grupo no Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Novamente, o nome de domínio gerenciado do AD DS do Azure deve ser inserido em letras MAIÚSCULAs. No exemplo a seguir, a conta chamada `contosoadmin@contoso.com` é usada para inicializar o Kerberos. Insira sua própria conta de usuário que seja membro do grupo de *Administradores de DC do AAD* :

    ```console
    kinit contosoadmin@CONTOSO.COM
    ```

1. Por fim, ingresse o computador no domínio gerenciado AD DS do Azure usando o comando `realm join`. Use a mesma conta de usuário que é membro do grupo de *Administradores de DC do AAD* que você especificou no comando `kinit` anterior, como `contosoadmin@CONTOSO.COM`:

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM'
    ```

Leva alguns minutos para ingressar a VM no domínio gerenciado AD DS do Azure. A saída de exemplo a seguir mostra que a VM ingressou com êxito no domínio gerenciado do Azure AD DS:

```output
Successfully enrolled machine in realm
```

Se sua VM não puder concluir com êxito o processo de ingresso no domínio, verifique se o grupo de segurança de rede da VM permite o tráfego de saída do Kerberos na porta TCP + UDP 464 para a sub-rede da rede virtual para seu domínio gerenciado AD DS do Azure.

## <a name="allow-password-authentication-for-ssh"></a>Permitir autenticação de senha para SSH

Por padrão, os usuários só podem entrar em uma VM usando a autenticação baseada em chave pública SSH. Falha na autenticação baseada em senha. Quando você ingressa a VM em um domínio gerenciado AD DS do Azure, essas contas de domínio precisam usar a autenticação baseada em senha. Atualize a configuração de SSH para permitir a autenticação baseada em senha da seguinte maneira.

1. Abra o arquivo *sshd_conf* com um editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Atualize a linha de *PasswordAuthentication* para *Sim*:

    ```console
    PasswordAuthentication yes
    ```

    Quando terminar, salve e saia do arquivo *sshd_conf* usando o comando `:wq` do editor.

1. Para aplicar as alterações e permitir que os usuários entrem usando uma senha, reinicie o serviço SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder ao grupo ' Administradores do AAD DC ' privilégios do sudo

Para conceder aos membros do grupo de *Administradores do AAD DC* privilégios administrativos na VM CentOS, você adiciona uma entrada ao */etc/sudoers*. Depois de adicionados, os membros do grupo de *Administradores do AAD DC* podem usar o comando `sudo` na VM CentOS.

1. Abra o arquivo do *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao final do arquivo */etc/sudoers* . O grupo de *Administradores do AAD DC* contém espaço em branco no nome, portanto, inclua o caractere de escape de barra invertida no nome do grupo. Adicione seu próprio nome de domínio, como *contoso.com*:

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@contoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, salve e saia do editor usando o comando `:wq` do editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Entrar na VM usando uma conta de domínio

Para verificar se a VM foi unida com êxito ao domínio gerenciado AD DS do Azure, inicie uma nova conexão SSH usando uma conta de usuário de domínio. Confirme se um diretório base foi criado e se a associação de grupo do domínio foi aplicada.

1. Crie uma nova conexão SSH no console do. Use uma conta de domínio que pertença ao domínio gerenciado usando o comando `ssh -l`, como `contosoadmin@contoso.com` e, em seguida, insira o endereço da VM, como *CentOS.contoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@CONTOSO.com centos.contoso.com
    ```

1. Quando você se conectou com êxito à VM, verifique se o diretório base foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no diretório */Home* com seu próprio diretório que corresponda à conta de usuário.

1. Agora, verifique se as associações de grupo estão sendo resolvidas corretamente:

    ```console
    id
    ```

    Você deve ver suas associações de grupo no domínio gerenciado AD DS do Azure.

1. Se você entrou na VM como um membro do grupo de *Administradores do controlador de domínio do AAD* , verifique se você pode usar corretamente o comando `sudo`:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Passos seguintes

Se você tiver problemas para conectar a VM ao domínio gerenciado AD DS do Azure ou entrar com uma conta de domínio, consulte [Solucionando problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
