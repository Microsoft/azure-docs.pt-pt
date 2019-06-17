---
title: Aplicar encriptação de disco no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar a recomendação do Centro de segurança do Azure **aplicar encriptação de disco**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d285461aef1e56dfd08a162e51ada0340d6eeae4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62095426"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Aplicar encriptação de disco no Centro de segurança do Azure
Centro de segurança do Azure recomenda que aplicar encriptação de disco se tiver discos da VM do Linux ou do Windows que não estão encriptados com o Azure Disk Encryption. Encriptação de disco permite-lhe encriptar os discos da VM de IaaS de Linux e Windows.  A encriptação é recomendada para o SO e os volumes de dados na sua VM.

Encriptação de disco utiliza o padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) recurso do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux. Esses recursos fornecem criptografia de dados e SO para o ajudar a proteger e salvaguardar os seus dados e respeitar os compromissos de segurança e conformidade. Encriptação de disco está integrada [do Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, enquanto garantir que todos os dados nos discos VM são encriptados em inatividade na sua [Armazenamento do azure](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> O Azure Disk Encryption é suportado no Windows server sistemas operativos seguintes - Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Encriptação de disco é suportada nos seguintes sistemas operativos Linux server - Ubuntu, CentOS, SUSE e SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Na **recomendações** painel, selecione **aplicar encriptação de disco**.
2. Na **aplicar encriptação de disco** painel, verá uma lista de VMs para as quais se recomenda a encriptação de disco.
3. Siga as instruções para aplicar encriptação a estas VMs.

![][1]

Para encriptar máquinas virtuais do Azure que tenham sido identificadas pelo centro de segurança como tendo necessidade de encriptação, recomendamos os seguintes passos:

* Instalar e configurar o Azure PowerShell. Isto permite-lhe executar os comandos do PowerShell necessários para configurar os pré-requisitos necessários para encriptar máquinas virtuais do Azure.
* Obter e executar o script do Azure disco encriptação pré-requisitos do Azure PowerShell.
* Encripte as suas máquinas virtuais.

[Encriptar uma VM de IaaS do Windows com o Azure PowerShell](../security/quick-encrypt-vm-powershell.md) explica esses passos. Este tópico pressupõe que está a utilizar uma máquina de cliente do Windows do qual configura a encriptação de disco.

Existem várias abordagens que podem ser utilizadas para máquinas de virtuais do Azure. Se já estiver bem familiarizado com o Azure PowerShell ou o CLI do Azure, pode preferir utilizar abordagens alternativas. Para saber mais sobre essas outras abordagens, veja [encriptação de disco do Azure](../security/azure-security-disk-encryption.md).

## <a name="see-also"></a>Consulte também
Este documento de mostrar como implementar a recomendação do Centro de segurança "Aplicar encriptação de disco". Para saber mais sobre a encriptação de disco, consulte o seguinte:

* [Encriptação e gestão de chaves com o Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vídeo, 36 min 39 seg) – Saiba como utilizar a gestão de encriptação de discos para VMs de IaaS e o Azure Key Vault para ajudar a proteger e salvaguardar os seus dados.
* [Encriptação de disco do Azure](../security/azure-security-disk-encryption-overview.md) (documento) – Saiba como ativar a encriptação de disco para VMs de Linux e do Windows.

Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md) – Saiba como configurar as políticas de segurança.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e de conformidade.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
