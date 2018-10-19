---
title: Descrição geral da autenticação do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização) - armazenamento do Azure
description: Ficheiros do Azure suportam autenticação baseada em identidades, através de SMB (Server Message Block) (pré-visualização) através dos serviços de domínio do Azure Active Directory (Azure AD). Máquinas de virtuais de Windows (VMs) do seu associados a um domínio, em seguida, pode aceder a partilhas de ficheiros do Azure com credenciais do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/19/2018
ms.author: tamram
ms.openlocfilehash: 1962a3237fb54409d17fefa314605bafa91c3e9c
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49427642"
---
# <a name="overview-of-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Descrição geral da autenticação do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como ativar a autenticação do Azure AD através de SMB para ficheiros do Azure, veja [autenticação de ativar o Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossário 
É útil compreender alguns termos-chave relacionadas à autenticação do Azure AD através de SMB, para ficheiros do Azure:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) é multi-inquilino com base na cloud diretório e identidade do serviço de gestão da Microsoft. O Azure AD combina serviços de diretório centrais, gestão de acesso de aplicações e proteção de identidade numa única solução. Para obter mais informações, consulte [o que é o Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Serviços de domínio do Azure AD**  
    O Azure AD Domain Services fornece serviços de domínio geridos, tais como associação a um domínio, as políticas de grupo, LDAP e Kerberos/NTLM authentication. Esses serviços são totalmente compatíveis com o Windows Server Active Directory. Para obter mais informações, consulte [dos serviços de domínio do Azure Active Directory (AD)](../../active-directory-domain-services/active-directory-ds-overview.md).

-   **Controlo de acesso (RBAC) baseado em funções do Azure**  
    O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode gerir o acesso aos recursos ao conceder as permissões de menor necessárias para desempenhar as suas funções de utilizadores. Para obter mais informações sobre RBAC, veja [o que é o controlo de acesso baseado em funções (RBAC) no Azure?](../../role-based-access-control/overview.md)

-   **Autenticação Kerberos**

    Kerberos é um protocolo de autenticação que serve para verificar a identidade de um utilizador ou o anfitrião. Para obter mais informações sobre Kerberos, consulte [descrição geral da autenticação do Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo do Server Message Block (SMB)**  
    SMB é um protocolo de partilha de ficheiros de rede de norma da indústria. SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre SMB, consulte [protocolo SMB da Microsoft e a descrição geral do protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-authentication"></a>Vantagens da autenticação do Azure AD
O Azure AD através de SMB para ficheiros do Azure oferece vários benefícios com o uso de autenticação de chave partilhada:

-   **Expandir a experiência de acesso de partilha de ficheiros tradicional com base na identidade para a cloud com o Azure AD**  
    Se planeja "migração lift- and -shift" servidores com de ficheiros do seu aplicativo para a cloud, substituindo o tradicional ficheiros do Azure, em seguida, pode desejar a sua aplicação para autenticar com o Azure AD para aceder a dados de ficheiro. O Azure suporta ficheiros a utilizar credenciais do Azure AD associado a um domínio VMS através de SMB para acessar compartilhamentos de arquivos, diretórios ou ficheiros. Também pode optar por sincronizar todos os seus objetos do Active Directory no local para o Azure AD para preservar os nomes de utilizador, palavras-passe e outras atribuições de grupo.

-   **Impor o controlo de acesso granular em partilhas de ficheiros do Azure**  
    Com a autenticação do Azure AD através de SMB, pode conceder permissões para uma identidade específica no nível de arquivo, diretório ou partilha. Por exemplo, suponha que tenha várias equipes de utilizar uma partilha de ficheiros do Azure única para colaboração em projetos. Pode conceder acesso de equipes todos os diretórios não sensíveis, ao limitar o acesso a diretórios que contêm dados financeiros confidenciais para a sua equipa de finanças apenas. 

-   **Fazer backup das ACLs, juntamente com os seus dados**  
    Pode utilizar ficheiros do Azure para criar cópias de segurança suas partilhas de ficheiros no local existente. Os ficheiros do Azure preserva suas ACLs, juntamente com os seus dados quando efetuar cópias de segurança um ficheiro de partilha de ficheiros do Azure através de SMB.

## <a name="how-it-works"></a>Como funciona
Os ficheiros do Azure utiliza o Azure AD Domain Services para suportar a autenticação Kerberos com credenciais do Azure AD associado a um domínio VMS. Antes de poder utilizar o Azure AD com ficheiros do Azure, tem de ativar os serviços de domínio do Azure AD e associar o domínio das VMs a partir do qual pretende aceder a dados de ficheiros. A VM associados a um domínio têm de residir na mesma rede virtual (VNET), como serviços de domínio do Azure AD. 

Quando uma identidade associada uma aplicação em execução numa VM tenta aceder a dados nos ficheiros do Azure, a solicitação é enviada aos serviços de domínio do Azure AD para autenticar a identidade. Se a autenticação for bem-sucedida, o Azure AD Domain Services devolve um token de Kerberos. A aplicação envia um pedido que inclui o token de Kerberos e ficheiros do Azure utiliza esse token para autorizar a solicitação. Os ficheiros do Azure recebe apenas o token e não persiste as credenciais do Azure AD.

![Captura de ecrã que mostra diagrama de autenticação do Azure AD através de SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-authentication-over-smb"></a>Ativar a autenticação do Azure AD por SMB
Pode ativar a autenticação do Azure AD através de SMB para ficheiros do Azure nas suas contas de armazenamento de novas e existentes criadas após 24 de Setembro de 2018. 

Antes de ativar a autenticação do Azure AD através de SMB, certifique-se de que os serviços de domínio do Azure AD foi implementado para as primárias inquilino do Azure AD com o qual a sua conta de armazenamento está associada. Se ainda não tiver configurado o Azure AD Domain Services, siga as orientações passo a passo fornecidas [ativar o Azure Active Directory Domain Services no portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

Implementação de serviços de domínio do AD do Azure geralmente leva de 10 a 15 minutos. Depois de ter sido implementado o Azure AD Domain Services, pode ativar a autenticação do Azure AD através de SMB para ficheiros do Azure. Para obter mais informações, consulte [autenticação de ativar o Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões de nível da partilha de ficheiros do Azure
Assim que tiver sido ativada a autenticação do Azure AD, pode configurar funções RBAC personalizadas para identidades do Azure AD e atribuir direitos de acesso a quaisquer partilhas de ficheiros na conta de armazenamento.

Quando uma aplicação em execução numa VM associados a um domínio tenta montar uma partilha de ficheiros do Azure ou aceder a um ficheiro ou diretório, credenciais do Azure AD da aplicação são verificadas para garantir que as permissões adequadas de nível de compartilhamento e permissões de NTFS. Para obter informações sobre como configurar permissões de nível de partilha, consulte [autenticação de ativar o Azure Active Directory através de SMB (pré-visualização)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurar permissões de nível de diretório ou arquivo para ficheiros do Azure 
Os ficheiros do Azure impõe permissões de ficheiros NTFS padrão ao nível do diretório e arquivo, incluindo no diretório raiz. Configuração de permissões de nível de diretório ou arquivo é suportada apenas através de SMB. Montar a partilha de ficheiros de destino da VM e configurar permissões com o Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) comando. 

> [!NOTE]
> Configurando permissões de NTFS através do Explorador de ficheiros do Windows não é suportada na pré-visualização.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Utilize a chave de conta de armazenamento para permissões de Superutilizador 
Um utilizador possessing a chave de conta de armazenamento pode aceder a ficheiros do Azure com permissões de Superutilizador. Permissões de Superutilizador superar as todas as restrições de controlo de acesso configurada a nível de partilha com o RBAC e impostas pelo Azure AD. Permissões de Superutilizador são necessárias para montar uma partilha de ficheiros do Azure. 

> [!IMPORTANT]
> Como parte de melhores práticas para segurança, evite partilhar as chaves de conta de armazenamento e tirar partido das permissões do Azure AD, sempre que possível.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Preservar as ACLs de diretório e arquivo para importação de dados para partilhas de ficheiros do Azure
Autenticação do Azure AD através de SMB suporta preservando diretório ou ACLs de ficheiros, quando copiar dados em partilhas de ficheiros do Azure. Na versão de pré-visualização, pode copiar as ACLs num arquivo ou diretório de ficheiros do Azure. Por exemplo, pode usar [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com o sinalizador `/copy:s` para copiar dados e as ACLs para uma partilha de ficheiros do Azure.

## <a name="pricing"></a>Preços
Não existe nenhum custo adicional de serviço para ativar a autenticação do Azure AD através de SMB na sua conta de armazenamento. Para obter mais informações sobre os preços, consulte [preços de ficheiros do Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [preços dos serviços de domínio do Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/) páginas.

## <a name="next-steps"></a>Próximos Passos
Para obter mais informações sobre a autenticação de ficheiros do Azure e o Azure AD através de SMB, consulte estes recursos:

- [Introdução aos ficheiros do Azure](storage-files-introduction.md)
- [Ativar a autenticação do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](storage-files-active-directory-enable.md)
- [FAQ](storage-files-faq.md)