---
title: Visão geral - Autorização de identidade do Azure Files
description: O Azure Files suporta a autenticação baseada na identidade sobre o SMB (Server Message Block) através dos Serviços de Domínio do Diretório Ativo (AD DS) e do Ative Directory. As suas máquinas virtuais (VMs) unidas a domínios podem então aceder a partilhas de ficheiros Azure utilizando credenciais AZure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519797"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Visão geral das opções de autenticação baseadas na identidade do Azure Files para acesso AMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como permitir a autenticação dos Serviços de Domínio do Diretório Ativo para ações de ficheiros Azure, consulte [Ativar no local a autenticação dos Serviços de Domínio do Diretório Ativo sobre as ações de ficheiros Azure](storage-files-identity-auth-active-directory-enable.md).

Para saber como ativar a autenticação Azure AD DS para ações de ficheiros Azure, consulte [a autenticação do Enable Azure Ative Directory Domain Services nos Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossário 
É útil compreender alguns termos-chave relativos à autenticação do Serviço de Domínio Azure AD sobre a SMB para ações de ficheiros Azure:

-   **Autenticação kerberos**

    O Kerberos é um protocolo de autenticação utilizado para confirmar a identidade de um utilizador ou o anfitrião. Para obter mais informações sobre Kerberos, consulte [a Visão Geral de Autenticação de Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protocolo do Bloco de Mensagens do Servidor (SMB)**

    SMB é um protocolo de partilha de ficheiros padrão da indústria. SMB também é conhecido como Common Internet File System ou CIFS. Para obter mais informações sobre o SMB, consulte [o Protocolo microsoft SMB e a visão geral do protocolo CIFS](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Ative Directory (Azure AD) é o serviço de gestão de identidade e diretório multi-inquilino da Microsoft. A Azure AD combina serviços de diretório principal, gestão de acesso a aplicações e proteção de identidade numa única solução. As máquinas virtuais (VMs) azure AD-joined Windows não conseguem aceder a partilhas de ficheiros Azure com as suas credenciais AZure AD. Para mais informações, consulte [o que é o Diretório Ativo Azure?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Serviços de Domínio do Diretório Ativo Azure (Azure AD DS)**

    A Azure AD DS fornece serviços de domínio geridos, tais como a junção de domínio, políticas de grupo, LDAP e a autenticação Kerberos/NTLM. Estes serviços são totalmente compatíveis com os Serviços de Domínio do Diretório Ativo. Para mais informações, consulte [os Serviços de Domínio do Diretório Ativo Azure](../../active-directory-domain-services/overview.md).

- **Serviços de Domínio de Diretório Ativo no local (DS AD)**

    No local, a integração dos Serviços de Domínio do Diretório Ativo (DS AD) com a Azure Files fornece os métodos para armazenar dados de diretórios, disponibilizando-os aos utilizadores e administradores da rede. A segurança é integrada com DS AD através da autenticação de logon e controlo de acesso a objetos no diretório. Com uma única rede, os administradores podem gerir dados de diretório e organização em toda a sua rede, e os utilizadores de rede autorizados podem aceder a recursos em qualquer parte da rede. A DS AD é comumente adotada por empresas em ambientes no local e as credenciais de DS AD são usadas como identidade para controlo de acesso. Para obter mais informações, consulte a [visão geral dos serviços de domínio do diretório ativo.](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)

-   **Controlo de acesso baseado em funções do Azure (RBAC do Azure)**

    O controlo de acesso baseado em funções Azure (Azure RBAC) permite uma gestão de acesso de grãos finos para o Azure. Utilizando o Azure RBAC, pode gerir o acesso aos recursos, concedendo aos utilizadores o menor número de permissões necessárias para executar os seus trabalhos. Para obter mais informações sobre o Azure RBAC, veja [o que é o controlo de acesso baseado em funções do Azure (Azure RBAC)?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>Casos de utilização comuns

A autenticação e suporte baseados na identidade para ACLs do Windows em Ficheiros Azure é melhor alavancado para os seguintes casos de utilização:

### <a name="replace-on-premises-file-servers"></a>Substitua servidores de ficheiros no local

Depreciar e substituir servidores de ficheiros dispersos no local é um problema comum que todas as empresas encontram na sua jornada de modernização de TI. A ações de ficheiros Azure com autenticação AD DS no local é o melhor ajuste aqui, quando você pode migrar os dados para Azure Files. Uma migração completa permitir-lhe-á tirar partido dos elevados benefícios de disponibilidade e escalabilidade, minimizando também as alterações do lado do cliente. Proporciona uma experiência de migração perfeita aos utilizadores finais, para que possam continuar a aceder aos seus dados com as mesmas credenciais usando as máquinas de adesão de domínio existentes.

### <a name="lift-and-shift-applications-to-azure"></a>Pedidos de elevação e mudança para Azure

Quando levantar e deslocar as aplicações para a nuvem, pretende manter o mesmo modelo de autenticação para os seus dados. À medida que alargamos a experiência de controlo de acesso baseada na identidade às partilhas de ficheiros Azure, elimina a necessidade de alterar a sua aplicação para métodos modernos de auth e acelerar a adoção em nuvem. As ações de ficheiros Azure oferecem a opção de integração com a Azure AD DS ou no local AD DS para autenticação. Se o seu plano for 100% nativo em nuvem e minimizar os esforços de gestão de infraestruturas em nuvem, a Azure AD DS seria um melhor ajuste como um serviço de domínio totalmente gerido. Se necessitar de compatibilidade total com as capacidades de DS AD, talvez deva considerar alargar o seu ambiente AD DS para cloud através de controladores de domínio auto-hospedados em VMs. De qualquer forma, fornecemos a flexibilidade para escolher os serviços de domínio que se adequam às necessidades do seu negócio.

### <a name="backup-and-disaster-recovery-dr"></a>Backup e recuperação de desastres (DR)

Se mantiver o armazenamento de ficheiros primário no local, as ações de ficheiros Azure podem servir como um armazenamento ideal para backup ou DR, para melhorar a continuidade do negócio. Pode utilizar as ações de ficheiros Azure para fazer o armazenamento dos seus dados a partir dos servidores de ficheiros existentes, preservando ao mesmo tempo os DACLs do Windows. Para cenários DR, pode configurar uma opção de autenticação para suportar a aplicação adequada do controlo de acessos em failover.

## <a name="supported-scenarios"></a>Cenários suportados

A tabela seguinte resume os cenários de autenticação de ficheiros Azure suportados para Azure AD DS e AD DS no local. Recomendamos a seleção do serviço de domínio que adotou para o ambiente do seu cliente para integração com ficheiros Azure. Se tiver DS AD já configurado no local ou em Azure onde os seus dispositivos são domínio ligados ao seu AD, deve optar por alavancar a AD DS para autenticação de partilhas de ficheiros Azure. Da mesma forma, se já adotou Azure AD DS, deve usá-lo para autenticar ações de ficheiros Azure.


|Autenticação Azure AD DS  | Autenticação AD DS no local  |
|---------|---------|
|As máquinas windows azure AD DS podem aceder a partilhas de ficheiros Azure com credenciais AD AD sobre SMB.     |No local, as máquinas do Windows aderidas a AD DS ou Azure AD DS podem aceder a partilhas de ficheiros Azure com credenciais de Ative Directory no local que são sincronizadas com Azure AD sobre SMB. O seu cliente deve ter uma linha de visão para o seu DS AD.        |

### <a name="restrictions"></a>Restrições

- A azure AD DS e no local a autenticação AD DS não suportam a autenticação contra contas de computador. Em vez disso, pode considerar a utilização de uma conta de início de síl em quando.
- Nem a autenticação AZURE AD DS nem no local a autenticação AD DS é suportada contra dispositivos aderidos a AD Azure ou dispositivos registados em Ad AD.
- As ações de ficheiros Azure apenas suportam a autenticação baseada na identidade contra um dos seguintes serviços de domínio, quer a [Azure Ative Directory Domain Services (Azure AD DS)](#azure-ad-ds) quer [nos locais Ative Directory Domain Services (AD DS)](#ad-ds).
- Nenhum dos métodos de autenticação baseados na identidade é suportado com o Sistema de Ficheiros de Rede (NFS), que está em pré-visualização.

## <a name="advantages-of-identity-based-authentication"></a>Vantagens da autenticação baseada na identidade
A autenticação baseada em identidade para ficheiros Azure oferece vários benefícios ao utilizar a autenticação da Chave Partilhada:

-   **Alargar a experiência tradicional de acesso à partilha de ficheiros baseada em identidade à nuvem com AD DS e Azure AD DS**  
    Se planeia levantar e transferir a sua aplicação para a nuvem, substituindo os servidores de ficheiros tradicionais por partilhas de ficheiros Azure, então poderá querer que a sua aplicação autenha com credenciais AD DS ou AZure AD para aceder aos dados dos ficheiros. A Azure Files suporta a utilização de credenciais AD DS ou Azure AD DS para aceder a ações de ficheiros Azure sobre SMB a partir de VMs de AD DS ou Azure AD DS.

-   **Impor controlo de acesso granular em ações de ficheiros Azure**  
    Pode conceder permissões a uma identidade específica ao nível da partilha, diretório ou ficheiro. Por exemplo, suponha que tem várias equipas usando uma única partilha de ficheiros Azure para colaboração de projetos. Pode conceder a todas as equipas acesso a diretórios não sensíveis, limitando o acesso a diretórios que contenham dados financeiros sensíveis apenas à sua equipa de Finanças. 

-   **Back up Windows ACLs (também conhecidos como NTFS) juntamente com os seus dados**  
    Pode utilizar ações de ficheiros Azure para fazer o back up das suas ações de ficheiros existentes no local. O Azure Files preserva os seus ACLs juntamente com os seus dados quando faz uma reserva de uma partilha de ficheiros para ações de ficheiros Azure sobre AMB.

## <a name="how-it-works"></a>Como funciona

A ações de ficheiros Azure aproveita o protocolo Kerberos para autenticação com AD DS ou Azure AD DS. Quando uma identidade associada a um utilizador ou aplicação em execução num cliente tenta aceder a dados em ações de ficheiros Azure, o pedido é enviado para o serviço de domínio, quer AD DS quer Azure AD DS, para autenticar a identidade. Se a autenticação for bem sucedida, devolve um token Kerberos. O cliente envia um pedido que inclui o token Kerberos e as ações de ficheiros Azure usam esse símbolo para autorizar o pedido. As ações de ficheiros Azure só recebem o token Kerberos, não credenciais de acesso.

Antes de poder ativar a autenticação baseada na identidade nas ações de ficheiros Azure, tem primeiro de configurar o seu ambiente de domínio.

### <a name="ad-ds"></a>AD DS

Para a autenticação AD DS no local, deve configurar os controladores de domínio AD e o domínio junta-se às suas máquinas ou VMs. Pode hospedar os seus controladores de domínio em VMs Azure ou no local. De qualquer forma, os seus clientes unidos devem ter linha de visão para o serviço de domínio, por isso devem estar dentro da rede corporativa ou da rede virtual (VNET) do seu serviço de domínio.

O diagrama seguinte retrata a autenticação AD DS no local para ações de ficheiros Azure sobre a SMB. O DS AD on-prem deve ser sincronizado com a AD AZure utilizando a sincronização Azure AD Connect. Apenas os utilizadores híbridos que existam tanto no local AD DS como Azure AD podem ser autenticados e autorizados para acesso a partilha de ficheiros Azure. Isto porque a permissão do nível de ação é configurada contra a identidade representada no Azure AD onde a permissão de nível de diretório/arquivo é aplicada com a da DS AD. Certifique-se de que configura corretamente as permissões contra o mesmo utilizador híbrido.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagrama que retrata a autenticação AD DS no local para ações de ficheiros Azure sobre SMB.":::

### <a name="azure-ad-ds"></a>Azure AD DS

Para a autenticação Azure AD DS, deve permitir que os Serviços de Domínio AD do Azure e o domínio se juntem aos VMs que planeia aceder aos dados dos ficheiros a partir do. O seu VM de domínio deve residir na mesma rede virtual (VNET) que o seu AZure AD DS. 

O diagrama seguinte representa o fluxo de trabalho para a autenticação Azure AD DS para ações de ficheiros Azure sobre SMB. Segue um padrão semelhante à autenticação em DS AD pré-pré-m para ações de ficheiros Azure. Há duas grandes diferenças:

- Primeiro, não precisa de criar a identidade em Azure AD DS para representar a conta de armazenamento. Isto é realizado pelo processo de habilitação em segundo plano.

- Em segundo lugar, todos os utilizadores que existam em Azure AD podem ser autenticados e autorizados. O utilizador pode ser apenas em nuvem ou híbrido. A sincronização de Azure AD a Azure AD DS é gerida pela plataforma sem necessitar de qualquer configuração do utilizador. No entanto, o cliente deve ser domínio associado à Azure AD DS, não pode ser a Azure AD aderido ou registado. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Diagrama":::

### <a name="enable-identity-based-authentication"></a>Ativar a autenticação baseada na identidade

Pode ativar a autenticação baseada em identidade com Azure AD DS ou no local AD DS para ações de ficheiros Azure nas suas novas e existentes contas de armazenamento. Apenas um serviço de domínio pode ser utilizado para a autenticação de acesso a ficheiros na conta de armazenamento, que se aplica a todas as ações de ficheiros da conta. Orientações detalhadas sobre a configuração das suas ações de ficheiros para autenticação com Azure AD DS no nosso artigo [Enable Azure Ative Directory Domain Services autenticação em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) e orientação para DS AD no nosso outro artigo, [Ativar no local a autenticação de Serviços de Domínio de Diretório Ativo no local sobre as ações de ficheiros Azure.](storage-files-identity-auth-active-directory-enable.md)

### <a name="configure-share-level-permissions-for-azure-files"></a>Configure permissões de nível de partilha para ficheiros Azure

Uma vez ativada a autenticação AZure AD DS ou no local, a autenticação AD DS está ativada, pode utilizar funções incorporadas do Azure ou configurar funções personalizadas para identidades AD Azure e atribuir direitos de acesso a quaisquer ações de ficheiros nas suas contas de armazenamento. A permissão atribuída permite que a identidade concedida tenha acesso apenas à parte, nada mais, nem mesmo o diretório de raiz. Você ainda precisa configurar separadamente diretório ou permissões de nível de arquivo para ações de ficheiros Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configure o diretório ou as permissões de nível de ficheiros para ficheiros Azure

As ações de ficheiros Azure impõem permissões padrão de ficheiros Windows tanto ao nível do diretório como ao nível dos ficheiros, incluindo o diretório de raiz. A configuração de permissões de diretório ou de nível de ficheiro é suportada tanto em SMB como EM REST. Monte a partilha de ficheiros-alvo a partir do seu VM e configuure permissões utilizando o Windows File Explorer, Windows [ICACLs](/windows-server/administration/windows-commands/icacls)ou o comando [Set-ACL.](/powershell/module/microsoft.powershell.security/get-acl)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Use a chave de conta de armazenamento para permissões de super-mento

Um utilizador com a chave da conta de armazenamento pode aceder a partilhas de ficheiros Azure com permissões super-mais. As permissões superuser contornam todas as restrições de controlo de acesso.

> [!IMPORTANT]
> As nossas melhores práticas de segurança recomendadas são evitar partilhar as chaves da sua conta de armazenamento e alavancar a autenticação baseada na identidade sempre que possível.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Preservar diretório e arquivar ACLs ao importar dados para ações de ficheiros Azure

A Azure Files suporta a preservação de diretórios ou ACLs de nível de ficheiro ao copiar dados para ações de ficheiros Azure. Pode copiar ACLs num diretório ou arquivar ações de ficheiros Azure utilizando o Azure File Sync ou os blocos de ferramentas comuns de movimento de ficheiros. Por exemplo, pode utilizar [robocopia](/windows-server/administration/windows-commands/robocopy) com a `/copy:s` bandeira para copiar dados, bem como ACLs para uma partilha de ficheiros Azure. Os ACLs são preservados por padrão, não é necessário ativar a autenticação baseada na identidade na sua conta de armazenamento para preservar ACLs.

## <a name="pricing"></a>Preços
Não existe qualquer taxa adicional de serviço para permitir a autenticação baseada na identidade sobre a SMB na sua conta de armazenamento. Para obter mais informações sobre preços, consulte [os preços dos Ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/) e [os preços dos Serviços de Domínio AZure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre os Ficheiros Azure e a autenticação baseada na identidade sobre a SMB, consulte estes recursos:

- [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)
- [Ativar no local autenticação de Serviços de Domínio de Diretório Ativo sobre SMB para ações de ficheiros Azure](storage-files-identity-auth-active-directory-enable.md)
- [Ativar a autenticação dos Serviços de Domínio do Diretório Ativo do Azure em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)