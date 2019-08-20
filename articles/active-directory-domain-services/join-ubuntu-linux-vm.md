---
title: 'Azure Active Directory Domain Services: Ingressar uma VM do Ubuntu em um domínio gerenciado | Microsoft Docs'
description: Ingressar uma máquina virtual Ubuntu Linux em Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c782629d422eb8846b209fed7ab6b5a5c015de25
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612291"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Adicionar uma máquina virtual Ubuntu no Azure a um domínio gerenciado
Este artigo mostra como ingressar uma máquina virtual Ubuntu Linux em um domínio Azure AD Domain Services gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:  
1. Uma **assinatura válida do Azure**.
2. Um **diretório do Azure ad** -seja sincronizado com um diretório local ou um diretório somente na nuvem.
3. **Azure AD Domain Services** deve ser habilitado para o diretório do Azure AD. Se você ainda não fez isso, siga todas as tarefas descritas no [Guia de introdução](tutorial-create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Conclua as etapas necessárias para [sincronizar as senhas para seu Azure AD Domain Services domínio gerenciado](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Provisionar uma máquina virtual Ubuntu Linux
Provisione uma máquina virtual Ubuntu Linux no Azure, usando qualquer um dos seguintes métodos:
* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implante a máquina virtual na **mesma rede virtual na qual você habilitou Azure AD Domain Services**.
> * Escolha uma **sub-rede diferente** daquela na qual você habilitou Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Conectar-se remotamente à máquina virtual Ubuntu Linux
A máquina virtual Ubuntu foi provisionada no Azure. A próxima tarefa é conectar-se remotamente à máquina virtual usando a conta de administrador local criada durante o provisionamento da VM.

Siga as instruções no artigo [como entrar em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o arquivo de hosts na máquina virtual Linux
No seu terminal SSH, edite o arquivo/etc/hosts e atualize o endereço IP e o nome do host do seu computador.

```console
sudo vi /etc/hosts
```

No arquivo de hosts, insira o seguinte valor:

```console
127.0.0.1 contoso-ubuntu.contoso.com contoso-ubuntu
```

Aqui, ' contoso.com ' é o nome de domínio DNS do seu domínio gerenciado. ' contoso-Ubuntu ' é o nome do host da máquina virtual Ubuntu que você está unindo ao domínio gerenciado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar os pacotes necessários na máquina virtual Linux
Em seguida, instale os pacotes necessários para o ingresso no domínio na máquina virtual. Execute as seguintes etapas:

1.  No terminal SSH, digite o seguinte comando para baixar as listas de pacotes dos repositórios. Esse comando atualiza as listas de pacotes para obter informações sobre as versões mais recentes de pacotes e suas dependências.

    ```console
    sudo apt-get update
    ```

2. Digite o comando a seguir para instalar os pacotes necessários.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Durante a instalação do Kerberos, você verá uma tela rosa. A instalação do pacote ' krb5-User ' solicita o nome do Realm (em letras MAIÚSCULAs). A instalação grava as seções [Realm] e [domain_realm] em/etc/krb5.conf.

    > [!TIP]
    > Se o nome do domínio gerenciado for contoso.com, insira contoso.COM como o realm. Lembre-se de que o nome do Realm deve ser especificado em letras MAIÚSCULAs.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Definir as configurações de NTP (protocolo NTP) na máquina virtual Linux
A data e a hora da sua VM do Ubuntu devem sincronizar com o domínio gerenciado. Adicione o nome de host NTP do seu domínio gerenciado no arquivo/etc/ntp.conf.

```console
sudo vi /etc/ntp.conf
```

No arquivo NTP. conf, insira o seguinte valor e salve o arquivo:

```console
server contoso.com
```

Aqui, ' contoso.com ' é o nome de domínio DNS do seu domínio gerenciado.

Agora, sincronize a data e hora da VM Ubuntu com o servidor NTP e, em seguida, inicie o serviço NTP:

```console
sudo systemctl stop ntp
sudo ntpdate contoso.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ingresse a máquina virtual Linux no domínio gerenciado
Agora que os pacotes necessários estão instalados na máquina virtual Linux, a próxima tarefa é unir a máquina virtual ao domínio gerenciado.

1. Descubra o domínio gerenciado dos serviços de domínio do AAD. No terminal SSH, digite o seguinte comando:

    ```console
    sudo realm discover contoso.COM
    ```

   > [!NOTE]
   > **Solução** Se a *descoberta de territórios* não conseguir localizar seu domínio gerenciado:
   >   * Verifique se o domínio está acessível da máquina virtual (tente executar ping).
   >   * Verifique se a máquina virtual realmente foi implantada na mesma rede virtual em que o domínio gerenciado está disponível.
   >   * Verifique se você atualizou as configurações do servidor DNS para a rede virtual para apontar para os controladores de domínio do domínio gerenciado.

2. Inicialize o Kerberos. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > * Certifique-se de especificar um usuário que pertença ao grupo ' Administradores do controlador de domínio do AAD '. Se necessário, [adicione uma conta de usuário a um grupo no Azure ad](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)
    > * Especifique o nome de domínio em letras maiúsculas, caso contrário, kinit falhará.
    >

    ```console
    kinit bob@contoso.COM
    ```

3. Ingresse o computador no domínio. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > Use a mesma conta de usuário especificada na etapa anterior (' kinit ').
    >
    > Se sua VM não puder ingressar no domínio, verifique se o grupo de segurança de rede da VM permite o tráfego de saída do Kerberos na porta TCP + UDP 464 para a sub-rede da rede virtual para o domínio gerenciado do Azure AD DS.

    ```console
    sudo realm join --verbose contoso.COM -U 'bob@contoso.COM' --install=/
    ```

Você deverá receber uma mensagem ("computador registrado com êxito no Realm") quando o computador tiver ingressado com êxito no domínio gerenciado.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Atualizar a configuração do SSSD e reiniciar o serviço
1. No terminal SSH, digite o comando a seguir. Abra o arquivo SSSD. conf e faça a seguinte alteração
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Comente a linha **use_fully_qualified_names = true** e salve o arquivo.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Reinicie o serviço SSSD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configurar a criação automática do diretório base
Para habilitar a criação automática do diretório base após a entrada de usuários, digite os seguintes comandos em seu terminal de saída:

```console
sudo vi /etc/pam.d/common-session
```

Adicione a seguinte linha neste arquivo abaixo da linha ' session optional pam_sss. portanto ' e salve-o:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Verificar ingresso no domínio
Verifique se o computador foi ingressado com êxito no domínio gerenciado. Conecte-se à VM Ubuntu ingressada no domínio usando uma conexão SSH diferente. Use uma conta de usuário de domínio e verifique se a conta de usuário foi resolvida corretamente.

1. No terminal SSH, digite o seguinte comando para se conectar à máquina virtual Ubuntu ingressada no domínio usando SSH. Use uma conta de domínio que pertença ao domínio gerenciado (por exemplo,bob@contoso.COM' ' neste caso).
    
    ```console
    ssh -l bob@contoso.COM contoso-ubuntu.contoso.com
    ```

2. No terminal SSH, digite o seguinte comando para ver se o diretório base foi inicializado corretamente.
    
    ```console
    pwd
    ```

3. No terminal SSH, digite o seguinte comando para ver se as associações de grupo estão sendo resolvidas corretamente.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder ao grupo ' Administradores do AAD DC ' privilégios do sudo
Você pode conceder aos membros do grupo ' Administradores do controlador de domínio do AAD ' privilégios administrativos na VM do Ubuntu. O arquivo sudo está localizado em/etc/sudoers. Os membros de grupos do AD adicionados em sudoers podem executar sudo.

1. No seu terminal SSH, verifique se você está conectado com privilégios de superusuário. Você pode usar a conta de administrador local que você especificou ao criar a VM. Execute o seguinte comando:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Adicione a seguinte entrada ao arquivo/etc/sudoers e salve-o:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Agora você pode entrar como um membro do grupo ' Administradores do controlador de domínio do AAD ' e deve ter privilégios administrativos na VM.


## <a name="troubleshooting-domain-join"></a>Solucionando problemas de ingresso no domínio
Consulte o artigo [solução de problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues) .


## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](tutorial-create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um Azure AD Domain Services domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como entrar em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
