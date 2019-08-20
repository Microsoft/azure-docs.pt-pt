---
title: 'Azure Active Directory Domain Services: Ingressar uma VM RHEL em um domínio gerenciado | Microsoft Docs'
description: Ingressar uma máquina virtual Red Hat Enterprise Linux Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 0e3803edd47c3589652b3fedecd12125e3ff40b7
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612796"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Associar uma máquina virtual do Red Hat Enterprise Linux 7 a um domínio gerido
Este artigo mostra como unir uma máquina virtual Red Hat Enterprise Linux (RHEL) 7 a um Azure AD Domain Services domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:  
1. Uma **assinatura válida do Azure**.
2. Um **diretório do Azure ad** -seja sincronizado com um diretório local ou um diretório somente na nuvem.
3. **Azure AD Domain Services** deve ser habilitado para o diretório do Azure AD. Se você ainda não fez isso, siga todas as tarefas descritas no [Guia de introdução](tutorial-create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Conclua as etapas necessárias para [sincronizar as senhas para seu Azure AD Domain Services domínio gerenciado](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Provisionar uma máquina virtual Red Hat Enterprise Linux
Provisione uma máquina virtual RHEL 7 no Azure, usando qualquer um dos seguintes métodos:
* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implante a máquina virtual na **mesma rede virtual na qual você habilitou Azure AD Domain Services**.
> * Escolha uma **sub-rede diferente** daquela na qual você habilitou Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conectar-se remotamente à máquina virtual do Linux recém-provisionado
A máquina virtual RHEL 7,2 foi provisionada no Azure. A próxima tarefa é conectar-se remotamente à máquina virtual usando a conta de administrador local criada durante o provisionamento da VM.

Siga as instruções no artigo [como entrar em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o arquivo de hosts na máquina virtual Linux
No seu terminal SSH, edite o arquivo/etc/hosts e atualize o endereço IP e o nome do host do seu computador.

```console
sudo vi /etc/hosts
```

No arquivo de hosts, insira o seguinte valor:

```console
127.0.0.1 contoso-rhel.contoso.com contoso-rhel
```

Aqui, ' contoso.com ' é o nome de domínio DNS do seu domínio gerenciado. ' contoso-RHEL ' é o nome do host da máquina virtual RHEL que você está unindo ao domínio gerenciado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalar os pacotes necessários na máquina virtual Linux
Em seguida, instale os pacotes necessários para o ingresso no domínio na máquina virtual. No terminal SSH, digite o seguinte comando para instalar os pacotes necessários:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
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
    > * Certifique-se de especificar um usuário que pertença ao grupo ' Administradores do controlador de domínio do AAD '.
    > * Especifique o nome de domínio em letras maiúsculas, caso contrário, kinit falhará.

    ```console
    kinit bob@contoso.COM
    ```

3. Ingresse o computador no domínio. No terminal SSH, digite o seguinte comando:

    > [!TIP]
    > Use a mesma conta de usuário especificada na etapa anterior (' kinit ').
    >
    > Se sua VM não puder ingressar no domínio, verifique se o grupo de segurança de rede da VM permite o tráfego de saída do Kerberos na porta TCP + UDP 464 para a sub-rede da rede virtual para o domínio gerenciado do Azure AD DS.

    ```console
    sudo realm join --verbose contoso.COM -U 'bob@contoso.COM'
    ```

Você deverá receber uma mensagem ("computador registrado com êxito no Realm") quando o computador tiver ingressado com êxito no domínio gerenciado.


## <a name="verify-domain-join"></a>Verificar ingresso no domínio
Verifique se o computador foi ingressado com êxito no domínio gerenciado. Conecte-se à VM RHEL ingressada no domínio usando uma conexão SSH diferente. Use uma conta de usuário de domínio e verifique se a conta de usuário foi resolvida corretamente.

1. No terminal SSH, digite o seguinte comando para se conectar à máquina virtual RHEL ingressada no domínio usando SSH. Use uma conta de domínio que pertença ao domínio gerenciado (por exemplo,bob@contoso.COM' ' neste caso).
    
    ```console
    ssh -l bob@contoso.COM contoso-rhel.contoso.com
    ```

2. No terminal SSH, digite o seguinte comando para ver se o diretório base foi inicializado corretamente.
    
    ```console
    pwd
    ```

3. No terminal SSH, digite o seguinte comando para ver se as associações de grupo estão sendo resolvidas corretamente.
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Solucionando problemas de ingresso no domínio
Consulte o artigo [solução de problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-domain-join-issues) .

## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](tutorial-create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um Azure AD Domain Services domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como entrar em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Instalando o Kerberos](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7-guia de integração do Windows](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
