---
title: Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup
description: Fornece orientações de resolução de problemas para ajudar a diagnosticar a causa dos problemas de desempenho de cópia de segurança do Azure
services: backup
author: genlin
manager: cshepard
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f24a60ab9bdcf1231085de4edeeb89ce1edf4e80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337634"
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
| Processador –\%tempo do processador (todas as instâncias) |• Menos de 60% consumidos = bom estado de funcionamento</br>• 61% a 90% consumida = Monitor ou cuidado</br>• 91% a 100% consumida = crítico |

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
