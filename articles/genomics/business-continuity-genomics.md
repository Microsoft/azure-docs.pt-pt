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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "72249183"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Visão geral da continuidade do negócio com a Microsoft Genomics
Esta visão geral descreve as capacidades que a Microsoft Genomics fornece para a continuidade do negócio e recuperação de desastres. Saiba mais sobre opções para recuperar de eventos disruptivos, como uma interrupção da região de Azure, que pode causar perda de dados. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Funcionalidades da Microsoft Genomics que suportam a continuidade do negócio 
Apesar de raro, um centro de dados Azure pode ter uma paragem, o que pode causar uma perturbação do negócio que pode durar alguns minutos a algumas horas. Quando ocorre uma paralisação, todos os postos de trabalho atualmente em execução no centro de dados falharão, e o serviço Microsoft Genomics não irá automaticamente reenviar automaticamente os postos de trabalho para uma região secundária. 

* Uma opção é esperar que o centro de dados volte a estar online quando a paragem do data center terminar. Se a paralisação for curta, o serviço Microsoft Genomics detetará automaticamente os trabalhos falhados e o fluxo de trabalho será automaticamente reiniciado.

* Outra opção é submeter proactivamente o fluxo de trabalho noutra região de dados. A Microsoft Genomics implementa casos em [várias regiões do Azure](https://azure.microsoft.com/regions/services/), e cada instância específica da região é independente. Se uma das instâncias da Microsoft Genomics experimentar uma falha local da região, outras regiões que executam instâncias da Microsoft Genomics continuarão a processar empregos. Esta transferência para uma região alternativa está sob o controlo do utilizador e está disponível a qualquer momento.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Falha manualmente sobre os fluxos de trabalho da Microsoft Genomics para outra região
Se ocorrer uma falha regional do data center, poderá optar por submeter fluxos de trabalho da Microsoft Genomics numa região secundária, com base nos seus requisitos individuais de soberania de dados e continuidade de negócios. Para falhar manualmente os fluxos de trabalho da Microsoft Genomics, utilizaria uma região específica diferente. Conta genómica e submeter o trabalho com credenciais genómicas e de armazenamento específicas da região.

Especificamente, terá de:
* Crie uma conta Geómica na região secundária, utilizando o portal Azure. 
* Migrar os seus dados de entrada para uma conta de armazenamento na região secundária e criar uma pasta de saída na região secundária.
* Submeta o fluxo de trabalho na região secundária.

Quando a região original é restaurada, o serviço Microsoft Genomics não migra os dados da região secundária de volta para a região original. Pode optar por transferir os ficheiros de entrada e saída da região secundária para a região original.  Se optar por mover os seus dados, este está fora do serviço Genomics e todos os encargos relacionados com o movimento de dados seriam da sua responsabilidade. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparação para uma possível paralisação específica da região
Se estiver preocupado com uma recuperação mais rápida no caso de uma interrupção do data center, existem alguns passos que pode tomar para reduzir o tempo que leva para reenviar manualmente os fluxos de trabalho da Microsoft Genomics para uma região secundária:

* Identifique uma região secundária adequada e crie uma conta genómica nessa região
* Duplique os seus dados na região primária e secundária para que os seus dados se mantenham imediatamente disponíveis na região secundária. Isto poderia ser feito manualmente ou utilizando a função [de armazenamento geo-redundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy) disponível no armazenamento Azure. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu sobre as suas opções de continuidade de negócios e recuperação de desastres ao utilizar o serviço Microsoft Genomics. Para obter mais informações sobre a continuidade do negócio e a recuperação de desastres dentro de Azure em geral, consulte [a orientação técnica de resiliência do Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 