---
title: Visão geral das etiquetas FQDN para firewall azure
description: Uma etiqueta FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados a serviços bem conhecidos da Microsoft.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/24/2020
ms.author: victorh
ms.openlocfilehash: f4fcadb6d930f7863c733dad105432a64127b59b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232654"
---
# <a name="fqdn-tags-overview"></a>Visão geral das etiquetas FQDN

Uma etiqueta FQDN representa um grupo de nomes de domínio totalmente qualificados (FQDNs) associados a serviços bem conhecidos da Microsoft. Pode utilizar uma etiqueta FQDN nas regras de aplicação para permitir o tráfego de rede de saída necessário através da sua firewall.

Por exemplo, para permitir manualmente o tráfego da rede Windows Update através da sua firewall, é necessário criar várias regras de aplicação de acordo com a documentação da Microsoft. Utilizando etiquetas FQDN, pode criar uma regra de aplicação, incluir a etiqueta **Windows Updates** e agora o tráfego de rede para os pontos finais do Microsoft Windows Update pode fluir através da sua firewall.

Não pode criar as suas próprias tags FQDN, nem especifica quais FQDNs estão incluídos dentro de uma etiqueta. A Microsoft gere os FQDNs englobados pela tag FQDN e atualiza a etiqueta à medida que as FQDNs mudam. 

<!--- screenshot of application rule with a FQDN tag.-->

A tabela a seguir mostra as etiquetas FQDN atuais que pode utilizar. A Microsoft mantém estas tags e pode esperar que as etiquetas adicionais sejam adicionadas periodicamente.

## <a name="current-fqdn-tags"></a>Tags FQDN atuais

|Etiqueta FQDN  |Descrição  |
|---------|---------|
|Windows Update     |Permitir o acesso de saída ao Microsoft Update, tal como descrito em [Como Configurar uma Firewall para atualizações de software](https://technet.microsoft.com/library/bb693717.aspx).|
|Diagnóstico do Windows|Permitir o acesso de saída a todos os [pontos finais do Windows Diagnostics](https://docs.microsoft.com/windows/privacy/configure-windows-diagnostic-data-in-your-organization#endpoints).|
|Microsoft Ative Protection Service (MAPS)|Permitir o acesso de saída ao [MAPS](https://cloudblogs.microsoft.com/enterprisemobility/2016/05/31/important-changes-to-microsoft-active-protection-service-maps-endpoint/).|
|Ambiente de Serviço de Aplicativos (ASE)|Permite o acesso de saída ao tráfego da plataforma ASE. Esta etiqueta não cobre pontos finais de Armazenamento e SQL específicos do cliente criados pela ASE. Estes devem ser ativados através de [pontos finais](../virtual-network/tutorial-restrict-network-access-to-resources.md) de serviço ou adicionados manualmente.<br><br>Para obter mais informações sobre a integração do Firewall Azure com a ASE, consulte Bloquear um Ambiente de Serviço de [Aplicações](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).|
|Azure Backup|Permite o acesso de saída aos serviços de backup Azure.|
|Azure HDInsight|Permite o acesso de saída para o tráfego da plataforma HDInsight. Esta etiqueta não cobre o armazenamento específico do cliente ou o tráfego SQL da HDInsight. Ative-os utilizando [pontos finais](../virtual-network/tutorial-restrict-network-access-to-resources.md) de serviço ou adicione-os manualmente.|
|WindowsVirtualDesktop (WVD)|Permite o tráfego da plataforma de desktop virtual Windows de saída. Esta etiqueta não cobre pontos finais de armazenamento e ônibus de serviço específicos de implementação criados pela WVD. Além disso, são necessárias regras de rede DNS e KMS. Para obter mais informações sobre a integração da Firewall Azure com WVD, consulte [Use Azure Firewall para proteger as implementações de ambiente de trabalho virtual da Janela](protect-windows-virtual-desktop.md). 

> [!NOTE]
> Ao selecionar a Etiqueta FQDN numa regra de aplicação, o campo protocol:porta deve ser definido para **https**.

## <a name="next-steps"></a>Passos seguintes

Para aprender a implementar uma Firewall Azure, consulte [Tutorial: Implementar e configurar firewall Azure utilizando o portal Azure](tutorial-firewall-deploy-portal.md).
