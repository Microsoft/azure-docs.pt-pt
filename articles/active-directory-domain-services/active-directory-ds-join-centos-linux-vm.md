---
title: 'Azure Active Directory Domain Services: Junte-se numa VM CentOS a um domínio gerido | Documentos da Microsoft'
description: Junte-se a uma máquina virtual CentOS Linux para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 6d78e310ffd5b473c3d7e44b4edbf9caaff7655f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077671"
---
# <a name="join-a-centos-linux-virtual-machine-to-a-managed-domain"></a>Junte-se a uma máquina virtual CentOS Linux a um domínio gerido
Este artigo mostra-lhe como associar uma máquina virtual CentOS Linux no Azure a um domínio gerido do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:
1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe ao seu domínio gerido do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-centos-linux-virtual-machine"></a>Aprovisionar uma máquina virtual CentOS Linux
Aprovisione a máquina virtual CentOS no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado o Azure AD Domain Services**.
> * Escolher uma **outra sub-rede** em que tiver ativado o Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Ligar remotamente à máquina virtual do Linux recentemente aprovisionada
A máquina virtual do CentOS tiver sido aprovisionada no Azure. A tarefa seguinte é ligar remotamente à máquina virtual com a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão a uma máquina virtual em execução no Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP do seu computador e o nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-centos.contoso100.com contoso-centos
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. "contoso-centos" é o nome do anfitrião da máquina virtual CentOS que são a associar ao domínio gerido.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar pacotes necessários na máquina virtual do Linux
Em seguida, instale pacotes necessários para associação a um domínio na máquina virtual. No seu terminal SSH, escreva o seguinte comando para instalar os pacotes necessários:

    ```
    sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
    ```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Junte-se a máquina virtual do Linux ao domínio gerido
Agora que os pacotes necessários estão instalados na máquina virtual do Linux, a próxima tarefa é associar a máquina virtual para o domínio gerido.

1. Descubra o domínio gerido dos serviços de domínio do AAD. No seu terminal SSH, escreva o seguinte comando:

    ```
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **Resolução de problemas:** Se *realm detetar* não conseguiu encontrar o seu domínio gerido:  
   >    * Certifique-se de que o domínio está acessível a partir da máquina virtual (tente ping).  
   >    * Verifique que a máquina virtual, de fato, foi implementada para a mesma rede virtual em que o domínio gerido está disponível.
   >    * Verifique se a atualizar as definições do servidor DNS para a rede virtual para que apontem para os controladores de domínio do domínio gerido.  

2. Inicialize o Kerberos. No seu terminal SSH, escreva o seguinte comando:

    > [!TIP]
    > * Especifique um utilizador a quem pertence ao grupo "Administradores do AAD DC".
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
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM'
    ```

Obterá uma mensagem ("inscrito com êxito máquina no realm") quando a máquina com êxito é associada ao domínio gerido.


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que se a máquina foi associada com êxito para o domínio gerido. Ligue-se para as VM com uma ligação de SSH diferente de CentOS do associados a um domínio. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador foi resolvida corretamente.

1. O SSH do terminal, escreva o seguinte comando para ligar ao domínio associado a máquina de virtual CentOS através de SSH. Utilize uma conta de domínio a que pertence ao domínio gerido (por exemplo, "bob@CONTOSO100.COM" em vez disso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-centos.contoso100.com
    ```

2. No seu terminal SSH, escreva o seguinte comando para ver se o diretório de raiz foi inicializado corretamente.
    ```
    pwd
    ```

3. No seu terminal SSH, escreva o seguinte comando para ver se as associações de grupo estão a ser resolvidas corretamente.
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>Resolução de problemas de associação a um domínio
Consulte a [associação de domínio de resolução de problemas](active-directory-ds-admin-guide-join-windows-vm-portal.md#troubleshoot-joining-a-domain) artigo.

## <a name="related-content"></a>Conteúdo relacionado
* [Azure AD Domain Services - guia de introdução](active-directory-ds-getting-started.md)
* [Junte-se a uma máquina virtual do Windows Server a um domínio gerido do Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como iniciar sessão a uma máquina virtual em execução no Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Instalar o Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 – guia de integração do Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
