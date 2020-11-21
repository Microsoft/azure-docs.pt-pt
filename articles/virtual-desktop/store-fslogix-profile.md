---
title: Recipiente de perfil FSLogix de armazenamento Windows Virtual Desktop - Azure
description: Opções para armazenar o seu perfil Virtual Desktop FSLogix no Azure Storage.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 189ff3bbfdb3b8533defcedb77e15fef433598b5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95023093"
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
|Redundância|Localmente redundante/zona redundante/geo-redundante|Localmente redundante|Localmente redundante/zona redundante/geo-redundante|
|Níveis e desempenho|Standard<br>Premium<br>Até máximo 100 k IOPS por ação com 5 GBps por ação a cerca de 3 ms de latência|Standard<br>Premium<br>Ultra<br>IOPS até 320k (16K) com 4,5 GBps por volume a cerca de 1 ms de latência|HDD padrão: até 500 limites de IOPS por disco<br>SSD padrão: até 4k limites de IOPS por disco<br>Premium SSD: até 20k limites de IOPS por disco<br>Recomendamos discos Premium para espaços de armazenamento direto|
|Capacidade|100 TiB por ação|100 TiB por volume, até 12,5 PiB por subscrição|Máximo 32 TiB por disco|
|Infraestrutura necessária|Tamanho mínimo de partilha 1 GiB|Piscina mínima de capacidade 4 TiB, tamanho de volume min 100 GiB|Dois VMs em Azure IaaS (+ Cloud Witness) ou pelo menos três VMs sem e custos para discos|
|Protocolos|SMB 2.1/3. e DESCANSAR|NFSv3, NFSv4.1 (pré-visualização), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Detalhes da gestão do Azure

|Funcionalidades|Ficheiros do Azure|Azure NetApp Files|Direto de Espaços de Armazenamento|
|--------|-----------|------------------|---------------------|
|Access|Nuvem, no local e híbrido (sincronização de ficheiros Azure)|Nuvem, no local (via ExpressRoute)|Nuvem, no local|
|Cópia de segurança|Integração de instantâneo de backup Azure|Fotos de ficheiros Azure NetApp|Integração de instantâneo de backup Azure|
|Segurança e conformidade|[Todos os certificados suportados pela Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO concluído|[Todos os certificados suportados pela Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integração do Azure Active Directory|[Diretório Ativo Nativo e Serviços de Domínio do Diretório Ativo Azure](../storage/files/storage-files-active-directory-overview.md)|[Serviços de domínio do diretório ativo Azure e diretório ativo nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Diretório Ativo Nativo ou Suporte de Serviços de Domínio de Diretório Ativo Azure apenas|

Assim que tiver escolhido o seu método de armazenamento, consulte [os preços do Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) para obter informações sobre os nossos planos de preços.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre os recipientes de perfil FSLogix, discos de perfil do utilizador e outras tecnologias de perfil de utilizador, consulte a tabela em [recipientes de perfil FSLogix e ficheiros Azure](fslogix-containers-azure-files.md).

Se estiver pronto para criar os seus próprios recipientes de perfil FSLogix, iniciou-se com um destes tutoriais:

- [Começando com recipientes de perfil FSLogix em ficheiros Azure no Windows Virtual Desktop](create-file-share.md)
- [Crie um recipiente de perfil FSLogix para uma piscina hospedeira utilizando ficheiros Azure NetApp](create-fslogix-profile-container.md)
- As instruções em [Implementar um bloco de armazenamento de dois sentidos Servidor de ficheiro de escala direta para armazenamento UPD em Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) também se aplica quando utiliza um recipiente de perfil FSLogix em vez de um disco de perfil de utilizador

Também pode começar desde o início e configurar a sua própria solução de Ambiente de Trabalho Virtual windows na [Create a tenant in Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).