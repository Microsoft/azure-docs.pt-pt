---
title: Armazenamento FSLogix porta-perfis Windows Virtual Desktop - Azure
description: Opções para armazenar o seu perfil FSLogix do Windows Virtual Desktop no Armazenamento Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
ms.openlocfilehash: 165e87b47deca2b7f175ec07b5c615ac25db0dd2
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251755"
---
# <a name="storage-options-for-fslogix-profile-containers-in-windows-virtual-desktop"></a>Opções de armazenamento para recipientes de perfil FSLogix no Windows Virtual Desktop

O Azure oferece várias soluções de armazenamento que pode utilizar para armazenar o seu recipiente de perfil FSLogix. Este artigo compara soluções de armazenamento que o Azure oferece para os recipientes de perfil do utilizador Do Windows Virtual Desktop FSLogix.

O Windows Virtual Desktop oferece os recipientes de perfil FSLogix como a solução de perfil recomendada para o utilizador. O FSLogix foi concebido para percorrer perfis em ambientes de computação remota, como o Windows Virtual Desktop. No início do sessão, este recipiente está dinamicamente ligado ao ambiente de computação utilizando um Disco Rígido Virtual (VHD) apoiado de forma nativa e um Disco Rígido Virtual Hiper-V (VHDX). O perfil do utilizador está imediatamente disponível e aparece no sistema exatamente como um perfil de utilizador nativo.

As tabelas seguintes comparam as soluções de armazenamento Que o Armazenamento Azure oferece para os perfis de utilizador do windows Virtual Desktop FSLogix.

## <a name="azure-platform-details"></a>Detalhes da plataforma Azure

|Funcionalidades|Ficheiros do Azure|Azure NetApp Files|Espaços de Armazenamento Direto|
|--------|-----------|------------------|---------------------|
|Caso de utilização|Fins gerais|Ultra desempenho ou migração do NetApp no local|Plataforma cruzada|
|Serviço de plataforma|Sim, solução azure-nativa|Sim, solução azure-nativa|Não, auto-gerido|
|Disponibilidade regional|Todas as regiões|[Regiões selecionadas](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Todas as regiões|
|Redundância|Localmente redundante/zona redundante/geo-redundante|Localmente redundante|Localmente redundante/zona redundante/geo-redundante|
|Níveis e desempenho|Standard<br>Premium<br>Até máximo 100k IOPS por ação com 5 GBps por ação a cerca de 3 ms de latência|Standard<br>Premium<br>Ultra<br>Até 320k (16K) IOPS com 4,5 GBps por volume a cerca de 1 ms de latência|HDD padrão: até 500 iOPS por disco<br>SSD padrão: até 4k iOPS por disco<br>SSD Premium: até 20k IOPS por disco<br>Recomendamos discos Premium para Espaços de Armazenamento Direto|
|Capacidade|100 TiB por ação|100 TiB por volume, até 12,5 PiB por subscrição|Máximo 32 TiB por disco|
|Infraestrutura necessária|Tamanho mínimo da parte 1 GiB|Piscina de capacidade mínima 4 TiB, min volume tamanho 100 GiB|Dois VMs em Azure IaaS (+ Cloud Witness) ou pelo menos três VMs sem e custos para discos|
|Protocolos|SMB 2.1/3. e REST|NFSv3, NFSv4.1 (pré-visualização), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

## <a name="azure-management-details"></a>Detalhes da gestão do Azure

|Funcionalidades|Ficheiros do Azure|Azure NetApp Files|Espaços de Armazenamento Direto|
|--------|-----------|------------------|---------------------|
|Access|Nuvem, no local e híbrido (sincronização de ficheiros Azure)|Nuvem, no local (via ExpressRoute)|Nuvem, no local|
|Cópia de segurança|Integração de instantâneos de backup Azure|Fotos de ficheiros Azure NetApp|Integração de instantâneos de backup Azure|
|Segurança e conformidade|[Todos os certificados suportados pela Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO concluído|[Todos os certificados suportados pela Azure](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Integração do Azure Active Directory|[Serviços de Domínio de Diretório Ativo Nativo e Azure](https://docs.microsoft.com/azure/storage/files/storage-files-active-directory-overview)|[Serviços de Domínio de Diretório Ativo Azure e Diretório Ativo Nativo](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Apenas suporte a Serviços de Domínio de Diretório Ativo Nativo ou Azure|

Depois de ter escolhido o seu método de armazenamento, consulte os preços do [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/) para obter informações sobre os nossos planos de preços.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os recipientes de perfil FSLogix, os discos de perfil do utilizador e outras tecnologias de perfil do utilizador, consulte a tabela em recipientes de [perfil FSLogix e ficheiros Azure](fslogix-containers-azure-files.md).

Se estiver pronto para criar os seus próprios recipientes de perfil FSLogix, inicie-se com um destes tutoriais:

- [Começar com recipientes de perfil FSLogix em Ficheiros Azure no Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-FSLogix-profile-containers-on-Azure-Files/ba-p/746477)
- [Crie um recipiente de perfil FSLogix para uma piscina de anfitriões usando ficheiros Azure NetApp](create-fslogix-profile-container.md)
- As instruções em Implementar um servidor de ficheiros de dois nós Espaços de [Armazenamento Direto para armazenamento UPD em Azure](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) também se aplicam quando utiliza um recipiente de perfil FSLogix em vez de um disco de perfil do utilizador

Também pode começar desde o início e configurar a sua própria solução de ambiente de trabalho virtual Windows na [Create a tenant in Windows Virtual Desktop](tenant-setup-azure-active-directory.md).
