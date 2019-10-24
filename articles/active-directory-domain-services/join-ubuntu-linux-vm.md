---
title: Ingresse em uma VM do Ubuntu para Azure AD Domain Services | Microsoft Docs
description: Saiba como configurar e ingressar um Ubuntu Linux máquina virtual em um domínio Azure AD Domain Services gerenciado.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2019
ms.author: iainfou
ms.openlocfilehash: 9fb41b08cb29a68b39fb416b4b7b7bcce9e821dd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754356"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Ingressar uma máquina virtual Ubuntu Linux em um domínio Azure AD Domain Services gerenciado

Para permitir que os usuários entrem em máquinas virtuais (VMs) no Azure usando um único conjunto de credenciais, você pode ingressar VMs em um domínio gerenciado Azure Active Directory Domain Services (AD DS). Quando você une uma VM a um domínio gerenciado AD DS do Azure, as contas de usuário e as credenciais do domínio podem ser usadas para entrar e gerenciar servidores. As associações de grupo do domínio gerenciado do AD DS do Azure também são aplicadas para permitir que você controle o acesso a arquivos ou serviços na VM.

Este artigo mostra como unir uma VM Ubuntu Linux a um domínio gerenciado do Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário Azure Active Directory associado à sua assinatura, seja sincronizado com um diretório local ou um diretório somente em nuvem.
    * Se necessário, [crie um locatário Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um Azure Active Directory Domain Services domínio gerenciado habilitado e configurado em seu locatário do Azure AD.
    * Se necessário, o primeiro tutorial [cria e configura uma instância de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Uma conta de usuário que é membro do grupo de *Administradores de DC do Azure ad* em seu locatário do Azure AD.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Criar e conectar-se a uma VM Ubuntu Linux

Se você tiver uma VM Ubuntu Linux existente no Azure, conecte-se a ela usando o SSH e continue na próxima etapa para [começar a configurar a VM](#configure-the-hosts-file).

Se você precisar criar uma VM Ubuntu Linux ou desejar criar uma VM de teste para uso com este artigo, poderá usar um dos seguintes métodos:

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
* o *Ubuntu* é o nome do host da sua VM do Ubuntu que você está unindo ao domínio gerenciado.

Atualize esses nomes com seus próprios valores:

```console
127.0.0.1 ubuntu.contoso.com ubuntu
```

Quando terminar, salve e saia do arquivo de *hosts* usando o comando `:wq` do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

A VM precisa de alguns pacotes adicionais para ingressar a VM no domínio gerenciado AD DS do Azure. Para instalar e configurar esses pacotes, atualize e instale as ferramentas de ingresso no domínio usando `apt-get`

Durante a instalação do Kerberos, o pacote *krb5-User* solicita o nome do realm em letras maiúsculas. Por exemplo, se o nome do seu domínio gerenciado AD DS do Azure for *contoso.com*, insira *contoso.com* como o realm. A instalação grava as seções `[realm]` e `[domain_realm]` no arquivo de configuração */etc/krb5.conf* . Certifique-se de especificar o realm em letras MAIÚSCULAs:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Configurar protocolo NTP (NTP)

Para que a comunicação de domínio funcione corretamente, a data e hora da sua VM do Ubuntu devem sincronizar com o domínio gerenciado AD DS do Azure. Adicione o nome de host NTP do domínio gerenciado AD DS do Azure ao arquivo */etc/ntp.conf* .

1. Abra o arquivo *NTP. conf* com um editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. No arquivo *NTP. conf* , crie uma linha para adicionar o nome DNS do domínio gerenciado AD DS do Azure. No exemplo a seguir, uma entrada para *contoso.com* é adicionada. Use seu próprio nome DNS:

    ```console
    server contoso.com
    ```

    Quando terminar, salve e saia do arquivo *NTP. conf* usando o comando `:wq` do editor.

1. Para certificar-se de que a VM está sincronizada com o domínio gerenciado AD DS do Azure, as seguintes etapas são necessárias:

    * Parar o servidor NTP
    * Atualizar a data e a hora do domínio gerenciado
    * Iniciar o serviço NTP

    Execute os comandos a seguir para concluir essas etapas. Use seu próprio nome DNS com o comando `ntpdate`:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate contoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Ingressar VM no domínio gerenciado

Agora que os pacotes necessários estão instalados na VM e o NTP está configurado, ingresse a VM no domínio gerenciado AD DS do Azure.

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
    sudo realm join --verbose CONTOSO.COM -U 'contosoadmin@CONTOSO.COM' --install=/
    ```

Leva alguns minutos para ingressar a VM no domínio gerenciado AD DS do Azure. A saída de exemplo a seguir mostra que a VM ingressou com êxito no domínio gerenciado do Azure AD DS:

```output
Successfully enrolled machine in realm
```

Se sua VM não puder concluir com êxito o processo de ingresso no domínio, verifique se o grupo de segurança de rede da VM permite o tráfego de saída do Kerberos na porta TCP + UDP 464 para a sub-rede da rede virtual para seu domínio gerenciado AD DS do Azure.

## <a name="update-the-sssd-configuration"></a>Atualizar a configuração do SSSD

Um dos pacotes instalados em uma etapa anterior era para o daemon dos serviços de segurança do sistema (SSSD). Quando um usuário tenta entrar em uma VM usando credenciais de domínio, o SSSD transmite a solicitação para um provedor de autenticação. Nesse cenário, o SSSD usa o Azure AD DS para autenticar a solicitação.

1. Abra o arquivo *SSSD. conf* com um editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Comente a linha para *use_fully_qualified_names* da seguinte maneira:

    ```console
    # use_fully_qualified_names = True
    ```

    Quando terminar, salve e saia do arquivo *SSSD. conf* usando o comando `:wq` do editor.

1. Para aplicar a alteração, reinicie o serviço SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Definir configurações de conta de usuário e grupo

Com a VM ingressada no domínio gerenciado AD DS do Azure e configurada para autenticação, há algumas opções de configuração de usuário a serem concluídas. Essas alterações de configuração incluem a permissão de autenticação baseada em senha e a criação automática de diretórios base na VM local quando os usuários do domínio entram pela primeira vez.

### <a name="allow-password-authentication-for-ssh"></a>Permitir autenticação de senha para SSH

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
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Configurar a criação automática do diretório base

Para habilitar a criação automática do diretório base quando um usuário entrar pela primeira vez, conclua as seguintes etapas:

1. Abra o arquivo */etc/pam.d/common-Session* em um editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Adicione a seguinte linha a este arquivo abaixo da linha `session optional pam_sss.so`:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Quando terminar, salve e saia do arquivo de *sessão comum* usando o comando `:wq` do editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder ao grupo ' Administradores do AAD DC ' privilégios do sudo

Para conceder aos membros do grupo de *Administradores do AAD DC* privilégios administrativos na VM do Ubuntu, você adiciona uma entrada ao */etc/sudoers*. Depois de adicionados, os membros do grupo de *Administradores do AAD DC* podem usar o comando `sudo` na VM do Ubuntu.

1. Abra o arquivo do *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao final do arquivo */etc/sudoers* :

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, salve e saia do editor usando o comando `Ctrl-X`.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Entrar na VM usando uma conta de domínio

Para verificar se a VM foi unida com êxito ao domínio gerenciado AD DS do Azure, inicie uma nova conexão SSH usando uma conta de usuário de domínio. Confirme se um diretório base foi criado e se a associação de grupo do domínio foi aplicada.

1. Crie uma nova conexão SSH no console do. Use uma conta de domínio que pertença ao domínio gerenciado usando o comando `ssh -l`, como `contosoadmin@contoso.com` e, em seguida, insira o endereço da VM, como *Ubuntu.contoso.com*. Se você usar o Azure Cloud Shell, use o endereço IP público da VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@CONTOSO.com ubuntu.contoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Passos seguintes

Se você tiver problemas para conectar a VM ao domínio gerenciado AD DS do Azure ou entrar com uma conta de domínio, consulte [Solucionando problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
