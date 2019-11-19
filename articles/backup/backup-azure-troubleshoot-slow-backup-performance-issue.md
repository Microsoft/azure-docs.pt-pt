---
title: Solucionar problemas de backup lento de arquivos e pastas
description: Fornece orientação para solução de problemas para ajudá-lo a diagnosticar a causa de problemas de desempenho de backup do Azure
ms.reviewer: saurse
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 2b7b8903da0d8dd83591b260bacb496b0c253ae3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172588"
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

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: afunilamentos de desempenho no computador

Os afunilamentos no computador que está sendo submetido a backup podem causar atrasos. Por exemplo, a capacidade do computador de ler ou gravar no disco ou a largura de banda disponível para enviar dados pela rede pode causar afunilamentos.

O Windows fornece uma ferramenta interna chamada Perfmon ( [Monitor de desempenho](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) ) para detectar esses afunilamentos.

Aqui estão alguns contadores de desempenho e intervalos que podem ser úteis para diagnosticar afunilamentos para backups ideais.

| Neutraliza | Estado |
| --- | --- |
| Disco lógico (disco físico)--% ocioso |• 100% ociosos a 50% ocioso = íntegro</br>• 49% ociosos a 20% ociosos = aviso ou monitor</br>• 19% de ociosidade a 0% ocioso = crítico ou fora de especificação |
| Disco lógico (disco físico)--% média de disco s leitura ou gravação |• 0, 1 ms a 0, 15 ms = íntegro</br>• 0, 15 ms a 0, 25 MS = aviso ou monitor</br>• 0, 26 MS ou maior = crítico ou fora de especificação |
| Disco lógico (disco físico)--comprimento da fila de disco atual (para todas as instâncias) |80 solicitações por mais de 6 minutos |
| Memória--bytes de pool não paginável |• Menos de 60% do pool consumido = íntegro<br>• 61% a 80% do pool consumido = aviso ou monitor</br>• Mais de 80% de pool consumidos = crítico ou fora de especificação |
| Memória--bytes de pool paginável |• Menos de 60% do pool consumido = íntegro</br>• 61% a 80% do pool consumido = aviso ou monitor</br>• Mais de 80% de pool consumidos = crítico ou fora de especificação |
| Memória--megabytes disponíveis |• 50% de memória livre disponível ou mais = íntegro</br>• 25% de memória livre disponível = monitor</br>• 10% de memória livre disponível = aviso</br>• Menos de 100 MB ou 5% de memória livre disponível = crítico ou fora de especificação |
| Processador--\%tempo do processador (todas as instâncias) |• Menos de 60% consumidas = íntegro</br>• 61% a 90% consumidas = monitor ou cuidado</br>• 91% a 100% consumido = crítico |

> [!NOTE]
> Se você determinar que a infraestrutura é a culpa, é recomendável desfragmentar os discos regularmente para obter um melhor desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: outro processo ou software antivírus está interferindo com o backup do Azure

Vimos várias instâncias em que outros processos no sistema Windows afetaram negativamente o desempenho do processo do agente de backup do Azure. Por exemplo, se você usar o agente de backup do Azure e outro programa para fazer backup de dados, ou se o software antivírus estiver em execução e tiver um bloqueio nos arquivos cujo backup será feito, os vários bloqueios nos arquivos poderão causar contenção. Nessa situação, o backup pode falhar ou o trabalho pode demorar mais do que o esperado.

A melhor recomendação nesse cenário é desativar o outro programa de backup para ver se o tempo de backup do agente de backup do Azure é alterado. Geralmente, garantir que vários trabalhos de backup não estejam em execução ao mesmo tempo é suficiente para impedir que eles afetem uns aos outros.

Para programas antivírus, recomendamos que você exclua os seguintes arquivos e locais:

* C:\Arquivos de Programas\microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Arquivos de Programas\microsoft Azure Recovery Services Agent \ pastas
* Local de rascunho (se você não estiver usando o local padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: agente de backup em execução em uma máquina virtual do Azure

Se você estiver executando o agente de backup em uma VM, o desempenho será mais lento do que quando você executá-lo em um computador físico. Isso é esperado devido a limitações de IOPS.  No entanto, você pode otimizar o desempenho alternando as unidades de dados que estão sendo submetidas a backup para o armazenamento Premium do Azure. Estamos trabalhando para corrigir esse problema, e a correção estará disponível em uma versão futura.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: fazer backup de um número grande (milhões) de arquivos

Mover um grande volume de dados levará mais tempo do que mover um volume menor de dados. Em alguns casos, o tempo de backup está relacionado não apenas ao tamanho dos dados, mas também ao número de arquivos ou pastas. Isso é especialmente verdadeiro quando são feitos backup de milhões de arquivos pequenos (alguns bytes a alguns quilobytes).

Esse comportamento ocorre porque, enquanto você está fazendo backup dos dados e movendo-os para o Azure, o Azure está catalogando seus arquivos simultaneamente. Em alguns cenários raros, a operação de catálogo pode demorar mais do que o esperado.

Os seguintes indicadores podem ajudá-lo a entender o afunilamento e o trabalho adequado nas próximas etapas:

* **A interface do usuário está mostrando o progresso da transferência de dados**. Os dados ainda estão sendo transferidos. A largura de banda da rede ou o tamanho dos dados podem estar causando atrasos.
* **A interface do usuário não está mostrando o progresso da transferência de dados**. Abra os logs localizados em C:\Program Files\Microsoft Azure Recovery Services Agent\Temp e, em seguida, verifique a entrada FileProvider:: endData nos logs. Essa entrada significa que a transferência de dados foi concluída e a operação de catálogo está acontecendo. Não cancele os trabalhos de backup. Em vez disso, aguarde um pouco mais para que a operação de catálogo seja concluída. Se o problema persistir, entre em contato com o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).
