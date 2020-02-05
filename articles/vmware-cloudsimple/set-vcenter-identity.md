---
title: Azure VMware Solutions (AVS) - Configurar fontes de identidade vCenter em AVS Private Cloud
description: Descreve como configurar o seu VCenter De Nuvem Privada AVS para autenticar com Diretório Ativo para administradores vMware aceder em vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad4a7b2bc67b7d50d9e9a5f8337a09dbe77366ea
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014220"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Configurar fontes de identidade do vCenter para usar Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Sobre as fontes de identidade do VMware vCenter

O VMware vCenter dá suporte a diferentes fontes de identidade para autenticação de usuários que acessam o vCenter. O vCenter Da Nuvem Privada AVS pode ser configurado para autenticar com Diretório Ativo para os seus administradores VMware acederem ao vCenter. Quando a instalação for concluída, o usuário **cloudowner** poderá adicionar usuários da origem de identidade ao vCenter. 

Você pode configurar seu Active Directory controladores de domínio e domínio de qualquer uma das seguintes maneiras:

* Active Directory controladores de domínio e domínio em execução no local
* Active Directory controladores de domínio e domínio em execução no Azure como máquinas virtuais em sua assinatura do Azure
* Novos controladores de domínio e domínio de diretório ativo em execução na sua Nuvem Privada AVS
* Serviço de Azure Active Directory

Este guia explica as tarefas para configurar Active Directory controladores de domínio e domínio em execução no local ou como máquinas virtuais em suas assinaturas. Se quiser utilizar o Azure AD como fonte de identidade, consulte o [Usa Azure AD como fornecedor de identidade para vCenter na AVS Private Cloud](azure-ad.md) para obter instruções detalhadas na configuração da fonte de identidade.

Antes de [Adicionar uma fonte de identidade](#add-an-identity-source-on-vcenter), [Escale temporariamente os privilégios do vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Novos usuários devem ser adicionados somente a *Cloud-Owner-Group*, *Cloud-global-cluster-admin-Group*, *Cloud-Global-Storage-admin-Group*, *Cloud-Global-Network-admin-Group* ou, *Cloud-global-VM-admin-Group*.  Os usuários adicionados ao grupo de *Administradores* serão removidos automaticamente.  Somente as contas de serviço devem ser adicionadas ao grupo de *Administradores* e as contas de serviço não devem ser usadas para entrar na interface do usuário da Web do amvSphere.   


## <a name="identity-source-options"></a>Opções de origem de identidade

* [Adicionar Active Directory local como uma fonte de identidade de logon único](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Configurar novo diretório ativo numa nuvem privada AVS](#set-up-new-active-directory-on-an-avs-private-cloud)
* [Configurar Active Directory no Azure](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Adicionar Active Directory local como uma fonte de identidade de logon único

Para configurar seu Active Directory local como uma fonte de identidade de logon único, você precisa:

* [Ligação VPN do site-para-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) desde o seu centro de dados no local até à sua Nuvem Privada AVS.
* IP do servidor DNS local adicionado ao vCenter e ao PSC (controlador de serviços de plataforma).

Use as informações na tabela a seguir ao configurar seu domínio de Active Directory.

| **Opção** | **Descrição** |
|------------|-----------------|
| **Nome** | Nome da origem da identidade. |
| **DN de base para usuários** | Nome distinto base para os usuários. |
| **Nome de domínio** | FDQN do domínio, por exemplo, example.com. Não forneça um endereço IP nesta caixa de texto. |
| **Alias de domínio** | O nome NetBIOS do domínio. Adicione o nome NetBIOS do domínio Active Directory como um alias da origem de identidade se você estiver usando autenticações SSPI. |
| **DN de base para grupos** | O nome distinto base para os grupos. |
| **URL do servidor primário** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Use o formato `ldap://hostname:port` ou `ldaps://hostname:port`. A porta geralmente é 389 para conexões LDAP e 636 para conexões LDAPs. Para Active Directory implantações de controlador de vários domínios, a porta geralmente é 3268 para LDAP e 3269 para LDAPs.<br><br>Um certificado que estabelece confiança para o ponto de extremidade LDAPs do servidor Active Directory é necessário quando você usa `ldaps://` na URL LDAP primária ou secundária. |
| **URL do servidor secundário** | Endereço de um servidor LDAP do controlador de domínio secundário que é usado para failover. |
| **Escolher certificado** | Se você quiser usar LDAPs com o servidor LDAP Active Directory ou a origem de identidade do servidor OpenLDAP, um botão escolher certificado será exibido depois que você digitar `ldaps://` na caixa de texto URL. Uma URL secundária não é necessária. |
| **Nome de Utilizador** | ID de um usuário no domínio que tem um mínimo de acesso somente leitura ao DN base para usuários e grupos. |
| **Palavra-passe** | Senha do usuário que é especificada pelo nome de usuário. |

Quando tiver as informações na tabela anterior, você poderá adicionar seu Active Directory local como uma fonte de identidade de logon único no vCenter.

> [!TIP]
> Você encontrará mais informações sobre as fontes de identidade de logon único na [página de documentação do VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-an-avs-private-cloud"></a>Configurar novo Diretório Ativo numa Nuvem Privada AVS

Pode configurar um novo domínio de Diretório Ativo na sua Nuvem Privada AVS e usá-lo como fonte de identidade para o Single Sign-On. O domínio Active Directory pode fazer parte de uma floresta Active Directory existente ou pode ser configurado como uma floresta independente.

### <a name="new-active-directory-forest-and-domain"></a>Novo Active Directory floresta e domínio

Para configurar um novo Active Directory floresta e domínio, você precisa:

* Uma ou mais máquinas virtuais que executam o Microsoft Windows Server para usar como controladores de domínio para o novo Active Directory floresta e domínio.
* Uma ou mais máquinas virtuais executando o serviço DNS para resolução de nomes.

Consulte [instalar uma nova floresta do Windows Server 2012 Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) para obter etapas detalhadas.

> [!TIP]
> Para alta disponibilidade de serviços, é recomendável configurar vários controladores de domínio e servidores DNS.

Depois de configurar o Active Directory floresta e o domínio, você pode [Adicionar uma fonte de identidade no vCenter](#add-an-identity-source-on-vcenter) para o novo Active Directory.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Novo domínio Active Directory em uma floresta Active Directory existente

Para configurar um novo domínio Active Directory em uma floresta existente do Active Directory, você precisa:

* Conexão VPN site a site para o local da floresta do Active Directory.
* Servidor DNS para resolver o nome da floresta de Active Directory existente.

Consulte [instalar um novo Windows Server 2012 Active Directory domínio filho ou de árvore](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) para obter etapas detalhadas.

Depois de configurar o domínio de Active Directory, você pode [Adicionar uma fonte de identidade no vCenter](#add-an-identity-source-on-vcenter) para o novo Active Directory.

## <a name="set-up-active-directory-on-azure"></a>Configurar Active Directory no Azure

Active Directory em execução no Azure é semelhante a Active Directory em execução no local. Para configurar Active Directory em execução no Azure como uma fonte de identidade de logon único no vCenter, o servidor vCenter e o PSC devem ter conectividade de rede com a rede virtual do Azure em que Active Directory serviços estão em execução. Pode estabelecer esta conectividade utilizando a Ligação de [Rede Virtual Azure utilizando](azure-expressroute-connection.md) a ExpressRoute da rede virtual Azure, onde os Serviços de Diretório Ativo estão a funcionar para a Cloud Privada AVS.

Depois que a conexão de rede for estabelecida, siga as etapas em [adicionar Active Directory local como uma origem de identidade de logon único](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) para adicioná-la como uma fonte de identidade. 

## <a name="add-an-identity-source-on-vcenter"></a>Adicionar uma fonte de identidade no vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) na sua Nuvem Privada AVS.

2. Inscreva-se no vCenter para a sua Nuvem Privada AVS.

3. Selecione **Home > administração**.

    ![Administração](media/OnPremAD01.png)

4. Selecione **logon único > configuração**.

    ![Início de Sessão Único](media/OnPremAD02.png)

5. Abra a guia **fontes de identidade** e clique em **+** para adicionar uma nova fonte de identidade.

    ![Fontes de identidade](media/OnPremAD03.png)

6. Selecione **Active Directory como um servidor LDAP** e clique em **Avançar**.

    ![Active Directory](media/OnPremAD04.png)

7. Especifique os parâmetros de origem de identidade para seu ambiente e clique em **Avançar**.

    ![Active Directory](media/OnPremAD05.png)

8. Examine as configurações e clique em **concluir**.
