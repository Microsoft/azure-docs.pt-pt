---
title: 'Azure Active Directory Domain Services: Junte-se a uma VM CoreOS com Linux a um domínio gerido | Documentos da Microsoft'
description: Associar uma máquina virtual Linux do CoreOS ao Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: fe9ec05b3d8f084f58d5836f1a077e952966ecef
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119331"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Junte-se a uma máquina virtual Linux do CoreOS a um domínio gerido
Este artigo mostra-lhe como associar uma máquina virtual Linux do CoreOS no Azure a um domínio gerido do Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas apresentadas neste artigo, terá de:
1. Válido **subscrição do Azure**.
2. Uma **diretório do Azure AD** -seja sincronizada com um diretório no local ou um diretório apenas na cloud.
3. **O Azure AD Domain Services** tem de estar ativada para o diretório do Azure AD. Se ainda não o fez, siga todas as tarefas descritas a [guia de introdução](active-directory-ds-getting-started.md).
4. Certifique-se de que configurou os endereços IP do domínio gerido que os servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua os passos necessários para [sincronizar as palavras-passe ao seu domínio gerido do Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Aprovisionar uma máquina virtual Linux do CoreOS
Aprovisione uma máquina virtual do CoreOS no Azure, utilizando qualquer um dos seguintes métodos:
* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Este artigo utiliza a **CoreOS Linux (estável)** imagem de máquina virtual no Azure.

> [!IMPORTANT]
> * Implementar a máquina virtual para o **mesma rede virtual em que tiver ativado o Azure AD Domain Services**.
> * Escolher uma **outra sub-rede** em que tiver ativado o Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Ligar remotamente à máquina virtual do Linux recentemente aprovisionada
A máquina virtual do CoreOS tiver sido aprovisionada no Azure. A tarefa seguinte é ligar remotamente à máquina virtual com a conta de administrador local criada durante o aprovisionamento da VM.

Siga as instruções no artigo [como iniciar sessão a uma máquina virtual em execução no Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o ficheiro de anfitriões da máquina virtual do Linux
No seu terminal SSH, edite o ficheiro /etc/hosts e atualizar o endereço IP do seu computador e o nome de anfitrião.

```
sudo vi /etc/hosts
```

No ficheiro de anfitriões, introduza o seguinte valor:

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
Aqui, 'contoso100.com' é o nome de domínio DNS do seu domínio gerido. "contoso-coreos" é o nome do anfitrião da máquina virtual CoreOS que são a associar ao domínio gerido.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configurar o serviço SSSD na máquina virtual do Linux
Em seguida, atualize o ficheiro de configuração de SSSD em ("/ etc/sssd/sssd.conf") de acordo com o exemplo a seguir:

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
Substitua "CONTOSO100. COM "com o nome de domínio DNS do seu domínio gerido. Certifique-se de que especificar o nome de domínio no caso de capital no ficheiro de conf.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Junte-se a máquina virtual do Linux ao domínio gerido
Agora que os pacotes necessários estão instalados na máquina virtual do Linux, a próxima tarefa é associar a máquina virtual para o domínio gerido.

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Resolução de problemas:** Se *adcli* não conseguiu encontrar o seu domínio gerido:
>   * Certifique-se de que o domínio está acessível a partir da máquina virtual (tente ping).
>   * Verifique que a máquina virtual, de fato, foi implementada para a mesma rede virtual em que o domínio gerido está disponível.
>   * Verifique se a atualizar as definições do servidor DNS para a rede virtual para que apontem para os controladores de domínio do domínio gerido.

Inicie o serviço SSSD. No seu terminal SSH, escreva o seguinte comando:
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>Certifique-se a associação a um domínio
Certifique-se de que se a máquina foi associada com êxito para o domínio gerido. Ligar ao domínio associado a um VM CoreOS com uma ligação de SSH diferente. Utilize uma conta de utilizador de domínio e, em seguida, verifique se a conta de utilizador foi resolvida corretamente.

1. O SSH do terminal, escreva o seguinte comando para ligar ao domínio associado CoreOS máquina de virtual através de SSH. Utilize uma conta de domínio a que pertence ao domínio gerido (por exemplo, "bob@CONTOSO100.COM" em vez disso.)
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
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
