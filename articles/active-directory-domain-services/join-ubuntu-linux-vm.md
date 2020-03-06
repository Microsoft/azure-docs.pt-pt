---
title: Junte-se a um Ubuntu VM para os Serviços de Domínio Azure AD  Microsoft Docs
description: Aprenda a configurar e junte-se a uma máquina virtual Ubuntu Linux para um domínio gerido pela Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: bc5371ccbd3ba66117d5c613090b70ce7f07d51e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298846"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Junte-se a uma máquina virtual Ubuntu Linux para um domínio gerido pela Azure AD Domain Services

Para permitir que os utilizadores inscrevam em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido pelo Azure Ative Directory Domain Services (AD DS). Quando se junta a um VM a um domínio gerido pelo Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar sessão e gerir servidores. As adesões do grupo do domínio gerido pelo Azure AD DS também são aplicadas para permitir controlar o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um Ubuntu Linux VM a um domínio gerido pela Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, o primeiro tutorial cria e configura uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Crie e ligue a um Ubuntu Linux VM

Se tiver um Ubuntu Linux VM existente em Azure, ligue-o utilizando SSH e continue para o próximo passo para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um Ubuntu Linux VM, ou quiser criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

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
* *Ubuntu* é o nome de anfitrião do seu Ubuntu VM que está se juntando ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Quando estiver feito, guarde e saia do ficheiro dos *anfitriões* utilizando o comando `:wq` do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

O VM precisa de alguns pacotes adicionais para se juntar ao VM ao domínio gerido pela AD DS Azure. Para instalar e configurar estes pacotes, atualize e instale as ferramentas de união de domínio utilizando `apt-get`

Durante a instalação Kerberos, o pacote *krb5-user* solicita o nome do reino em TODAS AS MAIÚScas. Por exemplo, se o nome do seu domínio gerido pelo Azure AD DS for *aaddscontoso.com,* introduza *AADDSCONTOSO.COM* como o reino. A instalação escreve as secções `[realm]` e `[domain_realm]` no ficheiro de configuração */etc/krb5.conf.* Certifique-se de que especifica o reino de uma MAIÚSta:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Configure Protocolo de Tempo de Rede (NTP)

Para que a comunicação de domínio funcione corretamente, a data e a hora do seu Ubuntu VM devem sincronizar com o domínio gerido pelo Azure AD DS. Adicione o nome de anfitrião NTP gerido pelo seu domínio Azure DS ao ficheiro */etc/ntp.conf.*

1. Abra o ficheiro *ntp.conf* com um editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. No ficheiro *ntp.conf,* crie uma linha para adicionar o nome DNS gerido pelo domínio azure DS. No exemplo seguinte, é acrescentada uma entrada para *aaddscontoso.com.* Use o seu próprio nome DNS:

    ```console
    server aaddscontoso.com
    ```

    Quando estiver feito, guarde e saia do ficheiro *ntp.conf* utilizando o comando `:wq` do editor.

1. Para garantir que o VM é sincronizado com o domínio gerido pelo Azure AD DS, são necessários os seguintes passos:

    * Pare o servidor NTP
    * Atualizar a data e a hora do domínio gerido
    * Inicie o serviço NTP

    Executar os seguintes comandos para completar estes passos. Use o seu próprio nome DNS com o comando `ntpdate`:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Junte-se à VM ao domínio gerido

Agora que os pacotes necessários são instalados no VM e ntP está configurado, junte o VM ao domínio gerido pelo Azure AD DS.

1. Use o comando `realm discover` para descobrir o domínio gerido pela AD DS Azure. O exemplo que se segue descobre o reino *AADDSCONTOSO.COM.* Especifique o seu próprio nome de domínio gerido azure AD DS em TODAS as MAIDES:

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   Se o comando `realm discover` não encontrar o seu domínio gerido pelo Azure AD DS, reveja os seguintes passos de resolução de problemas:

    * Certifique-se de que o domínio é acessível a partir do VM. Tente `ping aaddscontoso.com` para ver se uma resposta positiva é devolvida.
    * Verifique se o VM está implantado na mesma rede virtual, ou numa rede virtual em que o domínio gerido pelo Azure AD DS está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerido pelo Azure AD DS.

1. Agora rubrica kerberos usando o comando `kinit`. Especifique um utilizador que faça parte do domínio gerido pelo Azure AD DS. Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Mais uma vez, o nome de domínio gerido pela AD DS azure deve ser introduzido em TODAS as MAIDES. No exemplo seguinte, a conta chamada `contosoadmin@aaddscontoso.com` é usada para inicializar Kerberos. Insira a sua própria conta de utilizador que faz parte do domínio gerido pelo Azure AD DS:

    ```console
    kinit contosoadmin@AADDSCONTOSO.COM
    ```

1. Por fim, junte a máquina ao domínio gerido azure AD DS utilizando o comando `realm join`. Utilize a mesma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS que especificou no comando `kinit` anterior, como `contosoadmin@AADDSCONTOSO.COM`:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Leva alguns momentos para se juntar ao VM ao domínio gerido pela AD DS Azure. A saída de exemplo que se segue mostra que o VM se juntou com sucesso ao domínio gerido pelo Azure AD DS:

```output
Successfully enrolled machine in realm
```

Se o seu VM não conseguir completar com sucesso o processo de união de domínios, certifique-se de que o grupo de segurança de rede da VM permite o tráfego de saída da Kerberos na porta 464 TCP + UDP para a subnet de rede virtual para o seu domínio gerido pelo Azure AD DS.

## <a name="update-the-sssd-configuration"></a>Atualizar a configuração SSSD

Um dos pacotes instalados num passo anterior foi para o System Security Services Daemon (SSSD). Quando um utilizador tenta iniciar sessão num VM utilizando credenciais de domínio, o SSSD transmite o pedido a um fornecedor de autenticação. Neste cenário, a SSSD utiliza o Azure AD DS para autenticar o pedido.

1. Abra o ficheiro *sssd.conf* com um editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Comente a linha para *use_fully_qualified_names* da seguinte forma:

    ```console
    # use_fully_qualified_names = True
    ```

    Quando feito, guarde e saia do ficheiro *sssd.conf* utilizando o comando `:wq` do editor.

1. Para aplicar a alteração, reinicie o serviço SSSD:

    ```console
    sudo service sssd restart
    ```

## <a name="configure-user-account-and-group-settings"></a>Configure as definições da conta de utilizador e do grupo

Com o VM a de lado junto ao domínio gerido pelo Azure AD DS e configurado para autenticação, existem algumas opções de configuração do utilizador para serem completadas. Estas alterações de configuração incluem permitir a autenticação baseada em palavras-passe e criar automaticamente diretórios domésticos no VM local quando os utilizadores de domínio iniciarem o insto.

### <a name="allow-password-authentication-for-ssh"></a>Permitir a autenticação de senha para SSH

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

1. Para aplicar as alterações e deixar os utilizadores iniciarem o contrato com uma palavra-passe, reinicie o serviço SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Configure criação automática de diretório seleções

Para permitir a criação automática do diretório doméstico quando um utilizador entrar pela primeira vez, complete os seguintes passos:

1. Abra o ficheiro */etc/pam.d/common-session* num editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Adicione a seguinte linha neste ficheiro abaixo da linha `session optional pam_sss.so`:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Quando feito, guarde e saia do ficheiro *de sessão comum* utilizando o comando `:wq` do editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda os privilégios do grupo 'Administradores da AAD DC'

Para conceder aos membros do grupo de administradores da *AAD DC privilégios* administrativos no Ubuntu VM, adicione uma entrada para os */etc/sudoers*. Uma vez adicionados, os membros do grupo de administradores da *AAD DC* podem usar o comando `sudo` no Ubuntu VM.

1. Abra o ficheiro *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada ao final do ficheiro */etc/sudoers:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, guarde e saia do editor usando o comando `Ctrl-X`.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi bem-sucedido juntou-se ao domínio gerido pelo Azure AD DS, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo a partir do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença ao domínio gerido utilizando o comando `ssh -l`, como `contosoadmin@aaddscontoso.com` e, em seguida, introduza o endereço do seu VM, como *ubuntu.aaddscontoso.com*. Se utilizar a Concha de Nuvem Azure, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido pelo Azure AD DS ou iniciar sessão com uma conta de domínio, consulte problemas de resolução de [domínios](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
