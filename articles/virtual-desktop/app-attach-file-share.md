---
title: Windows Virtual Desktop configurar ficheiros de partilha de aplicativos MSIX anexa pré-visualização - Azure
description: Como configurar uma partilha de ficheiros para o anexo de aplicações MSIX para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1e7a956b358d486250fbfc26da141c47c0238b56
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448394"
---
# <a name="set-up-a-file-share-for-msix-app-attach-preview"></a>Configurar uma partilha de ficheiros para o anexo de aplicações MSIX (pré-visualização)

> [!IMPORTANT]
> O anexo de aplicações MSIX encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Todas as imagens MSIX devem ser armazenadas numa partilha de rede que pode ser acedida pelos utilizadores numa piscina de anfitrião com permissões apenas de leitura.

O anexo de aplicações MSIX (pré-visualização) não tem qualquer dependência do tipo de tecido de armazenamento que a partilha de ficheiros utiliza. As considerações para a partilha de anexação da app MSIX são as mesmas que para uma partilha FSLogix. Para saber mais sobre os requisitos de armazenamento, consulte [as opções de armazenamento de recipientes de perfil FSLogix no Windows Virtual Desktop](store-fslogix-profile.md).

## <a name="performance-requirements"></a>Requisitos de desempenho

Os limites de tamanho de imagem da aplicação MSIX para o seu sistema dependem do tipo de armazenamento que está a utilizar para armazenar os ficheiros VHD ou VHDx, bem como as limitações de tamanho dos ficheiros VHD, VHSD ou CIM e do sistema de ficheiros.

A tabela a seguir dá um exemplo de quantos recursos uma única imagem MSIX de 1 GB com uma aplicação MSIX no seu interior requer para cada VM:

| Recurso             | Requisitos |
|----------------------|--------------|
| IOPs de estado estável    | 1 IOPs       |
| Sinal de bota de máquina em | 10 IOPs      |
| Latência              | 400 ms       |

Os requisitos podem variar muito dependendo de quantas aplicações embaladas pelo MSIX são armazenadas na imagem MSIX. Para imagens maiores do MSIX, terá de alocar mais largura de banda.

### <a name="storage-recommendations"></a>Recomendações de armazenamento

O Azure oferece várias opções de armazenamento que podem ser usadas para anexar aplicações MISX. Recomendamos a utilização de Ficheiros Azure ou Ficheiros Azure NetApp, uma vez que estas opções oferecem o melhor valor entre custos e despesas de gestão. As [opções de armazenamento de artigos para contentores de perfil FSLogix no Windows Virtual Desktop](store-fslogix-profile.md) comparam as diferentes soluções de armazenamento geridas que o Azure oferece no contexto do Windows Virtual Desktop.

### <a name="optimize-msix-app-attach-performance"></a>Otimizar o desempenho da app MSIX

Aqui estão outras coisas que recomendamos que faça para otimizar o desempenho da app MSIX:

- A solução de armazenamento que utiliza para o anexo de aplicações MSIX deve estar no mesmo local do datacenter que os anfitriões da sessão.
- Para evitar estrangulamentos de desempenho, exclua os seguintes ficheiros VHD, VHDX e CIM das análises antivírus:
   
    - <MSIXAppAttachFileShare \> \* . Rio VHD
    - <MSIXAppAttachFileShare \> \* . VHDX
    - \\\\storageaccount.file.core.windows.net \\ \* \* parte. Rio VHD
    - \\\\storageaccount.file.core.windows.net \\ \* \* parte. VHDX
    - <MSIXAppAttachFileShare>. CIM
    - \\\\storageaccount.file.core.windows.net \\ \* \* parte. CIM

- Separe o tecido de armazenamento para a aplicação MSIX anexada a recipientes de perfil FSLogix.
- Todas as contas do sistema VM e contas de utilizador devem ter permissões apenas de leitura para aceder à partilha de ficheiros.
- Quaisquer planos de recuperação de desastres para o Windows Virtual Desktop devem incluir a replicação da partilha de ficheiros anexar a app MSIX na sua localização de falha secundária. Para saber mais sobre a recuperação de desastres, consulte [criar um plano de continuidade de negócios e recuperação de desastres.](disaster-recovery.md)

## <a name="how-to-set-up-the-file-share"></a>Como configurar a partilha de ficheiros

O processo de configuração da partilha de ficheiros anexar a aplicação MSIX é em grande parte o mesmo [que o processo de configuração das partilhas de ficheiros de perfil FSLogix](create-host-pools-user-profile.md). No entanto, terá de atribuir aos utilizadores diferentes permissões. O anexo de aplicações MSIX requer permissões apenas de leitura para aceder à partilha de ficheiros.

Se estiver a armazenar as suas aplicações MSIX em Ficheiros Azure, então para os anfitriões da sessão, terá de atribuir todas as VMs do anfitrião da sessão, tanto o controlo de acesso baseado na função de armazenamento (RBAC) como a partilha de ficheiros new technology file System (NTFS) na partilha.

| Objeto azul                      | Papel necessário                                     | Função de função                                  |
|-----------------------------------|--------------------------------------------------|-----------------------------------------------|
| Anfitrião de sessão (objetos de computador VM)| Contribuinte de Partilhas SMB de Dados de Ficheiros de Armazenamento          | Ler e executar, ler, listar conteúdos de pastas  |
| Administradores em Partilha de Ficheiros              | Contribuinte Elevado de Partilhas SMB de Dados de Ficheiros de Armazenamento | Controlo total                                  |
| Utilizadores na Partilha de Ficheiros               | Contribuinte de Partilhas SMB de Dados de Ficheiros de Armazenamento          | Ler e executar, ler, listar conteúdos de pastas  |

Para atribuir permissões vMs do anfitrião da sessão para a conta de armazenamento e partilha de ficheiros:

1. Criar um grupo de segurança Ative Directory Domain Services (AD DS).

2. Adicione as contas do computador para todos os VMs do anfitrião da sessão como membros do grupo.

3. Sync o grupo AD DS para Azure Ative Directory (Azure AD).

4. Criar uma conta de armazenamento.

5. Crie uma partilha de ficheiros na conta de armazenamento seguindo as instruções na [Criar uma partilha de ficheiros Azure](../storage/files/storage-how-to-create-file-share.md#create-file-share).

6. Junte a conta de armazenamento à AD DS seguindo as instruções [da Primeira Parte: ative a autenticação AD DS para as suas ações de ficheiroS Azure](../storage/files/storage-files-identity-ad-ds-enable.md#option-one-recommended-use-azfileshybrid-powershell-module).

7. Atribua o grupo AD DS sincronizado ao Azure AD e atribua à conta de armazenamento a função de Contribuinte de Partilha de Ficheiros de Armazenamento SMB.

8. Monte a partilha de ficheiros em qualquer anfitrião da sessão seguindo as instruções da [Segunda Parte: atribua permissões de nível de partilha a uma identidade](../storage/files/storage-files-identity-ad-ds-assign-permissions.md).

9. Conceda permissões ntfs sobre a partilha de ficheiros para o grupo DS AD.

10. Configurar permissões NTFS para as contas do utilizador. Você precisará de uma unidade operacional (OU) proveniente do DS AD a que as contas no VM pertencem.

Assim que tiver atribuído a identidade ao seu armazenamento, siga as instruções dos artigos nos [próximos passos](#next-steps) para conceder outras permissões necessárias à identidade que atribuiu aos VMs.

Também terá de se certificar de que os VMs do anfitrião da sessão têm permissões do New Technology File System (NTFS). Deve ter um recipiente de unidade operacional proveniente dos Serviços de Domínio do Diretório Ativo (DS AD), e os seus utilizadores devem ser membros dessa unidade operacional para utilizar estas permissões.

## <a name="next-steps"></a>Passos seguintes

Aqui estão as outras coisas que terá de fazer depois de configurar a partilha do ficheiro:

- Saiba como configurar os Serviços de Domínio do Diretório Ativo (AD DS) da Azure Ative Directory Services (AD DS) na [Criação de um recipiente de perfil com Ficheiros Azure e DS AD](create-file-share.md).
- Saiba como configurar ficheiros Azure e Azure AD DS na [Create a profile container with Azure Files e Azure AD DS](create-profile-container-adds.md).
- Saiba como configurar ficheiros Azure NetApp para app MSIX anexar na [Criar um recipiente de perfil com ficheiros Azure NetApp e DS AD](create-fslogix-profile-container.md).
- Saiba como utilizar uma partilha de ficheiros virtual baseada em máquinas na Criar um recipiente de [perfil para uma piscina hospedeira utilizando uma partilha de ficheiros.](create-host-pools-user-profile.md)

Uma vez terminado, aqui estão outros recursos que pode achar útil:

- Faça perguntas à nossa comunidade sobre esta funcionalidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).
- Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
- [App MSIX anexa glossário](app-attach-glossary.md)
- [App MSIX anexa FAQ](app-attach-faq.md)
