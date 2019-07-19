---
title: Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup
description: Fornece orientação para solução de problemas para ajudá-lo a diagnosticar a causa de problemas de desempenho de backup do Azure
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/18/2019
ms.locfileid: "67704926"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup
Este artigo fornece orientação para a solução de problemas para ajudá-lo a diagnosticar a causa do baixo desempenho de backup para arquivos e pastas quando você estiver usando o backup do Azure. Quando você usa o agente de backup do Azure para fazer backup de arquivos, o processo de backup pode demorar mais do que o esperado. Esse atraso pode ser causado por um ou mais dos seguintes itens:

* [Há afunilamentos de desempenho no computador que está sendo submetido a backup.](#cause1)
* [Outro processo ou software antivírus está interferindo com o processo de backup do Azure.](#cause2)
* [O agente de backup está em execução em uma VM (máquina virtual) do Azure.](#cause3)  
* [Você está fazendo backup de um grande número (milhões) de arquivos.](#cause4)

Antes de começar a solucionar problemas, recomendamos que você baixe e instale o [agente de backup do Azure mais recente](https://aka.ms/azurebackup_agent). Fazemos atualizações frequentes para o agente de backup para corrigir vários problemas, adicionar recursos e melhorar o desempenho.

Também é altamente recomendável que você examine as [perguntas frequentes do serviço de backup do Azure](backup-azure-backup-faq.md) para certificar-se de que você não está enfrentando problemas comuns de configuração.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Motivo: Afunilamentos de desempenho no computador
Os afunilamentos no computador que está sendo submetido a backup podem causar atrasos. Por exemplo, a capacidade do computador de ler ou gravar no disco ou a largura de banda disponível para enviar dados pela rede pode causar afunilamentos.

O Windows fornece uma ferramenta interna chamada Perfmon ( [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) ) para detectar esses afunilamentos.

Aqui estão alguns contadores de desempenho e intervalos que podem ser úteis para diagnosticar afunilamentos para backups ideais.

| Neutraliza | Estado |
| --- | --- |
| Disco lógico (disco físico)--% ocioso |• 100% ociosos a 50% ocioso = íntegro</br>• 49% ociosos a 20% ociosos = aviso ou monitor</br>• 19% de ociosidade a 0% ocioso = crítico ou fora de especificação |
| Disco lógico (disco físico)--% Méd. Leitura ou gravação de disco s |• 0, 1 ms a 0, 15 ms = íntegro</br>• 0, 15 ms a 0, 25 MS = aviso ou monitor</br>• 0, 26 MS ou maior = crítico ou fora de especificação |
| Disco lógico (disco físico)--comprimento da fila de disco atual (para todas as instâncias) |80 solicitações por mais de 6 minutos |
| Memória--bytes de pool não paginável |• Menos de 60% do pool consumido = íntegro<br>• 61% a 80% do pool consumido = aviso ou monitor</br>• Mais de 80% de pool consumidos = crítico ou fora de especificação |
| Memória--bytes de pool paginável |• Menos de 60% do pool consumido = íntegro</br>• 61% a 80% do pool consumido = aviso ou monitor</br>• Mais de 80% de pool consumidos = crítico ou fora de especificação |
| Memória--megabytes disponíveis |• 50% de memória livre disponível ou mais = íntegro</br>• 25% de memória livre disponível = monitor</br>• 10% de memória livre disponível = aviso</br>• Menos de 100 MB ou 5% de memória livre disponível = crítico ou fora de especificação |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processador--\`tempo do processador (todas as instâncias)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Menos de 60% consumidas = íntegro</br>• 61% a 90% consumidas = monitor ou cuidado</br>• 91% a 100% consumido = crítico |

> [!NOTE]
> Se você determinar que a infraestrutura é a culpa, é recomendável desfragmentar os discos regularmente para obter um melhor desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Motivo: Outro processo ou software antivírus está interferindo com o backup do Azure
Vimos várias instâncias em que outros processos no sistema Windows afetaram negativamente o desempenho do processo do agente de backup do Azure. Por exemplo, se você usar o agente de backup do Azure e outro programa para fazer backup de dados, ou se o software antivírus estiver em execução e tiver um bloqueio nos arquivos cujo backup será feito, os vários bloqueios nos arquivos poderão causar contenção. Nessa situação, o backup pode falhar ou o trabalho pode demorar mais do que o esperado.

A melhor recomendação nesse cenário é desativar o outro programa de backup para ver se o tempo de backup do agente de backup do Azure é alterado. Geralmente, garantir que vários trabalhos de backup não estejam em execução ao mesmo tempo é suficiente para impedir que eles afetem uns aos outros.

Para programas antivírus, recomendamos que você exclua os seguintes arquivos e locais:

* C:\Arquivos de Programas\microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Arquivos de Programas\microsoft Azure Recovery Services Agent \ pastas
* Local de rascunho (se você não estiver usando o local padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Motivo: Agente de backup em execução em uma máquina virtual do Azure
Se você estiver executando o agente de backup em uma VM, o desempenho será mais lento do que quando você executá-lo em um computador físico. Isso é esperado devido a limitações de IOPS.  No entanto, você pode otimizar o desempenho alternando as unidades de dados que estão sendo submetidas a backup para o armazenamento Premium do Azure. Estamos trabalhando para corrigir esse problema, e a correção estará disponível em uma versão futura.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Motivo: Fazendo backup de um grande número (milhões) de arquivos
Mover um grande volume de dados levará mais tempo do que mover um volume menor de dados. Em alguns casos, o tempo de backup está relacionado não apenas ao tamanho dos dados, mas também ao número de arquivos ou pastas. Isso é especialmente verdadeiro quando são feitos backup de milhões de arquivos pequenos (alguns bytes a alguns quilobytes).

Esse comportamento ocorre porque, enquanto você está fazendo backup dos dados e movendo-os para o Azure, o Azure está catalogando seus arquivos simultaneamente. Em alguns cenários raros, a operação de catálogo pode demorar mais do que o esperado.

Os seguintes indicadores podem ajudá-lo a entender o afunilamento e o trabalho adequado nas próximas etapas:

* **A interface do usuário está mostrando o progresso da transferência de dados**. Os dados ainda estão sendo transferidos. A largura de banda da rede ou o tamanho dos dados podem estar causando atrasos.
* **A interface do usuário não está mostrando o progresso da transferência de dados**. Abra os logs localizados em C:\Program Files\Microsoft Azure Recovery Services Agent\Temp e, em seguida, verifique a entrada FileProvider:: endData nos logs. Essa entrada significa que a transferência de dados foi concluída e a operação de catálogo está acontecendo. Não cancele os trabalhos de backup. Em vez disso, aguarde um pouco mais para que a operação de catálogo seja concluída. Se o problema persistir, entre em contato com o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).
