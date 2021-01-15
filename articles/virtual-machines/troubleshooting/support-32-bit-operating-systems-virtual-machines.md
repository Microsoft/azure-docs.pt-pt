---
title: Suporte para sistemas operativos de 32 bits em máquinas virtuais Azure Microsoft Docs
description: Informação sobre sistemas operativos suportados em máquinas virtuais Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210193"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Suporte para sistemas operativos de 32 bits nas máquinas virtuais do Azure

O Microsoft Azure permite agora que os utilizadores levem os seus sistemas Operativos Windows de 32 bits para o Azure. Apenas VHDs especializados são suportados e imagens generalizadas não funcionarão em Azure. Como alguns destes sistemas operativos já chegaram ao acordo de suporte de fim de vida, a Microsoft pode não oferecer suporte adicional para os mesmos. O suporte também não é oferecido para sistemas operativos baseados em Linux, ou Berkeley Software Distribution (BSD) que funcionam numa máquina virtual Microsoft Azure (VM).

> [!NOTE]
> A plataforma Azure tem uma limitação de espaço de endereço de memória imposta aos VMs que executam sistemas operativos de 32 bits onde apenas 1GB de memória podem ser disponibilizados ao VM (*especialmente em SKUs clientes como Win7 ou Win10*), e o resto da memória para o VM mostrará como reservado dentro do VM convidado. Trata-se de uma questão conhecida e, atualmente, não dispomos de um ETA para uma correção. Recomendamos que se motenda para versões 64bit OS.
> 

## <a name="more-information"></a>Mais informações

Para obter mais informações sobre sistemas operativos suportados em máquinas virtuais Azure, aceda aos seguintes artigos da Base de Conhecimento do Microsoft:

* [Microsoft server software support for Microsoft Azure virtual machines](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) (Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure)
* [Suporte para o Linux e tecnologia open source no Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Referências

* [Saiba mais sobre atualizações de segurança estendidas gratuitas para Windows Server 2008/R2 em Azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Saiba mais sobre o suporte para imagens especializadas do Windows Server 2008 SP2 32-bit em Azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Saiba mais sobre o suporte à migração de imagens do Windows Server 2008 para o Azure usando a Recuperação do Site Azure](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Saiba mais sobre os sistemas operativos suportados pela Extensão Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Saiba mais sobre a execução do Windows Server 2003 no Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Passos seguintes

Se precisar de mais ajuda em qualquer ponto deste artigo, contacte os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

Em alternativa, apresente um incidente de suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter **Apoio**.
