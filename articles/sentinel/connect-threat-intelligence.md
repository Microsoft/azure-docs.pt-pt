---
title: Ligue dados de inteligência de ameaça ao Azure Sentinel Microsoft Docs
description: Saiba como ligar dados de inteligência de ameaça ao Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: 223f3e45f25e3aed3ed6fa15e5b9ea04b17f6c59
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023913"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Ligar dados de fornecedores de inteligência de ameaças

> [!IMPORTANT]
> Os conectores de dados da Threat Intelligence em Azure Sentinel estão atualmente em pré-visualização pública.
> Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel permite-lhe importar os indicadores de ameaça que a sua organização está a usar, o que pode aumentar a capacidade dos seus analistas de segurança de detetar e priorizar ameaças conhecidas. Várias funcionalidades do Azure Sentinel ficam então disponíveis ou são melhoradas:

- **O Analytics** inclui um conjunto de modelos de regras programados que pode permitir gerar alertas e incidentes com base em jogos de eventos de registo a partir dos seus indicadores de ameaça.

- **Os livros de trabalho** fornecem informações resumidas sobre os indicadores de ameaça importados para o Azure Sentinel e quaisquer alertas gerados a partir de regras de análise que correspondam aos seus indicadores de ameaça.

- **As** consultas de caça permitem que os investigadores de segurança utilizem indicadores de ameaça no contexto de cenários comuns de caça.

- **Os cadernos** podem usar indicadores de ameaça quando investigam anomalias e caçam comportamentos maliciosos.

Pode transmitir indicadores de ameaça ao Azure Sentinel utilizando um dos produtos integrados da plataforma de inteligência de ameaças (TIP) listados na secção seguinte, conectando-se aos servidores TAXII, ou utilizando a integração direta com os [tiIndicators de Segurança do Microsoft Graph API](/graph/api/resources/tiindicator).

## <a name="integrated-threat-intelligence-platform-products"></a>Produtos integrados da plataforma de inteligência de ameaças

- [Plataforma de Inteligência de Ameaça de Fonte Aberta MISP](https://www.misp-project.org/)
    
    Para obter um script de amostra que forneça aos clientes casos MISP para migrar indicadores de ameaça para a API de Segurança do Gráfico da Microsoft, consulte o [MISP para o Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    Para descarregar o Integrador e Extensões ThreatStream e as instruções para ligar a inteligência ThreatStream à API de Segurança do Gráfico da Microsoft, consulte a página [de downloads do ThreatStream.](https://ui.threatstream.com/downloads)

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Para obter instruções guiadas, consulte [enviar IOCs para a API de segurança do gráfico da Microsoft utilizando MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Plataforma ThreatConnect](https://threatconnect.com/solution/)

    Para obter informações, consulte [As Integrações ThreatConnect](https://threatconnect.com/integrations/) e procure a API de Segurança do Gráfico da Microsoft na página.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Ligue o Azure Sentinel à sua plataforma de inteligência de ameaças

### <a name="prerequisites"></a>Pré-requisitos  

- Papel de Ad Azure de administrador global ou administrador de segurança para conceder permissões ao seu produto TIP ou aplicação personalizada que utiliza integração direta com a API de Segurança de Gráficos da Microsoft.

- Leia e escreva permissões para o espaço de trabalho Azure Sentinel para armazenar os seus indicadores de ameaça.

### <a name="instructions"></a>Instruções

1. [Registe um requerimento](/graph/auth-v2-service#1-register-your-app) no Azure Ative Directory para obter uma identificação de aplicação, segredo de aplicação e ID do inquilino do Azure Ative Directory. Precisa destes valores para quando configurar o seu produto TIP ou app integrado que utiliza a integração direta com a Microsoft Graph Security tiIndicators API.

2. [Configure permissões API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) para a aplicação registada: Adicione a permissão de aplicação de gráficos da Microsoft **ThreatIndicators.ReadWrite.OwnedBy** à sua aplicação registada.

3. Peça ao administrador do Azure Ative Directory para conceder o consentimento administrativo ao pedido registado para a sua organização. A partir do portal Azure: Registos de App **Azure Ative Directory**  >  **App registrations**  >  **\<_app name_>**  >  **Ver Permissões API**  >  **Conceder consentimento administrativo para \<_tenant name_>**.

4. Configure o seu produto TIP ou app que utiliza a integração direta com a Microsoft Graph Security tiIndicators API para enviar indicadores para O Azure Sentinel especificando o seguinte:
    
    a. Os valores para a identificação do pedido registado, segredo e identificação do inquilino.
    
    b. Para o produto-alvo, especifique a Azure Sentinel.
    
    c. Para a ação, especifique o alerta.

5. No portal Azure, navegue nos conectores de dados **do Azure Sentinel**  >  **Data connectors** e, em seguida, selecione o conector de Plataformas de Inteligência de **Ameaça (Pré-visualização).**

6. Selecione **abrir a página do conector** e, em seguida, **ligar**.

7. Para ver os indicadores de ameaça importados em Azure Sentinel, navegue até **Azure Sentinel - Logs**  >  **SecurityInsights**, e, em seguida, **expanda o ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Ligue o Azure Sentinel aos servidores TAXII

### <a name="prerequisites"></a>Pré-requisitos

- Leia e escreva permissões para o espaço de trabalho Azure Sentinel para armazenar os seus indicadores de ameaça.

- TAXII 2.0 servidor URI e ID de coleção.

### <a name="instructions"></a>Instruções

1. No portal Azure, navegue nos conectores de dados **Azure Sentinel**  >  **Data connectors** e, em seguida, selecione o conector Threat Intelligence **- TAXII (Preview).**

2. Selecione **Abrir a página do conector**.

3. Especifique as informações necessárias e opcionais nas caixas de texto.

4. **Selecione Adicionar** para ativar a ligação ao servidor TAXII 2.0.

5. Se tiver servidores TAXII 2.0 adicionais: Repita os passos 3 e 4.

6. Para ver os indicadores de ameaça importados em Azure Sentinel, navegue até **Azure Sentinel - Logs**  >  **SecurityInsights**, e, em seguida, **expanda o ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o seu fornecedor de inteligência de ameaça a Azure Sentinel. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos.

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)