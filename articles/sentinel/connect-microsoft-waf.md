---
title: Ligue os dados do Firewall de Aplicação Web ao Azure Sentinel
description: Saiba como ligar os dados de firewall da aplicação web da Microsoft ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77588183"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Conecte dados a partir da firewall da aplicação web da Microsoft



Pode transmitir registos a partir da firewall da aplicação web microsoft do Azure Application Gateway (WAF). Este WAF protege as suas aplicações contra vulnerabilidades comuns da web, como injeção de SQL e scripts de cross-site, e permite personalizar regras para reduzir falsos positivos. Siga estas instruções para transmitir os registos de firewall da aplicação microsoft Web para o Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de gateway de aplicação existente

## <a name="connect-to-microsoft-web-application-firewall"></a>Conecte-se à firewall da aplicação web da Microsoft

Se já tiver firewall de aplicação web da Microsoft, certifique-se de que tem um recurso de gateway existente.
Uma vez que a firewall da sua aplicação web microsoft é implementada e obter dados, os dados de alerta podem ser facilmente transmitidos para Azure Sentinel.
    
1. No portal Azure Sentinel, selecione **Conectores de Dados**.
1. Na página de conectores de dados, selecione o azulejo **WAF.**
1. Vá ao [recurso Application Gateway](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways)e escolha o seu   WAF.
    1. Selecione **definições de diagnóstico**.
    1. **Selecione + Adicione a definição** de diagnóstico debaixo da tabela.
    1. Na página de **definições de Diagnóstico,** digite um **nome** e selecione **Enviar para registar análises**.
    1. No **log analytics workspace** selecione o espaço de trabalho Azure Sentinel.
    1. Selecione os tipos de registo que pretende analisar. Recomendamos: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Para utilizar o esquema relevante no Log Analytics para os alertas de firewall da aplicação web da Microsoft, procure por **AzureDiagnostics**.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a ligar a firewall da aplicação web da Microsoft ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
