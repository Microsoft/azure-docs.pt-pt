---
title: 'Azure Active Directory Domain Services: Junte-se a uma VM do Ubuntu a um domínio gerido | Documentos da Microsoft'
description: Junte-se a uma máquina virtual do Ubuntu Linux para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 8699585a7f8e5cdfc81a40b94fbe10fa677a0030
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120997"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Junte-se a uma máquina virtual do Ubuntu no Azure a um domínio gerido
Este artigo mostra-lhe como associar uma máquina virtual do Ubuntu Linux a um domínio gerido do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:  
1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe ao seu domínio gerido do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Aprovisionar uma máquina virtual do Ubuntu Linux
Aprovisione uma máquina virtual do Ubuntu Linux no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado o Azure AD Domain Services**.
> * Escolher uma **outra sub-rede** em que tiver ativado o Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Ligar remotamente à máquina virtual do Ubuntu Linux
A máquina virtual do Ubuntu tiver sido aprovisionada no Azure. A tarefa seguinte é ligar remotamente à máquina virtual com a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão a uma máquina virtual em execução no Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP do seu computador e o nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. "contoso-ubuntu" é o nome do anfitrião da máquina virtual Ubuntu que são a associar ao domínio gerido.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina virtual do Linux
Em seguida, instale pacotes necessários para associação a um domínio na máquina virtual. Execute os seguintes passos:

1.  No seu terminal SSH, escreva o seguinte comando para transferir as listas de pacote a partir de repositórios. Este comando atualiza as listas de pacote para obter informações sobre as versões mais recentes dos pacotes e suas dependências.

    ```
    sudo apt-get update
    ```

2. Escreva o seguinte comando para instalar os pacotes necessários.
    ```
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Durante a instalação de Kerberos, verá um ecrã rosa. A instalação do pacote 'krb5-user' solicita o nome do realm (em todas as letras maiúsculas). A instalação escreve [território] e [domain_realm] secções /etc/krb5.conf.

    > [!TIP]
    > Se o nome do seu domínio gerido é contoso100.com, digite CONTOSO100.COM como o realm. Lembre-se de que o nome do realm deve ser especificado em UPPERCASE.
    >
    >


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Configure as definições de NTP (Network Time Protocol) na máquina virtual do Linux
A data e hora da sua VM do Ubuntu devem sincronizar com o domínio gerido. Adicione o nome de anfitrião do seu domínio gerido NTP no ficheiro /etc/ntp.conf.

```
sudo vi /etc/ntp.conf
```

No ficheiro ntp.conf, introduza o seguinte valor e guarde o ficheiro:

```
server contoso100.com
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido.

Sincronizar agora a data e hora com o servidor NTP da VM do Ubuntu e, em seguida, inicie o serviço NTP:

```
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Junte-se a máquina virtual do Linux ao domínio gerido
Agora que os pacotes necessários estão instalados na máquina virtual do Linux, a próxima tarefa é associar a máquina virtual para o domínio gerido.

1. Descubra o domínio gerido dos serviços de domínio do AAD. No seu terminal SSH, escreva o seguinte comando:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Resolução de problemas:** Se *realm detetar* não conseguiu encontrar o seu domínio gerido:
   >   * Certifique-se de que o domínio está acessível a partir da máquina virtual (tente ping).
   >   * Verifique que a máquina virtual, de fato, foi implementada para a mesma rede virtual em que o domínio gerido está disponível.
   >   * Verifique se a atualizar as definições do servidor DNS para a rede virtual para que apontem para os controladores de domínio do domínio gerido.

2. Inicialize o Kerberos. No seu terminal SSH, escreva o seguinte comando:

    > [!TIP]
    > * Certifique-se de que especifica um usuário que pertence ao grupo "Administradores do AAD DC".
    > * Especifique o nome de domínio em letras maiúsculas, kinit outra falha.
    >

    ```
    kinit bob@CONTOSO100.COM
    ```

3. Junte-se a máquina ao domínio. No seu terminal SSH, escreva o seguinte comando:

    > [!TIP]
    > Utilize a mesma conta de utilizador que especificou no passo anterior ("kinit').
    >

    ```
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

Obterá uma mensagem ("inscrito com êxito máquina no realm") quando a máquina com êxito é associada ao domínio gerido.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Atualize a configuração de SSSD e reinicie o serviço
1. No seu terminal SSH, escreva o seguinte comando. Abra o ficheiro de sssd.conf e faça a alteração seguinte
    ```
    sudo vi /etc/sssd/sssd.conf
    ```

2. Comente a linha **use_fully_qualified_names = True** e guarde o ficheiro.
    ```
    # use_fully_qualified_names = True
    ```

3. Reinicie o serviço SSSD.
    ```
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configurar a criação do diretório de raiz automática
Para ativar a criação automática de directório raiz depois de iniciar sessão dos utilizadores, escreva os seguintes comandos no seu terminal PuTTY:
```
sudo vi /etc/pam.d/common-session
```

Adicione a seguinte linha neste ficheiro abaixo da linha 'pam_sss.so opcional de sessão' e guarde-o:
```
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que se a máquina foi associada com êxito para o domínio gerido. Ligar ao domínio associado a um VM com Ubuntu utilizando uma ligação de SSH diferente. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador foi resolvida corretamente.

1. O SSH do terminal, escreva o seguinte comando para ligar ao domínio associado através de SSH de máquina virtual do Ubuntu. Utilize uma conta de domínio a que pertence ao domínio gerido (por exemplo, "bob@CONTOSO100.COM" em vez disso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. No seu terminal SSH, escreva o seguinte comando para ver se o diretório de raiz foi inicializado corretamente.
    ```
    pwd
    ```

3. No seu terminal SSH, escreva o seguinte comando para ver se as associações de grupo estão a ser resolvidas corretamente.
    ```
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceder privilégios de sudo de grupo "Administradores do AAD DC"
Pode conceder a membros dos privilégios administrativos do grupo "Administradores do AAD DC" na VM do Ubuntu. O ficheiro de sudo está localizado em /etc/sudoers. Os membros de grupos do AD adicionados no sudoers podem executar o sudo.

1. No seu terminal SSH, certifique-se de que iniciou sessão com privilégios de Superutilizador. Pode utilizar a conta de administrador local que especificou ao criar a VM. Execute o seguinte comando:
    ```
    sudo vi /etc/sudoers
    ```

2. Adicione a seguinte entrada para o ficheiro de /etc/sudoers e guardá-lo:
    ```
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Pode agora iniciar sessão como um membro do grupo "Administradores do AAD DC" e deve ter privilégios administrativos na VM.


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação a um domínio
Consulte a [associação de domínio de resolução de problemas](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artigo.


## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](active-directory-ds-getting-started.md)
* [Junte-se a uma máquina virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como iniciar sessão a uma máquina virtual em execução no Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
