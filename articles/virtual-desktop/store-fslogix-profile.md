---
title: Recipiente de perfil FSLogix de armazenamento Windows Virtual Desktop - Azure
description: Opções para armazenar o seu perfil Virtual Desktop FSLogix no Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: 1ff8c645b1ad670f3824920d39aa0c6bf9783408
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445555"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opções de armazenamento para recipientes de perfil FSLogix no Windows Virtual Desktop

O Azure oferece múltiplas soluções de armazenamento que pode utilizar para armazenar o seu contentor de perfil FSLogix. Este artigo compara as soluções de armazenamento que o Azure oferece para os recipientes de perfil de utilizador do Windows Virtual Desktop FSLogix. Recomendamos o armazenamento de recipientes de perfil FSLogix em Ficheiros Azure para a maioria dos nossos clientes.

O Windows Virtual Desktop oferece recipientes de perfil FSLogix como a solução recomendada para o perfil do utilizador. O FSLogix foi concebido para percorrer perfis em ambientes informáticos remotos, como o Windows Virtual Desktop. No início de sposição, este recipiente é ligado dinamicamente ao ambiente de computação utilizando um Disco Rígido Virtual (VHD) suportado de forma nativa e um Disco Rígido Virtual Hiper-V (VHDX). O perfil do utilizador fica imediatamente disponível e aparece no sistema exatamente como um perfil de utilizador nativo.

As tabelas que se seguem comparam as soluções de armazenamento que o Azure Storage oferece para os perfis de utilizadores do perfil do pcra do Windows Virtual Desktop FSLogix.

## <a name="azure-platform-details"></a>Detalhes da plataforma Azure

|Funcionalidades|Ficheiros do Azure|Azure NetApp Files|Direto de Espaços de Armazenamento|
|--------|-----------|------------------|---------------------|
|Caso de utilização|Fins gerais|Ultra desempenho ou migração do NetApp no local|Plataforma transversal|
|Serviço de plataforma|Sim, solução nativa do Azure|Sim, solução nativa do Azure|Não, auto-gerido|
|Disponibilidade regional|Todas as regiões|[Selecione regiões](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Todas as regiões|
|Redundância|Localmente redundante/zona redundante/geo-redundante/geo-zona redundante|Localmente redundante|Localmente redundante/zona redundante/geo-redundante|
|Níveis e desempenho| Standard (Transação otimizada)<br>Premium<br>Até máximo 100K IOPS por ação com 10 GBps por ação a cerca de 3 ms de latência|Standard<br>Premium<br>Ultra<br>IOPS até 320k (16K) com 4,5 GBps por volume a cerca de 1 ms de latência|HDD padrão: até 500 limites de IOPS por disco<br>SSD padrão: até 4k limites de IOPS por disco<br>Premium SSD: até 20k limites de IOPS por disco<br>Recomendamos discos Premium para espaços de armazenamento direto|
|Capacidade|100 TiB por ação, Até 5 PiB por conta para fins gerais |100 TiB por volume, até 12,5 PiB por subscrição|Máximo 32 TiB por disco|
|Infraestrutura necessária|Tamanho mínimo de partilha 1 GiB|Piscina mínima de capacidade 4 TiB, tamanho de volume min 100 GiB|Dois VMs em Azure IaaS (+ Cloud Witness) ou pelo menos três VMs sem e custos para discos|
|Protocolos|SMB 3.0/2.1, NFSv4.1 (pré-visualização), REST|NFSv3, NFSv4.1 (pré-visualização), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Detalhes da gestão do Azure

|Funcionalidades|Ficheiros do Azure|Azure NetApp Files|Direto de Espaços de Armazenamento|
|--------|-----------|------------------|---------------------|
|Access|Nuvem, no local e híbrido (sincronização de ficheiros Azure)|Nuvem, no local (via ExpressRoute)|Nuvem, no local|
|Backup|Integração de instantâneo de backup Azure|Fotos de ficheiros Azure NetApp|Integração de instantâneo de backup Azure|
|Segurança e conformidade|[Todos os certificados suportados pela Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO concluído|[Todos os certificados suportados pela Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integração do Azure Active Directory|[Diretório Ativo Nativo e Serviços de Domínio do Diretório Ativo Azure](../storage/files/storage-files-active-directory-overview.md)|[Serviços de domínio do diretório ativo Azure e diretório ativo nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Diretório Ativo Nativo ou Suporte de Serviços de Domínio de Diretório Ativo Azure apenas|

Assim que tiver escolhido o seu método de armazenamento, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) para obter informações sobre os nossos planos de preços.

## <a name="azure-files-tiers"></a>Níveis de Ficheiros Azure

A Azure Files oferece dois níveis diferentes de armazenamento: premium e standard. Estes níveis permitem-lhe adaptar o desempenho e o custo das suas ações de ficheiros para satisfazer os requisitos do seu cenário.

- As ações de ficheiros premium são apoiadas por unidades de estado sólido (SSDs) e são implantadas no tipo de conta de armazenamento de armazenamento FileStorage. As ações de ficheiros premium proporcionam um desempenho consistente e baixa latência para cargas de trabalho intensivas de entrada e produção (IO). 

- As ações de ficheiros standard são apoiadas por discos rígidos (HDDs) e são implantadas no tipo de conta de armazenamento versão 2 (GPv2) para fins gerais. As ações de ficheiros standard proporcionam um desempenho fiável para cargas de trabalho io menos sensíveis à variabilidade do desempenho, tais como ações de ficheiros de uso geral e ambientes dev/teste. As ações de ficheiros padrão só estão disponíveis num modelo de faturação paga.

A tabela a seguir enumera as nossas recomendações para as quais o nível de desempenho utilizar com base na sua carga de trabalho. Estas recomendações irão ajudá-lo a selecionar o nível de desempenho que cumpre os seus objetivos de desempenho, orçamento e considerações regionais. Baseamos estas recomendações nos cenários de exemplo dos tipos de carga de [trabalho de desktop remoto.](/windows-server/remote/remote-desktop-services/remote-desktop-workloads) 

| Tipo de carga de trabalho | Nível de arquivo recomendado |
|--------|-----------|
| Luz (menos de 200 utilizadores) | Ações de ficheiros padrão |
| Luz (mais de 200 utilizadores) | Ações de ficheiros premium ou padrão com várias ações de ficheiros |
|Médio|Ações de ficheiros premium|
|Pesado|Ações de ficheiros premium|
|Energia|Ações de ficheiros premium|

Para obter mais informações sobre o desempenho dos Ficheiros Azure, consulte [os alvos da partilha de ficheiros e da escala de ficheiros](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets). Para obter mais informações sobre preços, consulte [os preços da Azure Files](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os recipientes de perfil FSLogix, discos de perfil do utilizador e outras tecnologias de perfil de utilizador, consulte a tabela em [recipientes de perfil FSLogix e ficheiros Azure](fslogix-containers-azure-files.md).

Se estiver pronto para criar os seus próprios recipientes de perfil FSLogix, iniciou-se com um destes tutoriais:

- [Começando com recipientes de perfil FSLogix em ficheiros Azure no Windows Virtual Desktop](create-file-share.md)
- [Crie um recipiente de perfil FSLogix para uma piscina hospedeira utilizando ficheiros Azure NetApp](create-fslogix-profile-container.md)
- As instruções em [Implementar um bloco de armazenamento de dois sentidos Servidor de ficheiro de escala direta para armazenamento UPD em Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) também se aplica quando utiliza um recipiente de perfil FSLogix em vez de um disco de perfil de utilizador

Também pode começar desde o início e configurar a sua própria solução de Ambiente de Trabalho Virtual windows na [Create a tenant in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
