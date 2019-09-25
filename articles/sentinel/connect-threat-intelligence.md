---
title: Conectar dados de inteligência contra ameaças ao Azure Sentinel | Microsoft Docs
description: Saiba mais sobre como conectar dados de inteligência contra ameaças ao Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: c1c374977460de04d2339ba8c93019cf9152dbe3
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262706"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Conectar dados de provedores de inteligência contra ameaças

> [!IMPORTANT]
> O conector de dados das plataformas de inteligência contra ameaças no Azure Sentinel está atualmente em visualização pública.
> Esse recurso é fornecido sem um contrato de nível de serviço e não é recomendado para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Azure Sentinel permite que você importe os indicadores de ameaça que sua organização está usando, o que pode aprimorar a capacidade dos analistas de segurança de detectar e priorizar ameaças conhecidas. Vários recursos do Azure Sentinel ficam disponíveis ou são aprimorados:

- O **Analytics** inclui um conjunto de modelos de regra agendada que você pode habilitar para gerar alertas e incidentes que se baseiam em correspondências de eventos de log de seus indicadores de ameaça.

- As **pastas de trabalho** fornecem informações resumidas sobre os indicadores de ameaça que são importados para o Azure Sentinel e todos os alertas gerados por meio de regras de análise que correspondem aos seus indicadores de ameaça.

- As consultas de **busca** permitem que os investigadores de segurança usem indicadores de ameaça no contexto de cenários de busca comuns.

- Os **notebooks** podem usar indicadores de ameaça quando você investiga anomalias e busca por comportamentos mal-intencionados.

Você pode transmitir indicadores de ameaça para o Azure Sentinel usando um dos produtos de dica (plataforma de inteligência contra ameaças) integrados listados na próxima seção ou usando a integração direta com a [API do Microsoft Graph Security tiIndicators](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Produtos integrados da plataforma de inteligência contra ameaças

- [Plataforma de inteligência contra ameaças de código-fonte aberto MISP](https://www.misp-project.org/)
    
    Para obter um script de exemplo que fornece aos clientes instâncias de MISP para migrar indicadores de ameaça para a API de segurança Microsoft Graph, consulte o [script MISP to Microsoft Graph Security](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo Alto Networks MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Para obter instruções guiadas, consulte [enviando IOCs para a API de segurança de Microsoft Graph usando MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [Plataforma ThreatConnect](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Pré-requisitos  

- Função do Azure AD de administrador global ou administrador de segurança para conceder permissões ao seu produto TIP ou aplicativo personalizado que usa a integração direta com a API do Microsoft Graph Security tiIndicators.

- Permissões de leitura e gravação para o espaço de trabalho do Azure Sentinel para armazenar seus indicadores de ameaça.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Conectar o Azure Sentinel ao seu provedor de inteligência contra ameaças

1. [Registre um aplicativo](/graph/auth-v2-service#1-register-your-app) no Azure Active Directory para obter uma ID do aplicativo, segredo do aplicativo e Azure Active Directory ID do locatário. Você precisa desses valores para quando configura seu produto ou aplicativo da TIP integrado que usa a integração direta com a API do Microsoft Graph Security tiIndicators.

2. [Configurar permissões de API](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) para o aplicativo registrado: Adicione a permissão de aplicativo Microsoft Graph **ThreatIndicators. ReadWrite. OwnedBy** ao seu aplicativo registrado.

3. Peça ao administrador de locatários do Azure Active Directory para conceder consentimento de administrador para o aplicativo registrado para sua organização. No portal do Azure: **Azure Active Directory** > registros de aplicativo > **nomedoaplicativoexibirpermissões>deAPIconceder consentimento de administrador para\<**  >  >  ***nome do locatário*.> \<**

4. Configure seu produto ou aplicativo TIP que usa a integração direta com a API do Microsoft Graph Security tiIndicators para enviar indicadores para o Azure Sentinel, especificando o seguinte:
    
    a. Os valores para a ID, o segredo e a ID do locatário do aplicativo registrado.
    
    b. Para o produto de destino, especifique Azure Sentinel.
    
    c. Para a ação, especifique alerta.

5. Na portal do Azure, navegue até**conectores de dados** **do Azure Sentinel** > e selecione o conector de **plataformas de inteligência contra ameaças (versão prévia)** .

6. Selecione a **página abrir conector**e **Conecte-se**.

7. Para exibir os indicadores de ameaça que são importados para o Azure Sentinel, navegue até **Azure Sentinel-logs** > **SecurityInsights**e, em seguida, expanda **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Passos seguintes

Neste documento, você aprendeu a conectar seu provedor de inteligência contra ameaças ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os artigos a seguir.

- Saiba como [obter visibilidade de seus dados e ameaças potenciais](quickstart-get-visibility.md).
- Comece [a detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).