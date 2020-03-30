---
title: Ligue dados da inteligência de ameaça ao Azure Sentinel. Microsoft Docs
description: Saiba como ligar dados de inteligência de ameaça ao Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 5c79642d287224cd15531701d7cc87ebfd72eb69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588047"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Conectar dados de fornecedores de inteligência de ameaças

> [!IMPORTANT]
> Os conectores de dados da Threat Intelligence em Azure Sentinel estão atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

O Azure Sentinel permite-lhe importar os indicadores de ameaça que a sua organização está a usar, o que pode aumentar a capacidade dos seus analistas de segurança de detetarem e priorizarem ameaças conhecidas. Várias funcionalidades do Azure Sentinel ficam disponíveis ou são melhoradas:

- **O Analytics** inclui um conjunto de modelos de regras programados que pode permitir gerar alertas e incidentes com base em partidas de eventos de registo a partir dos seus indicadores de ameaça.

- **Os livros** de trabalho fornecem informações resumidas sobre os indicadores de ameaça importados para o Azure Sentinel e quaisquer alertas gerados a partir de regras de análise que correspondam aos seus indicadores de ameaça.

- **As** consultas de caça permitem que os investigadores de segurança utilizem indicadores de ameaça no contexto de cenários comuns de caça.

- **Os cadernos** podem usar indicadores de ameaça quando investiga anomalias e caça por comportamentos maliciosos.

Pode transmitir indicadores de ameaça ao Azure Sentinel utilizando um dos produtos integrados da plataforma de inteligência de ameaça (TIP) listados na secção seguinte, ligando-se aos servidores TAXII, ou utilizando a integração direta com o [Microsoft Graph Security tiIndicators API](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Produtos integrados da plataforma de inteligência de ameaças

- [Plataforma de inteligência de ameaça de código aberto MISP](https://www.misp-project.org/)
    
    Para obter um script de amostra que forneça aos clientes instâncias MISP para migrar indicadores de ameaça para a API de Segurança do Gráfico da Microsoft, consulte o [MISP para o Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Redes Palo Alto MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Para obter instruções guiadas, consulte [o Envio de IOCs para a API de Segurança do Gráfico da Microsoft utilizando o MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Plataforma ThreatConnect](https://threatconnect.com/solution/)

    Para obter informações, consulte [as Integrações ThreatConnect](https://threatconnect.com/integrations/) e procure a Microsoft Graph Security API na página.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Ligue o Azure Sentinel à sua plataforma de inteligência de ameaça

## <a name="prerequisites"></a>Pré-requisitos  

- Função Azure AD de administrador global ou administrador de segurança para conceder permissões ao seu produto TIP ou aplicação personalizada que utiliza integração direta com a API de tiIndicators de Segurança do Gráfico da Microsoft.

- Leia e escreva permissões no espaço de trabalho do Azure Sentinel para armazenar os seus indicadores de ameaça.

## <a name="instructions"></a>Instruções

1. [Registe uma aplicação](/graph/auth-v2-service#1-register-your-app) no Azure Ative Directory para obter um ID de aplicação, segredo de aplicação e ID de inquilino do Diretório Ativo Azure. Precisa destes valores para quando configurar o seu produto ou app TIP integrado que utiliza a integração direta com a API de tiIndicators de Segurança do Gráfico do Microsoft.

2. [Configure as permissões DaPI](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) para a aplicação registada: Adicione a permissão de permissão de aplicação do Microsoft Graph **Application ThreatIndicators.ReadWrite.OwnedBy** à sua aplicação registada.

3. Peça ao seu administrador de inquilino do Azure Ative Directory que conceda consentimento ao administrador do pedido registado para a sua organização. Do portal Azure: **Azure Ative Directory** > **App registra** > **\<_app nome_>** > **Ver API Permissions** > **Grant consentimento administrador para \< _nome_>** de inquilino .

4. Configure o seu produto TIP ou app que utilize a integração direta com a Microsoft Graph Security tiIndicators API para enviar indicadores para o Azure Sentinel, especificando o seguinte:
    
    a. Os valores para a identificação do pedido registado, segredo e identificação do inquilino.
    
    b. Para o produto-alvo, especifique o Azure Sentinel.
    
    c. Para a ação, especifique o alerta.

5. No portal Azure, navegue para os**conectores** De Dados **Azure Sentinel** > e, em seguida, selecione o conector **De Inteligência ameaçadora (Pré-visualização).**

6. Selecione página de **conector Aberto**e, em seguida, **ligue**.

7. Para ver os indicadores de ameaça importados para o Azure Sentinel, navegue para **o Azure Sentinel - Logs** > **SecurityInsights,** e depois expanda o **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Ligue o Azure Sentinel aos servidores TAXII

## <a name="prerequisites"></a>Pré-requisitos  

- Leia e escreva permissões no espaço de trabalho do Azure Sentinel para armazenar os seus indicadores de ameaça.

- TAXII 2.0 servidor URI e Id de Recolha.

## <a name="instructions"></a>Instruções

1. No portal Azure, navegue para os**conectores** De Dados **Azure Sentinel** > e, em seguida, selecione o conector De inteligência de **ameaça - TAXII (Pré-visualização).**

2. Selecione página do **conector Aberto**.

3. Especifique as informações necessárias e opcionais nas caixas de texto.

4. Selecione **Adicionar** para ativar a ligação ao servidor TAXII 2.0.

5. Se tiver servidores TAXII 2.0 adicionais: Repita os passos 3 e 4.

6. Para ver os indicadores de ameaça importados para o Azure Sentinel, navegue para **o Azure Sentinel - Logs** > **SecurityInsights,** e depois expanda o **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o seu fornecedor de inteligência de ameaça ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos.

- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats.md)
