---
title: Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup
description: Fornece orientações de resolução de problemas para ajudar a diagnosticar a causa dos problemas de desempenho de cópia de segurança do Azure
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704926"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup
Este artigo fornece orientações de resolução de problemas para o ajudar a diagnosticar a causa do desempenho de cópia de segurança lenta de ficheiros e pastas quando estiver usando a cópia de segurança do Azure. Quando utiliza o agente de cópia de segurança do Azure para fazer backup de arquivos, o processo de cópia de segurança poderá demorar mais do que esperado. Este atraso pode ser causado por uma ou mais dos seguintes procedimentos:

* [Existem afunilamentos de desempenho no computador que está a cópia de segurança.](#cause1)
* [Outro processo ou antivírus estiver interferindo com o processo de cópia de segurança do Azure.](#cause2)
* [O agente de cópia de segurança está em execução numa máquina virtual do Azure (VM).](#cause3)  
* [Está fazendo o backup de um grande número (milhões) de ficheiros.](#cause4)

Antes de iniciar a resolução de problemas, recomendamos que transfira e instale o [agente de cópia de segurança do Azure mais recente](https://aka.ms/azurebackup_agent). Podemos fazer atualizações frequentes para o agente de cópia de segurança para corrigir diversos problemas, adicionar recursos e melhorar o desempenho.

Também recomendamos que reveja os [FAQ do serviço de cópia de segurança do Azure](backup-azure-backup-faq.md) para se certificar de que não está a experienciar qualquer um dos problemas comuns de configuração.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Afunilamentos de desempenho no computador
Afunilamentos no computador que está a cópia de segurança podem causar atrasos. Por exemplo, a capacidade do computador para ler ou escrever para o disco ou a largura de banda disponível para enviar dados através da rede, pode causar afunilamentos.

Windows fornece uma ferramenta interna que é chamada [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) para detectar esses afunilamentos.

Aqui estão alguns contadores de desempenho e os intervalos que podem ser útil para diagnosticar afunilamentos para cópias de segurança ideal.

| Contador | Estado |
| --- | --- |
| Disco lógico (disco físico) – % inativo |• 100% de inatividade para 50% inativo = bom estado de funcionamento</br>• 49% idle para 20% inativo = aviso ou um Monitor</br>• 19% idle para 0% inativo = críticas ou de especificação |
| Disco lógico (disco físico) – média de % Disco seg de leitura ou escrita |• ms 0,001 a 0.015 ms = bom estado de funcionamento</br>• 0.015 ms a 0.025 ms = aviso ou um Monitor</br>• 0.026 ms ou já = críticas ou de especificação |
| Disco lógico (disco físico) - comprimento de fila de disco atual (para todas as instâncias) |80 pedidos por mais de 6 minutos |
| Memória – não na memória paginável Bytes |• Menos de 60% de agrupamento consumido = bom estado de funcionamento<br>• de 61% a 80% do conjunto consumido = aviso ou um Monitor</br>• Maior do que o conjunto de 80% consumido = críticas ou de especificação |
| Memória – na memória paginável Bytes |• Menos de 60% de agrupamento consumido = bom estado de funcionamento</br>• de 61% a 80% do conjunto consumido = aviso ou um Monitor</br>• Maior do que o conjunto de 80% consumido = críticas ou de especificação |
| Memória – disponível em Megabytes |• 50% de memória livre disponível ou mais = bom estado de funcionamento</br>• 25% de memória livre disponível = Monitor</br>• 10% de memória livre disponível = aviso</br>• Menos de 100 MB ou 5% de memória livre disponível = críticas ou de especificação |
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
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processador –\`tempo do processador (todas as instâncias)es and folders in Azure Backup
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
| Processor--\%Processor Time (all instances) |• Menos de 60% consumidos = bom estado de funcionamento</br>• 61% a 90% consumida = Monitor ou cuidado</br>• 91% a 100% consumida = crítico |

> [!NOTE]
> Se determinar que a infraestrutura é o culpado, recomendamos que desfragmente os discos regularmente para um melhor desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Outro processo ou software antivírus que esteja a interferir com o Azure Backup
Já vimos várias instâncias em que outros processos no sistema Windows tem afetado negativamente o desempenho do processo do agente de cópia de segurança do Azure. Por exemplo, se usar o agente de cópia de segurança do Azure e o outro programa para criar cópias de segurança ou se o software antivírus está em execução e tem um bloqueio em ficheiros de cópia de segurança, o vários bloqueios forem realizados em arquivos poderão provocar contenção. Nesta situação, a cópia de segurança poderá falhar ou o trabalho pode demorar mais do que esperado.

A melhor recomendação neste cenário é desativar o programa de cópia de segurança para ver se o tempo de cópia de segurança para o agente de cópia de segurança do Azure é alterada. Normalmente, a certificar-se de que várias tarefas de cópia de segurança não estão em execução ao mesmo tempo é suficiente para impedir que afetem entre si.

Para programas antivírus, recomendamos que exclua os seguintes ficheiros e localizações:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Program Files\Microsoft Azure Recovery Services agent\. pastas
* Localização de rascunho (se não estiver a utilizar a localização padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de cópia de segurança em execução numa máquina virtual do Azure
Se estiver a executar o agente de cópia de segurança numa VM, o desempenho será mais lento do que quando executá-lo numa máquina física. Isto é esperado devido a limitações de IOPS.  No entanto, pode otimizar o desempenho ao mudar as unidades de dados que estão a cópia de segurança para armazenamento Premium do Azure. Estamos a corrigir o problema e a correção vai estar disponível numa versão futura.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Cópias de segurança um grande número (milhões) de ficheiros
Mover um grande volume de dados demora mais tempo do que mover o menor volume de dados. Em alguns casos, o tempo de cópia de segurança está relacionado ao não apenas o tamanho dos dados, mas também o número de ficheiros ou pastas. Isso é especialmente verdadeiro quando milhões de ficheiros pequenos (alguns bytes para alguns quilobytes) são a cópia de segurança.

Este comportamento ocorre porque, enquanto estiver backup de dados e movê-lo para o Azure, Azure em simultâneo é catalogação seus arquivos. Em alguns cenários raros, a operação de catálogo pode demorar mais do que esperado.

Os indicadores seguintes podem ajudar a compreender o afunilamento e funcionam da mesma forma nos próximos passos:

* **Interface do Usuário está a mostrar o progresso para a transferência de dados**. Os dados ainda estão a ser transferidos. A largura de banda de rede ou o tamanho dos dados poderá estar a causar atrasos.
* **Interface do Usuário não está a mostrar o progresso para a transferência de dados**. Abra os registos localizados em C:\Program Files\Microsoft Azure Recovery Services Agent\Temp e, em seguida, verifique a entrada de FileProvider::EndData nos registos. Esta entrada indica que a transferência de dados foi concluída e a operação de catálogo está acontecendo. Não cancele as tarefas de cópia de segurança. Em alternativa, aguarde um pouco mais para a operação de catálogo concluir. Se o problema persistir, contacte [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).
