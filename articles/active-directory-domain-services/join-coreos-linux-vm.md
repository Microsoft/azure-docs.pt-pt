---
title: Junte-se a um CoreOS VM para Azure AD Domain Services | Microsoft Docs
description: Aprenda a configurar e junte uma máquina virtual CoreOS a um domínio gerido por Azure AD Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.openlocfilehash: 1e98f32bd6fe7d5373d5ab6621ffdce5e79abc08
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619594"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Junte uma máquina virtual CoreOS a um domínio gerido por Serviços de Domínio do Diretório Ativo Azure

Para permitir que os utilizadores inscrevam-se em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido por Azure Ative Directory Domain Services (Azure AD DS). Quando se junta um VM a um domínio gerido por Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar scontabilidade e gerir servidores. Os membros do grupo do domínio gerido também são aplicados para permitir que você controle o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um CoreOS VM a um domínio gerido.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, precisa dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma subscrição do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um inquilino do Azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou um diretório apenas na nuvem.
    * Se necessário, [crie um inquilino do Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um domínio de domínio do Azure Ative Directory Services gerido ativo e configurado no seu inquilino AZure AD.
    * Se necessário, o primeiro tutorial cria e configura um domínio gerido pelos [Serviços de Domínio do Diretório Ativo Azure][create-azure-ad-ds-instance].
* Uma conta de utilizador que faz parte do domínio gerido.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Criar e ligar a um CoreOS Linux VM

Se tiver um CoreOS Linux VM existente em Azure, ligue-o utilizando sSH e continue para o passo seguinte para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um CoreOS Linux VM, ou quiser criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

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
* *coreos* é o nome de anfitrião do seu CoreOS VM que está a juntar-se ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Quando terminar, guarde e saia do ficheiro dos *anfitriões* utilizando `:wq` o comando do editor.

## <a name="configure-the-sssd-service"></a>Configurar o serviço SSSD

Atualize a configuração */etc/sssd/sssd.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Especifique o seu próprio nome de domínio gerido para os seguintes parâmetros:

* *domínios* em TODOS OS CASOS SUPERIORES
* *[domínio/AADDSCONTOSO]* onde a AADDSCONTOSO está em TODOS OS CASOS SUPERIORES
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* em todos os casos superiores

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aaddscontoso.com
ldap_search_base = dc=aaddscontoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aaddscontoso.com
krb5_realm = AADDSCONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Junte-se ao VM ao domínio gerido

Com o ficheiro de configuração SSSD atualizado, junte-se agora à máquina virtual para o domínio gerido.

1. Primeiro, utilize o `adcli info` comando para verificar se consegue ver informações sobre o domínio gerido. O exemplo seguinte obtém informações para o *domínio AADDSCONTOSO.COM*. Especifique o seu próprio nome de domínio gerido em TODOS OS MAI:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Se o `adcli info` comando não conseguir encontrar o seu domínio gerido, reveja as seguintes etapas de resolução de problemas:

    * Certifique-se de que o domínio está acessível a partir do VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é devolvida.
    * Verifique se o VM é implantado na mesma, ou numa rede virtual esprevada em que o domínio gerido está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para indicar os controladores de domínio do domínio gerido.

1. Agora junte-se ao VM para o domínio gerido usando o `adcli join` comando. Especifique um utilizador que faz parte do domínio gerido. Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Mais uma vez, o nome de domínio gerido deve ser introduzido em TODOS OS MAI. No exemplo seguinte, a conta nomeada `contosoadmin@aaddscontoso.com` é usada para inicializar Kerberos. Insira a sua própria conta de utilizador que faz parte do domínio gerido.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    O `adcli join` comando não devolve nenhuma informação quando o VM se juntou com sucesso ao domínio gerido.

1. Para aplicar a configuração de união de domínios, inicie o serviço SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi ligado com sucesso ao domínio gerido, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença ao domínio gerido utilizando o `ssh -l` comando, tal como `contosoadmin@aaddscontoso.com` e, em seguida, introduza o endereço do seu VM, como *coreos.aaddscontoso.com*. Se utilizar o Azure Cloud Shell, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Agora verifique se os membros do grupo estão a ser resolvidos corretamente:

    ```console
    id
    ```

    Devia ver os seus membros do grupo do domínio gerido.

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido ou iniciar sessão com uma conta de domínio, consulte [o domínio de resolução de problemas para se juntar a problemas](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
