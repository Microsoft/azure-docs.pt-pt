---
title: Contentores de perfil de FSLogix e ficheiros do Azure no Windows área de Trabalho Virtual - Azure
description: Este artigo descreve os contentores de perfil FSLogix dentro de arquivos da área de Trabalho Virtual do Windows e o Azure.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: v-chjenk
ms.openlocfilehash: c3f31e8d260ea5e462e8782fadd9f61f34d03add
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66307276"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>Contentores de perfil FSLogix e ficheiros do Azure

O serviço de pré-visualização de ambiente de Trabalho Virtual do Windows recomenda FSLogix contentores de perfil, como uma solução de perfil do usuário. FSLogix foi projetado para se mover perfis em ambientes de computação remotas, como a área de Trabalho Virtual do Windows. Armazena um perfil de usuário completa num único contentor. No início de sessão, o contentor dinamicamente está ligado para o ambiente de computação com nativos, no convidado rígido Virtual do Hyper-V e de disco rígido Virtual (VHD) disco (VHDX) serviços da Microsoft. O perfil de utilizador está imediatamente disponível e é apresentado no sistema exatamente como um perfil de utilizador nativa.

Neste artigo, descreveremos os contentores de perfil de FSLogix utilizados com ficheiros do Azure. As informações estão no contexto de Trabalho Virtual de Windows, o que foi [anunciados no 3/21](https://www.microsoft.com/microsoft-365/blog/2019/03/21/windows-virtual-desktop-public-preview/).

## <a name="user-profiles"></a>Perfis de utilizador

Um perfil de utilizador contém elementos de dados sobre um indivíduo, incluindo informações de configuração como definições de ambiente de trabalho, ligações de rede persistente e as definições da aplicação. Por predefinição, o Windows cria um perfil de utilizador local que está totalmente integrado com o sistema operativo.

Um perfil de utilizador remoto fornece uma partição entre os dados de utilizador e o sistema operativo. Ele permite que o sistema operacional a ser substituído ou alterados sem afetar os dados do utilizador. No anfitrião de sessões de ambiente de trabalho remoto (RDSH) e infraestruturas de ambiente de Trabalho Virtual (VDI), o sistema operativo podem ser substituído pelos seguintes motivos:

- Uma atualização do sistema operativo
- Uma substituição de uma Máquina Virtual existente (VM)
- Um utilizador que está a ser parte de um ambiente agrupado (não persistentes) de RDSH ou VDI

Produtos da Microsoft operam com várias tecnologias para perfis de utilizador remoto, incluindo essas tecnologias:
- Perfis de utilizador itinerantes (RUP)
- Discos do perfil de utilizador (UDP)
- Roaming (ESR) de estado empresarial

UDP e RUP são as tecnologias mais amplamente utilizadas para perfis de utilizador em ambientes de anfitrião de sessões de ambiente de trabalho remoto (RDSH) e o disco rígido Virtual (VHD).

### <a name="challenges-with-previous-user-profile-technologies"></a>Desafios com tecnologias de perfil do usuário anterior

Soluções da Microsoft existentes e o legado para perfis de utilizador foi criado com vários desafios. Nenhuma solução anterior processadas todas as necessidades de perfil de utilizador que vêm com um ambiente RDSH ou VDI. Por exemplo, UPD não consegue processar os ficheiros OST grandes e RUP não persiste definições modernos.

#### <a name="functionality"></a>Funcionalidade

A tabela seguinte mostra os benefícios e limitações das tecnologias de perfil do usuário anterior.

| Tecnologia | Definições de modernas | Definições do Win32 | Definições do SO | Dados de utilizador | Suportado no SKU do servidor | Armazenamento de back-end no Azure | Armazenamento de back-end no local | Suporte da versão | Início de sessão subsequentes no tempo |Notas|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Discos do perfil de utilizador (UDP)** | Sim | Sim | Sim | Sim | Sim | Não | Sim | Windows 7 e posteriores | Sim | |
| **Roaming perfil por utilizador (RUP), modo de manutenção** | Não | Sim | Sim | Sim | Sim| Não | Sim | Windows 7 e posteriores | Não | |
| **Enterprise State Roaming (ESR)** | Sim | Não | Sim | Não | Consulte as notas | Sim | Não | Windows 10 | Não | Funções no servidor SKU, mas nenhuma interface de utilizador de suporte |
| **Virtualização de experiência do usuário (UE-V)** | Sim | Sim | Sim | Não | Sim | Não | Sim | Windows 7 e posteriores | Não |  |
| **Ficheiros de cloud do OneDrive** | Não | Não | Não | Sim | Consulte as notas | Consulte as notas  | Consulte as notas | Win 10 RS3 | Não | Não testados num servidor SKU. Armazenamento de back-end no Azure depende do cliente de sincronização. Armazenamento de back-end no local precisa de um cliente de sincronização. |

#### <a name="performance"></a>Desempenho

Requer UPD [espaços de armazenamento direto (S2D)](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment) aos requisitos de desempenho de endereço. UPD utiliza o protocolo bloco de mensagem de servidor (SMB). Copia o perfil para a VM em que o usuário está a ser conectado. UPD com o S2D foi a solução, a equipe RDS recomendada para a área de Trabalho Virtual do Windows durante a pré-visualização do serviço.  

#### <a name="cost"></a>Custo

Enquanto os clusters de S2D alcançar o desempenho necessário, o custo é dispendioso para os clientes empresariais, mas especialmente dispendiosa para clientes de empresas de pequeno e médio (SMB). Para esta solução, as empresas pagam discos de armazenamento premium, juntamente com o custo das VMs que utilizam os discos para uma partilha.

#### <a name="administrative-overhead"></a>Overhead administrativo

Os clusters de S2D requerem um sistema operativo que é corrigido, atualizado e mantido num estado seguro. Esses processos e a complexidade da configuração de recuperação após desastre de S2D tornam S2D viável apenas para empresas com uma equipe de TI dedicada.

## <a name="fslogix-profile-containers"></a>Contentores de perfil FSLogix

19 de Novembro de 2018 [a Microsoft adquiriu FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). Os endereços de FSLogix muitos perfil contentor desafios, chaves entre eles são:

- **Desempenho:** O [contentores de perfil FSLogix](https://fslogix.com/products/profile-containers) são de alto desempenho e resolva os problemas de desempenho que, historicamente, tem bloqueado do exchange modo de cache.
- **OneDrive:** Sem FSLogix contentores de perfil, o OneDrive para empresas não é suportado em ambientes de RDSH ou VDI não persistentes. [OneDrive para empresas e FSLogix melhores práticas](https://fslogix.com/products/technical-faqs/284-onedrive-for-business-and-fslogix-best-practices) descreve a forma como eles interagem. Para obter mais informações, consulte [utilizar o cliente de sincronização em áreas de trabalho virtuais](https://docs.microsoft.com/deployoffice/rds-onedrive-business-vdi).
- **Pastas adicionais:** FSLogix fornece a capacidade de estender os perfis de utilizador a incluir pastas adicionais.

Desde a aquisição, a Microsoft começou a substituir soluções existentes de perfil de utilizador, como o UDP, com FSLogix contentores de perfil.

## <a name="azure-files-integration-with-azure-active-directory"></a>Integração de ficheiros do Azure com o Azure Active Directory

Funcionalidades de desempenho de contentores do perfil de FSLogix e tire partido da cloud. Em 24 de Setembro de 2018, ficheiros do Microsoft Azure anunciou a pré-visualização pública de [ficheiros do Azure que suporta a autenticação do Azure Active Directory](https://azure.microsoft.com/blog/azure-active-directory-integration-for-smb-access-now-in-public-preview/). Ao tratar o custo e a sobrecarga administrativa, os ficheiros do Azure com a autenticação do Azure Active Directory é uma solução de premium para perfis de utilizador no novo serviço de área de Trabalho Virtual do Windows.

## <a name="best-practices-for-windows-virtual-desktop"></a>Melhores práticas para a área de Trabalho Virtual do Windows

Área de Trabalho Virtual do Windows oferece controlo total sobre o tamanho, tipo e contagem de VMs que estão a ser utilizado pelos clientes. Para obter mais informações, consulte [o que é o Windows Virtual Desktop Preview?](https://docs.microsoft.com/azure/virtual-desktop/overview).

Ambiente segue as melhores práticas para garantir que a sua área de Trabalho Virtual do Windows:

- Conta de armazenamento de ficheiros do Azure tem de ser na mesma região que o anfitrião de sessões de VMs.
- Permissões de ficheiros do Azure devem corresponder ao permissões descritas [requisitos - perfil contentores](https://docs.fslogix.com/display/20170529/Requirements+-+Profile+Containers).
- Cada pool de anfitrião deve ser criado do mesmo tipo e tamanho da VM com base na imagem mestre do mesmo.
- Cada VM do agrupamento de anfitrião tem de estar no mesmo grupo de recursos para ajudar a gestão, dimensionamento e a atualização.
- Para um desempenho ideal, a solução de armazenamento e o contentor de perfil deve ser os mesmos dados de FSLogix localização do Datacenter.
- A conta de armazenamento que contém a imagem mestra tem de ser na mesma região e subscrição em que as VMs estão a ser aprovisionadas.

## <a name="next-steps"></a>Passos Seguintes

Utilize as seguintes instruções para configurar um ambiente de área de Trabalho Virtual do Windows.

- Para começar a criar sua solução de Virtualização de área de trabalho, consulte [criar um inquilino na área de Trabalho Virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory).
- Para criar um conjunto de anfitriões no seu inquilino de área de Trabalho Virtual do Windows, consulte [criar um conjunto de anfitriões com o Azure Marketplace](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace).
- Para configurar a totalmente gerido partilhas de ficheiros na cloud, consulte [configurar a partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-enable).
- Para configurar FSLogix contentores de perfil, veja [configurar uma partilha do perfil de utilizador para um conjunto de anfitrião](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-user-profile).
- Para atribuir utilizadores a um conjunto de anfitriões, consulte [gerir grupos de aplicações para a área de Trabalho Virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/manage-app-groups).
- Para aceder aos recursos de área de Trabalho Virtual do Windows a partir de um navegador da web, consulte [ligar à área de Trabalho Virtual do Windows](https://docs.microsoft.com/azure/virtual-desktop/connect-web).
