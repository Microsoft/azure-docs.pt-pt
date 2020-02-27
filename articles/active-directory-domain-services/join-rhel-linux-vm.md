---
title: Junte-se a um VM RHEL para os Serviços de Domínio Azure AD  Microsoft Docs
description: Aprenda a configurar e junte-se a uma máquina virtual Red Hat Enterprise Linux para um domínio gerido pela Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 16100caa-f209-4cb0-86d3-9e218aeb51c6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: 1be9134ee217cb91461e89c9908b889a14ec0c3a
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613790"
---
# <a name="join-a-red-hat-enterprise-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Associar uma máquina virtual do Red Hat Enterprise Linux a um domínio gerido dos Serviços de Domínio do Azure AD

Para permitir que os utilizadores inscrevam em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido pelo Azure Ative Directory Domain Services (AD DS). Quando se junta a um VM a um domínio gerido pelo Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar sessão e gerir servidores. As adesões do grupo do domínio gerido pelo Azure AD DS também são aplicadas para permitir controlar o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um Red Hat Enterprise Linux (RHEL) VM para um domínio gerido pela Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, o primeiro tutorial cria e configura uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Uma conta de utilizador que é membro do grupo de administradores da *Azure AD DC* no seu inquilino Azure AD.

## <a name="create-and-connect-to-a-rhel-linux-vm"></a>Criar e ligar a um VM RHEL Linux

Se tiver um VM RHEL Linux existente em Azure, ligue-o utilizando SSH e continue para o próximo passo para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um VM RHEL Linux, ou quiser criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

* [Portal do Azure](../virtual-machines/linux/quick-create-portal.md)
* [CLI do Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Quando criar o VM, preste atenção às definições de rede virtual para se certificar de que o VM pode comunicar com o domínio gerido pelo Azure AD DS:

* Desloque o VM para a mesma rede virtual, ou uma rede virtual em que permitiu o Azure AD Domain Services.
* Coloque o VM numa subrede diferente da instância dos Serviços de Domínio Azure AD.

Uma vez implantado o VM, siga os passos para ligar ao VM utilizando SSH.

## <a name="configure-the-hosts-file"></a>Configure o ficheiro de anfitriões

Para se certificar de que o nome de anfitrião VM está corretamente configurado para o domínio gerido, edite o ficheiro */etc/anfitriões* e detetete o nome de anfitrião:

```console
sudo vi /etc/hosts
```

No ficheiro dos *anfitriões,* atualize *o* endereço local. No seguinte exemplo:

* *aaddscontoso.com* é o nome de domínio DNS do seu domínio gerido pelo Azure AD DS.
* *rhel* é o nome de anfitrião do seu VM RHEL que está se juntando ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 rhel rhel.aaddscontoso.com
```

Quando estiver feito, guarde e saia do ficheiro dos *anfitriões* utilizando o comando `:wq` do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

O VM precisa de alguns pacotes adicionais para se juntar ao VM ao domínio gerido pela AD DS Azure. Para instalar e configurar estes pacotes, atualize e instale as ferramentas de união de domínio utilizando `yum`. Existem algumas diferenças entre RHEL 7.x e RHEL 6.x, por isso use os comandos apropriados para a sua versão de distro nas restantes secções deste artigo.

**RHEL 7**

```console
sudo yum install realmd sssd krb5-workstation krb5-libs oddjob oddjob-mkhomedir samba-common-tools
```

**RHEL 6**

```console
sudo yum install adcli sssd authconfig krb5-workstation
```

## <a name="join-vm-to-the-managed-domain"></a>Junte-se à VM ao domínio gerido

Agora que os pacotes necessários são instalados no VM, junte-se ao VM ao domínio gerido pelo Azure AD DS. Mais uma vez, utilize os passos adequados para a sua versão rhel distro.

### <a name="rhel-7"></a>RHEL 7

1. Use o comando `realm discover` para descobrir o domínio gerido pela AD DS Azure. O exemplo que se segue descobre o reino *AADDSCONTOSO.COM.* Especifique o seu próprio nome de domínio gerido azure AD DS em TODAS as MAIDES:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se o comando `realm discover` não encontrar o seu domínio gerido pelo Azure AD DS, reveja os seguintes passos de resolução de problemas:

    * Certifique-se de que o domínio é acessível a partir do VM. Tente `ping aaddscontoso.com` para ver se uma resposta positiva é devolvida.
    * Verifique se o VM está implantado na mesma rede virtual, ou numa rede virtual em que o domínio gerido pelo Azure AD DS está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerido pelo Azure AD DS.

1. Agora rubrica kerberos usando o comando `kinit`. Especifique um utilizador que pertença ao grupo de *administradores da AAD DC.* Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Mais uma vez, o nome de domínio gerido pela AD DS azure deve ser introduzido em TODAS as MAIDES. No exemplo seguinte, a conta chamada `contosoadmin@aaddscontoso.com` é usada para inicializar Kerberos. Insira a sua própria conta de utilizador que é membro do grupo de administradores da *AAD DC:*

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Por fim, junte a máquina ao domínio gerido azure AD DS utilizando o comando `realm join`. Utilize a mesma conta de utilizador que é membro do grupo de administradores da *AAD DC* que especificou no comando de `kinit` anterior, como `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM'
    ```

Leva alguns momentos para se juntar ao VM ao domínio gerido pela AD DS Azure. A saída de exemplo que se segue mostra que o VM se juntou com sucesso ao domínio gerido pelo Azure AD DS:

```output
Successfully enrolled machine in realm
```

### <a name="rhel-6"></a>RHEL 6

1. Use o comando `adcli info` para descobrir o domínio gerido pela AD DS Azure. O exemplo que se segue descobre o reino *ADDDSCONTOSO.COM.* Especifique o seu próprio nome de domínio gerido azure AD DS em TODAS as MAIDES:

    ```console
    sudo adcli info aaddscontoso.com
    ```

   Se o comando `adcli info` não encontrar o seu domínio gerido pelo Azure AD DS, reveja os seguintes passos de resolução de problemas:

    * Certifique-se de que o domínio é acessível a partir do VM. Tente `ping aaddscontoso.com` para ver se uma resposta positiva é devolvida.
    * Verifique se o VM está implantado na mesma rede virtual, ou numa rede virtual em que o domínio gerido pelo Azure AD DS está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerido pelo Azure AD DS.

1. Primeiro, junte-se ao domínio utilizando o comando `adcli join`, este comando também criará o separador chave para autenticar a máquina. Utilize uma conta de utilizador que seja membro do grupo de administradores da *AAD DC.*

    ```console
    sudo adcli join aaddscontoso.com -U contosoadmin
    ```

1. Agora configure o `/ect/krb5.conf` e crie os ficheiros `/etc/sssd/sssd.conf` para utilizar o domínio `aaddscontoso.com` Diretório Ativo.
   Certifique-se de que `AADDSCONTOSO.COM` é substituído pelo seu próprio nome de domínio:

    Abra o ficheiro `/ect/krb5.conf` com um editor:

    ```console
    sudo vi /etc/krb5.conf
    ```

    Atualize o ficheiro `krb5.conf` para combinar com a seguinte amostra:

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
    
   Crie o ficheiro `/etc/sssd/sssd.conf`:
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

    Atualize o ficheiro `sssd.conf` para combinar com a seguinte amostra:

    ```console
    [sssd]
     services = nss, pam, ssh, autofs
     config_file_version = 2
     domains = AADDSCONTOSO.COM
    
    [domain/AADDSCONTOSO.COM]
    
     id_provider = ad
    ```

1. Certifique-se de `/etc/sssd/sssd.conf` permissões são 600 e é propriedade do utilizador raiz:

    ```console
    sudo chmod 600 /etc/sssd/sssd.conf
    sudo chown root:root /etc/sssd/sssd.conf
    ```

1. Utilize `authconfig` para instruir o VM sobre a integração do AD Linux:

    ```console
    sudo authconfig --enablesssd --enablesssdauth --update
    ```

1. Iniciar e ativar o serviço sssd:

    ```console
    sudo service sssd start
    sudo chkconfig sssd on
    ```

Se o seu VM não conseguir completar com sucesso o processo de união de domínios, certifique-se de que o grupo de segurança de rede da VM permite o tráfego de saída da Kerberos na porta 464 TCP + UDP para a subnet de rede virtual para o seu domínio gerido pelo Azure AD DS.

Verifique agora se pode consultar informações do utilizador usando `getent`

```console
sudo getent passwd contosoadmin
```

## <a name="allow-password-authentication-for-ssh"></a>Permitir a autenticação de senha para SSH

Por predefinição, os utilizadores só podem iniciar sessão num VM utilizando a autenticação baseada em chaves SSH. A autenticação baseada em palavra-passe falha. Quando se junta ao VM a um domínio gerido pelo Azure AD DS, essas contas de domínio precisam de utilizar a autenticação baseada em palavra-passe. Atualize a configuração SSH para permitir a autenticação baseada em palavra-passe da seguinte forma.

1. Abra o ficheiro *sshd_conf* com um editor:

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. Atualize a linha para *A Autenticação de Palavras-Passe* para *sim:*

    ```console
    PasswordAuthentication yes
    ```

    Quando estiver feito, guarde e saia do ficheiro *sshd_conf* utilizando o comando `:wq` do editor.

1. Para aplicar as alterações e deixar os utilizadores iniciarem o início com uma palavra-passe, reinicie o serviço SSH para a sua versão distro RHEL:

   **RHEL 7**

    ```console
    sudo systemctl restart sshd
    ```

   **RHEL 6**

    ```console
    sudo service sshd restart
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda os privilégios do grupo 'Administradores da AAD DC'

Para conceder aos membros do grupo de *administradores da AAD DC privilégios* administrativos no VM RHEL, adicione uma entrada para os */etc/sudoers*. Uma vez adicionados, os membros do grupo de administradores da *AAD DC* podem usar o comando `sudo` no VM RHEL.

1. Abra o ficheiro *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao fim do ficheiro */etc/sudoers.* O grupo de administradores da *AAD DC* contém espaço branco no nome, por isso inclua o personagem de fuga backslash no nome do grupo. Adicione o seu próprio nome de domínio, como *aaddscontoso.com:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    Quando estiver feito, guarde e saia do editor usando o comando `:wq` do editor.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi bem-sucedido juntou-se ao domínio gerido pelo Azure AD DS, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo a partir do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença ao domínio gerido utilizando o comando `ssh -l`, como `contosoadmin@aaddscontoso.com` e, em seguida, introduza o endereço do seu VM, como *rhel.aaddscontoso.com*. Se utilizar a Concha de Nuvem Azure, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com rhel.aaddscontoso.com
    ```

1. Quando tiver ligações com sucesso ao VM, verifique se o diretório inicialmente foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no diretório */home* com o seu próprio diretório que corresponda à conta de utilizador.

1. Verifique agora se os membros do grupo estão a ser resolvidos corretamente:

    ```console
    id
    ```

    Deve ver os seus membros do grupo do domínio gerido pelo Azure AD DS.

1. Se inscreveu o VM como membro do grupo de administradores da *AAD DC,* verifique se pode utilizar corretamente o comando `sudo`:

    ```console
    sudo yum update
    ```

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido pelo Azure AD DS ou iniciar sessão com uma conta de domínio, consulte problemas de resolução de [domínios](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
