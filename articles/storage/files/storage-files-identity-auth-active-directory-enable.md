---
title: Visão geral - Autenticação AD DS no local para ações de ficheiros Azure
description: Saiba mais sobre a autenticação dos Serviços de Domínio do Diretório Ativo (AD DS) para ações de ficheiros Azure. Este artigo passa por cenários de suporte, disponibilidade e explica como as permissões funcionam entre o seu diretório ativo AD DS e Azure.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: bb408c762c33e4d146a2f0ef36f32e525b3859bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91758273"
---
# <a name="overview---on-premises-active-directory-domain-services-authentication-over-smb-for-azure-file-shares"></a>Visão geral - autenticação de serviços de domínio de diretório ativo no local sobre SMB para ações de ficheiros Azure

Ficheiros Azure [Azure Files](storage-files-introduction.md)   suporta a autenticação baseada na identidade sobre o Bloco de Mensagens do Servidor (SMB) através de dois tipos de Serviços de Domínio: no local Serviços de Domínio de Diretório Ativo (AD DS) e Azure Ative Directory Domain Services (Azure AD DS). Recomendamos vivamente que reveja a [secção Como funciona](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview#how-it-works) para selecionar o serviço de domínio certo para autenticação. A configuração é diferente dependendo do serviço de domínio que escolher. Estas séries de artigos focam-se em ativar e configurar o DS AD no local para autenticação com ações de ficheiros Azure.

Se é novo nas ações de ficheiros Azure, recomendamos que leia o nosso [guia de planeamento](storage-files-planning.md) antes de ler a seguinte série de artigos.

## <a name="supported-scenarios-and-restrictions"></a>Cenários e restrições apoiados

- As identidades AD DS utilizadas para a autenticação AD DS dos Ficheiros AZure devem ser sincronizadas com a Azure AD. A sincronização de hash de palavra-passe é opcional. 
- Suporta ações de ficheiros Azure geridas pela Azure File Sync.
- Suporta a autenticação Kerberos com encriptação AD com encriptação RC4-HMAC e [AES 256](https://docs.microsoft.com/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). A encriptação AES 128 Kerberos ainda não está suportada.
- Suporta uma única experiência de inscrição.
- Suportado apenas em clientes que executam em versões DE mais recentes do que o Windows 7 ou Windows Server 2008 R2.
- Apenas suportado contra a floresta AD para a qual a conta de armazenamento está registada. Só é possível aceder a partilhas de ficheiros Azure com as credenciais AD DS de uma única floresta por padrão. Se precisar de aceder à sua partilha de ficheiros Azure a partir de uma floresta diferente, certifique-se de que tem a confiança florestal adequada configurada, consulte as [FAQ](storage-files-faq.md#ad-ds--azure-ad-ds-authentication) para obter mais detalhes.
- Não suporta a autenticação contra contas de computador criadas em DS AD.
- Não suporta a autenticação contra ações de ficheiros do Sistema de Ficheiros de Rede (NFS).

Quando ativa ações de ficheiros AD DS para Azure sobre a SMB, as suas máquinas aderidas a DS AD podem montar ações de ficheiros Azure utilizando as credenciais de DS AD existentes. Esta capacidade pode ser ativada com um ambiente AD DS hospedado em máquinas on-prem ou hospedado em Azure.

> [!NOTE]
> Para ajudá-lo a configurar a autenticação Azure Files AD para alguns casos de uso comum, publicámos dois vídeos com orientação passo a passo para os seguintes cenários:
> - [Substituição de servidores de ficheiros no local por Ficheiros Azure (incluindo configuração em link privado para ficheiros e autenticação de AD)](https://sec.ch9.ms/ch9/3358/0addac01-3606-4e30-ad7b-f195f3ab3358/ITOpsTalkAzureFiles_high.mp4)
> - [Utilização de Ficheiros Azure como recipiente de perfil para o Windows Virtual Desktop (incluindo configuração na autenticação AD e configuração FsLogix)](https://www.youtube.com/embed/9S5A1IJqfOQ)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de ativar a autenticação AD DS para ações de ficheiros Azure, certifique-se de que completou os seguintes pré-requisitos: 

- Selecione ou crie o seu [ambiente AD DS](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) e [sincronize-o para Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) com Azure AD Connect. 

    Pode ativar a funcionalidade num ambiente AD DS novo ou existente no local. As identidades utilizadas para o acesso devem ser sincronizadas com a Azure AD. O inquilino da AZure AD e a parte de ficheiro a que está a aceder devem estar associados à mesma subscrição.

- Junte-se a um domínio numa máquina no local ou num Azure VM para o AD DS no local. Para obter informações sobre como aderir ao domínio, consulte [a Junção de um Computador a um Domínio](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain).

    Se a sua máquina não estiver de domínio associada a um DS AD, poderá ainda ser capaz de aproveitar as credenciais de AD para autenticação se a sua máquina tiver uma linha de visão do controlador de domínio AD.

- Selecione ou crie uma conta de armazenamento Azure.  Para um melhor desempenho, recomendamos que implemente a conta de armazenamento na mesma região que o cliente a partir do qual planeia aceder à partilha. Em seguida, [monte a partilha de ficheiros Azure](storage-how-to-use-files-windows.md) com a chave da sua conta de armazenamento. A montagem com a chave da conta de armazenamento verifica a conectividade.

    Certifique-se de que a conta de armazenamento que contém as suas ações de ficheiros ainda não está configurada para a Autenticação DS AD AZure. Se a autenticação Azure Files AD DS estiver ativada na conta de armazenamento, tem de ser desativada antes de ser alterada para utilizar o DS AD no local. Isto implica que os ACLs existentes configurados no ambiente Azure AD DS terão de ser reconfigurados para uma aplicação adequada da permissão.


    Se tiver problemas na ligação aos Ficheiros Azure, consulte a [ferramenta de resolução de problemas que publicámos para erros de montagem de Ficheiros Azure no Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Também fornecemos [orientações](https://docs.microsoft.com/azure/storage/files/storage-files-faq#on-premises-access) para trabalhar em torno de cenários quando a porta 445 está bloqueada. 


- Faça qualquer configuração de rede relevante antes de ativar e configurar a autenticação DS AD nas suas ações de ficheiroS Azure. Consulte [as considerações de networking dos Ficheiros Azure](storage-files-networking-overview.md) para obter mais informações.

## <a name="regional-availability"></a>Disponibilidade regional

A autenticação de Ficheiros Azure com DS AD está disponível em [todas as regiões Azure Public e Gov](https://azure.microsoft.com/global-infrastructure/locations/).

## <a name="overview"></a>Descrição geral

Se pretender ativar quaisquer configurações de rede na sua partilha de ficheiros, recomendamos que leia o artigo [de consideração de rede](https://docs.microsoft.com/azure/storage/files/storage-files-networking-overview) e complete a configuração relacionada antes de permitir a autenticação de DS AD.

Permitir a autenticação AD DS para as suas ações de ficheiroS Azure permite-lhe autenticar as suas ações de ficheiro Azure com as suas credenciais de DS AD pré-pré-m. Além disso, permite-lhe gerir melhor as suas permissões para permitir o controlo de acesso granular. Isto requer a sincronização de identidades de DS AD on-prem a Azure AD com ligação AD. Controla o acesso ao nível de partilha com identidades sincronizadas com a Azure AD enquanto gere o acesso ao nível de ficheiro/partilha com credenciais DS de DS em pré-m.

Em seguida, siga os passos abaixo para configurar ficheiros Azure para autenticação DS AD: 

1. [Parte um: ativar a autenticação AD DS na sua conta de armazenamento](storage-files-identity-ad-ds-enable.md)

1. [Parte dois: atribuir permissões de acesso para uma parte à identidade AZure AD (um utilizador, grupo ou principal de serviço) que esteja em sintonia com a identidade de AD alvo](storage-files-identity-ad-ds-assign-permissions.md)

1. [Parte três: configurar ACLs do Windows sobre SMB para diretórios e ficheiros](storage-files-identity-ad-ds-configure-permissions.md)
 
1. [Parte quatro: monte uma partilha de ficheiros Azure para um VM unidos ao seu DS AD](storage-files-identity-ad-ds-mount-file-share.md)

1. [Atualize a palavra-passe da sua identidade de conta de armazenamento em DS AD](storage-files-identity-ad-ds-update-password.md)

O diagrama seguinte ilustra o fluxo de trabalho de ponta a ponta para permitir a autenticação Azure AD sobre a SMB para ações de ficheiros Azure. 

![Ficheiros Diagrama de fluxo de trabalho de AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

As identidades utilizadas para aceder às ações de ficheiros Azure devem ser sincronizadas com a Azure AD para impor permissões de ficheiros de nível de ação através do modelo [Azure role-based access control (Azure RBAC).](../../role-based-access-control/overview.md) [Os DACLs de estilo Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) em ficheiros/diretórios transportados a partir de servidores de ficheiros existentes serão preservados e aplicados. Isto oferece uma integração perfeita com o ambiente AD DS da sua empresa. À medida que substitui os servidores de ficheiros on-prem por ações de ficheiros Azure, os utilizadores existentes podem aceder às ações de ficheiros Azure dos seus clientes atuais por uma única experiência de login, sem qualquer alteração às credenciais em uso.  

## <a name="next-steps"></a>Passos seguintes

Para ativar no local a autenticação AD DS para a sua partilha de ficheiros Azure, continue até ao artigo seguinte:

[Parte um: ativar a autenticação AD DS para a sua conta](storage-files-identity-ad-ds-enable.md)
