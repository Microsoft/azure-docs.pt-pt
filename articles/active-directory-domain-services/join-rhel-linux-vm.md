---
title: Junte-se a um RHEL VM para Azure AD Domain Services | Microsoft Docs
description: Aprenda a configurar e junte-se a uma máquina virtual Red Hat Enterprise Linux para um domínio gerido por Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 285a972936bfdf4b173e2a20223143883cd8b7d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619560"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Junte-se a uma máquina virtual Red Hat Enterprise Linux para um domínio gerido por Azure Ative Directory Domain Services

Para permitir que os utilizadores inscrevam-se em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS). Quando se junta um VM a um domínio gerido por Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar scontabilidade e gerir servidores. Os membros do grupo do domínio gerido também são aplicados para permitir que você controle o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um VM red hat Enterprise Linux (RHEL) a um domínio gerido.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, o primeiro tutorial cria e configura um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Criar e ligar a um RHEL Linux VM

Se tiver um RHEL Linux VM existente em Azure, ligue-o utilizando sSH, em seguida, continue para o passo seguinte para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um RHEL Linux VM, ou quiser criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando criar o VM, preste atenção às definições de rede virtual para garantir que o VM pode comunicar com o domínio gerido:

* Implemente o VM na mesma, ou numa rede virtual esprevada na qual ativou os Serviços de Domínio AD Azure.
* Coloque o VM numa sub-rede diferente do domínio gerido pelos Serviços de Domínio AZure AD.

Uma vez implantado o VM, siga os passos para ligar ao VM utilizando SSH.

## <a name="configure-the-hosts-file"></a>Configure o ficheiro dos anfitriões

Para se certificar de que o nome do anfitrião VM está corretamente configurado para o domínio gerido, edite o ficheiro */etc/anfitriões* e desajuste o nome de anfitrião:

```console
sudo vi /etc/hosts
```

No ficheiro dos *anfitriões,* atualize o endereço *local.* No seguinte exemplo:

* *aaddscontoso.com* é o nome de domínio DNS do seu domínio gerido.
* *Rhel* é o nome de anfitrião do seu RHEL VM que está se juntando ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Quando terminar, guarde e saia do ficheiro dos *anfitriões* utilizando `:wq` o comando do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

O VM precisa de alguns pacotes adicionais para juntar o VM ao domínio gerido. Para instalar e configurar estes pacotes, atualize e instale as ferramentas de união de domínios utilizando `yum` . Existem algumas diferenças entre RHEL 7.x e RHEL 6.x, por isso use os comandos apropriados para a sua versão distro nas secções restantes deste artigo.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Junte-se ao VM para o domínio gerido

Agora que os pacotes necessários são instalados no VM, junte o VM ao domínio gerido. Mais uma vez, utilize os passos adequados para a sua versão de distro RHEL.

### <a name="rhel-7"></a>RHEL 7

1. Utilize o `realm discover` comando para descobrir o domínio gerido. O exemplo a seguir descobre o reino *AADDSCONTOSO.COM.* Especifique o seu próprio nome de domínio gerido em TODOS OS MAI:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se o `realm discover` comando não conseguir encontrar o seu domínio gerido, reveja as seguintes etapas de resolução de problemas:

    * Certifique-se de que o domínio está acessível a partir do VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é devolvida.
    * Verifique se o VM é implantado na mesma, ou numa rede virtual esprevada em que o domínio gerido está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para indicar os controladores de domínio do domínio gerido.

1. Agora inicialize Kerberos usando o `kinit` comando. Especifique um utilizador que faz parte do domínio gerido. Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Mais uma vez, o nome de domínio gerido deve ser introduzido em TODOS OS MAI. No exemplo seguinte, a conta nomeada `contosoadmin@aaddscontoso.com` é usada para inicializar Kerberos. Insira a sua própria conta de utilizador que faz parte do domínio gerido:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Por fim, junte o VM ao domínio gerido utilizando o `realm join` comando. Utilize a mesma conta de utilizador que é uma parte do domínio gerido que especificou no `kinit` comando anterior, `contosoadmin@AADDSCONTOSO.COM` como:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Leva alguns momentos para juntar o VM ao domínio gerido. A saída de exemplo a seguir mostra que o VM se juntou com sucesso ao domínio gerido:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Utilize o `adcli info` comando para descobrir o domínio gerido. O exemplo a seguir descobre o reino *ADDDSCONTOSO.COM.* Especifique o seu próprio nome de domínio gerido em TODOS OS MAI:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Se o `adcli info` comando não conseguir encontrar o seu domínio gerido, reveja as seguintes etapas de resolução de problemas:

    * Certifique-se de que o domínio está acessível a partir do VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é devolvida.
    * Verifique se o VM é implantado na mesma, ou numa rede virtual esprevada em que o domínio gerido está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para indicar os controladores de domínio do domínio gerido.

1. Primeiro, junte-se ao domínio usando o `adcli join` comando, este comando também cria o keytab para autenticar a máquina. Utilize uma conta de utilizador que faça parte do domínio gerido.

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Agora, configuure os `/ect/krb5.conf` ficheiros e crie os `/etc/sssd/sssd.conf` ficheiros para utilizar o `aaddscontoso.com` domínio ative directory.
   Certifique-se de que `AADDSCONTOSO.COM` é substituído pelo seu próprio nome de domínio:

    Abra o `/ect/krb5.conf` ficheiro com um editor:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Atualize o `krb5.conf` ficheiro para corresponder à seguinte amostra:

    ```console
    [logging]
     default = FILE:/var/log/krb5libs.log
     kdc = FILE:/var/log/krb5kdc.log
     admin_server = FILE:/var/log/kadmind.log
    
    [libdefaults]
     default_realm = AADDSCONTOSO.COM
     dns_lookup_realm = true
     dns_lookup_kdc = true
     ticket_lifetime = 24h
     renew_lifetime = 7d
     forwardable = true
    
    [realms]
     AADDSCONTOSO.COM = {
     kdc = AADDSCONTOSO.COM
     admin_server = AADDSCONTOSO.COM
     }
    
    [domain_realm]
     .AADDSCONTOSO.COM = AADDSCONTOSO.COM
     AADDSCONTOSO.COM = AADDSCONTOSO.COM
    ```
    
   Criar o `/etc/sssd/sssd.conf` ficheiro:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Atualize o `sssd.conf` ficheiro para corresponder à seguinte amostra:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Certifique-se de que `/etc/sssd/sssd.conf` as permissões são 600 e que são propriedade do utilizador raiz:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Utilize `authconfig` para instruir o VM sobre a integração da AD Linux:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Iniciar e ativar o serviço sssd:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Se o seu VM não conseguir completar com sucesso o processo de ligação ao domínio, certifique-se de que o grupo de segurança de rede da VM permite o tráfego kerberos de saída na porta TCP + UDP 464 para a sub-rede de rede virtual para o seu domínio gerido.

Agora verifique se pode consultar as informações da AD do utilizador usando `getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Permitir a autenticação de palavra-passe para SSH

Por predefinição, os utilizadores só podem iniciar súm numa VM utilizando a autenticação baseada em chaves SSH. A autenticação baseada em palavras-passe falha. Quando se junta o VM a um domínio gerido, essas contas de domínio precisam de utilizar a autenticação baseada em palavras-passe. Atualize a configuração SSH para permitir a autenticação baseada em palavras-passe da seguinte forma.

1. Abra o ficheiro *sshd_conf* com um editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Atualizar a linha para *passwordAufercation* para *sim*:

    ```console
    PasswordAuthentication yes
    ```

    Quando terminar, guarde e saia do ficheiro *sshd_conf* utilizando `:wq` o comando do editor.

1. Para aplicar as alterações e deixar que os utilizadores inscrevam-se através de uma palavra-passe, reinicie o serviço SSH para a sua versão de distro RHEL:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda privilégios ao grupo "Administradores AAD DC"

Para conceder aos membros do grupo *de administradores da AAD DC privilégios* administrativos no RHEL VM, adicione uma entrada aos */etc/sudoers*. Uma vez adicionados, os membros do grupo *de administradores AAD DC* podem utilizar o comando no `sudo` RHEL VM.

1. Abra o ficheiro *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada no ficheiro de */etc/sudoers.* O grupo *de administradores AAD DC* contém espaço em branco no nome, por isso inclua o personagem de escape de backslash no nome de grupo. Adicione o seu próprio nome de domínio, como *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, salve e saia do editor usando o `:wq` comando do editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi ligado com sucesso ao domínio gerido, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença ao domínio gerido utilizando o `ssh -l` comando, tal como `contosoadmin@aaddscontoso.com` e, em seguida, introduza o endereço do seu VM, como *rhel.aaddscontoso.com*. Se utilizar o Azure Cloud Shell, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Quando tiver uma ligação com sucesso ao VM, verifique se o diretório doméstico foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no *diretório /home* com o seu próprio diretório que corresponde à conta de utilizador.

1. Agora verifique se os membros do grupo estão a ser resolvidos corretamente:

    ```console
    id
    ```

    Devia ver os seus membros do grupo do domínio gerido.

1. Se se inscreveu no VM como membro do grupo de administradores da *AAD DC,* verifique se pode utilizar corretamente o `sudo` comando:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido ou iniciar sessão com uma conta de domínio, consulte [o domínio de resolução de problemas para se juntar a problemas](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
