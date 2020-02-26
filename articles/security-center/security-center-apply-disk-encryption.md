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
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604522"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar encriptação de disco no Centro de Segurança Azure
O Azure Security Center recomenda que aplique encriptação de disco se tiver discos Windows ou Linux VM que não sejam encriptados utilizando encriptação do disco Azure. A Encriptação do Disco permite-lhe encriptar os discos VM Do Windows e do Linux IaaS.  A encriptação é recomendada para o SO e os volumes de dados na sua VM.

A Encriptação do Disco utiliza a funcionalidade [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) do Linux. Estas funcionalidades fornecem os sistemas operativos e encriptação de dados para ajudar a proteger e salvaguardar os seus dados e cumprir os seus compromissos de segurança organizacional e conformidade. A encriptação do disco está integrada com o [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerir as chaves e segredos de encriptação do disco na subscrição do Key Vault, garantindo ao mesmo tempo que todos os dados dos discos VM estão encriptados em repouso no seu [Armazenamento Azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> A Encriptação do Disco Azure é suportada nos seguintes sistemas operativos do servidor Windows - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. A encriptação do disco é suportada nos seguintes sistemas operativos do servidor Linux - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na lâmina **de recomendações,** selecione **Aplicar encriptação**do disco .
2. Na lâmina de encriptação do **disco Apply,** vê-se uma lista de VMs para os quais é recomendada a encriptação do disco.
3. Siga as instruções para aplicar a encriptação a estes VMs.

![][1]

Para encriptar as Máquinas Virtuais Azure que foram identificadas pelo Security Center como necessitando de encriptação, recomendamos os seguintes passos:

* Instalar e configurar o Azure PowerShell. Isto permite-lhe executar os comandos PowerShell necessários para configurar os pré-requisitos necessários para encriptar as Máquinas Virtuais Azure.
* Obtenha e execute o script de encriptação azure disk pré-requisitos Azure PowerShell.
* Criptografe as suas máquinas virtuais.

[Criptografe um VM Windows IaaS com O PowerShell Azure](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) percorre-o através destes passos. Este tópico pressupõe que está a utilizar uma máquina cliente do Windows a partir da qual configura a encriptação do disco.

Existem muitas abordagens que podem ser usadas para máquinas virtuais Azure. Se já estiver bem familiarizado com o Azure PowerShell ou o CLI do Azure, pode preferir utilizar abordagens alternativas. Para conhecer estas outras abordagens, consulte a encriptação do [disco Azure.](../security/fundamentals/encryption-overview.md)

## <a name="see-also"></a>Consulte também
Este documento mostrou-lhe como implementar a recomendação do Centro de Segurança "Aplicar encriptação de disco". Para saber mais sobre encriptação de disco, consulte o seguinte:

* [Encriptação e gestão](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) de chaves com Cofre chave Azure (vídeo, 36 min 39 seg) -- Aprenda a usar a gestão de encriptação do disco para VMs IaaS e Cofre chave Azure para ajudar a proteger e proteger os seus dados.
* [Encriptação](../security/fundamentals/encryption-overview.md) de disco azure (documento) -- Aprenda a ativar a encriptação do disco para VMs Windows e Linux.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* Definir políticas de segurança no Centro de [Segurança Azure.](tutorial-security-policy.md)
* [Monitorização](security-center-monitoring.md) de saúde de segurança no Centro de Segurança Azure.- Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center -- Saiba como as recomendações ajudam a proteger os seus recursos Azure.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) -- Encontre posts de blog sobre segurança e conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
