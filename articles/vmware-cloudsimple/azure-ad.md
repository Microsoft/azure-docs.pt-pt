---
title: Azure VMware Solution by CloudSimple - Use a Azure AD como fonte de identidade na Nuvem Privada
description: Descreve como adicionar Azure AD como um fornecedor de identidade na cloudSimple Private Cloud para autenticar utilizadores que acedem ao CloudSimple a partir do Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898611"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Use o Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud

Pode configurar o seu CloudSimple Private Cloud vCenter para autenticar com o Azure Ative Directory (Azure AD) para que os seus administradores VMware acedam a vCenter. Após a configuração da única fonte de identificação de sinal, o utilizador **de cloudowner** pode adicionar os utilizadores da fonte de identidade ao vCenter.  

Pode configurar o seu domínio de Diretório Ativo e controladores de domínio de qualquer das seguintes formas:

* Domínio do diretório ativo e controladores de domínio que estão a funcionar no local
* Controladores de diretório ativo e controladores de domínio em execução no Azure como máquinas virtuais na sua subscrição Azure
* Novos controladores de diretório ativo e controladores de domínio em execução na cloudSimple Private Cloud
* Serviço de Diretório Ativo Azure

Este guia explica as tarefas necessárias para configurar o Azure AD como fonte de identidade.  Para obter informações sobre a utilização de Diretório Ativo ou Diretório Ativo em funcionamento em Azure, consulte [configurar fontes de identidade vCenter para utilizar](set-vcenter-identity.md) o Ative Directory para obter instruções detalhadas na configuração da fonte de identidade.

## <a name="about-azure-ad"></a>Sobre o Azure AD

Azure AD é o multi-inquilino da Microsoft, diretório baseado na nuvem e serviço de gestão de identidade.  O Azure AD fornece um mecanismo de autenticação escalável, consistente e fiável para os utilizadores autenticarem e acederem a diferentes serviços no Azure.  Também fornece serviços LDAP seguros para quaisquer serviços de terceiros para usar a Azure AD como uma fonte de autenticação/identidade.  O Azure AD combina serviços de diretório principal, governação avançada de identidade e gestão de acesso a aplicações, que podem ser usados para dar acesso à sua Nuvem Privada para utilizadores que administram a Nuvem Privada.

Para utilizar o Azure AD como fonte de identidade com o vCenter, tem de configurar os serviços de domínio AD AD e Azure. Siga estas instruções:

1. [Como criar serviços de domínio AZure AD e Azure AD](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Como configurar uma fonte de identidade no seu VCenter Private Cloud](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Criar serviços de domínio AD AD e Azure AD

Antes de começar, terá de ter acesso à sua subscrição Azure com privilégios de Administrador Global.  Os passos seguintes dão orientações gerais. Os detalhes estão contidos na documentação do Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Se já tem Azure AD, pode saltar esta secção.

1. Configurar a Azure AD na sua subscrição, conforme descrito na  [documentação AZure AD](../active-directory/fundamentals/active-directory-whatis.md).
2. Ativar o Azure Ative Directory Premium na sua subscrição, conforme descrito no [Inscrição para O Azure Ative Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Confie um nome de domínio personalizado e verifique o nome de domínio personalizado descrito no [Adicionar um nome de domínio personalizado ao Azure Ative Directory](../active-directory/fundamentals/add-custom-domain.md).
    1. Estabeleça um registo DNS no seu registo de domínio com as informações fornecidas no Azure.
    2. Desa esta medida o nome de domínio personalizado é o domínio primário.

Pode configurar opcionalmente outras funcionalidades AD Azure.  Estes não são necessários para permitir a autenticação do vCenter com Azure AD.

### <a name="azure-ad-domain-services"></a>Serviços de domínio AZure AD

> [!NOTE]
> Este é um passo importante para permitir a Azure AD como fonte de identidade para o vCenter.  Para evitar quaisquer problemas, certifique-se de que todos os passos são executados corretamente.

1. Ativar os serviços de domínio Azure AD, conforme descrito no [Enable Azure Ative Directory, utilizando o portal Azure](../active-directory-domain-services/tutorial-create-instance.md).
2. Crie a rede que será utilizada pelos serviços de domínio Azure AD, conforme descrito no [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../active-directory-domain-services/tutorial-create-instance.md).
3. Configure o Grupo de Administrador para a gestão dos Serviços de Domínio Azure AD, conforme descrito no [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../active-directory-domain-services/tutorial-create-instance.md).
4. Atualize as definições de DNS para os seus Serviços de Domínio Azure AD, conforme descrito no [Enable Azure Ative Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md).  Se pretender ligar a AD através da Internet, confiem o registo DNS para o endereço IP público dos serviços de domínio Azure AD ao nome de domínio.
5. Ativar a sincronização de hash de palavra-passe para os utilizadores.  Este passo permite a sincronização dos hashes de palavra-passe necessários para a autenticação do NT LAN Manager (NTLM) e da autenticação kerberos para os Serviços de Domínio AD Azure. Assim que a sincronização de hash de palavras-passe estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa. Consulte [Ativar a sincronização de hash de palavra-passe para os Serviços de Domínio do Diretório Ativo Azure](../active-directory-domain-services/tutorial-create-instance.md).
    1. Se os utilizadores apenas na nuvem estiverem presentes, devem alterar a sua palavra-passe utilizando <a href="https://myapps.microsoft.com/" target="_blank">o painel de acesso AD Azure</a> para garantir que os hashes de palavra-passe são armazenados no formato exigido pela NTLM ou pela Kerberos.  Siga as instruções em [Ativar a sincronização de hash de palavra-passe para o seu domínio gerido para contas de utilizador apenas na nuvem](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).  Este passo deve ser dado para os utilizadores individuais e para qualquer novo utilizador que seja criado no seu diretório AD Azure utilizando o portal Azure ou os cmdlets Azure AD PowerShell. Os utilizadores que necessitem de acesso aos serviços de domínio Azure AD devem utilizar o <a href="https://myapps.microsoft.com/" target="_blank">painel de acesso AZure AD</a> e aceder ao seu perfil para alterar a palavra-passe.

        > [!NOTE]
        > Se a sua organização tiver contas de utilizador apenas na cloud, todos os utilizadores que precisam de utilizar o Azure Active Directory Domain Services têm de alterar as respetivas palavras-passe. Uma conta de utilizador apenas na cloud é uma conta que foi criada no diretório do Azure AD com o portal do Azure ou os cmdlets do PowerShell do Azure AD. Essas contas de utilizador não são sincronizadas a partir de um diretório no local.

    2. Se estiver a sincronizar palavras-passe a partir do seu diretório ativo, siga os passos na documentação do [Ative Directory](../active-directory-domain-services/tutorial-configure-password-hash-sync.md).

6.  Configure lDAP seguro seguro nos seus Serviços de Domínio do Diretório Ativo Azure, conforme descrito em [Configure secure LDAP (LDAPS) para um domínio gerido por Serviços de Domínio AD AD Azure](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Faça o upload de um certificado para utilização por LDAP seguro, conforme descrito no tópico Azure, [obtenha um certificado para LDAP seguro](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  A CloudSimple recomenda a utilização de um certificado assinado emitido por uma autoridade de certificados para garantir que o vCenter pode confiar no certificado.
    2. Ativar lDAP seguro como descrito [Ativar lDAP seguro (LDAPS) para um domínio gerido por Serviços de Domínio AD AZure](../active-directory-domain-services/tutorial-configure-ldaps.md).
    3. Guarde a parte pública do certificado (sem a chave privada) em .cer formato para utilização com vCenter enquanto configura a fonte de identidade.
    4. Se for necessário aceder à Internet aos serviços de domínio Azure AD, ative a opção "Permitir o acesso seguro ao LDAP através da internet".
    5. Adicione a regra de segurança de entrada para os serviços de domínio Azure AD NSG para a porta TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Crie uma fonte de identidade no seu VCenter Private Cloud

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para o seu Private Cloud vCenter.
2. Recolher os parâmetros de configuração necessários para a configuração da fonte de identidade.

    | **Opção** | **Descrição** |
    |------------|-----------------|
    | **Nome** | Nome da fonte de identidade. |
    | **Base DN para utilizadores** | Nome distinto base para utilizadores.  Para Azure AD, use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  Exemplo: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça os seus dados pessoais (IP) nem mais, por favor, |
    | **Pseudónimo de domínio** | *(opcional)* O nome NetBIOS de domínio. Adicione o nome NetBIOS do domínio Ative Directory como pseudónimo da fonte de identidade se estiver a utilizar autenticações SSPI. |
    | **Base DN para grupos** | O nome distinto da base para grupos. Para Azure AD, use: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  Exemplo: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL do servidor primário** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Utilize o formato `ldaps://hostname:port`. A porta é tipicamente 636 para ligações LDAPS. <br><br>É necessário um certificado que estabeleça confiança para o ponto final LDAPS do servidor Ative Directory quando utilizar `ldaps://` no URL LDAP primário ou secundário. |
    | **URL do servidor secundário** | Endereço de um servidor LDAP controlador de domínio secundário que é utilizado para falha. |
    | **Escolha o certificado** | Se pretender utilizar LDAPS com o seu Servidor LDAP do Diretório Ativo ou fonte de identidade do Servidor OpenLDAP, um botão de certificado Escolha aparece depois de escrever `ldaps://` na caixa de texto URL. Não é necessária uma URL secundária. |
    | **Nome de Utilizador** | ID de um utilizador no domínio que tem um mínimo de acesso apenas de leitura à Base DN para utilizadores e grupos. |
    | **Palavra-passe** | Palavra-passe do utilizador que é especificado pelo Nome de Utilizador. |

3. Inscreva-se no seu VCenter Private Cloud depois de os privilégios serem aumentados.
4. Siga as instruções no [Adicionar uma fonte de identidade no vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) utilizando os valores do passo anterior para configurar o Azure Ative Directory como fonte de identidade.
5. Adicione utilizadores/grupos de Azure AD a grupos vCenter, conforme descrito no tópico VMware [Adicionar Membros a um grupo de Sign-On único vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores *adicionados* ao grupo de Administradores serão removidos automaticamente.  Apenas as contas de serviço devem ser adicionadas ao grupo *de Administradores.*

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o modelo de permissão private Cloud](learn-private-cloud-permissions.md)
