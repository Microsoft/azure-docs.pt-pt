---
title: Ligue os dados do Microsoft 365 Defender ao Azure Sentinel| Microsoft Docs
description: Saiba como ingerir incidentes, alertas e dados de eventos crus da Microsoft 365 Defender para o Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709347"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Ligue os dados do Microsoft 365 Defender ao Azure Sentinel

> [!IMPORTANT]
>
> **O Microsoft 365 Defender** era anteriormente conhecido como **Microsoft Threat Protection** ou **MTP**.
>
> **O Microsoft Defender for Endpoint** era anteriormente conhecido como **Microsoft Defender Advanced Threat Protection** ou **MDATP**.
>
> Pode ver os nomes antigos ainda em uso por um período de tempo.

## <a name="background"></a>Fundo

O conector [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) do Azure Sentinel com integração de incidentes permite transmitir todos os incidentes e alertas M365D para o Azure Sentinel, e mantém os incidentes sincronizados entre ambos os portais. Os incidentes M365D incluem todos os seus alertas, entidades e outras informações relevantes, e são enriquecidos por e grupos de alertas dos serviços componentes da M365D **Microsoft Defender for Endpoint,** **Microsoft Defender for Identity,** **Microsoft Defender para o Office 365**, e **Microsoft Cloud App Security.**

O conector também permite transmitir eventos **de caça avançados** do Microsoft Defender para Endpoint para OZure Sentinel, permitindo-lhe copiar consultas avançadas de caça MDE em Azure Sentinel, enriquecer alertas Sentinel com dados de eventos brutos do MDE para fornecer informações adicionais, e armazenar os registos com uma maior retenção no Log Analytics.

Para obter mais informações sobre integração de incidentes e recolha avançada de eventos de caça, consulte [a integração do Microsoft 365 Defender com o Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> O conector Microsoft 365 Defender encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

## <a name="prerequisites"></a>Pré-requisitos

- Deve ter uma licença válida para o Microsoft 365 Defender, conforme descrito nos [pré-requisitos do Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites). 

- Deve ser **administrador global** ou **administrador de segurança** no Azure Ative Directory.

## <a name="connect-to-microsoft-365-defender"></a>Ligue-se ao Microsoft 365 Defender

1. Em Azure Sentinel, selecione **os conectores de dados**, selecione **Microsoft 365 Defender (Preview)** da galeria e selecione **a página de conector Open**.

1. Em Configuração na secção **de alertas de & de incidentes de** **Ligação,** clique no botão **Desembaraco & alertas.**

1. Para evitar duplicações de incidentes, recomenda-se marcar a caixa de verificação rotulada **Desligue todas as regras de criação de incidentes** da Microsoft para estes produtos.

    > [!NOTE]
    > Quando ativa o conector Microsoft 365 Defender, todos os conectores dos componentes M365D (os mencionados no início deste artigo) estão automaticamente ligados em segundo plano. Para desligar um dos conectores dos componentes, deve primeiro desligar o conector Microsoft 365 Defender.

1. Para consultar os dados do incidente do M365 Defender, utilize a seguinte declaração na janela de consulta:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Se pretender recolher eventos de caça avançados do Microsoft Defender para Endpoint, os seguintes tipos de eventos podem ser recolhidos a partir das suas correspondentes tabelas de caça avançadas.

    1. Marque as caixas de verificação das tabelas com os tipos de eventos que pretende recolher:

       | Nome da tabela | Tipo de eventos |
       |-|-|
       | DispositivoInfo | Informações da máquina (incluindo informações sobre os SO) |
       | DeviceNetworkInfo | Propriedades de rede de máquinas |
       | DispositivoProcessEvents | Criação de processos e eventos relacionados |
       | DeviceNetworkEvents | Ligação de rede e eventos relacionados |
       | DeviceFileEvents | Criação de ficheiros, modificação e outros eventos do sistema de ficheiros |
       | DeviceRegistryEvents | Criação e modificação de inscrições no registo |
       | DeviceLogonEvents | Inscrições e outros eventos de autenticação |
       | DispositivoImageLoadEvents | Eventos de carregamento dLL |
       | DispositivoSEventos | Tipos de eventos adicionais |
       | DeviceFileCertificateInfo | Informações de certificados de ficheiros assinados |
       |

    1. Clique **em Aplicar Alterações**.

    1. Para consultar as tabelas de caça avançadas no Log Analytics, introduza o nome da tabela a partir da lista acima na janela de consulta.

## <a name="verify-data-ingestion"></a>Verificar a ingestão de dados

O gráfico de dados na página do conector indica que está a ingerir dados. Você vai notar que mostra uma linha para cada um para incidentes, alertas e eventos, e a linha de eventos é uma agregação do volume de eventos em todas as tabelas ativadas. Uma vez ativado o conector, pode utilizar as seguintes consultas KQL para gerar gráficos mais específicos.

Utilize a seguinte consulta KQL para um gráfico dos incidentes do M365 Defender:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Utilize a seguinte consulta KQL para gerar um gráfico de volume de evento para uma única tabela (altere a tabela *DeviceEvents* para a tabela necessária à sua escolha):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

No separador **etapas seguintes,** encontrará alguns livros úteis, consultas de amostras e modelos de regras de análise que foram incluídos. Pode executá-los no local ou modificá-los e salvá-los.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a integrar os incidentes do Microsoft 365 Defender e os dados avançados do Microsoft Defender for Endpoint, para o Azure Sentinel, utilizando o conector Microsoft 365 Defender. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)
