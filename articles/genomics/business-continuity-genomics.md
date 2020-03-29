---
title: Visão geral da continuidade do negócio
titleSuffix: Microsoft Genomics
description: Esta visão geral descreve as capacidades que a Microsoft Genomics fornece para a continuidade do negócio e recuperação de desastres.
keywords: continuidade do negócio, recuperação de desastres
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72249183"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Visão geral da continuidade dos negócios com a Microsoft Genomics
Esta visão geral descreve as capacidades que a Microsoft Genomics fornece para a continuidade do negócio e recuperação de desastres. Saiba mais sobre as opções para recuperar de eventos disruptivos, como uma falha na região de Azure, que pode causar perda de dados. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics apresenta funcionalidades que suportam a continuidade do negócio 
Embora raro, um centro de dados Azure pode ter uma paragem, o que pode causar uma perturbação no negócio que pode durar alguns minutos a algumas horas. Quando ocorrer uma paralisação, todos os postos de trabalho atualmente em funcionamento no centro de dados falharão, e o serviço Microsoft Genómica não reenviará automaticamente postos de trabalho para uma região secundária. 

* Uma opção é esperar que o centro de dados volte a funcionar quando a interrupção do centro de dados terminar. Se a paralisação for curta, o serviço Microsoft Genomics detetará automaticamente os postos de trabalho falhados e o fluxo de trabalho será automaticamente reiniciado.

* Outra opção é submeter proactivamente o fluxo de trabalho noutra região de dados. A Microsoft Genomics implementa casos em [várias regiões do Azure](https://azure.microsoft.com/regions/services/), e cada instância específica da região é independente. Se um dos casos da Microsoft Genomics experimentar uma falha local na região, outras regiões que executam casos de Microsoft Genomics continuarão a processar postos de trabalho. Esta transferência para uma região alternativa está sob o controlo do utilizador e disponível a qualquer momento.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Falha manual sobre os fluxos de trabalho da Microsoft Genomics para outra região
Se ocorrer uma falha regional de data center, poderá optar por submeter fluxos de trabalho da Microsoft Genómica numa região secundária, com base na sua soberania individual de dados e requisitos de continuidade do negócio. Para falhar manualmente os fluxos de trabalho da Microsoft Genomics, utilizaria uma região específica. Conta genómica e submeta o trabalho com credenciais de genómica e conta de armazenamento específicas da região.

Especificamente, terá de:
* Crie uma conta Genómica na região secundária, utilizando o portal Azure. 
* Emigra os seus dados de entrada para uma conta de armazenamento na região secundária e cria uma pasta de saída na região secundária.
* Submeta o fluxo de trabalho na região secundária.

Quando a região original é restaurada, o serviço Microsoft Genomics não migra os dados da região secundária de volta para a região original. Pode optar por transferir os ficheiros de entrada e saída da região secundária para a região original.  Se optar por mover os seus dados, isto está fora do serviço genómica e todos os encargos relacionados com o movimento de dados seriam da sua responsabilidade. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparação para uma possível paralisação específica da região
Se está preocupado com uma recuperação mais rápida em caso de falha no data center, existem alguns passos que pode tomar para reduzir o tempo que demora a reenviar manualmente os seus fluxos de trabalho da Microsoft Genómica para uma região secundária:

* Identifique uma região secundária adequada e crie uma conta genómica naquela região
* Duplique os seus dados na região primária e secundária para que os seus dados se disponibilizem imediatamente na região secundária. Isto poderia ser feito manualmente ou utilizando a funcionalidade [de armazenamento geo-redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy) disponível no armazenamento Azure. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre as suas opções de continuidade de negócios e recuperação de desastres ao utilizar o serviço Microsoft Genomics. Para obter mais informações sobre a continuidade do negócio e a recuperação de desastres dentro do Azure em geral, consulte a orientação técnica de [resiliência do Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 