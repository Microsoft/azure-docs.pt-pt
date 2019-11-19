---
title: Visão geral das marcas de FQDN do firewall do Azure
description: Uma marca FQDN representa um grupo de FQDNs (nomes de domínio totalmente qualificados) associados aos serviços da Microsoft conhecidos.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 6396f8292a4c54f7fce237439f37c3e8156d59e8
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169042"
---
# <a name="fqdn-tags-overview"></a>Visão geral das marcas de FQDN

Uma marca FQDN representa um grupo de FQDNs (nomes de domínio totalmente qualificados) associados aos serviços da Microsoft conhecidos. Você pode usar uma marca de FQDN em regras de aplicativo para permitir o tráfego de rede de saída necessário por meio do firewall.

Por exemplo, para permitir manualmente Windows Update tráfego de rede por meio do firewall, você precisa criar várias regras de aplicativo por meio da documentação da Microsoft. Usando marcas de FQDN, você pode criar uma regra de aplicativo, incluir a marca de **atualizações do Windows** e agora o tráfego de rede para os pontos de extremidade do Microsoft Windows Update pode fluir pelo firewall.

Você não pode criar suas próprias marcas de FQDN, nem pode especificar quais FQDNs estão incluídos em uma marca. A Microsoft gerencia os FQDNs abrangedos pela marca FQDN e atualiza a marca conforme os FQDNs são alterados. 

<!--- screenshot of application rule with a FQDN tag.-->

A tabela a seguir mostra as marcas de FQDN atuais que você pode usar. A Microsoft mantém essas marcas e você pode esperar que marcas adicionais sejam adicionadas periodicamente.

## <a name="current-fqdn-tags"></a>Marcas de FQDN atuais

|Marca FQDN  |Descrição  |
|---------|---------|
|Windows Update     |Permita acesso de saída a Microsoft Update conforme descrito em [como configurar um firewall para atualizações de software](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnóstico do Windows|Permitir acesso de saída a todos os [pontos de extremidade do diagnóstico do Windows](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Serviço de Proteção Ativa Microsoft (MAPS)|Permitir acesso de saída a [mapas](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Ambiente do Serviço de Aplicativo (ASE)|Permite o acesso de saída ao tráfego da plataforma ASE. Essa marca não abrange o armazenamento específico do cliente e os pontos de extremidade do SQL criados pelo ASE. Eles devem ser habilitados por meio de [pontos de extremidade de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicionados manualmente.<br><br>Para obter mais informações sobre como integrar o Firewall do Azure com o ASE, consulte [bloquear um ambiente do serviço de aplicativo](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Permite o acesso de saída aos serviços de backup do Azure.|
|Azure HDInsight|Permite o acesso de saída para o tráfego da plataforma HDInsight. Essa marca não abrange o armazenamento específico do cliente ou o tráfego do SQL do HDInsight. Habilite-os usando [pontos de extremidade de serviço](../virtual-network/tutorial-restrict-network-access-to-resources.md) ou adicione-os manualmente.|

> [!NOTE]
> Ao selecionar a marca FQDN em uma regra de aplicativo, o campo protocolo: porta deve ser definido como **https**.

## <a name="next-steps"></a>Passos seguintes

Para saber como implantar um firewall do Azure, consulte [tutorial: implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md).
