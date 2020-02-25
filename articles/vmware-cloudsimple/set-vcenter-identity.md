---
title: Solução Azure VMware by CloudSimple - Configurar fontes de identidade vCenter em Private Cloud
description: Descreve como configurar o seu vCenter de Cloud Privada para autenticar com Diretório Ativo para administradores vMware aceder em vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5355e43ca6ac075e76a76ceb51be135cf4b62b0a
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2020
ms.locfileid: "77564028"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Configurar fontes de identidade vCenter para usar o Diretório Ativo

## <a name="about-vmware-vcenter-identity-sources"></a>Sobre fontes de identidade VMware vCenter

VMware vCenter suporta diferentes fontes de identidade para autenticação de utilizadores que acedem ao vCenter.  O vCenter CloudSimple Private Cloud pode ser configurado para autenticar com Diretório Ativo para os seus administradores VMware acederem ao vCenter. Quando a configuração estiver concluída, o utilizador **do cloudowner** pode adicionar utilizadores da fonte de identidade ao vCenter.  

Pode configurar os seus controladores de domínio e domínio ative diretivos de qualquer uma das seguintes formas:

* Controladores de domínio e domínio de diretório ativo que executam no local
* Controladores de domínio e domínio de diretório ativo que executam o Azure como máquinas virtuais na sua subscrição Azure
* Novos controladores de domínio e domínio de diretório ativo em execução na sua Nuvem Privada
* Serviço de Diretório Ativo Azure

Este guia explica as tarefas de configurar os controladores de domínio e domínio do Diretório Ativo que executam no local ou como máquinas virtuais nas suas subscrições.  Se quiser utilizar o Azure AD como fonte de identidade, consulte o [Usa Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud](azure-ad.md) para obter instruções detalhadas na configuração da fonte de identidade.

Antes de adicionar uma fonte de [identidade,](#add-an-identity-source-on-vcenter)aumente temporariamente [os privilégios vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas as contas de serviço devem ser adicionadas ao grupo *de administradores* e as contas de serviço não devem ser utilizadas para iniciar sessão na VSphere web UI.   


## <a name="identity-source-options"></a>Opções de fonte de identidade

* [Adicione no local O Diretório Ativo como uma única fonte de identidade de sinal](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Configurar novo diretório ativo numa nuvem privada](#set-up-new-active-directory-on-a-private-cloud)
* [Configurar diretório ativo em Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Adicione o diretório ativo no local como uma fonte de identidade de sinal único

Para configurar o seu Diretório Ativo no local como uma fonte de identidade de início único, precisa de:

* [Ligação VPN do site-para-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) do seu centro de dados no local para a sua Nuvem Privada.
* NO local, o IP do servidor DNS adicionado ao VCenter e ao Platform Services Controller (PSC).

Utilize as informações na tabela seguinte ao configurar o seu domínio de Diretório Ativo.

| **Opção** | **Descrição** |
|------------|-----------------|
| **Nome** | Nome da fonte de identidade. |
| **Base DN para utilizadores** | Nome distinto da base para os utilizadores. |
| **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça os seus endereços ip. |
| **Pseudónimo de domínio** | O nome de domínio NetBIOS. Adicione o nome NetBIOS do domínio Ative Diretório como pseudónimo da fonte de identidade se estiver a utilizar autenticações SSPI. |
| **Base DN para grupos** | A base distinguia o nome dos grupos. |
| **URL do Servidor Primário** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Utilize o formato `ldap://hostname:port` ou `ldaps://hostname:port`. A porta é tipicamente 389 para ligações LDAP e 636 para ligações LDAPS. Para implementações de controladores multidomínio de Diretório Ativo, a porta é tipicamente 3268 para LDAP e 3269 para LDAPS.<br><br>É necessário um certificado que estabeleça confiança para o ponto final lDAPS do servidor Ative Directory quando utilizar `ldaps://` no URL LDAP primário ou secundário. |
| **URL do servidor secundário** | Endereço de um servidor LDAP do controlador de domínio secundário que é usado para falha. |
| **Escolha certificado** | Se pretender utilizar o LDAPS com o seu Servidor LDAP de Diretório Ativo ou a fonte de identidade do Servidor OpenLDAP, aparece um botão de certificado Choose depois de escrever `ldaps://` na caixa de texto URL. Não é necessário um URL secundário. |
| **Nome de Utilizador** | ID de um utilizador no domínio que tenha um mínimo de acesso apenas a leitura a Base DN para utilizadores e grupos. |
| **Palavra-passe** | Palavra-passe do utilizador especificado pelo Username. |

Quando tiver a informação na tabela anterior, pode adicionar o seu Diretório Ativo no local como uma fonte de identidade de um único sign-on no vCenter.

> [!TIP]
> Encontrará mais informações sobre fontes de identidade single sign-on na página de [documentação VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Configurar novo Diretório Ativo numa Nuvem Privada

Pode configurar um novo domínio de Diretório Ativo na sua Nuvem Privada e usá-lo como fonte de identidade para o Single Sign-On.  O domínio do Diretório Ativo pode fazer parte de uma floresta de Diretório Ativo existente ou pode ser configurado como uma floresta independente.

### <a name="new-active-directory-forest-and-domain"></a>Nova floresta e domínio de Diretório Ativo

Para criar uma nova floresta e domínio de Diretório Ativo, você precisa:

* Uma ou mais máquinas virtuais que executam o Microsoft Windows Server para utilizar como controladores de domínio para a nova floresta e domínio do Diretório Ativo.
* Uma ou mais máquinas virtuais que executam o serviço DNS para resolução de nomes.

Consulte [a instalação de um Novo Windows Server 2012 Ative Directory Forest](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) para obter passos detalhados.

> [!TIP]
> Para uma elevada disponibilidade de serviços, recomendamos a criação de vários controladores de domínio e servidores DNS.

Depois de configurar a floresta e domínio do Diretório Ativo, pode adicionar uma fonte de [identidade no vCenter](#add-an-identity-source-on-vcenter) para o seu novo Diretório Ativo.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Novo domínio de Diretório Ativo numa floresta de Diretório Ativo existente

Para criar um novo domínio de Diretório Ativo numa floresta de Diretório Ativo existente, precisa de:

* Ligação VPN do site ao local para a sua localização florestal de Diretório Ativo.
* DNS Server para resolver o nome da sua floresta de Diretório Ativo existente.

Consulte [a instalação de um novo domínio de diretório ativo do Windows Server 2012 para](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) obter passos detalhados.

Depois de configurar o domínio ative diretório, pode adicionar uma fonte de [identidade no vCenter](#add-an-identity-source-on-vcenter) para o seu novo Diretório Ativo.

## <a name="set-up-active-directory-on-azure"></a>Configurar diretório ativo em Azure

O Diretório Ativo em funcionamento no Azure é semelhante ao Ative Directory que funciona no local.  Para configurar o Ative Directory em funcionamento no Azure como uma fonte de identidade de entrada única no vCenter, o servidor vCenter e psc devem ter conectividade de rede para a Rede Virtual Azure onde os serviços de Diretório Ativo estão em execução.  Pode estabelecer esta conectividade utilizando a Ligação de [Rede Virtual Azure utilizando](azure-expressroute-connection.md) a ExpressRoute da rede virtual Azure, onde os Serviços de Diretório Ativo estão a funcionar para a CloudSimple Private Cloud.

Após a criação da ligação de rede, siga os passos no [Diretório Ativo Add On-Premises como uma Fonte de Identidade de Assinatura Única](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) para adicioná-lo como Fonte de Identidade.  

## <a name="add-an-identity-source-on-vcenter"></a>Adicione uma fonte de identidade no vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) na sua Nuvem Privada.

2. Inscreva-se no vCenter para a sua Nuvem Privada.

3. Selecione **Home > Administration**.

    ![Administração](media/OnPremAD01.png)

4. Selecione **um sinal único ligado > Configuração**.

    ![Sinal único](media/OnPremAD02.png)

5. Abra o separador **Fontes de Identidade** e clique **em+** para adicionar uma nova fonte de identidade.

    ![Fontes de Identidade](media/OnPremAD03.png)

6. Selecione **Ative Directory como um Servidor LDAP** e clique **em Next**.

    ![Active Directory](media/OnPremAD04.png)

7. Especifique os parâmetros de fonte de identidade para o seu ambiente e clique em **Next**.

    ![Active Directory](media/OnPremAD05.png)

8. Reveja as definições e clique em **Terminar**.
