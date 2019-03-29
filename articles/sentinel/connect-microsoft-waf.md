---
title: Recolha de dados do Microsoft web application firewall sentinela na pré-visualização no Azure | Documentos da Microsoft
description: Saiba como recolher dados do Microsoft web application firewall no Azure sentinela.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 2238060acb60b1be0d06b81f62fb45a7f1c7a9b6
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580602"
---
# <a name="collect-data-from-microsoft-web-application-firewall"></a>Recolher dados de firewall de aplicações do Microsoft web

> [!IMPORTANT]
> Sentinel do Azure está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pode transmitir em fluxo registos de firewall de aplicações do Azure do Gateway de aplicação Microsoft web (WAF). Este WAF protege seus aplicativos contra vulnerabilidades web comuns, como injeção de SQL e scripts entre sites e permite-lhe personalizar regras para reduzir os falsos positivos. Siga estas instruções para transmitir os registos de firewall de aplicação Web da Microsoft para o Azure sentinela.


## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de gateway de aplicação existente

## <a name="connect-to-microsoft-web-application-firewall"></a>Ligar à firewall de aplicações do Microsoft web

Se já tiver o firewall de aplicações do Microsoft web, certifique-se de que tem um recurso de gateway existente.
Assim que a firewall de aplicações do Microsoft web é implementada e obtenção de dados, os dados de alertas podem facilmente ser transmitidos em sentinela do Azure.
    
5. No portal do Azure sentinela, selecione **conectores de dados**.
5. Na página de conectores de dados, selecione o **WAF** mosaico.
1. Aceda a [recurso de Gateway de aplicação](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) e escolha o WAF.
    1. Selecione **das definições de diagnóstico**.
    1. Selecione **+ Adicionar definição de diagnóstico** na tabela.
    1. Na **das definições de diagnóstico** página, escreva um **nome** e selecione **enviar para o Log Analytics**.
    1. Sob **área de trabalho do Log Analytics** selecione a área de trabalho de sentinela do Azure.
    1. Selecione os tipos de registo que pretende analisar. Recomendamos que: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
6. Para utilizar o esquema relevante no Log Analytics para os alertas de firewall de aplicação do Microsoft web, procure **AzureDiagnostics**.

## <a name="next-steps"></a>Passos Seguintes
Neste documento, aprendeu a ligar a firewall de aplicações do Microsoft web ao Azure sentinela. Para saber mais sobre sentinela do Azure, veja os artigos seguintes:
- Saiba como [Obtenha visibilidade sobre os seus dados e a potenciais ameaças](quickstart-get-visibility.md).
- Começar a utilizar [deteção de ameaças com Azure sentinela](tutorial-detect-threats.md).
