---
title: Ligue o Ponto de Força DLP ao Sentinela Azul. Microsoft Docs
description: Aprenda a ligar o Forcepoint DLP ao Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 3bdb9d2b23ce05929ba5612e0c6a03fe1aab05de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588251"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Ligue o seu Ponto de Força DLP ao Sentinela Azure

> [!IMPORTANT]
> O conector de dados de Prevenção de Perdas de Dados do Ponto de Força (DLP) no Azure Sentinel encontra-se atualmente em pré-visualização pública. Esta funcionalidade é fornecida sem um acordo de nível de serviço, e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .



O conector Forcepoint DLP permite exportar automaticamente dados de incidentes dLP para o Azure Sentinel. Pode usá-lo para obter visibilidade nas atividades dos utilizadores e incidentes de perda de dados. Também permite correlações com dados de cargas de trabalho do Azure e outros feeds, e melhora a capacidade de monitorização com os Livros de Trabalho dentro do Azure Sentinel.

> [!NOTE]
> Os dados serão armazenados na localização geográfica do espaço de trabalho em que está a executar o Azure Sentinel.

## <a name="configure-and-connect-forcepoint-dlp"></a>Configure e ligue o Ponto de Força DLP

Configure o Forcepoint DLP para encaminhar dados de incidentes em formato JSON para o seu espaço de trabalho Azure através da API REST, conforme explicado no Guia de [Integração DoLP](https://frcpnt.com/dlp-sentinel)de Forcepoint .


## <a name="find-your-data"></a>Encontre os seus dados

Após a configuração do conector Forcepoint DLP, os dados aparecem no Log Analytics em CustomLogs **ForcepointDLPEvents_CL**.


Para utilizar o esquema relevante no Log Analytics para O DLP do Forcepoint, procure **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Validar a conectividade

Pode demorar mais de 20 minutos até que os seus registos comecem a aparecer no Log Analytics.

## <a name="next-steps"></a>Passos seguintes

Neste documento, aprendeu a ligar o Forcepoint DLP ao Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:

- Aprenda a [obter visibilidade nos seus dados e ameaças potenciais.](quickstart-get-visibility.md)
- Começar [a detetar ameaças com o Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Utilize livros](tutorial-monitor-your-data.md) de trabalho para monitorizar os seus dados.