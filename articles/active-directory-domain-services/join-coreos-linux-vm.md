---
title: 'Azure Active Directory Domain Services: Ingressar em uma VM do Linux CoreOS | Microsoft Docs'
description: Ingressar uma máquina virtual CoreOS Linux para Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 78a6c5262cd6668712beac1e041fa4f25c05a724
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234069"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Ingressar uma máquina virtual CoreOS Linux em um domínio gerenciado
Este artigo mostra como unir uma máquina virtual CoreOS Linux no Azure a um Azure AD Domain Services domínio gerenciado.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de começar
Para executar as tarefas listadas neste artigo, você precisará de:
1. Uma **assinatura válida do Azure**.
2. Um **diretório do Azure ad** -seja sincronizado com um diretório local ou um diretório somente na nuvem.
3. **Azure AD Domain Services** deve ser habilitado para o diretório do Azure AD. Se você ainda não fez isso, siga todas as tarefas descritas no [Guia de introdução](create-instance.md).
4. Verifique se você configurou os endereços IP do domínio gerenciado como servidores DNS para a rede virtual. Para obter mais informações, consulte [como atualizar as configurações de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
5. Conclua as etapas necessárias para [sincronizar as senhas para seu Azure AD Domain Services domínio gerenciado](active-directory-ds-getting-started-password-sync.md).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Provisionar uma máquina virtual CoreOS Linux
Provisione uma máquina virtual CoreOS no Azure, usando qualquer um dos seguintes métodos:
* [Azure portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Este artigo usa a imagem de máquina virtual **CoreOS Linux (estável)** no Azure.

> [!IMPORTANT]
> * Implante a máquina virtual na **mesma rede virtual na qual você habilitou Azure AD Domain Services**.
> * Escolha uma **sub-rede diferente** daquela na qual você habilitou Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conectar-se remotamente à máquina virtual do Linux recém-provisionado
A máquina virtual CoreOS foi provisionada no Azure. A próxima tarefa é conectar-se remotamente à máquina virtual usando a conta de administrador local criada durante o provisionamento da VM.

Siga as instruções no artigo [como fazer logon em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configurar o arquivo de hosts na máquina virtual Linux
No seu terminal SSH, edite o arquivo/etc/hosts e atualize o endereço IP e o nome do host do seu computador.

```console
sudo vi /etc/hosts
```

No arquivo de hosts, insira o seguinte valor:

```console
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```

Aqui, ' contoso100.com ' é o nome de domínio DNS do seu domínio gerenciado. ' contoso-Coreos ' é o nome do host da máquina virtual CoreOS que você está unindo ao domínio gerenciado.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configurar o serviço SSSD na máquina virtual Linux
Em seguida, atualize seu arquivo de configuração do SSSD no ('/etc/SSSD/SSSD.conf ') para corresponder ao exemplo a seguir:

```console
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

Substitua ' CONTOSO100 '. COM ' com o nome de domínio DNS do seu domínio gerenciado. Certifique-se de especificar o nome de domínio em maiúsculas no arquivo conf.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Ingresse a máquina virtual Linux no domínio gerenciado
Agora que os pacotes necessários estão instalados na máquina virtual Linux, a próxima tarefa é unir a máquina virtual ao domínio gerenciado.

```console
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **Solução** Se *adcli* não conseguir localizar seu domínio gerenciado:
>   * Verifique se o domínio está acessível da máquina virtual (tente executar ping).
>   * Verifique se a máquina virtual realmente foi implantada na mesma rede virtual em que o domínio gerenciado está disponível.
>   * Verifique se você atualizou as configurações do servidor DNS para a rede virtual para apontar para os controladores de domínio do domínio gerenciado.

Inicie o serviço SSSD. No terminal SSH, digite o seguinte comando:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Verificar ingresso no domínio
Verifique se o computador foi ingressado com êxito no domínio gerenciado. Conecte-se à VM CoreOS ingressada no domínio usando uma conexão SSH diferente. Use uma conta de usuário de domínio e verifique se a conta de usuário foi resolvida corretamente.

1. No terminal SSH, digite o seguinte comando para se conectar à máquina virtual CoreOS ingressada no domínio usando SSH. Use uma conta de domínio que pertença ao domínio gerenciado (por exemplo,bob@CONTOSO100.COM' ' neste caso).
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
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
Consulte o artigo [solução de problemas de ingresso no domínio](join-windows-vm.md#troubleshoot-joining-a-domain) .

## <a name="related-content"></a>Conteúdo relacionado
* [Guia de Introdução de Azure AD Domain Services](create-instance.md)
* [Ingressar uma máquina virtual do Windows Server em um Azure AD Domain Services domínio gerenciado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Como fazer logon em uma máquina virtual que executa o Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
