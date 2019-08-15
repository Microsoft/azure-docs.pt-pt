---
title: Visão geral da autenticação de Azure Active Directory sobre o SMB para arquivos do Azure – armazenamento do Azure
description: Os arquivos do Azure oferecem suporte à autenticação baseada em identidade sobre SMB (protocolo de mensagens do servidor) por meio dos serviços de domínio do Azure Active Directory (AD do Azure). Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: addb97d71da1ceac8717ae77204c87a8197dac86
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935176"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Visão geral do suporte à autenticação do Azure AD DS (serviço de domínio Azure Active Directory) de arquivos do Azure para acesso SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como habilitar a autenticação de AD DS do Azure para arquivos do Azure, consulte [habilitar a autenticação do serviço de domínio Azure Active Directory sobre SMB para arquivos do Azure](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Glossário 
É útil entender alguns termos-chave relacionados à autenticação do serviço de domínio do Azure AD sobre SMB para arquivos do Azure:

-   **Azure Active Directory (Azure AD)**  
    O Azure Active Directory (AD do Azure) é o serviço de gerenciamento de identidade e diretório multilocatário baseado em nuvem da Microsoft. O Azure AD combina os principais serviços de diretório, o gerenciamento de acesso a aplicativos e a proteção de identidade em uma única solução. Para obter mais informações, consulte [o que é Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    O Azure AD Domain Services fornece serviços de domínio gerenciado, como ingresso no domínio, diretivas de grupo, LDAP e autenticação Kerberos/NTLM. Esses serviços são totalmente compatíveis com o Windows Server Active Directory. Para obter mais informações, consulte [serviços de domínio do Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **RBAC (controle de acesso baseado em função) do Azure**  
    O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Usando o RBAC, você pode gerenciar o acesso aos recursos concedendo aos usuários o menor número de permissões necessárias para executar seus trabalhos. Para obter mais informações sobre o RBAC, consulte [o que é o RBAC (controle de acesso baseado em função) no Azure?](../../role-based-access-control/overview.md)

-   **Autenticação Kerberos**

    O Kerberos é um protocolo de autenticação usado para verificar a identidade de um usuário ou host. Para obter mais informações sobre o Kerberos, consulte [visão geral da autenticação Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo SMB (Server Message Block)**  
    O SMB é um protocolo de compartilhamento de arquivos de rede padrão do setor. O SMB também é conhecido como sistema de arquivos da Internet comum ou CIFS. Para obter mais informações sobre o SMB, consulte [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Vantagens da autenticação do serviço de domínio do Azure AD
A autenticação do serviço de domínio do Azure AD para arquivos do Azure oferece vários benefícios em relação ao uso da autenticação de chave compartilhada:

-   **Estenda a experiência tradicional de acesso ao compartilhamento de arquivos baseado em identidade para a nuvem com o Azure AD e o serviço de domínio do Azure AD**  
    Se você planeja "aumentar e deslocar" seu aplicativo para a nuvem, substituindo os servidores de arquivos tradicionais pelos arquivos do Azure, convém que seu aplicativo seja autenticado com as credenciais do Azure AD para acessar os dados do arquivo. Os arquivos do Azure dão suporte ao uso de credenciais do Azure AD para acessar os arquivos do Azure via SMB do Azure AD DS VMs do Windows ingressadas no domínio. Você também pode optar por sincronizar todos os seus objetos de Active Directory locais com o Azure AD para preservar nomes de domínio, senhas e outras atribuições de grupo.

-   **Impor o controle de acesso granular nos compartilhamentos de arquivos do Azure**  
    Você pode conceder permissões a uma identidade específica no nível de compartilhamento, diretório ou arquivo. Por exemplo, suponha que você tenha várias equipes usando um único compartilhamento de arquivos do Azure para colaboração do projeto. Você pode conceder a todas as equipes acesso a diretórios não confidenciais, limitando o acesso a diretórios que contêm dados financeiros confidenciais somente à sua equipe de finanças. 

-   **Fazer backup de ACLs junto com seus dados**  
    Você pode usar os arquivos do Azure para fazer backup de seus compartilhamentos de arquivos locais existentes. Os arquivos do Azure preservam suas ACLs junto com seus dados quando você faz backup de um compartilhamento de arquivos para arquivos do Azure por SMB.

## <a name="how-it-works"></a>Como funciona
Os arquivos do Azure usam Azure AD Domain Services para dar suporte à autenticação Kerberos com as credenciais do Azure AD de VMs ingressadas no domínio. Antes de usar o Azure AD com os arquivos do Azure, primeiro você deve habilitar Azure AD Domain Services e ingressar no domínio por meio das VMs das quais você planeja acessar os dados do arquivo. Sua VM ingressada no domínio deve residir na mesma rede virtual (VNET) que Azure AD Domain Services. 

Quando uma identidade associada a um aplicativo em execução em uma VM tenta acessar dados em arquivos do Azure, a solicitação é enviada para Azure AD Domain Services para autenticar a identidade. Se a autenticação for bem-sucedida, Azure AD Domain Services retornará um token Kerberos. O aplicativo envia uma solicitação que inclui o token Kerberos, e os arquivos do Azure usam esse token para autorizar a solicitação. O arquivos do Azure recebe apenas o token e não mantém as credenciais do Azure AD.

![Captura de tela mostrando o diagrama da autenticação do Azure AD sobre SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Habilitar a autenticação do serviço de domínio do Azure AD para acesso SMB
Você pode habilitar a autenticação do serviço de domínio do Azure AD para arquivos do Azure em suas contas de armazenamento novas e existentes criadas após 24 de setembro de 2018. 

Antes de habilitar esse recurso, verifique se Azure AD Domain Services foi implantado para o locatário primário do Azure AD ao qual sua conta de armazenamento está associada. Se ainda não tiver configurado Azure AD Domain Services, siga as diretrizes passo a passo fornecidas em [habilitar Azure Active Directory Domain Services usando o portal do Azure](../../active-directory-domain-services/create-instance.md).

Azure AD Domain Services implantação geralmente leva de 10 a 15 minutos. Depois que Azure AD Domain Services tiver sido implantado, você poderá habilitar a autenticação do Azure AD sobre o SMB para arquivos do Azure. Para obter mais informações, consulte [habilitar a autenticação do serviço de domínio Azure Active Directory sobre SMB para arquivos do Azure](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Configurar permissões de nível de compartilhamento para arquivos do Azure
Depois que a autenticação do serviço de domínio do Azure AD tiver sido habilitada, você poderá configurar funções RBAC personalizadas para identidades do Azure AD e atribuir direitos de acesso a qualquer compartilhamento de arquivo na conta de armazenamento.

Quando um aplicativo em execução em uma VM ingressada no domínio tenta montar um compartilhamento de arquivos do Azure ou acessar um diretório ou arquivo, as credenciais do Azure AD do aplicativo são verificadas para garantir as permissões de nível de compartilhamento adequadas e as permissões NTFS. Para obter informações sobre como configurar permissões de nível de compartilhamento, consulte [habilitar a autenticação do serviço de domínio Azure Active Directory sobre SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Configurar permissões no nível do diretório ou do arquivo para arquivos do Azure 
O arquivos do Azure impõe permissões de arquivo NTFS padrão no nível de diretório e arquivo, incluindo no diretório raiz. A configuração de permissões no nível do diretório ou do arquivo tem suporte apenas via SMB. Monte o compartilhamento de arquivos de destino de sua VM e configure permissões usando o explorador de arquivos do Windows, o Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou o comando [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Usar a chave da conta de armazenamento para permissões de superusuário 
Um usuário que possua a chave da conta de armazenamento pode acessar os arquivos do Azure com permissões de superusuário. As permissões de superusuário ultrapassam todas as restrições de controle de acesso configuradas no nível de compartilhamento com o RBAC e impostas pelo Azure AD. As permissões de superusuário são necessárias para montar um compartilhamento de arquivos do Azure. 

> [!IMPORTANT]
> Como parte das práticas recomendadas de segurança, evite compartilhar suas chaves de conta de armazenamento e aproveite as permissões do Azure AD sempre que possível.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Preservar ACLs de diretório e arquivo para importação de dados para compartilhamentos de arquivos do Azure
Os arquivos do Azure agora dão suporte à preservação de ACLs de diretório ou arquivo quando você copia dados para compartilhamentos de arquivos do Azure. Você pode copiar as ACLs em um diretório ou arquivo para os arquivos do Azure. Por exemplo, você pode usar o [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com `/copy:s` sinalizador para copiar dados e ACLs para um compartilhamento de arquivos do Azure. A preservação da ACL está ativada por padrão e você não precisa habilitar explicitamente o recurso de autenticação do serviço de domínio do Azure AD em sua conta de armazenamento. 

## <a name="pricing"></a>Preços
Não há nenhum encargo de serviço adicional para habilitar a autenticação do Azure AD sobre SMB em sua conta de armazenamento. Para obter mais informações sobre preços, consulte [preços de arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/) e Azure AD Domain Services páginas de [preços](https://azure.microsoft.com/pricing/details/active-directory-ds/) .

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os arquivos do Azure e a autenticação do Azure AD sobre o SMB, consulte estes recursos:

- [Introdução aos arquivos do Azure](storage-files-introduction.md)
- [Habilitar a autenticação de Azure Active Directory sobre o SMB para arquivos do Azure](storage-files-active-directory-enable.md)
- [FAQ](storage-files-faq.md)
