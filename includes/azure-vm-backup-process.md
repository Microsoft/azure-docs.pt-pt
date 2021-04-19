---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716754"
---
1. Para os VMs Azure que são selecionados para cópia de segurança, o Azure Backup inicia um trabalho de backup de acordo com o horário de backup especificado.
1. Durante a primeira cópia de segurança, é instalada uma extensão de backup no VM se o VM estiver em funcionamento.
    - Para os VMs do Windows, a [extensão VMSnapshot](../articles/virtual-machines/extensions/vmsnapshot-windows.md) está instalada.
    - Para os VMs Linux, a [extensão VMSnapshotLinux](../articles/virtual-machines/extensions/vmsnapshot-linux.md) está instalada.
1. Para os VMs do Windows que estão em execução, as coordenadas de backup com o Windows Volume Shadow Copy Service (VSS) para tirar uma imagem consistente da aplicação do VM.
    - Por predefinição, o Backup recebe cópias de segurança VSS completas.
    - Se o Backup não conseguir tirar uma imagem consistente da aplicação, então é necessário um instantâneo consistente de ficheiro do armazenamento subjacente (porque não ocorre nenhuma aplicação escrita enquanto o VM é parado).
1. Para os VMs Linux, o Backup recebe uma cópia de segurança consistente com ficheiros. Para instantâneos consistentes com aplicações, é necessário personalizar manualmente scripts pré/post.
1. Depois de o Backup tirar a fotografia, transfere os dados para o cofre.
    - A cópia de segurança é otimizada ao criar a cópia de segurança de cada disco da VM em paralelo.
    - Para cada disco cuja cópia de segurança está a ser criada, o Azure Backup lê os blocos no disco e identifica e transfere apenas os blocos de dados que mudaram (os delta) desde a primeira cópia de segurança.
    - Os dados do instantâneo podem não ser copiados para o cofre imediatamente. Pode levar algumas horas em horas de pico. O tempo total de backup de uma VM será inferior a 24 horas para políticas de cópias de segurança diárias.
1. As alterações efetuadas a um Windows VM após a ativação do Backup Azure são:
    - Microsoft Visual C++ 2013 Redistributable (x64) - 12.0.40660 está instalado no VM
    - Tipo de arranque do serviço Volume Shadow Copy (VSS) alterado para automático a partir de manual
    - Serviço IaaSVmProvider Windows é adicionado

1. Quando a transferência de dados está completa, o instantâneo é removido e é criado um ponto de recuperação.

![Arquitetura de backup de máquina virtual Azure](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
