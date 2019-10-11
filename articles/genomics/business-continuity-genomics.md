---
title: Visão geral da continuidade dos negócios
titleSuffix: Microsoft Genomics
description: Esta visão geral descreve os recursos que o Microsoft Genomics fornece para continuidade dos negócios e recuperação de desastres.
keywords: continuidade dos negócios, recuperação de desastres
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 04/06/2018
ms.openlocfilehash: 28a4a53851155c56e8d34981862bf52a3a2cf15b
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249183"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Visão geral da continuidade dos negócios com o Microsoft Genomics
Esta visão geral descreve os recursos que o Microsoft Genomics fornece para continuidade dos negócios e recuperação de desastres. Saiba mais sobre as opções de recuperação de eventos de interrupção, como uma interrupção de região do Azure, que podem causar perda de dados. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics recursos que dão suporte à continuidade dos negócios 
Embora seja raro, um data center do Azure pode ter uma interrupção, o que pode causar uma interrupção nos negócios que pode durar alguns minutos a algumas horas. Quando ocorrer uma interrupção, todos os trabalhos em execução no momento no data center falharão e o serviço de Microsoft Genomics não reenviará automaticamente os trabalhos para uma região secundária. 

* Uma opção é aguardar o data center ficar online novamente quando a interrupção de data center terminar. Se a interrupção for curta, o serviço de Microsoft Genomics detectará automaticamente os trabalhos com falha e o fluxo de trabalho será reiniciado automaticamente.

* Outra opção é enviar proativamente o fluxo de trabalho em outra região de dados. Microsoft Genomics implanta instâncias em várias [regiões do Azure](https://azure.microsoft.com/regions/services/)e cada instância específica de região é independente. Se uma das instâncias de Microsoft Genomics tiver uma falha local de região, outras regiões que executam instâncias do Microsoft Genomics continuarão a processar os trabalhos. Essa transferência para uma região alternativa está sob o controle do usuário e está disponível a qualquer momento.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Fazer failover manualmente Microsoft Genomics fluxos de trabalho para outra região
Se ocorrer uma interrupção de data center regional, você poderá optar por enviar fluxos de trabalho de Microsoft Genomics em uma região secundária, com base em seus requisitos de soberania de negócios e de continuidade de dados individuais. Para fazer failover manualmente Microsoft Genomics fluxos de trabalho, você usaria uma região específica diferente. A conta de genoma e o envio do trabalho com as credenciais apropriadas de conta de armazenamento e de genoma específicas da região.

Especificamente, você precisará:
* Crie uma conta de genoma na região secundária usando o portal do Azure. 
* Migre os dados de entrada para uma conta de armazenamento na região secundária e configure uma pasta de saída na região secundária.
* Envie o fluxo de trabalho na região secundária.

Quando a região original é restaurada, o serviço de Microsoft Genomics não migra os dados da região secundária de volta para a região original. Você pode optar por mover os arquivos de entrada e saída da região secundária de volta para a região original.  Se você optar por mover seus dados, isso estará fora do serviço de genoma e todos os encargos relacionados à movimentação de dados serão de sua responsabilidade. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparando para uma possível interrupção específica da região
Se você estiver preocupado com a recuperação mais rápida no caso de uma interrupção de data center, há algumas etapas que você pode tomar para reduzir o tempo necessário para reenviar manualmente seus fluxos de trabalho de Microsoft Genomics para uma região secundária:

* Identificar uma região secundária apropriada e criar ativamente uma conta de genomas nessa região
* Duplique os dados nas regiões primária e secundária para que os dados estejam imediatamente disponíveis na região secundária. Isso pode ser feito manualmente ou usando o recurso de [armazenamento com redundância geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy) disponível no armazenamento do Azure. 

## <a name="next-steps"></a>Passos seguintes
Neste artigo, você aprendeu sobre suas opções de continuidade de negócios e recuperação de desastres ao usar o serviço de Microsoft Genomics. Para obter mais informações sobre continuidade de negócios e recuperação de desastres no Azure em geral, consulte [orientação técnica de resiliência do Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 