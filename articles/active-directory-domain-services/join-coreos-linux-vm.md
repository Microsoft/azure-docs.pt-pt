---
title: Junte-se a um CoreOS VM para serviços de domínio azure AD [ Microsoft Docs
description: Aprenda a configurar e junte uma máquina virtual CoreOS a um domínio gerido pelo Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: b97b542d11e405bab00519c68d2365dada6b6c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78298875"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Junte-se a uma máquina virtual CoreOS para um domínio gerido pelo Azure AD Domain Services

Para permitir que os utilizadores inscrevam em máquinas virtuais (VMs) em Azure utilizando um único conjunto de credenciais, pode juntar VMs a um domínio gerido pelo Azure Ative Directory Domain Services (AD DS). Quando se junta a um VM a um domínio gerido pelo Azure AD DS, as contas de utilizador e credenciais do domínio podem ser usadas para iniciar sessão e gerir servidores. As adesões do grupo do domínio gerido pelo Azure AD DS também são aplicadas para permitir controlar o acesso a ficheiros ou serviços no VM.

Este artigo mostra-lhe como se juntar a um CoreOS VM a um domínio gerido pelo Azure AD DS.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, necessita dos seguintes recursos e privilégios:

* Uma subscrição ativa do Azure.
    * Se não tiver uma assinatura Azure, [crie uma conta.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Um inquilino azure Ative Directory associado à sua subscrição, sincronizado com um diretório no local ou com um diretório apenas na nuvem.
    * Se necessário, crie um inquilino do [Azure Ative Directory][create-azure-ad-tenant] ou [associe uma assinatura Azure à sua conta.][associate-azure-ad-tenant]
* Um Azure Ative Directory Domain Services gerido domínio habilitado e configurado no seu inquilino Azure AD.
    * Se necessário, o primeiro tutorial cria e configura uma instância de Serviços de [Domínio de Diretório Ativo Azure.][create-azure-ad-ds-instance]
* Uma conta de utilizador que faz parte do domínio gerido pelo Azure AD DS.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Criar e ligar a um CoreOS Linux VM

Se tiver um VM CoreOS Linux existente em Azure, ligue-o utilizando SSH e continue para o próximo passo para [começar a configurar o VM](#configure-the-hosts-file).

Se precisar de criar um VM CoreOS Linux, ou pretender criar um VM de teste para utilização com este artigo, pode utilizar um dos seguintes métodos:

* [Portal Azure](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
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
* *coreos* é o nome de anfitrião do seu CoreOS VM que está a juntar ao domínio gerido.

Atualize estes nomes com os seus próprios valores:

```console
127.0.0.1 coreos coreos.aaddscontoso.com
```

Quando estiver feito, guarde e `:wq` saia do ficheiro dos *anfitriões* utilizando o comando do editor.

## <a name="configure-the-sssd-service"></a>Configure o serviço SSSD

Atualize a configuração */etc/sssd/sssd.conf* SSSD.

```console
sudo vi /etc/sssd/sssd.conf
```

Especifique o seu próprio nome de domínio gerido pelo Azure AD DS para os seguintes parâmetros:

* *domínios* em TODAS AS MAIÚStes
* *[domínio/AADDS]* onde a AADDS está em TODAS AS MAIÚStes
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* em TODAS AS MAIÚSTES

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDSCONTOSO.COM

[domain/AADDSCONTOSO.COM]
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

Com o ficheiro de configuração SSSD atualizado, junte-se agora à máquina virtual ao domínio gerido.

1. Em primeiro `adcli info` lugar, utilize o comando para verificar se pode ver informações sobre o domínio gerido pelo Azure AD DS. O exemplo seguinte obtém informações para o *domínio AADDSCONTOSO.COM*. Especifique o seu próprio nome de domínio gerido azure AD DS em TODAS as MAIDES:

    ```console
    sudo adcli info AADDSCONTOSO.COM
    ```

   Se `adcli info` o comando não encontrar o seu domínio gerido pelo Azure AD DS, reveja os seguintes passos de resolução de problemas:

    * Certifique-se de que o domínio é acessível a partir do VM. Tente `ping aaddscontoso.com` ver se uma resposta positiva é devolvida.
    * Verifique se o VM está implantado na mesma rede virtual, ou numa rede virtual em que o domínio gerido pelo Azure AD DS está disponível.
    * Confirme que as definições do servidor DNS para a rede virtual foram atualizadas para apontar para os controladores de domínio do domínio gerido pelo Azure AD DS.

1. Agora junte-se ao VM ao domínio gerido pela `adcli join` AD DS azure usando o comando. Especifique um utilizador que faça parte do domínio gerido pelo Azure AD DS. Se necessário, [adicione uma conta de utilizador a um grupo em Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Mais uma vez, o nome de domínio gerido pela AD DS azure deve ser introduzido em TODAS as MAIDES. No exemplo seguinte, a `contosoadmin@aaddscontoso.com` conta nomeada é usada para inicializar Kerberos. Insira a sua própria conta de utilizador que faz parte do domínio gerido pelo Azure AD DS.

    ```console
    sudo adcli join -D AADDSCONTOSO.COM -U contosoadmin@AADDSCONTOSO.COM -K /etc/krb5.keytab -H coreos.aaddscontoso.com -N coreos
    ```

    O `adcli join` comando não devolve nenhuma informação quando o VM se juntou com sucesso ao domínio gerido pela AD DS azure.

1. Para aplicar a configuração de união de domínio, inicie o serviço SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inscreva-se no VM usando uma conta de domínio

Para verificar se o VM foi bem-sucedido juntou-se ao domínio gerido pelo Azure AD DS, inicie uma nova ligação SSH utilizando uma conta de utilizador de domínio. Confirme que foi criado um diretório doméstico e que a adesão ao grupo a partir do domínio é aplicada.

1. Crie uma nova ligação SSH a partir da sua consola. Utilize uma conta de domínio que pertença `ssh -l` ao domínio `contosoadmin@aaddscontoso.com` gerido utilizando o comando, como e, em seguida, introduza o endereço do seu VM, como *coreos.aaddscontoso.com*. Se utilizar a Concha de Nuvem Azure, utilize o endereço IP público do VM em vez do nome DNS interno.

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com coreos.aaddscontoso.com
    ```

1. Verifique agora se os membros do grupo estão a ser resolvidos corretamente:

    ```console
    id
    ```

    Deve ver os seus membros do grupo do domínio gerido pelo Azure AD DS.

## <a name="next-steps"></a>Passos seguintes

Se tiver problemas em ligar o VM ao domínio gerido pelo Azure AD DS ou iniciar sessão com uma conta de domínio, consulte problemas de resolução de [domínios](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
