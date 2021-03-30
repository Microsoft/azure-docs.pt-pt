---
title: Ligue os dados da Firewall de Aplicação Web (WAF) ao Azure Sentinel
description: Saiba como ligar os dados da Azure WAF ao Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655906"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Conecte dados a partir da Firewall de Aplicação Web Azure (WAF)

As aplicações web são cada vez mais direcionadas por ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. O Azure Web Application Firewall (WAF) fornece proteção centralizada das suas aplicações web contra explorações e ameaças comuns, tais como injeção de código e scripts de sites cruzados. O Azure WAF pode ser implantado no serviço [Azure Application Gateway,](../web-application-firewall/ag/ag-overview.md) no serviço [Azure Front Door](../web-application-firewall/afds/afds-overview.md) e através de uma política waf [da Rede de Entrega de Conteúdos Azure (CDN)](../web-application-firewall/cdn/cdn-overview.md) (esta última em pré-visualização pública).
Pode ligar os registos Azure WAF ao Azure Sentinel, permitindo-lhe visualizar dados de registo em livros de trabalho, usá-lo para criar alertas personalizados e incorporá-lo para melhorar a sua investigação.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter lido e escrito permissões no espaço de trabalho do Azure Sentinel.

## <a name="connect-to-azure-waf"></a>Ligue-se ao Azure WAF

### <a name="instructions-tab"></a>Separador de instruções

1. A partir do menu de navegação Azure Sentinel, selecione **Conectores de dados**.

1. Selecione a firewall da **aplicação web Azure (WAF)** da galeria de conectores de dados e, em seguida, selecione **A Página do Conector Aberto** no painel de pré-visualização.

1. Selecione o link para o tipo de recurso WAF cujos registos pretende ligar – **Open Application Gateway >**, Open Front Door resource **>**, ou Open Content Delivery **Network (CDN) WAF policy >** – e uma vez no ecrã da lista de recursos, escolha um recurso WAF da lista.

    1. A partir do menu de navegação do recurso WAF, selecione **definições de Diagnóstico**.

    1. Selecione + Adicione a **definição** de diagnóstico na parte inferior da lista.

    1. No ecrã de **definições de Diagnóstico,** digite um nome no campo **de nomes de definições de Diagnóstico.**

    1. Clique na caixa de verificação **Enviar para Registar Analytics.** Dois novos campos serão exibidos por baixo. Escolha o espaço de trabalho **de subscrição** e **log analytics** relevante (onde reside O Sentinela Azure).

    1. Clique nas caixas de verificação dos tipos de regras cujos registos pretende ingerir. As nossas recomendações para cada tipo de recurso:

        | Recurso | Registos para selecionar para ingestão |
        |----------|------------------------------|
        | Gateway de Aplicação | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | Política da CDN WAF      | WebApplicationFirewallLogs |
        |

    1. Selecione **Guardar**.

### <a name="next-steps-tab"></a>Separador de passos seguintes

- Consulte os livros de trabalho recomendados, amostras de consulta e modelos de regras de análise que estão agregados com o conector de dados **de firewall da aplicação web Azure,** para obter informações sobre os dados de registo Azure WAF.

- Para consultar os dados do Azure WAF em **Logs,** digite **AzureDiagnostics** na janela de consulta.

> [!NOTE]
>
> Com este conector de dados em particular, os indicadores de estado de conectividade (uma faixa de cor na galeria de conectores de dados e ícones de conexão ao lado dos nomes do tipo de dados) só mostrarão como *conectados* (verdes) se os dados ingerirem em algum ponto nas últimas duas semanas. Uma vez passadas duas semanas sem ingestão de dados, o conector mostrará como estando desligado. No momento em que mais dados chegam, o estado *ligado* voltará.

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu a ligar os registos Azure WAF ao Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)