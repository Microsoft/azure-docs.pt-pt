---
title: Junte-se a um Ubuntu VM para Azure AD Domain Services / Microsoft Docs
description: Aprenda a configurar e junte-se a uma máquina virtual Ubuntu Linux a um domínio gerido por Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: 8b6d022b9a1f3be70f69943a53754c9dd909ca99
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619492"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Junte-se a uma máquina virtual Ubuntu Linux a um domínio gerido por Azure Ative Directory Domain Services

Para permitir que os utilizadores inscrevam-se em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS). Quando se junta um VM a um domínio gerido por Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar scontabilidade e gerir servidores. Os membros do grupo do domínio gerido também são aplicados para permitir que você controle o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um Ubuntu Linux VM a um domínio gerido.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, o primeiro tutorial cria e configura um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido.

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Criar e ligar a um Ubuntu Linux VM

Se tiver um Ubuntu Linux VM existente em Azure, ligue-o utilizando SSH, em seguida, continue para o passo seguinte para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um Ubuntu Linux VM, ou quiser criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

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
* *Ubuntu* é o nome de anfitrião do seu VM Ubuntu que está a juntar-se ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

Quando terminar, guarde e saia do ficheiro dos *anfitriões* utilizando `:wq` o comando do editor.

## <a name="install-required-packages"></a>Instalar pacotes necessários

O VM precisa de alguns pacotes adicionais para juntar o VM ao domínio gerido. Para instalar e configurar estes pacotes, atualizar e instalar as ferramentas de união de domínios utilizando `apt-get`

Durante a instalação kerberos, o pacote *de utilizador krb5* solicita o nome do reino em TODOS OS MAI. Por exemplo, se o nome do seu domínio gerido for *aaddscontoso.com*, insira *AADDSCONTOSO.COM* como o reino. A instalação escreve as `[realm]` secções e `[domain_realm]` secções em */etc/krb5.conf* ficheiro de configuração. Certifique-se de que especifica o reino de um MAI:

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>Configure o protocolo de tempo de rede (NTP)

Para que a comunicação de domínio funcione corretamente, a data e a hora do seu VM Ubuntu devem sincronizar-se com o domínio gerido. Adicione o nome de anfitrião NTP do seu domínio gerido ao ficheiro */etc/ntp.conf.*

1. Abra o ficheiro *ntp.conf* com um editor:

    ```console
    sudo vi /etc/ntp.conf
    ```

1. No ficheiro *ntp.conf,* crie uma linha para adicionar o nome DNS do seu domínio gerido. No exemplo seguinte, é adicionada uma entrada para *aaddscontoso.com.* Use o seu próprio nome DNS:

    ```console
    server aaddscontoso.com
    ```

    Quando terminar, guarde e saia do ficheiro *ntp.conf* utilizando o `:wq` comando do editor.

1. Para garantir que o VM é sincronizado com o domínio gerido, são necessários os seguintes passos:

    * Pare o servidor NTP
    * Atualizar a data e a hora do domínio gerido
    * Inicie o serviço NTP

    Executar os seguintes comandos para completar estes passos. Use o seu próprio nome DNS com o `ntpdate` comando:

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>Junte-se ao VM para o domínio gerido

Agora que os pacotes necessários são instalados no VM e NTP está configurado, junte-se ao VM ao domínio gerido.

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
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. Por fim, junte o VM ao domínio gerido utilizando o `realm join` comando. Utilize a mesma conta de utilizador que é uma parte do domínio gerido que especificou no `kinit` comando anterior, `contosoadmin@AADDSCONTOSO.COM` como:

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

Leva alguns momentos para juntar o VM ao domínio gerido. A saída de exemplo a seguir mostra que o VM se juntou com sucesso ao domínio gerido:

```output
Successfully enrolled machine in realm
```

Se o seu VM não conseguir completar com sucesso o processo de ligação ao domínio, certifique-se de que o grupo de segurança de rede da VM permite o tráfego kerberos de saída na porta TCP + UDP 464 para a sub-rede de rede virtual para o seu domínio gerido.

Se recebeu o erro Falha não especificada do *GSS.  O código menor pode fornecer mais informações (Servidor não encontrado na base de dados de Kerberos)*, abrir o ficheiro */etc/krb5.conf* e adicionar o seguinte código na `[libdefaults]` secção e tentar novamente:

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>Atualizar a configuração SSSD

Um dos pacotes instalados num passo anterior foi para os Serviços de Segurança do Sistema Daemon (SSSD). Quando um utilizador tenta iniciar súm numa VM utilizando credenciais de domínio, a SSSD transmite o pedido a um fornecedor de autenticação. Neste cenário, a SSSD utiliza a Azure AD DS para autenticar o pedido.

1. Abra o ficheiro *sssd.conf* com um editor:

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. Comente a linha para *use_fully_qualified_names* da seguinte forma:

    ```console
    # use_fully_qualified_names = True
    ```

    Quando terminar, guarde e saia do ficheiro *sssd.conf* utilizando o `:wq` comando do editor.

1. Para aplicar a alteração, reinicie o serviço SSSD:

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>Configurar a conta de utilizador e as definições de grupo

Com o VM ligado ao domínio gerido e configurado para autenticação, existem algumas opções de configuração do utilizador para completar. Estas alterações de configuração incluem permitir a autenticação baseada em palavras-passe e criar automaticamente diretórios domésticos no VM local quando os utilizadores de domínio iniciarem o primeiro sºC.

### <a name="allow-password-authentication-for-ssh"></a>Permitir a autenticação de palavra-passe para SSH

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

1. Para aplicar as alterações e deixar que os utilizadores inscrevam-se através de uma palavra-passe, reinicie o serviço SSH:

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>Configurar a criação automática de diretórios domésticos

Para permitir a criação automática do diretório doméstico quando um utilizador iniciar a sua primeira s intervenção, complete os seguintes passos:

1. Abra o ficheiro */etc/pam.d/common session* num editor:

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. Adicione a seguinte linha neste ficheiro abaixo da `session optional pam_sss.so` linha:

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    Quando terminar, guarde e saia do ficheiro *de sessão comum* utilizando `:wq` o comando do editor.

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda privilégios ao grupo "Administradores AAD DC"

Para conceder aos membros do grupo *de administradores da AAD DC privilégios* administrativos no Ubuntu VM, adicione uma entrada aos */etc/sudoers*. Uma vez adicionados, os membros do grupo *de administradores AAD DC* podem usar o comando no `sudo` Ubuntu VM.

1. Abra o ficheiro *sudoers* para edição:

    ```console
    sudo visudo
    ```

1. Adicione a seguinte entrada no ficheiro */etc/sudoers:*

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    Quando terminar, guarde e saia do editor usando o `Ctrl-X` comando.

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi ligado com sucesso ao domínio gerido, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença ao domínio gerido utilizando o `ssh -l` comando, tal como `contosoadmin@aaddscontoso.com` e, em seguida, introduza o endereço do seu VM, como *ubuntu.aaddscontoso.com*. Se utilizar o Azure Cloud Shell, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
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
    sudo apt-get update
    ```

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido ou iniciar sessão com uma conta de domínio, consulte [o domínio de resolução de problemas para se juntar a problemas](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
