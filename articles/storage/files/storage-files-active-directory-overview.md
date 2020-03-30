---
title: Visão geral - Autorização baseada na identidade do Azure Files
description: O Azure Files suporta a autenticação baseada na identidade através do SMB (Bloco de Mensagens de Servidor) através dos Serviços de Domínio de Diretório Ativo Azure (AD DS) e do Ative Directory. As máquinas virtuais do Windows (VMs) podem aceder a partilhas de ficheiros Azure utilizando credenciais Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 737cdfaddca3a5f7532620bdafd86149e4d61f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061062"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Visão geral do suporte de autenticação baseado na identidade do Azure Files para acesso a SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para saber como ativar a autenticação de AD para ações de ficheiros Azure, consulte a [autenticação Ativa do Diretório Ativo sobre sMB para partilhas de ficheiros Azure](storage-files-identity-auth-active-directory-enable.md).

Para saber como ativar a autenticação Azure AD DS para ações de ficheiros Azure, consulte a autenticação enable [Azure Ative Directory Domain Services em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Glossário 
É útil compreender alguns termos-chave relativos à autenticação do Serviço de Domínio Azure AD sobre SMB para ações de ficheiros Azure:

-   **Autenticação Kerberos**

    O Kerberos é um protocolo de autenticação utilizado para confirmar a identidade de um utilizador ou o anfitrião. Para mais informações sobre Kerberos, consulte a visão geral da [autenticação kerberos.](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview)

-  **Protocolo do Bloco de Mensagens do Servidor (SMB)**

    A SMB é um protocolo de partilha de ficheiros padrão da indústria. O SMB também é conhecido como Sistema Comum de Ficheiros de Internet ou CIFS. Para obter mais informações sobre o SMB, consulte o Protocolo Microsoft SMB e a visão geral do [Protocolo CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Diretório Ativo Azure (Azure AD)**

    Azure Ative Directory (Azure AD) é o serviço de diretório e gestão de identidade multi-inquilinos da Microsoft. A Azure AD combina serviços de diretório sinuosos, gestão de acesso a aplicações e proteção de identidade numa única solução. O Azure AD permite que as suas máquinas virtuais do Windows (VMs) aderiram ao domínio para aceder a partilhas de ficheiros Azure com as suas credenciais Azure AD. Para mais informações, consulte [o what is Azure Ative Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Serviços de Domínio Azure AD (Azure AD DS)**

    O Azure AD Domain Services (GA) fornece serviços de domínio geridos, tais como a adesão ao domínio, políticas de grupo, LDAP e autenticação Kerberos/NTLM. Estes serviços são totalmente compatíveis com o Diretório Ativo do Windows Server. Para mais informações, consulte [o Azure Ative Directory (AD) Domain Services](../../active-directory-domain-services/overview.md).

- **Serviços de Domínio de Diretório Ativo (AD DS, também referido como AD)**

    O diretório ativo (AD) (pré-visualização) fornece os métodos para armazenar dados de diretório, ao mesmo tempo que os disponibiliza aos utilizadores e administradores da rede. A segurança é integrada com o Diretório Ativo através da autenticação de logon e do controlo de acesso a objetos no diretório. Com um único logon de rede, os administradores podem gerir dados e organização de diretórios em toda a sua rede, e os utilizadores autorizados da rede podem aceder a recursos em qualquer lugar da rede. A AD é geralmente adotada por empresas no local e utiliza credenciais aD como identidade para controlo de acesso. Para mais informações, consulte a visão geral dos serviços de domínio do [diretório ativo](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Controlo de Acesso Baseado em Funções Azure (RBAC)**

    O Controlo de Acesso Baseado em Funções (RBAC) do Azure permite uma gestão pormenorizada de acesso ao Azure. Utilizando o RBAC, pode gerir o acesso aos recursos, concedendo aos utilizadores o menor número de permissões necessárias para realizar os seus trabalhos. Para obter mais informações sobre rBAC, consulte [O que é o controlo de acesso baseado em papéis (RBAC) em Azure?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>Casos de utilização comuns

A autenticação e suporte baseados na identidade para Windows ACLs em Ficheiros Azure é melhor alavancado para os seguintes casos de utilização:

### <a name="replace-on-premises-file-servers"></a>Substituir servidores de ficheiros no local

Depreciar e substituir servidores de ficheiros dispersos no local é um problema comum que todas as empresas encontram na sua jornada de modernização de TI. As partilhas de ficheiros Azure com autenticação AD (pré-visualização) são as melhores aqui, quando pode migrar os dados para Ficheiros Azure. Uma migração completa permitir-lhe-á tirar partido dos benefícios de elevada disponibilidade e escalabilidade, minimizando também as mudanças do lado do cliente, especialmente a complicada infraestrutura de domínio da AD. Proporciona uma experiência de migração perfeita aos utilizadores finais, para que possam continuar a aceder aos seus dados com as mesmas credenciais utilizando as máquinas unidas ao domínio existente.

### <a name="lift-and-shift-applications-to-azure"></a>Aplicações de elevação e mudança para Azure

Quando "levantar e deslocar" aplicações para a nuvem, pretende manter o mesmo modelo de autenticação para os seus dados. À medida que estendemos a experiência de controlo de acesso baseada na identidade às partilhas de ficheiros Azure, elimina a necessidade de alterar a sua aplicação para métodos modernos de auth e acelerar a adoção em nuvem. As ações de ficheiros Azure oferecem a opção de integração com o Azure AD DS (GA) ou AD (pré-visualização) para autenticação. Se o seu plano for 100% nativo em nuvem e minimizar os esforços de gestão de infraestruturas em nuvem, o Azure AD DS seria um melhor ajuste como um serviço de domínio totalmente gerido. Se necessitar de compatibilidade total com as capacidades da AD DS (GA), talvez deva considerar o alargamento do seu ambiente aD à nuvem através de controladores de domínio auto-hospedeiros em VMs. De qualquer forma, fornecemos a flexibilidade para escolher os serviços de domínio que se adequam às necessidades do seu negócio.

### <a name="backup-and-disaster-recovery-dr"></a>Backup e recuperação de desastres (DR)

Se estiver a manter o seu armazenamento de ficheiros primário no local, as ações de ficheiros Azure podem servir como um armazenamento ideal para backup ou DR, para melhorar a continuidade do negócio. Pode utilizar as partilhas de ficheiros Azure para fazer o backback dos seus dados dos servidores de ficheiros existentes, preservando os DACLs do Windows. Para cenários DR, pode configurar uma opção de autenticação para suportar a aplicação adequada do controlo de acesso no failover.

## <a name="supported-scenarios"></a>Cenários suportados

O quadro seguinte resume os cenários de autenticação suportados do ficheiro Azure para Azure AD DS (GA) e AD (pré-visualização). Recomendamos selecionar o serviço de domínio que adotou para o ambiente do seu cliente para integração com ficheiros Azure. Se tiver AD (pré-visualização) já configurado no local ou no Azure onde os seus dispositivos são domínio ligados à AD, deverá optar por alavancar a Autenticação de Ações de AD (pré-visualização) para a autenticação de ações de ficheiro sinuoso. Da mesma forma, se já adotou O Azure AD DS (GA), deve usá-lo para a autenticação de ações de ficheiro Saque.


|Autenticação Azure AD DS (GA)  |Autenticação AD (pré-visualização)  |
|---------|---------|
|O domínio Azure AD DS juntou-se às máquinas Windows para aceder a partilhas de ficheiros Azure com credenciais Azure AD sobre SMB.     |O domínio AD juntou-se às máquinas Windows pode aceder a partilhas de ficheiros Azure com credenciais AD que são sincronizadas com AD Azure sobre SMB.         |

### <a name="unsupported-scenarios"></a>Cenários não suportados

- A autenticação Azure AD DS (GA) e AD (pré-visualização) não suportam a autenticação contra contas de computador. Em vez disso, pode considerar a utilização de uma conta de logon de serviço.
- A autenticação Azure AD DS (GA) não suporta a autenticação contra dispositivos unidos em nuvem Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Vantagens da autenticação baseada na identidade
A autenticação baseada na identidade dos Ficheiros Azure oferece vários benefícios sobre a utilização da autenticação de chave partilhada:

-   **Alargar a experiência tradicional de acesso ao ficheiro baseado na identidade para a nuvem com AD e Azure AD DS**  
    Se planeia "levantar e transferir" a sua aplicação para a nuvem, substituindo os servidores de ficheiros tradicionais por partilhas de ficheiros Azure, então poderá querer que a sua aplicação autentique com credenciais AD ou Azure AD para aceder a dados de ficheiros. O Azure Files suporta a utilização de credenciais AD ou Azure AD para aceder a ações de ficheiros Azure sobre SMB a partir de VMs adenados por AD ou Azure AD DS.

-   **Impor controlo de acesso granular em ações de ficheiros Azure**  
    Pode conceder permissões a uma identidade específica no nível de partilha, diretório ou ficheiro. Por exemplo, suponha que você tem várias equipas usando uma única partilha de ficheiros Azure para colaboração de projeto. Pode conceder a todas as equipas o acesso a diretórios não sensíveis, limitando ao mesmo tempo o acesso a diretórios que contenham dados financeiros sensíveis apenas à sua equipa de Finanças. 

-   **Back up Windows ACLs (também conhecido como NTFS) juntamente com os seus dados**  
    Pode utilizar ações de ficheiros Azure para fazer o back up das suas ações de ficheiros existentes no local. O Azure Files preserva os seus ACLs juntamente com os seus dados quando faz o back-up de uma partilha de ficheiros para as ações de ficheiros Azure sobre a SMB.

## <a name="how-it-works"></a>Como funciona
As ações de ficheiro azure suportam a autenticação kerberos para integração com Azure AD DS (GA) ou AD (pré-visualização). Antes de poder ativar a autenticação nas partilhas de ficheiros Azure, tem primeiro de configurar o seu ambiente de domínio. Para autenticação Azure AD DS (GA), deve ativar os Serviços de Domínio Azure AD e o domínio juntar-se aos VMs a partir do quais planeia aceder aos dados dos ficheiros. O Seu VM filiado em domíniodeve residir na mesma rede virtual (VNET) que os seus Serviços de Domínio AD Azure. Da mesma forma, para autenticação AD (pré-visualização), precisa de configurar o seu controlador de domínio Ative Directory e o domínio junte-se às suas máquinas ou VMs.

Quando uma identidade associada a uma aplicação em execução de um VM tenta aceder a dados em ações de ficheiros Azure, o pedido é enviado aos Serviços de Domínio da AD azure para autenticar a identidade. Se a autenticação for bem sucedida, a Azure AD Domain Services devolve um símbolo kerberos. O pedido envia um pedido que inclui o símbolo Kerberos, e as ações de ficheiro azure usam esse símbolo para autorizar o pedido. As ações de ficheiros Azure recebem apenas o símbolo e não persistem nas credenciais Azure AD. A autenticação da AD funciona de forma semelhante, onde a AD fornece o símbolo Kerberos.

![Screenshot mostrando diagrama de autenticação azure ad sobre SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Ativar a autenticação baseada na identidade

Pode ativar a autenticação baseada na identidade com o Azure AD DS (GA) ou AD (pré-visualização) para as ações de ficheiros Azure nas suas novas contas de armazenamento existentes. Apenas um serviço de domínio pode ser utilizado para autenticação de acesso de ficheiros na conta de armazenamento, o que se aplica a todas as ações de ficheiro sinuosas da conta. Orientação detalhada passo a passo na configuração das suas ações de ficheiro para autenticação com o Azure AD DS (GA) no nosso artigo [Enable Azure Ative Directory Domain Services autenticação em Ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) e orientação para AD (pré-visualização) no nosso outro artigo, [Enable Ative Directory autenticação sobre SMB para ações de ficheiros Azure](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Configure permissões de nível de partilha para Ficheiros Azure

Uma vez ativada a autenticação Azure AD DS (GA) ou AD (pré-visualização), pode utilizar funções RBAC incorporadas ou configurar funções personalizadas para identidades AD Azure e atribuir direitos de acesso a quaisquer ações de ficheiro nas suas contas de armazenamento. a permissão atribuída permite que a identidade concedida tenha acesso à partilha apenas, nada mais, nem mesmo o diretório raiz. Ainda precisa de configurar separadamente as permissões de diretório ou de nível de ficheiro para as partilhas de ficheiros Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Configure permissões de diretório ou nível de ficheiro para Ficheiros Azure

As partilhas de ficheiros Azure aplicam permissões padrão de ficheiros do Windows tanto ao nível do diretório como do ficheiro, incluindo o diretório raiz. A configuração de permissões de diretório ou de nível de ficheiro é suportada tanto em SMB como EM REST. Monte a parte do ficheiro alvo a partir do seu VM e configure permissões utilizando o Windows File Explorer, Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)ou o comando [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Utilize a chave da conta de armazenamento para permissões de superutilizadores

Um utilizador que possua a chave da conta de armazenamento pode aceder a partilhas de ficheiros Azure com permissões de superutilizador. As permissões de superutilizador contornam todas as restrições de controlo de acesso.

> [!IMPORTANT]
> As nossas melhores práticas de segurança recomendadas são evitar partilhar as chaves da sua conta de armazenamento e aproveitar a autenticação baseada na identidade sempre que possível.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Preservar o diretório e arquivar ACLs ao importar dados para ações de ficheiros Azure

O Azure Files suporta a preservação de ACLs de diretório ou de ficheiro ao copiar dados para as ações de ficheiros da Azure. Pode copiar ACLs num diretório ou arquivar para ações de ficheiros Azure utilizando o Azure File Sync ou os conjuntos comuns de ferramentas de movimento de ficheiros. Por exemplo, pode utilizar a `/copy:s` [robocopia](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) com a bandeira para copiar dados, bem como ACLs para uma partilha de ficheiros Azure. Os ACLs são preservados por defeito, não é necessário permitir a autenticação baseada na identidade na sua conta de armazenamento para preservar OS ACLs.

## <a name="pricing"></a>Preços
Não existe nenhuma taxa adicional de serviço para permitir a autenticação baseada na identidade sobre sMB na sua conta de armazenamento. Para obter mais informações sobre preços, consulte os preços dos [Ficheiros Azure](https://azure.microsoft.com/pricing/details/storage/files/) e as páginas de preços dos Serviços de [Domínio Azure AD](https://azure.microsoft.com/pricing/details/active-directory-ds/) se estiver à procura de informações aAD DS.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre ficheiros Azure e autenticação baseada na identidade através de SMB, consulte estes recursos:

- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
- [Ativar a autenticação de Diretório Ativo sobre SMB para ações de ficheiros Azure](storage-files-identity-auth-active-directory-enable.md)
- [Ativar a autenticação dos Serviços de Domínio de Diretório Ativo azure em ficheiros Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [FAQ](storage-files-faq.md)
