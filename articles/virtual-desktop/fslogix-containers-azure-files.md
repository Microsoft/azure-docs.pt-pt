---
title: Ficheiros de contentores de perfis FSLogix do Windows Virtual Desktop - Azure
description: Este artigo descreve os contentores de perfil FSLogix dentro dos ficheiros Windows Virtual Desktop e Azure.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 945bf806a813943d02fb4db1245dfb74eb7fb1f9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446898"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contentores de perfil FSLogix e ficheiros do Azure

O serviço de ambiente de trabalho virtual do Windows recomenda recipientes de perfil FSLogix como solução de perfil de utilizador. O FSLogix foi concebido para percorrer perfis em ambientes informáticos remotos, como o Windows Virtual Desktop. Armazena um perfil de utilizador completo num único contentor. Ao iniciar sposição, este recipiente está dinamicamente ligado ao ambiente de computação utilizando disco rígido virtual (VHD) e disco virtual de hiper-V (VHDX). O perfil do utilizador fica imediatamente disponível e aparece no sistema exatamente como um perfil de utilizador nativo. Este artigo descreve como os recipientes de perfil FSLogix utilizados com ficheiros Azure funcionam no Windows Virtual Desktop.

>[!NOTE]
>Se procura material de comparação sobre as diferentes opções de armazenamento de contentores de perfil FSLogix no Azure, consulte [as opções de armazenamento para recipientes de perfil FSLogix](store-fslogix-profile.md).

## <a name="user-profiles"></a>Perfis de utilizador

Um perfil de utilizador contém elementos de dados sobre um indivíduo, incluindo informações de configuração como definições de ambiente de trabalho, ligações de rede persistentes e definições de aplicação. Por predefinição, o Windows cria um perfil de utilizador local que está fortemente integrado no sistema operativo.

Um perfil de utilizador remoto fornece uma divisória entre os dados do utilizador e o sistema operativo. Permite que o sistema operativo seja substituído ou alterado sem afetar os dados do utilizador. No Host Remote Desktop Session (RDSH) e em Infraestruturas virtuais de desktop (VDI), o sistema operativo pode ser substituído pelas seguintes razões:

- Uma atualização do sistema operativo
- Substituição de uma máquina virtual existente (VM)
- Um utilizador que faça parte de um ambiente de RDSH ou VDI (não persistente)

Os produtos microsoft operam com várias tecnologias para perfis remotos de utilizadores, incluindo estas tecnologias:
- Perfis de utilizador roaming (RUP)
- Discos de perfil do utilizador (UPD)
- Roaming do estado da empresa (ESR)

UPD e RUP são as tecnologias mais utilizadas para perfis de utilizador em ambientes remote desktop session host (RDSH) e Virtual Hard Disk (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Desafios com tecnologias anteriores de perfil de utilizador

As soluções existentes e antigas da Microsoft para perfis de utilizador vieram com vários desafios. Nenhuma solução anterior manuseava todas as necessidades do perfil do utilizador que viessem com um ambiente RDSH ou VDI. Por exemplo, o UPD não consegue lidar com ficheiros OST grandes e o RUP não persiste em configurações modernas.

#### <a name="functionality"></a>Funcionalidade

A tabela a seguir mostra benefícios e limitações de tecnologias anteriores de perfil de utilizador.

| Tecnologia | Configurações modernas | Definições win32 | Definições de SO | Dados de utilizador | Suportado no servidor SKU | Armazenamento back-end em Azure | Armazenamento em back-end no local | Suporte de versão | Sinal subsequente a tempo |Notas|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Discos de perfil do utilizador (UPD)** | Sim | Sim | Sim | Sim | Sim | Não | Sim | Ganhar 7+ | Sim | |
| **Perfil do utilizador roaming (RUP), modo de manutenção** | Não | Sim | Sim | Sim | Sim| Não | Sim | Ganhar 7+ | Não | |
| **Roaming do Estado da Empresa (ESR)** | Sim | Não | Sim | Não | Ver notas | Sim | Não | Ganhar 10 | Não | Funções no servidor SKU, mas sem interface de utilizador de suporte |
| **Virtualização da experiência do utilizador (UE-V)** | Sim | Sim | Sim | Não | Sim | Não | Sim | Ganhar 7+ | Não |  |
| **Ficheiros de nuvem OneDrive** | Não | Não | Não | Sim | Ver notas | Ver notas  | Ver Notas | Ganhar 10 RS3 | Não | Não testado no servidor SKU. O armazenamento em back-end em Azure depende do cliente sincronizado. O armazenamento no prem precisa de um cliente sincronizado. |

#### <a name="performance"></a>Desempenho

A UPD requer [espaços de armazenamento direto (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) para atender aos requisitos de desempenho. UPD utiliza o protocolo Bloco de Mensagens do Servidor (SMB). Copia o perfil para o VM no qual o utilizador está a ser registado.

#### <a name="cost"></a>Custo

Enquanto os clusters S2D alcançam o desempenho necessário, o custo é caro para os clientes empresariais, mas especialmente caro para os clientes de pequenas e médias empresas (SMB). Para esta solução, as empresas pagam por discos de armazenamento, juntamente com o custo dos VMs que usam os discos para uma parte.

#### <a name="administrative-overhead"></a>Sobrecarga administrativa

Os clusters S2D requerem um sistema operativo remendado, atualizado e mantido em estado seguro. Estes processos e a complexidade da criação da recuperação de desastres S2D tornam o S2D viável apenas para empresas com um pessoal de TI dedicado.

## <a name="fslogix-profile-containers"></a>Recipientes de perfil FSLogix

Em 19 de novembro de 2018, [a Microsoft adquiriu a FSLogix.](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/) A FSLogix aborda muitos desafios de contentores de perfil. Entre eles estão:

- **Desempenho:** Os [recipientes de perfil FSLogix](/fslogix/configure-profile-container-tutorial/) são de alto desempenho e resolvem problemas de desempenho que bloquearam historicamente o modo de troca em cache.
- **OneDrive:** Sem recipientes de perfil FSLogix, o OneDrive for Business não é suportado em ambientes RDSH ou VDI não persistentes. A [página de suporte do OneDrive VDI](/onedrive/sync-vdi-support) dir-lhe-á como interagem. Para obter mais informações, consulte [utilizar o cliente sincronizado em ambientes de trabalho virtuais.](/deployoffice/rds-onedrive-business-vdi/)
- **Pastas adicionais:** A FSLogix fornece a capacidade de alargar os perfis do utilizador para incluir pastas adicionais.

Desde a aquisição, a Microsoft começou a substituir as soluções de perfil de utilizador existentes, como a UPD, por contentores de perfil FSLogix.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Integração de Ficheiros Azure com O Serviço de Domínio do Diretório Ativo Azure

O desempenho e as funcionalidades dos recipientes de perfil FSLogix aproveitam a nuvem. Em 7 de agosto de 2019, a Microsoft Azure Files anunciou a disponibilidade geral de autenticação de [Ficheiros Azure com o Azure Ative Directory Domain Service (AD DS)](../storage/files/storage-files-active-directory-overview.md). Ao abordar tanto os custos como as despesas administrativas, o Azure Files com Azure AD DS Authentication é uma solução premium para perfis de utilizador no serviço Windows Virtual Desktop.

## <a name="best-practices-for-windows-virtual-desktop"></a>Melhores práticas para Windows Virtual Desktop

O Windows Virtual Desktop oferece controlo total sobre o tamanho, tipo e contagem de VMs que estão a ser usados pelos clientes. Para mais informações, consulte [o que é o Windows Virtual Desktop?](overview.md)

Para garantir que o ambiente de ambiente de trabalho virtual do Windows segue as melhores práticas:

- A conta de armazenamento Azure Files deve estar na mesma região que os VMs anfitriões da sessão.
- As permissões de Ficheiros Azure devem corresponder às permissões descritas nos [Requisitos - Profile Containers](/fslogix/fslogix-storage-config-ht).
- Cada vm da piscina hospedeira deve ser construída do mesmo tipo e tamanho VM com base na mesma imagem principal.
- Cada grupo de anfitriões VM deve estar no mesmo grupo de recursos para ajudar a gestão, dimensionamento e atualização.
- Para um melhor desempenho, a solução de armazenamento e o recipiente de perfil FSLogix devem estar na mesma localização do centro de dados.
- A conta de armazenamento que contém a imagem principal deve estar na mesma região e na subscrição onde os VM estão a ser a provisionados.

## <a name="next-steps"></a>Passos seguintes

Utilize os seguintes guias para configurar um ambiente de ambiente de trabalho virtual do Windows.

- Para começar a construir a sua solução de virtualização no ambiente de trabalho, consulte [Criar um inquilino no Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- Para criar uma piscina de anfitriões dentro do seu inquilino Virtual Desktop do Windows, consulte [criar uma piscina de anfitriões com o Azure Marketplace.](create-host-pools-azure-marketplace.md)
- Para configurar ações de ficheiros totalmente geridas na nuvem, consulte [configurar a partilha de Ficheiros Azure](/azure/storage/files/storage-files-active-directory-enable/).
- Para configurar os recipientes de perfil FSLogix, consulte [criar um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md).
- Para atribuir os utilizadores a uma piscina de anfitriões, consulte [os grupos de aplicações Manage para Windows Virtual Desktop](manage-app-groups.md).
- Para aceder aos recursos de ambiente de trabalho virtual do Windows a partir de um navegador web, consulte [Connect to Windows Virtual Desktop](connect-web.md).
