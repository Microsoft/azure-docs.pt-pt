---
title: Ficheiros de contentores de perfil Do Windows Virtual Desktop FSLogix - Azure
description: Este artigo descreve os contentores de perfil FSLogix dentro dos ficheiros Windows Virtual Desktop e Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1dc5d54fa24217c91e14a8f37e092888b2bb6474
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127876"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contentores de perfil FSLogix e ficheiros do Azure

O serviço de ambiente de trabalho virtual windows recomenda os recipientes de perfil FSLogix como uma solução de perfil do utilizador. O FSLogix foi concebido para percorrer perfis em ambientes de computação remota, como o Windows Virtual Desktop. Armazena um perfil completo de utilizador num único recipiente. No signine, este recipiente está dinamicamente ligado ao ambiente de computação utilizando discos rígidos virtuais (VHD) e hiper-V virtual hard (VHDX). O perfil do utilizador está imediatamente disponível e aparece no sistema exatamente como um perfil de utilizador nativo. Este artigo descreve como os recipientes de perfil FSLogix utilizados com ficheiros Azure funcionam no Windows Virtual Desktop.

>[!NOTE]
>Se procura material de comparação sobre as diferentes opções de armazenamento de contentores de perfil FSLogix no Azure, consulte opções de armazenamento para recipientes de [perfil FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Perfis de utilizador

Um perfil de utilizador contém elementos de dados sobre um indivíduo, incluindo informações de configuração como configurações de ambiente de trabalho, ligações de rede persistentes e definições de aplicação. Por padrão, o Windows cria um perfil de utilizador local que está fortemente integrado com o sistema operativo.

Um perfil remoto do utilizador fornece uma partilha entre os dados do utilizador e o sistema operativo. Permite que o sistema operativo seja substituído ou alterado sem afetar os dados do utilizador. No Remote Desktop Session Host (RDSH) e nas Infraestruturas virtuais de ambiente de trabalho (VDI), o sistema operativo pode ser substituído pelas seguintes razões:

- Uma atualização do sistema operativo
- Uma substituição de uma máquina virtual existente (VM)
- Um utilizador que faz parte de um ambiente RDSH ou VDI (não persistente)

Os produtos da Microsoft operam com várias tecnologias para perfis remotos de utilizadores, incluindo estas tecnologias:
- Perfis de utilizador de roaming (RUP)
- Discos de perfil do utilizador (UPD)
- Roaming do estado empresarial (ESR)

UPD e RUP são as tecnologias mais utilizadas para perfis de utilizador em ambientes remote Desktop Session Host (RDSH) e Virtual Hard Disk (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Desafios com tecnologias de perfil de utilizador anteriores

As soluções existentes e antigas da Microsoft para perfis de utilizador vieram com vários desafios. Nenhuma solução anterior tratou de todas as necessidades de perfil do utilizador que vêm com um ambiente RDSH ou VDI. Por exemplo, a UPD não consegue lidar com ficheiros OST grandes e a RUP não persiste em configurações modernas.

#### <a name="functionality"></a>Funcionalidade

A tabela que se segue mostra benefícios e limitações de tecnologias de perfil de utilizador anteriores.

| Tecnologia | Configurações modernas | Configurações Win32 | Definições de OS | Dados de utilizador | Suportado no servidor SKU | Armazenamento de back-end em Azure | Armazenamento de back-end no local | Suporte de versão | Sinal subsequente a tempo |Notas|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Discos de perfil do utilizador (UPD)** | Sim | Sim | Sim | Sim | Sim | Não | Sim | Ganhar 7+ | Sim | |
| **Perfil de utilizador de roaming (RUP), modo de manutenção** | Não | Sim | Sim | Sim | Sim| Não | Sim | Ganhar 7+ | Não | |
| **Roaming do Estado Empresarial (ESR)** | Sim | Não | Sim | Não | Ver notas | Sim | Não | Ganhar 10 | Não | Funções no servidor SKU mas sem interface de utilizador de suporte |
| **Virtualização da experiência do utilizador (UE-V)** | Sim | Sim | Sim | Não | Sim | Não | Sim | Ganhar 7+ | Não |  |
| **Ficheiros de nuvem OneDrive** | Não | Não | Não | Sim | Ver notas | Ver notas  | Ver Notas | Ganhar 10 RS3 | Não | Não testado no servidor SKU. O armazenamento no Azure depende do cliente sincronizado. O armazenamento no ventel precisa de um cliente sincronizado. |

#### <a name="performance"></a>Desempenho

A UPD requer espaços de [armazenamento diretos (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) para atender aos requisitos de desempenho. UpD utiliza o protocolo do Bloco de Mensagens do Servidor (SMB). Copia o perfil para o VM em que o utilizador está a ser registado. UPD com S2D é a solução que recomendamos para o Windows Virtual Desktop.  

#### <a name="cost"></a>Custo

Embora os clusters S2D atinjam o desempenho necessário, o custo é caro para os clientes empresariais, mas especialmente caro para os clientes de pequena e média empresa (SMB). Para esta solução, as empresas pagam discos de armazenamento, juntamente com o custo dos VMs que usam os discos para uma ação.

#### <a name="administrative-overhead"></a>Sobrecarga administrativa

Os clusters S2D requerem um sistema operativo que é remendado, atualizado e mantido num estado seguro. Estes processos e a complexidade da criação de uma recuperação de desastres do S2D tornam o S2D viável apenas para empresas com um pessoal de TI dedicado.

## <a name="fslogix-profile-containers"></a>Recipientes de perfil FSLogix

Em 19 de novembro de 2018, a [Microsoft adquiriu o FSLogix.](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) O FSLogix aborda muitos desafios do contentor de perfil. Entre eles, a chave é:

- **Desempenho:** Os [recipientes de perfil FSLogix](/fslogix/configure-profile-container-tutorial/) são de alto desempenho e resolvem problemas de desempenho que historicamente bloquearam o modo de troca em cached.
- **OneDrive:** Sem recipientes de perfil FSLogix, o OneDrive for Business não é suportado em ambientes RDSH ou VDI não persistentes. [As melhores práticas do OneDrive for Business e FSLogix](/fslogix/overview/) descrevem como interagem. Para mais informações, consulte [Utilize o cliente sincronizado em ambientes de trabalho virtuais](/deployoffice/rds-onedrive-business-vdi/).
- **Pastas adicionais:** O FSLogix fornece a capacidade de estender os perfis do utilizador para incluir pastas adicionais.

Desde a aquisição, a Microsoft começou a substituir as soluções de perfil de utilizador existentes, como a UPD, por recipientes de perfil FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integração de Ficheiros Azure com o Serviço de Domínio de Diretório Ativo Azure

O desempenho e as características dos contentores de perfil FSLogix aproveitam a nuvem. No dia 7 de agosto de 2019, o Microsoft Azure Files anunciou a disponibilidade geral da autenticação dos [Ficheiros Azure com o Azure Ative Directory Domain Service (AD DS).](../storage/files/storage-files-active-directory-overview.md) Ao abordar tanto os custos como os custos administrativos, o Azure Files com a Autenticação DS Azure AD é uma solução premium para os perfis dos utilizadores no serviço de ambiente de trabalho virtual do Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>As melhores práticas para o Windows Virtual Desktop

O Windows Virtual Desktop oferece controlo total sobre tamanho, tipo e contagem de VMs que estão a ser utilizados pelos clientes. Para mais informações, consulte [o que é o Windows Virtual Desktop?](overview.md)

Para garantir que o ambiente de ambiente de trabalho virtual do Windows segue as melhores práticas:

- A conta de armazenamento do Azure Files deve estar na mesma região que os VMs hospedeiros da sessão.
- As permissões dos Ficheiros Azure devem corresponder às permissões descritas nos [Requisitos - Contentores](/fslogix/fslogix-storage-config-ht)de Perfil .
- Cada piscina hospedeira deve ser construída com o mesmo tipo e tamanho VM com base na mesma imagem principal.
- Cada conjunto de acolhimento VM deve estar no mesmo grupo de recursos para ajudar na gestão, escalação e atualização.
- Para um desempenho ótimo, a solução de armazenamento e o recipiente de perfil FSLogix devem estar na mesma localização do centro de dados.
- A conta de armazenamento que contém a imagem principal deve estar na mesma região e subscrição onde os VMs estão a ser provisionados.

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes guias para configurar um ambiente de ambiente de trabalho virtual windows.

- Para começar a construir a sua solução de virtualização de ambiente de trabalho, consulte [Criar um inquilino no Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
- Para criar uma piscina de anfitriões dentro do seu inquilino windows Virtual Desktop, consulte [Create a host pool com o Azure Marketplace](create-host-pools-azure-marketplace.md).
- Para configurar as ações de ficheiro totalmente geridas na nuvem, consulte a [partilha de Ficheiros Azure](/azure/storage/files/storage-files-active-directory-enable/).
- Para configurar os recipientes de perfil FSLogix, consulte Criar um recipiente de [perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).
- Para atribuir os utilizadores a um pool de anfitriões, consulte Gerir grupos de [aplicações para o Windows Virtual Desktop](manage-app-groups.md).
- Para aceder aos seus recursos de Ambiente de Trabalho Virtual do Windows a partir de um navegador web, consulte [Connect to Windows Virtual Desktop](connect-web.md).
