---
title: Solução Azure VMware by CloudSimple - Configurar fontes de identidade vCenter em Private Cloud
description: Descreve como configurar o seu VCenter Private Cloud para autenticar com Diretório Ativo para administradores VMware acederem ao vCenter
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f6f3b10219775adb02d47a91da2573ea99f30ac0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88212250"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Criar fontes de identidade vCenter para utilizar o Ative Directory

## <a name="about-vmware-vcenter-identity-sources"></a>Sobre as fontes de identidade vMware vCenter

O VMware vCenter suporta diferentes fontes de identidade para a autenticação dos utilizadores que acedem ao vCenter.  O seu CloudSimple Private Cloud vCenter pode ser configurado para autenticar com o Ative Directory para os seus administradores VMware acederem ao vCenter. Quando a configuração estiver concluída, o utilizador **de cloudowner** pode adicionar os utilizadores da fonte de identidade ao vCenter.  

Pode configurar o seu domínio de Diretório Ativo e controladores de domínio de qualquer das seguintes formas:

* Domínio do diretório ativo e controladores de domínio que estão a funcionar no local
* Controladores de diretório ativo e controladores de domínio em execução no Azure como máquinas virtuais na sua subscrição Azure
* Novos controladores de diretório ativo e controladores de domínio em execução na sua Nuvem Privada
* Serviço de Diretório Ativo Azure

Este guia explica as tarefas de configuração do domínio do Ative Directory e dos controladores de domínio que executam no local ou como máquinas virtuais nas suas subscrições.  Se quiser utilizar o Azure AD como fonte de identidade, consulte o [Use Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud](azure-ad.md) para obter instruções detalhadas na configuração da fonte de identidade.

Antes [de adicionar uma fonte de identidade,](#add-an-identity-source-on-vcenter)aumente temporariamente os seus [privilégios vCenter](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores *adicionados* ao grupo de Administradores serão removidos automaticamente.  Apenas as contas de serviço devem ser *adicionadas* ao grupo de administradores e as contas de serviço não devem ser usadas para iniciar súm na uI web vSphere.   


## <a name="identity-source-options"></a>Opções de origem de identidade

* [Adicione no local Ative Directory como uma única fonte de identidade sign-on](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Configurar novo diretório ativo em uma nuvem privada](#set-up-new-active-directory-on-a-private-cloud)
* [Configurar diretório ativo em Azure](#set-up-active-directory-on-azure)

> [!IMPORTANT]
> **O Diretório Ativo (Autenticação Integrada do Windows) não é suportado.** Apenas o Diretório Ativo sobre a opção LDAP é suportado como uma fonte de identidade.

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Adicionar diretório ativo nas instalações como uma única fonte de identidade Sign-On

Para configurar o seu Ative Directory como fonte de identidade single Sign-On, precisa:

* [Ligação VPN site-to-site](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) do seu centro de dados no local para a sua Nuvem Privada.
* Ip do servidor DNS nas instalações adicionado ao vCenter e ao Controlador de Serviços de Plataforma (PSC).

Utilize as informações na tabela seguinte ao configurar o seu domínio Ative Directory.

| **Opção** | **Descrição** |
|------------|-----------------|
| **Nome** | Nome da fonte de identidade. |
| **Base DN para utilizadores** | Nome distinto base para utilizadores. |
| **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça os seus dados pessoais (IP) nem mais, por favor, nem mais, por favor, nem mais, por favor, nem mais, por favor, |
| **Pseudónimo de domínio** | O nome NetBIOS de domínio. Adicione o nome NetBIOS do domínio Ative Directory como pseudónimo da fonte de identidade se estiver a utilizar autenticações SSPI. |
| **Base DN para grupos** | O nome distinto da base para grupos. |
| **URL do servidor primário** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Utilize o formato  `ldap://hostname:port`   ou  `ldaps://hostname:port` . A porta é tipicamente 389 para ligações LDAP e 636 para ligações LDAPS. Para as implementações do controlador multi-domínio do Ative Directory, a porta é tipicamente 3268 para LDAP e 3269 para LDAPS.<br><br>É necessário um certificado que estabeleça confiança para o ponto final LDAPS do servidor Ative Directory quando utilizar  `ldaps://`   no URL LDAP primário ou secundário. |
| **URL do servidor secundário** | Endereço de um servidor LDAP controlador de domínio secundário que é utilizado para falha. |
| **Escolha o certificado** | Se pretender utilizar LDAPS com o seu Servidor LDAP do Diretório Ativo ou fonte de identidade do Servidor OpenLDAP, um botão de certificado Escolha aparece depois de escrever  `ldaps://`   na caixa de texto URL. Não é necessária uma URL secundária. |
| **Nome de Utilizador** | ID de um utilizador no domínio que tem um mínimo de acesso apenas de leitura à Base DN para utilizadores e grupos. |
| **Palavra-passe** | Palavra-passe do utilizador que é especificado pelo Nome de Utilizador. |

Quando tiver as informações na tabela anterior, pode adicionar o seu Diretório Ativo no local como uma única fonte de identidade Sign-On no vCenter.

> [!TIP]
> Você encontrará mais informações sobre fontes de identidade single Sign-On na página de [documentação VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html).

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Configurar novo Diretório Ativo numa Nuvem Privada

Pode configurar um novo domínio ative directory na sua Nuvem Privada e usá-lo como fonte de identidade para O Único S-Dia.  O domínio ative directory pode fazer parte de uma floresta de Diretório Ativo existente ou pode ser criado como uma floresta independente.

### <a name="new-active-directory-forest-and-domain"></a>Nova floresta e domínio do Diretório Ativo

Para configurar uma nova floresta e domínio do Ative Directory, você precisa:

* Uma ou mais máquinas virtuais que executam o Microsoft Windows Server para usar como controladores de domínio para a nova floresta e domínio do Ative Directory.
* Uma ou mais máquinas virtuais que executam o serviço DNS para resolução de nomes.

Consulte [a Instalação de um Novo Windows Server 2012 Ative Directory Forest](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) para obter etapas detalhadas.

> [!TIP]
> Para uma elevada disponibilidade de serviços, recomendamos a criação de vários controladores de domínio e servidores DNS.

Depois de configurar a floresta e domínio do Ative Directory, pode [adicionar uma fonte de identidade no vCenter](#add-an-identity-source-on-vcenter) para o seu novo Diretório Ativo.

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Novo domínio do Diretório Ativo numa floresta de Diretório Ativo existente

Para configurar um novo domínio ative directory numa floresta de Diretório Ativo existente, você precisa:

* Ligação VPN site-to-site à sua localização florestal do Diretório Ativo.
* DNS Server para resolver o nome da sua floresta ative de diretório existente.

Consulte [a Instalação de um novo windows Server 2012 Ative Directory child ou domínio de árvores](/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) para obter etapas detalhadas.

Depois de configurar o domínio Ative Directory, pode [adicionar uma fonte de identidade no vCenter](#add-an-identity-source-on-vcenter) para o seu novo Ative Directory.

## <a name="set-up-active-directory-on-azure"></a>Configurar o Ative Directory em Azure

O Ative Directory que funciona no Azure é semelhante ao Ative Directory que funciona no local.  Para configurar o Ative Directory em execução no Azure como uma única fonte de identidade Sign-On no vCenter, o servidor vCenter e o PSC devem ter conectividade de rede com a Rede Virtual Azure onde os serviços de Diretório Ativo estão a funcionar.  Pode estabelecer esta conectividade utilizando [a Ligação de Rede Virtual Azure utilizando o ExpressRoute](azure-expressroute-connection.md) a partir da rede virtual Azure, onde os Serviços de Diretório Ativo estão a correr para cloudSimple Private Cloud.

Após a ligação da rede ser estabelecida, siga os passos no [Add On-Premises Ative Directory como uma Única Fonte de Identidade Sign-On](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) para adicioná-la como Fonte de Identidade.  

## <a name="add-an-identity-source-on-vcenter"></a>Adicione uma fonte de identidade no vCenter

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) na sua Nuvem Privada.

2. Inscreva-se no vCenter para a sua Nuvem Privada.

3. Selecione **Administração de > Domiciliários**.

    ![Administração](media/OnPremAD01.png)

4. Selecione **único sinal na configuração >**.

    ![Início de Sessão Único](media/OnPremAD02.png)

5. Abra o separador **Fontes de Identidade** e clique **+** para adicionar uma nova fonte de identidade.

    ![Fontes de identidade](media/OnPremAD03.png)

6. Selecione **Ative Directory como um Servidor LDAP** e clique em **Seguinte**.

    ![Active Directory](media/OnPremAD04.png)

7. Especifique os parâmetros de origem de identidade para o seu ambiente e clique em **Seguinte**.

    ![Active Directory](media/OnPremAD05.png)

8. Reveja as definições e clique **em Terminar**.
