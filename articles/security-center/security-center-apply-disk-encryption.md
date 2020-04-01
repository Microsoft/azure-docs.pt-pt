---
title: Aplicar encriptação de disco no Azure Security Center Microsoft Docs
description: Este documento mostra-lhe como implementar a recomendação do Azure Security Center **Aplicar encriptação**de disco .
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473269"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar encriptação de disco no Centro de Segurança Azure

O Azure Security Center recomenda que utilize encriptação de disco Azure em discos não encriptados tanto no Windows como no Linux VM. O Disk Encryption permite-lhe encriptar os discos da VM IaaS do Windows e do Linux.  A encriptação é recomendada para o SO e os volumes de dados na sua VM.

A Encriptação do Disco utiliza a funcionalidade [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux. Estas funcionalidades fornecem os sistemas operativos e encriptação de dados para ajudar a proteger e salvaguardar os seus dados e cumprir os seus compromissos de segurança organizacional e conformidade. A encriptação do disco está integrada com o [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do Key Vault, garantindo ao mesmo tempo que todos os dados dos discos VM estão encriptados em repouso no seu [Armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/).

Para a lista de versões suportadas do Windows e do Linux, consulte [VMs suportados e sistemas operativos](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) na documentação de encriptação do disco Azure.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na página **Recomendações,** a encriptação do Disco selecionada **deve ser aplicada em máquinas virtuais**.
2. A partir dos **recursos não saudáveis,** selecione um VM para o qual a encriptação do disco é recomendada.
3. Siga as instruções para aplicar a encriptação a estes VMs.

![Aplicação da encriptação do disco](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Para encriptar as Máquinas Virtuais Azure que foram identificadas pelo Security Center como necessitando de encriptação, recomendamos os seguintes passos:

* Instalar e configurar o Azure PowerShell. Isto permite-lhe executar os comandos PowerShell necessários para configurar os pré-requisitos necessários para encriptar as Máquinas Virtuais Azure.
* Obtenha e execute o script de encriptação azure disk pré-requisitos Azure PowerShell.
* Criptografe as suas máquinas virtuais.

[Criptografe um VM Windows IaaS com O PowerShell Azure](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - Percorre-o através destes passos e assume que está a utilizar uma máquina cliente Windows a partir da qual pode configurar encriptação de disco.

Existem muitas abordagens que podem ser usadas para máquinas virtuais Azure. Se já estiver bem versado no Azure PowerShell ou no Azure CLI, então pode preferir utilizar abordagens alternativas. Para conhecer estas outras abordagens, consulte a encriptação do [disco Azure.](../security/fundamentals/encryption-overview.md)

## <a name="see-also"></a>Consulte também
Este documento mostrou-lhe como implementar a recomendação do Centro de Segurança "Aplicar encriptação de disco". Para saber mais sobre encriptação de disco, consulte:

* [Encriptação e gestão](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) de chaves com Cofre chave Azure (vídeo, 36 min 39 seg)--Aprenda a usar a gestão de encriptação do disco para VMs IaaS e Cofre chave Azure para ajudar a proteger e proteger os seus dados.
* [Encriptação](../security/fundamentals/encryption-overview.md) de disco azure (documento)-- Saiba como ativar a encriptação do disco para VMs Windows e Linux.