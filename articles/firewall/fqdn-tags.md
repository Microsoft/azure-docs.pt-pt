---
title: FQDN tags visão geral para Azure Firewall
description: Uma etiqueta FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados a serviços bem conhecidos da Microsoft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: ed34bf755023ab866e95c51aca6d1bfb1dd7bf71
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099458"
---
# <a name="fqdn-tags-overview"></a>Visão geral das etiquetas FQDN

Uma etiqueta FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados a serviços bem conhecidos da Microsoft. Pode utilizar uma etiqueta FQDN nas regras de aplicação para permitir o tráfego de rede de saída necessário através da sua firewall.

Por exemplo, para permitir manualmente o tráfego da rede do Windows Update através da sua firewall, é necessário criar várias regras de aplicação de acordo com a documentação da Microsoft. Utilizando tags FQDN, pode criar uma regra de aplicação, incluir a etiqueta **de Atualizações** do Windows e agora o tráfego de rede para os pontos finais do Microsoft Windows Update pode fluir através da sua firewall.

Não é possível criar as suas próprias etiquetas FQDN, nem especifica quais as FQDNs incluídas numa etiqueta. A Microsoft gere as FQDNs englobadas pela tag FQDN, e atualiza a etiqueta à medida que as FQDNs mudam. 

<!--- screenshot of application rule with a FQDN tag.-->

A tabela a seguir mostra as atuais etiquetas FQDN que pode utilizar. A Microsoft mantém estas tags e pode esperar que sejam adicionadas etiquetas adicionais periodicamente.

## <a name="current-fqdn-tags"></a>Etiquetas FQDN atuais

|Tag FQDN  |Description  |
|---------|---------|
|Windows Update     |Permitir o acesso de saída ao Microsoft Update, conforme descrito em [Como Configurar uma Firewall para atualizações de software](https://docs.microsoft.com/mem/configmgr/sum/get-started/install-a-software-update-point).|
|Diagnóstico do Windows|Permitir o acesso de saída a todos os [pontos finais do Windows Diagnostics](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Ative Protection Service (MAPS)|Permitir o acesso de saída ao [MAPS.](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/)|
|Ambiente de Serviço de Aplicações (ASE)|Permite o acesso de saída ao tráfego da plataforma ASE. Esta etiqueta não cobre o armazenamento específico do cliente e os pontos finais SQL criados pela ASE. Estes devem ser ativados através [de pontos finais de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicionados manualmente.<br><br>Para obter mais informações sobre a integração do Azure Firewall com a ASE, consulte [bloquear um Ambiente de Serviço de Aplicações.](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase)|
|Azure Backup|Permite o acesso de saída aos serviços de Backup Azure.|
|Azure HDInsight|Permite o acesso de saída para o tráfego da plataforma HDInsight. Esta etiqueta não cobre o tráfego de Armazenamento ou SQL específico do cliente da HDInsight. Ative estes utilizando [pontos finais de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicione-os manualmente.|
|WindowsVirtualDesktop (WVD)|Permite o tráfego da plataforma virtual de ambiente de trabalho do Windows. Esta etiqueta não cobre pontos finais específicos de armazenamento e autocarro de serviço criados pela WVD. Além disso, são necessárias regras de rede DNS e KMS. Para obter mais informações sobre a integração do Azure Firewall com o WVD, consulte [use Azure Firewall para proteger as implementações do Ambiente de Trabalho Virtual da Janela](protect-windows-virtual-desktop.md).|
|Serviço de Kubernetes do Azure (AKS)|Permite o acesso de saída à AKS. Para obter mais informações, consulte [o Azure Firewall para proteger as implementações do Serviço Azure Kubernetes (AKS).](protect-azure-kubernetes-service.md)|

> [!NOTE]
> Ao selecionar a Etiqueta FQDN numa regra de aplicação, o campo protocol:porta deve ser definido em **https** .

## <a name="next-steps"></a>Passos seguintes

Para aprender a implantar uma Firewall Azure, consulte [Tutorial: Implemente e configuure Firewall utilizando o portal Azure](tutorial-firewall-deploy-portal.md).
