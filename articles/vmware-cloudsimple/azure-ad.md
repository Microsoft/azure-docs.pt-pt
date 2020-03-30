---
title: Solução Azure VMware by CloudSimple - Use Azure AD como fonte de identidade na Nuvem Privada
description: Descreve como adicionar AD Azure como fornecedor de identidade na sua CloudSimple Private Cloud para autenticar utilizadores que acedam à CloudSimple a partir do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 674ca8bea110d60557d1e50e7b68c9c3f7a92bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77564589"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Use o Azure AD como fornecedor de identidade para vCenter na CloudSimple Private Cloud

Pode configurar o seu vCenter Cloud Private Cloud Cloud para autenticar com o Azure Ative Directory (Azure AD) para que os seus administradores vMware acedam ao vCenter. Após a configuração da única fonte de identidade de início de sessão, o utilizador do **cloudowner** pode adicionar utilizadores da fonte de identidade ao vCenter.  

Pode configurar os seus controladores de domínio e domínio ative diretivos de qualquer uma das seguintes formas:

* Controladores de domínio e domínio de diretório ativo que executam no local
* Controladores de domínio e domínio de diretório ativo que executam o Azure como máquinas virtuais na sua subscrição Azure
* Novos controladores de domínio e domínio de diretório ativo que executam na sua CloudSimple Private Cloud
* Serviço de Diretório Ativo Azure

Este guia explica as tarefas necessárias para a criação de AD Azure como fonte de identidade.  Para obter informações sobre a utilização do Diretório Ativo ou Diretório Ativo no Local em Funcionamento em Azure, consulte a Configuração de fontes de [identidade vCenter para utilizar](set-vcenter-identity.md) o Ative Directory para obter instruções detalhadas na configuração da fonte de identidade.

## <a name="about-azure-ad"></a>Sobre o Azure AD

A Azure AD é o serviço de gestão de vários inquilinos da Microsoft, baseado na nuvem e gestão de identidade.  A Azure AD fornece um mecanismo de autenticação escalável, consistente e fiável para os utilizadores autenticarem e acederem a diferentes serviços no Azure.  Também fornece serviços LDAP seguros para quaisquer serviços de terceiros para usar a Azure AD como fonte de autenticação/identidade.  A Azure AD combina serviços de diretório sinuosos, governação avançada de identidade e gestão de acesso a aplicações, que podem ser usados para dar acesso à sua Cloud Privada para utilizadores que administram a Cloud Privada.

Para utilizar o Azure AD como fonte de identidade com vCenter, tem de configurar os serviços de domínio Azure AD e Azure AD. Siga estas instruções:

1. [Como criar serviços de domínio Azure AD e Azure AD](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Como configurar uma fonte de identidade no seu vCenter Private Cloud](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Criar serviços de domínio Azure AD e Azure AD

Antes de começar, precisará de acesso à sua subscrição Azure com privilégios de Administrador Global.  Os seguintes passos dão orientações gerais. Os detalhes estão contidos na documentação do Azure.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Se já tem AD Azure, pode saltar esta secção.

1. Configurar o Azure AD na sua subscrição, conforme descrito na [documentação da AD Azure.](../active-directory/fundamentals/get-started-azure-ad.md)
2. Ative Azure Ative Directory Premium na sua subscrição conforme descrito em [Signup para Azure Ative Directory Premium](../active-directory/fundamentals/active-directory-get-started-premium.md).
3. Configurar um nome de domínio personalizado e verificar o nome de domínio personalizado, tal como descrito em Adicionar um nome de [domínio personalizado ao Diretório Ativo azure](../active-directory/fundamentals/add-custom-domain.md).
    1. Instale um registo DNS no seu registo de domínio com as informações fornecidas no Azure.
    2. Desloque o nome de domínio personalizado como o domínio primário.

Pode configurar opcionalmente outras funcionalidades da AD Azure.  Estes não são necessários para permitir a autenticação vCenter com AD Azure.

### <a name="azure-ad-domain-services"></a>Serviços de domínio Azure AD

> [!NOTE]
> Este é um passo importante para permitir o Azure AD como fonte de identidade para vCenter.  Para evitar quaisquer problemas, certifique-se de que todos os passos são executados corretamente.

1. Ative serviços de domínio AD Azure conforme descrito nos serviços de [domínio Ative Diretório Enable Azure utilizando o portal Azure](../active-directory-domain-services/active-directory-ds-getting-started.md).
2. Criar a rede que será utilizada pelos serviços de domínio Da Azure AD, conforme descrito no [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../active-directory-domain-services/active-directory-ds-getting-started-network.md).
3. Configure Administrator Group para gerir os Serviços de Domínio Azure AD conforme descrito no [Enable Azure Ative Directory Domain Services utilizando o portal Azure](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md).
4. Atualize as definições de DNS para os seus Serviços de Domínio AD Azure, conforme descrito nos Serviços de Domínio ativo enable Azure Ative [Directory](../active-directory-domain-services/active-directory-ds-getting-started-dns.md).  Se quiser ligar-se à AD através da Internet, instale o registo DNS para o endereço IP público dos serviços de domínio Azure AD com o nome de domínio.
5. Ativar a sincronização de hash de palavra-passe para os utilizadores.  Este passo permite a sincronização de hashes de senha necessárias para nt LAN Manager (NTLM) e a autenticação Kerberos para os Serviços de Domínio Azure AD. Assim que a sincronização de hash de palavras-passe estiver configurada, os utilizadores podem iniciar sessão no domínio gerido com as credenciais da empresa. Ver Ativar a sincronização de hash de [palavra-passe para os Serviços de Domínio de Diretório Ativo Azure](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md).
    1. Se os utilizadores apenas na nuvem estiverem presentes, devem alterar a sua palavra-passe utilizando o painel de <a href="http://myapps.microsoft.com/" target="_blank">acesso Azure AD</a> para garantir que as hashes de senha são armazenadas no formato exigido pela NTLM ou pela Kerberos.  Siga as instruções em Ativação de sincronização de [hash no domínio gerido para contas de utilizador apenas](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)na nuvem .  Este passo deve ser feito para utilizadores individuais e qualquer novo utilizador que seja criado no seu diretório Azure AD utilizando o portal Azure ou os cmdlets Azure AD PowerShell. Os utilizadores que necessitem de acesso aos serviços de domínio Azure AD devem utilizar o painel de <a href="http://myapps.microsoft.com/" target="_blank">acesso Azure AD</a> e aceder ao seu perfil para alterar a palavra-passe.

        > [!NOTE]
        > Se a sua organização tiver contas de utilizador apenas na cloud, todos os utilizadores que precisam de utilizar o Azure Active Directory Domain Services têm de alterar as respetivas palavras-passe. Uma conta de utilizador apenas na cloud é uma conta que foi criada no diretório do Azure AD com o portal do Azure ou os cmdlets do PowerShell do Azure AD. Essas contas de utilizador não são sincronizadas a partir de um diretório no local.

    2. Se estiver a sincronizar palavras-passe do seu diretório Ativo no local, siga os passos na documentação do [Diretório Ativo](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md).

6.  Configure o LDAP seguro nos seus Serviços de Domínio de Diretório Ativo Azure, conforme descrito no [Configure secure LDAP (LDAPS) para um domínio gerido pelos Serviços de Domínio Azure AD](../active-directory-domain-services/tutorial-configure-ldaps.md).
    1. Faça upload de um certificado para utilização por LDAP seguro, conforme descrito no tópico [Azure, obtenha um certificado para LDAP seguro](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap).  A CloudSimple recomenda a utilização de um certificado assinado emitido por uma autoridade de certificados para garantir que o vCenter pode confiar no certificado.
    2. Ativar o LDAP seguro como descrito [Enable Secure LDAP (LDAPS) para um domínio gerido](../active-directory-domain-services/tutorial-configure-ldaps.md)pelos Serviços de Domínio Azure AD .
    3. Guarde a parte pública do certificado (sem a chave privada) em formato .cer para utilização com vCenter enquanto configura a fonte de identidade.
    4. Se for necessário o acesso à Internet aos serviços de domínio Azure AD, ative a opção "Permitir um acesso seguro ao LDAP através da Internet".
    5. Adicione a regra de segurança de entrada para os serviços de domínio Azure AD NSG para a porta TCP 636.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Instale uma fonte de identidade no seu vCenter de Nuvem Privada

1. [Aumente os privilégios](escalate-private-cloud-privileges.md) para o seu vCenter Private Cloud.
2. Recolher os parâmetros de configuração necessários para a configuração da fonte de identidade.

    | **Opção** | **Descrição** |
    |------------|-----------------|
    | **Nome** | Nome da fonte de identidade. |
    | **Base DN para utilizadores** | Nome distinto da base para os utilizadores.  Para a AD Azure, utilize: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Exemplo: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`.|
    | **Nome de domínio** | FQDN do domínio, por exemplo, example.com. Não forneça os seus endereços ip. |
    | **Pseudónimo de domínio** | *(opcional)* O nome de domínio NetBIOS. Adicione o nome NetBIOS do domínio Ative Diretório como pseudónimo da fonte de identidade se estiver a utilizar autenticações SSPI. |
    | **Base DN para grupos** | A base distinguia o nome dos grupos. Para a AD Azure, utilize: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` Exemplo:`OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **URL do Servidor Primário** | Servidor LDAP do controlador de domínio primário para o domínio.<br><br>Utilize o `ldaps://hostname:port`formato . A porta é tipicamente 636 para ligações LDAPS. <br><br>É necessário um certificado que estabeleça confiança para o ponto final lDAPS `ldaps://` do servidor Ative Directory quando utilizar o URL LDAP primário ou secundário. |
    | **URL do servidor secundário** | Endereço de um servidor LDAP do controlador de domínio secundário que é usado para falha. |
    | **Escolha certificado** | Se pretender utilizar o LDAPS com o seu Servidor LDAP de Diretório Ativo ou a `ldaps://` fonte de identidade do Servidor OpenLDAP, aparece um botão de certificado Choose depois de digitar a caixa de texto URL. Não é necessário um URL secundário. |
    | **Nome de utilizador** | ID de um utilizador no domínio que tenha um mínimo de acesso apenas a leitura a Base DN para utilizadores e grupos. |
    | **Palavra-passe** | Palavra-passe do utilizador especificado pelo Username. |

3. Inscreva-se no seu vCenter Private Cloud depois de os privilégios serem aumentados.
4. Siga as instruções em Adicionar uma fonte de [identidade no vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) utilizando os valores do passo anterior para configurar o Azure Ative Directory como fonte de identidade.
5. Adicione utilizadores/grupos de Azure AD a grupos vCenter conforme descrito no tópico VMware Adicione Membros a um grupo de [sign-on único vCenter](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Os novos utilizadores devem ser adicionados apenas ao *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group,* *Cloud-Global-Network-Admin-Group* ou, *Cloud-Global-VM-Admin-Group*.  Os utilizadores adicionados ao grupo *Administradores* serão removidos automaticamente.  Apenas as contas de serviço devem ser adicionadas ao grupo *administradores.*

## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o modelo de permissão private Cloud](learn-private-cloud-permissions.md)
