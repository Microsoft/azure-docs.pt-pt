---
title: Resolução lenta de reposições de ficheiros e pastas
description: Fornece orientações de resolução de problemas para ajudá-lo a diagnosticar a causa dos problemas de desempenho do Azure Backup
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 01fff1d970a76d0d4d38c2536b41d58a4db301c8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198626"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup

Este artigo fornece orientações de resolução de problemas para ajudá-lo a diagnosticar a causa de um desempenho de backup lento para ficheiros e pastas quando estiver a utilizar o Azure Backup. Quando utiliza o agente de backup Azure para fazer cópias de segurança, o processo de backup pode demorar mais do que o esperado. Este atraso pode ser causado por um ou mais dos seguintes:

* [Há estrangulamentos de desempenho no computador que estão a ser bloqueados.](#cause1)
* [Outro software de processo ou antivírus está a interferir com o processo de backup do Azure.](#cause2)
* [O agente de reserva está a funcionar numa máquina virtual Azure (VM).](#cause3)  
* [Estás a apoiar um grande número (milhões) de ficheiros.](#cause4)

Antes de iniciar problemas de resolução de problemas, recomendamos que descarregue e instale o [mais recente agente de backup Do Azure](https://aka.ms/azurebackup_agent). Fazemos atualizações frequentes ao agente de backup para corrigir vários problemas, adicionar funcionalidades e melhorar o desempenho.

Também recomendamos vivamente que reveja o serviço de [backup Azure FAQ](backup-azure-backup-faq.md) para se certificar de que não está a ter nenhum dos problemas de configuração comuns.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Causa: Trabalho de backup em modo não otimizado

* O agente MARS pode executar o trabalho de backup em **modo otimizado** usando USN (número de sequência de atualização) alterar o diário ou o **modo não otimizado,** verificando se há alterações nos diretórios ou ficheiros, digitalizando todo o volume.
* O modo não otimizado é lento porque o agente tem de digitalizar cada ficheiro no volume e comparar com os metadados para determinar os ficheiros alterados.
* Para verificar isto, abra **os Detalhes** de Trabalho da consola do agente MARS e verifique o estado para ver se diz que transferir **dados (não otimizados, pode demorar mais tempo)** como mostrado abaixo:

    ![Funcionando em modo não otimizado](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* As seguintes condições podem fazer com que o trabalho de backup corra em modo não otimizado:
  * A primeira cópia de segurança (também conhecida como Replicação Inicial) será sempre executada em modo não otimizado
  * Se o trabalho de reserva anterior falhar, então o próximo trabalho de reserva programado será executado como não otimizado.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Estrangulamentos de desempenho no computador

Estrangulamentos no computador que está a ser apoiado podem causar atrasos. Por exemplo, a capacidade do computador de ler ou escrever para o disco, ou largura de banda disponível para enviar dados através da rede, pode causar estrangulamentos.

O Windows fornece uma ferramenta incorporada chamada [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) para detetar estes estrangulamentos.

Aqui estão alguns contadores de desempenho e gamas que podem ser úteis no diagnóstico de estrangulamentos para backups ideais.

| Contador | Estado |
| --- | --- |
| Disco Lógico (Disco Físico)--%idle |• 100% ocioso a 50% inativo = Saudável</br>• 49% ocioso a 20% de inatividade = Aviso ou Monitor</br>• 19% ocioso a 0% de ocioso = Crítico ou Fora de Especificação |
| Disco Lógico (Disco Físico)--%Avg. Disk Sec Ler ou Escrever |• 0,001 ms a 0,015 ms = Saudável</br>• 0,015 ms a 0,025 ms = Aviso ou Monitor</br>• 0,026 ms ou mais = Crítico ou Fora de Especificação |
| Disco Lógico (Disco Físico)--Comprimento da fila do disco atual (para todos os casos) |80 pedidos por mais de 6 minutos |
| Memória -- Pool Não Paged Bytes |• Menos de 60% da piscina consumida = Saudável<br>• 61% a 80% da piscina consumida = Aviso ou Monitor</br>• Maior de 80% de piscina consumida = Crítica ou Fora de Especificação |
| Memória -Pool Paged Bytes |• Menos de 60% da piscina consumida = Saudável</br>• 61% a 80% da piscina consumida = Aviso ou Monitor</br>• Maior de 80% de piscina consumida = Crítica ou Fora de Especificação |
| Memória-- Megabytes Disponíveis |• 50% da memória gratuita disponível ou mais = Saudável</br>• 25% da memória gratuita disponível = Monitor</br>• 10% da memória gratuita disponível = Aviso</br>• Menos de 100 MB ou 5% da memória gratuita disponível = Crítico ou Fora de Especificação |
| Processador-- hora do processador\%(todas as instâncias) |• Menos de 60% consumido = Saudável</br>• 61% a 90% consumido = Monitor ou Precaução</br>• 91% a 100% consumido = Crítico |

> [!NOTE]
> Se determinar que a infraestrutura é o culpado, recomendamos que desfragmente regularmente os discos para um melhor desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Outro processo ou software antivírus que interfere com o Azure Backup

Temos visto vários casos em que outros processos no sistema Windows afetaram negativamente o desempenho do processo do agente de backup Azure. Por exemplo, se utilizar tanto o agente de backup Azure como outro programa para fazer backup de dados, ou se o software antivírus estiver a funcionar e tiver um bloqueio nos ficheiros a serem atualizados, as múltiplas fechaduras dos ficheiros podem causar contenção. Nesta situação, o backup pode falhar, ou o trabalho pode demorar mais do que o esperado.

A melhor recomendação neste cenário é desligar o outro programa de backup para ver se o tempo de backup para o agente de backup Azure muda. Normalmente, garantir que vários postos de trabalho de backup não estão a funcionar ao mesmo tempo é suficiente para evitar que se afetem mutuamente.

Para programas antivírus, recomendamos que exclua os seguintes ficheiros e locais:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Program Files\Microsoft Azure Recovery Services Agent\ pastas
* Localização de risco (se não estiver a usar a localização padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de reserva a funcionar numa máquina virtual Azure

Se estiver a executar o agente de reserva num VM, o desempenho será mais lento do que quando o executa numa máquina física. Isto é esperado devido a limitações iops.  No entanto, pode otimizar o desempenho trocando as unidades de dados que estão a ser apoiadas até ao Armazenamento Premium Azure. Estamos a trabalhar na resolução deste problema, e a correção estará disponível num futuro lançamento.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Apoiar um grande número (milhões) de ficheiros

Mover um grande volume de dados levará mais tempo do que mover um volume menor de dados. Em alguns casos, o tempo de backup está relacionado não só com o tamanho dos dados, mas também com o número de ficheiros ou pastas. Isto é especialmente verdade quando milhões de pequenos ficheiros (alguns bytes a alguns quilobytes) estão a ser apoiados.

Este comportamento ocorre porque enquanto está a fazer backup dos dados e a movê-los para o Azure, o Azure está simultaneamente a catalogar os seus ficheiros. Em alguns cenários raros, a operação do catálogo pode demorar mais do que o esperado.

Os seguintes indicadores podem ajudá-lo a compreender o estrangulamento e, consequentemente, trabalhar nos próximos passos:

* **A UI está a mostrar progressos para a transferência de dados.** Os dados ainda estão a ser transferidos. A largura de banda da rede ou o tamanho dos dados podem estar a causar atrasos.
* **A UI não está a mostrar progressos para a transferência de dados.** Abra os registos localizados em C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, e, em seguida, verifique se o FileProvider::EndData entrada nos registos. Esta entrada significa que a transferência de dados terminou e a operação do catálogo está a decorrer. Não cancele os trabalhos de reserva. Em vez disso, espere um pouco mais para que a operação do catálogo termine. Se o problema persistir, contacte o [suporte azure](https://portal.azure.com/#create/Microsoft.Support).
