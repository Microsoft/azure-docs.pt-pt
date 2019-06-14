---
title: Continuidade do negócio - Microsoft Genomics | Documentos da Microsoft
titleSuffix: Azure
description: Esta descrição geral descreve as capacidades que o Microsoft Genomics fornece para continuidade do negócio e recuperação após desastre. Saiba mais sobre as opções para recuperar de eventos problemáticos, como uma falha de região do Azure, o que poderia causar a perda de dados.
keywords: continuidade do negócio, recuperação após desastre
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 04/06/2018
ms.openlocfilehash: 7a51477dbbf6f4e50959a6d979342961c7e49ad9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641114"
---
# <a name="overview-of-business-continuity-with-microsoft-genomics"></a>Descrição geral da continuidade do negócio com o Microsoft Genomics
Esta descrição geral descreve as capacidades que o Microsoft Genomics fornece para continuidade do negócio e recuperação após desastre. Saiba mais sobre as opções para recuperar de eventos problemáticos, como uma falha de região do Azure, o que poderia causar a perda de dados. 


## <a name="microsoft-genomics-features-that-support-business-continuity"></a>Microsoft Genomics funcionalidades de continuidade do negócio esse suporte 
Embora seja raro, um centro de dados do Azure pode ficar indisponível, que pode causar uma interrupção de negócios que pode durar alguns minutos a algumas horas. Quando ocorrer uma falha, todas as tarefas atualmente em execução no Centro de dados falharão e o serviço Microsoft Genomics irá automaticamente voltar a submeter tarefas para uma região secundária. 

* Uma opção é aguardar que o Centro de dados esteja novamente online quando o Datacenter está acima. Se o problema da indisponibilidade é curto, o serviço Microsoft Genomics irá detetar automaticamente as tarefas com falhas e o fluxo de trabalho será reiniciado automaticamente.

* Outra opção é submeter proativamente o fluxo de trabalho em outra região de dados. Microsoft Genomics implementa instâncias em vários [regiões do Azure](https://azure.microsoft.com/regions/services/), e cada instância de específico da região é independente. Se uma das instâncias do Microsoft Genomics ocorrer uma falha do local de região, outras regiões de execução de instâncias do Microsoft Genomics continuará processar tarefas. Esta transferência para uma região alternativa está sob o controlo do utilizador e está disponível em qualquer altura.


### <a name="manually-failover-microsoft-genomics-workflows-to-another-region"></a>Manualmente ativação pós-falha Microsoft Genomics fluxos de trabalho para outra região
No caso de uma indisponibilidade do Centro de dados regionais, pode optar por enviar o Microsoft Genomics fluxos de trabalho numa região secundária, com base nos seus requisitos de continuidade de comerciais e soberania de dados individuais. Para manualmente ativação pós-falha Microsoft Genomics fluxos de trabalho, usaria um específico da região diferente. Genomics conta e submeter a tarefa com apropriado específico da região Genomics e credenciais de conta de armazenamento.

Especificamente, precisará para:
* Crie uma conta do Genomics na região secundária, no portal do Azure. 
* Migrar os dados de entrada para uma conta de armazenamento na região secundária e configurar uma pasta de saída na região secundária.
* Submeta o fluxo de trabalho na região secundária.

Quando a região original é restaurada, o serviço Microsoft Genomics não migra os dados da região secundária para a região original. Pode optar por mover a entrada e fazer uma cópia de ficheiros de saída da região secundária para a região original.  Se optar por mover seus dados, isso está fora o serviço Genomics e todos os custos relacionados com o movimento de dados seria sua responsabilidade. 

### <a name="preparing-for-a-possible-region-specific-outage"></a>Preparação para uma possível falha de específico da região
Se estiver preocupado com a recuperação mais rápida no caso de uma indisponibilidade do Centro de dados, existem alguns passos que pode tomar para reduzir o tempo necessário para que possa manualmente volte a submeter os fluxos de trabalho do Microsoft Genomics para uma região secundária:

* Identificar uma região secundária apropriada e pró-ativa, criar uma conta do Genomics nessa região
* Duplicar os dados na região primária e secundária, para que os seus dados estão imediatamente disponíveis na região secundária. Isto pode ser feito manualmente ou utilizando o [armazenamento georredundante](https://docs.microsoft.com/azure/storage/common/storage-redundancy) recurso disponível no armazenamento do Azure. 

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, aprendeu sobre suas opções de continuidade do negócio e recuperação após desastre ao utilizar o serviço Microsoft Genomics. Para obter mais informações sobre a continuidade do negócio e recuperação após desastre no Azure em geral, consulte [orientações técnicas sobre resiliência do Azure.](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region) 