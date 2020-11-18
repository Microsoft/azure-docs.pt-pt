---
title: Ligue os dados brutos do Microsoft 365 Defender ao Azure Sentinel. Microsoft Docs
description: Saiba como ingerir dados brutos de eventos da Microsoft 365 Defender para o Azure Sentinel.
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
ms.openlocfilehash: 756c245fe06ae81545a125dd98f30fb27fdff2dd
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655584"
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

O novo conector [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) permite-lhe transmitir registos **de caça avançados** - um tipo de dados de eventos brutos - do Microsoft 365 Defender para o Azure Sentinel. 

Com a integração do [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) no guarda-chuva de segurança Microsoft 365 Defender, pode agora recolher o seu Microsoft Defender para eventos [de caça avançados](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview) do Endpoint utilizando o conector Microsoft 365 Defender e transmiti-los diretamente para novas tabelas construídas de propósito no seu espaço de trabalho Azure Sentinel. Estas tabelas são construídas sobre o mesmo esquema que é usado no portal Microsoft 365 Defender, dando-lhe acesso completo ao conjunto completo de registos de caça avançados, e permitindo-lhe fazer o seguinte:

- Copie facilmente as consultas de caça avançadas do Microsoft Defender EM Azure Sentinel.

- Utilize os registos de eventos brutos para fornecer informações adicionais para os seus alertas, caça e investigação, e correlacionar eventos com dados de fontes de dados adicionais em Azure Sentinel.

- Guarde os registos com uma retenção aumentada, para além do Microsoft Defender para o Endpoint ou para a retenção padrão do Microsoft 365 Defender de 30 dias. Pode fazê-lo configurando a retenção do seu espaço de trabalho ou configurando a retenção por mesa no Log Analytics.

> [!IMPORTANT]
>
> O conector Microsoft 365 Defender encontra-se atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um contrato de nível de serviço, e não é recomendado para cargas de trabalho de produção. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma licença válida para o Microsoft Defender para o Endpoint, conforme descrito no [Microsoft Defender para a implementação de Endpoint](/windows/security/threat-protection/microsoft-defender-atp/licensing). 

- O seu utilizador deve ser atribuído à função de Administrador Global no arrendatário (no Diretório Ativo Azure).

## <a name="connect-to-microsoft-365-defender"></a>Ligue-se ao Microsoft 365 Defender

Se o Microsoft Defender for Endpoint estiver implantado e ingerir os seus dados, os registos do evento podem ser facilmente transmitidos para o Azure Sentinel.

1. Em Azure Sentinel, selecione **os conectores de dados**, selecione **Microsoft 365 Defender (Preview)** da galeria e selecione **a página de conector Open**.

1. Os seguintes tipos de eventos podem ser recolhidos a partir das respetivas tabelas de caça avançadas correspondentes. Marque as caixas de verificação dos tipos de eventos que pretende recolher:

    | Tipo de eventos | Nome da tabela |
    |-|-|
    | Informações da máquina (incluindo informações sobre os SO) | DispositivoInfo |
    | Propriedades de rede de máquinas | DeviceNetworkInfo |
    | Criação de processos e eventos relacionados | DispositivoProcessEvents |
    | Ligação de rede e eventos relacionados | DeviceNetworkEvents |
    | Criação de ficheiros, modificação e outros eventos do sistema de ficheiros | DeviceFileEvents |
    | Criação e modificação de inscrições no registo | DeviceRegistryEvents |
    | Inscrições e outros eventos de autenticação | DeviceLogonEvents |
    | Eventos de carregamento dLL | DispositivoImageLoadEvents |
    | Tipos de eventos adicionais | DispositivoSEventos |
    |

1. Clique **em Aplicar Alterações**. 

1. Para consultar as tabelas de caça avançadas no Log Analytics, introduza o nome da tabela a partir da lista acima na janela de consulta.

## <a name="verify-data-ingestion"></a>Verificar a ingestão de dados

O gráfico de dados na página do conector indica que está a ingerir dados. Você vai notar que mostra uma única linha que agrega o volume de eventos em todas as tabelas ativadas. Uma vez ativado o conector, pode utilizar a seguinte consulta KQL para gerar um gráfico semelhante de volume de eventos para uma única tabela (altere a tabela *DeviceEvents* para a tabela necessária à sua escolha):

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

No separador **Etapas Seguintes,** encontrará algumas consultas de amostra que foram incluídas. Pode executá-los no local, ou modificá-los e salvá-los.

## <a name="next-steps"></a>Próximos passos
Neste documento, aprendeu a obter dados de eventos brutos do Microsoft Defender para Endpoint em Azure Sentinel, utilizando o conector Microsoft 365 Defender. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade nos seus dados e potenciais ameaças.](quickstart-get-visibility.md)
- Começa [a detetar ameaças com o Azure Sentinel.](./tutorial-detect-threats-built-in.md)