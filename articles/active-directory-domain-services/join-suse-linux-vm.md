---
title: Junte-se a um SLE VM para Azure AD Domain Services / Microsoft Docs
description: Aprenda a configurar e junte-se a uma máquina virtual SUSE Linux Enterprise a um domínio gerido por Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619611"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Junte-se a uma máquina virtual SUSE Linux Enterprise a um domínio gerido por Azure Ative Directory Domain Services

Para permitir que os utilizadores inscrevam-se em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS). Quando se junta um VM a um domínio gerido por Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar scontabilidade e gerir servidores. Os membros do grupo do domínio gerido também são aplicados para permitir que você controle o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um VM SUSE Linux Enterprise (SLE) a um domínio gerido.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, o primeiro tutorial cria e configura um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido.

## <a name="create-and-connect-to-a-sle-linux-vm"></a>Criar e ligar a um SLE Linux VM

Se tiver um VM SLE Linux existente em Azure, ligue-o utilizando sSH, em seguida, continue para o passo seguinte para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um VM SLE Linux ou quiser criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

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
* *linux-q2gr* é o nome de anfitrião do seu SLE VM que está a juntar-se ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

Quando terminar, guarde e saia do ficheiro dos *anfitriões* utilizando `:wq` o comando do editor.

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>Junte-se a VM ao domínio gerido utilizando SSSD

Para se juntar ao domínio gerido utilizando **o SSSD** e o módulo de Gestão de *Logon* do utilizador da YaST, complete os seguintes passos:

1. Instale o módulo YaST *de Gestão de Logon do Utilizador:*

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. Abre o YaST.

1. Para utilizar com sucesso a autodiscovery DNS mais tarde, configuure os endereços IP de domínio gerido (o *servidor Ative Directory*) como o servidor de nomes do seu cliente.

    No YaST, selecione **Definições de rede > do sistema**.

1. Selecione o *separador Hostname/DNS* e, em seguida, introduza o endereço IP(es) do domínio gerido na caixa de texto *Name Server 1*. Estes endereços IP são apresentados na janela *Propriedades* do portal Azure para o seu domínio gerido, tais como *10.0.2.4* e *10.0.2.5*.

    Adicione os seus próprios endereços IP de domínio gerido e, em seguida, selecione **OK**.

1. A partir da janela principal do YaST, escolha *a*  >  *Gestão do Logon do Utilizador* dos Serviços de Rede.

    O módulo abre com uma visão geral mostrando diferentes propriedades de rede do seu computador e o método de autenticação atualmente em uso, como mostra o seguinte exemplo screenshot:

    ![Imagem de exemplo da janela de Gestão de Login do Utilizador no YaST](./media/join-suse-linux-vm/overview-window.png)

    Para começar a editar, selecione **Alterar Definições**.

Para juntar o VM ao domínio gerido, complete os seguintes passos:

1. Na caixa de diálogo, selecione **Add Domain**.

1. Especifique o *nome de domínio* correto, como *aaddscontoso.com,* e depois especifique os serviços a utilizar para os dados de identidade e autenticação. Selecione *o Microsoft Ative Directory* para ambos.

    Certifique-se de que a opção para *Ativar o domínio* está selecionada.

1. Quando estiver pronto, selecione **OK**.

1. Aceite as definições predefinidos no diálogo seguinte e, em seguida, selecione **OK**.

1. O VM instala software adicional conforme necessário e, em seguida, verifica se o domínio gerido está disponível.

    Se tudo estiver correto, o seguinte diálogo de exemplo é mostrado para indicar que o VM descobriu o domínio gerido, mas que *ainda não* está matriculado .

    ![Imagem de exemplo da janela de inscrição do Ative Directory em YaST](./media/join-suse-linux-vm/enroll-window.png)

1. No diálogo, especifique o nome de *utilizador* e *a palavra-passe* de um utilizador que faz parte do domínio gerido. Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Para se certificar de que o domínio atual está ativado para o Samba, ative *a configuração de Samba overwrite para trabalhar com este AD*.

1. Para se inscrever, selecione **OK**.

1. É mostrada uma mensagem para confirmar que está matriculado com sucesso. Para terminar, selecione **OK**.

Depois de o VM ser matriculado no domínio gerido, configura o cliente utilizando o *Logotipo do Utilizador do Domínio de Gestão,* como mostra o seguinte exemplo de imagem:

![Imagem de exemplo da janela de início de sínio do utilizador do domínio de gestão em YaST](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. Para permitir a entrada de dados utilizando dados fornecidos pelo domínio gerido, verifique a caixa para permitir a *logon do utilizador do domínio*.

1. Opcionalmente, em *Enable data source*, verifique fontes de dados adicionais conforme necessário para o seu ambiente. Estas opções incluem quais os utilizadores autorizados a usar **sudo** ou quais as unidades de rede disponíveis.

1. Para permitir que os utilizadores do domínio gerido tenham diretórios domésticos no VM, verifique a caixa para *criar diretórios domésticos*.

1. A partir da barra lateral, selecione **Opções de Serviço › Comutador de** nomes, em seguida, *Opções Estendidas*. A partir dessa janela, selecione *fallback_homedir* ou *override_homedir,* selecione **Add**.

1. Especifique um valor para a localização do diretório doméstico. Para que os diretórios domésticos sigam o formato de */home/USER_NAME,* use */home/%u*. Para obter mais informações sobre possíveis variáveis, consulte a página sssd.conf man `man 5 sssd.conf` (), secção *override_homedir*.

1. Selecione **OK**.

1. Selecione **OK** para guardar as alterações. Certifique-se de que os valores apresentados agora estão corretos. Para deixar o diálogo, **selecione Cancelar**.

1. Se pretender executar SSSD e Winbind simultaneamente (como quando se junta através de SSSD, mas depois executa um servidor de ficheiros Samba), o *método kerberos* de opção Samba deve ser definido para *segredos e keytab* em smb.conf. A opção SSSD *ad_update_samba_machine_account_password* também deve ser definida como *verdadeira* em sssd.conf. Estas opções impedem que o porta-chaves do sistema fique dessincronizado.

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Junte-se a VM ao domínio gerido usando Winbind

Para se juntar ao domínio gerido utilizando **winbind** e o módulo de *membros do Domínio* do Windows da YaST, complete os seguintes passos:

1. No YaST, selecione **Serviços de Rede > Windows Domain Membership**.

1. Introduza o domínio para se juntar ao *Grupo de Domínio ou Trabalho* no ecrã de Membro do Domínio do *Windows.* Introduza o nome de domínio gerido, como *aaddscontoso.com*.

    ![Imagem de exemplo da janela de membros do domínio do Windows em YaST](./media/join-suse-linux-vm/samba-client-window.png)

1. Para utilizar a fonte SMB para autenticação Linux, consulte a opção *de Utilização de Informações SMB para autenticação linux*.

1. Para criar automaticamente um diretório local para utilizadores de domínio gerido no VM, consulte a opção de *Criar Diretório Doméstico no Login*.

1. Verifique a opção de *Autenticação Offline* para permitir que os utilizadores do seu domínio entrem em súmido mesmo que o domínio gerido esteja temporariamente indisponível.

1. Se pretender alterar as gamas UID e GID para os utilizadores e grupos de Samba, selecione *Expert Settings*.

1. Configure a sincronização do tempo do Protocolo de Tempo de Rede (NTP) para o seu domínio gerido selecionando a *Configuração NTP*. Insira os endereços IP do domínio gerido. Estes endereços IP são apresentados na janela *Propriedades* do portal Azure para o seu domínio gerido, tais como *10.0.2.4* e *10.0.2.5*.

1. Selecione **OK** e confirme a união de domínio quando solicitado.

1. Forneça a palavra-passe para um administrador no domínio gerido e selecione **OK**.

    ![Exemplo de screenshot do pedido de diálogo de autenticação quando se junta um VM SLE ao domínio gerido](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

Depois de se ter juntado ao domínio gerido, pode iniciar sedutação a partir da sua estação de trabalho utilizando o gestor de ecrã do seu ambiente de trabalho ou da consola.

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>Junte-se ao VM para o domínio gerido usando Winbind da interface da linha de comando YaST

Para aderir ao domínio gerido utilizando **winbind** e a interface de *linha de comando YaST:*

* Junte-se ao domínio:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>Junte-se vM ao domínio gerido usando Winbind do terminal

Para aderir ao domínio gerido usando **winbind** e o *`samba net` comando*:

1. Instale o cliente kerberos e o samba-winbind:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. Editar os ficheiros de configuração:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/segurança/pam_winbind.conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Verifique se a data e hora em Azure AD e Linux estão sincronizadas. Pode fazê-lo adicionando o servidor AD Azure ao serviço NTP:
   
   1. Adicione a seguinte linha a /etc/ntp.conf:
     
      ```console
      server aaddscontoso.com
      ```

   1. Reiniciar o serviço NTP:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. Junte-se ao domínio:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Ativar o winbind como fonte de login nos Módulos de Autenticação Pluggable Linux (PAM):

   ```console
   pam-config --add --winbind
   ```

6. Permitir a criação automática de diretórios domésticos para que os utilizadores possam iniciar sessão:

   ```console
   pam-config -a --mkhomedir
   ```

7. Iniciar e ativar o serviço winbind:

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
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

1. Para aplicar as alterações e deixar que os utilizadores inscrevam-se através de uma palavra-passe, reinicie o serviço SSH:

    ```console
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda privilégios ao grupo "Administradores AAD DC"

Para conceder aos membros do grupo *de administradores da AAD DC privilégios* administrativos no SLE VM, adicione uma entrada aos */etc/sudoers*. Uma vez adicionados, os membros do grupo *de administradores AAD DC* podem utilizar o comando no `sudo` SLE VM.

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

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença ao domínio gerido utilizando o `ssh -l` comando, tal como `contosoadmin@aaddscontoso.com` e, em seguida, introduza o endereço do seu VM, como *linux-q2gr.aaddscontoso.com*. Se utilizar o Azure Cloud Shell, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. Quando tiver uma ligação com sucesso ao VM, verifique se o diretório doméstico foi inicializado corretamente:

    ```console
    pwd
    ```

    Você deve estar no *diretório /home* com o seu próprio diretório que corresponde à conta de utilizador.

3. Agora verifique se os membros do grupo estão a ser resolvidos corretamente:

    ```console
    id
    ```

    Devia ver os seus membros do grupo do domínio gerido.

4. Se se inscreveu no VM como membro do grupo de administradores da *AAD DC,* verifique se pode utilizar corretamente o `sudo` comando:

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido ou iniciar sessão com uma conta de domínio, consulte [o domínio de resolução de problemas para se juntar a problemas](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
