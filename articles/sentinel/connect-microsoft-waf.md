---
title: Conecte dados de firewall de aplicação web ao Azure Sentinel
description: Saiba como ligar os dados de firewall da Microsoft ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: a5cef16694fa2cfae036152d22cfa4473956fc72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588183"
---
# <a name="connect-data-from-microsoft-web-application-firewall"></a>Conecte dados da firewall da aplicação web da Microsoft



Pode transmitir registos a partir da firewall de aplicação web do Portal de Aplicações Azure Gateway (WAF). Este WAF protege as suas aplicações de vulnerabilidades comuns da Web, como a injeção SQL e scripts transversais, e permite personalizar regras para reduzir falsos positivos. Siga estas instruções para transmitir os registos de firewall da aplicação Microsoft Web para o Azure Sentinel.


## <a name="prerequisites"></a>Pré-requisitos

- Um recurso de gateway de aplicação existente

## <a name="connect-to-microsoft-web-application-firewall"></a>Ligar à firewall da aplicação web da Microsoft

Se já possui a firewall da aplicação web da Microsoft, certifique-se de que tem um recurso de gateway existente.
Uma vez que a firewall da aplicação web da Microsoft é implementada e obter dados, os dados de alerta podem ser facilmente transmitidos para o Azure Sentinel.
    
1. No portal Azure Sentinel, selecione **conectores de dados**.
1. Na página dos conectores data, selecione o azulejo **WAF.**
1. Vá ao [recurso](https://ms.portal.azure.com/#blade/HubsExtension/BrowseAllResourcesBlade/resourceType/Microsoft.Network%2FapplicationGateways) Application Gateway e escolha o seu WAF.
    1. Selecione **definições de diagnóstico**.
    1. Selecione **+ Adicione a definição de diagnóstico** por baixo da tabela.
    1. Na página de **definições** de Diagnóstico, escreva um **Nome** e selecione Enviar para **Registar Análises**.
    1. Em **Log Analytics Workspace,** selecione o espaço de trabalho Azure Sentinel.
    1. Selecione os tipos de registo que pretende analisar. Recomendamos: ApplicationGatewayAccessLog e ApplicationGatewayFirewallLog.
1. Para utilizar o esquema relevante no Log Analytics para os alertas de firewall da aplicação web da Microsoft, procure o **AzureDiagnostics**.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar a firewall da aplicação web da Microsoft ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
