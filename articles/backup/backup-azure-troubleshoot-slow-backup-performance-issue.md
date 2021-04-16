---
title: Resolução de problemas cópias de segurança lenta de ficheiros e pastas
description: Fornece orientação de resolução de problemas para ajudá-lo a diagnosticar a causa de problemas de desempenho de backup Azure
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518529"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Resolver problemas relacionados com cópias de segurança lentas de ficheiros e pastas no Azure Backup

Este artigo fornece orientações para a resolução de problemas para ajudá-lo a diagnosticar a causa do desempenho de cópia de segurança lenta para ficheiros e pastas quando estiver a usar a Cópia de Segurança Azure. Quando utilizar o agente Azure Backup para fazer cópias de segurança, o processo de backup pode demorar mais do que o esperado. Este atraso pode ser causado por um ou mais dos seguintes:

* [Há estrangulamentos de desempenho no computador que estão a ser apoiados.](#cause1)
* [Outro processo ou software antivírus está a interferir com o processo de Backup Azure.](#cause2)
* [O agente de reserva está a funcionar numa máquina virtual Azure (VM).](#cause3)  
* [Estás a fazer backup de um grande número (milhões) de ficheiros.](#cause4)

Antes de começar a resolver problemas, recomendamos que descarregue e instale o [mais recente agente Azure Backup](https://aka.ms/azurebackup_agent). Fazemos atualizações frequentes ao agente Backup para corrigir vários problemas, adicionar funcionalidades e melhorar o desempenho.

Também recomendamos vivamente que reveja o [serviço de backup Azure FAQ](backup-azure-backup-faq.yml) para se certificar de que não está a experimentar nenhum dos problemas de configuração comuns.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Causa: Trabalho de backup em execução em modo nãotimizado

* O agente MARS pode executar a tarefa de backup em **modo otimizado** utilizando o diário de alteração USN (número de sequência de atualização) ou **modo nãotimizado,** verificando se há alterações nos diretórios ou ficheiros, digitalizando todo o volume.
* O modo nãotimizado é lento porque o agente tem de digitalizar cada ficheiro do volume e comparar com os metadados para determinar os ficheiros alterados.
* Para verificar isto, abra detalhes de **trabalho** da consola do agente MARS e verifique o estado para ver se diz **que os dados de transferência (nãotimizados, podem demorar mais tempo)** como mostrado abaixo:

    ![Correndo em modo nãotimizado](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* As seguintes condições podem fazer com que o trabalho de backup seja executado em modo nãotimizado:
  * A primeira cópia de segurança (também conhecida como Replicação Inicial) será sempre executada em modo nãotimizado
  * Se o trabalho de reserva anterior falhar, então o próximo trabalho de reserva agendado funcionará como nãotimizado.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Causa: Estrangulamentos de desempenho no computador

Estrangulamentos no computador que estão a ser apoiados podem causar atrasos. Por exemplo, a capacidade do computador de ler ou escrever para o disco, ou largura de banda disponível para enviar dados através da rede, pode causar estrangulamentos.

O Windows fornece uma ferramenta incorporada chamada Monitor de [Desempenho](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) para detetar estes estrangulamentos.

Aqui estão alguns contadores de desempenho e gamas que podem ser úteis no diagnóstico de estrangulamentos para cópias de segurança ideais.

| Contador | Estado |
| --- | --- |
| Disco lógico (Disco Físico)--%inativo |<li> 100% inativo a 50% inativo = Saudável</br><li> 49% inativo a 20% inativo = Aviso ou Monitor</br><li> 19% ocioso a 0% inativo = Crítico ou Fora de Especificação |
| Disco lógico (Disco Físico)--%Avg. Leitura ou escrita de disco |<li> 0.001 ms a 0,015 ms = Saudável</br><li> 0,015 ms a 0,025 ms = Aviso ou Monitor</br><li> 0.026 ms ou mais = Crítico ou fora de especificação |
| Disco lógico (Disco Físico)-- Comprimento da fila do disco atual (para todos os casos) |80 pedidos por mais de 6 minutos |
| Memory-- Pool Non Paged Bytes |<li> Menos de 60% da piscina consumida = Saudável<br><li> 61% a 80% da piscina consumida = Aviso ou Monitor</br><li> Maior do que 80% de piscina consumida = Crítico ou Fora de Especificação |
| Memória-- Pool Paged Bytes |<li> Menos de 60% da piscina consumida = Saudável</br><li> 61% a 80% da piscina consumida = Aviso ou Monitor</br><li> Maior do que 80% de piscina consumida = Crítico ou Fora de Especificação |
| Megabytes disponíveis |<li> 50% da memória gratuita disponível ou mais = Saudável</br><li> 25% da memória gratuita disponível = Monitor</br><li>10% da memória gratuita disponível = Aviso</br><li> Menos de 100 MB ou 5% da memória gratuita disponível = Crítico ou Fora de Especificação |
| Processador-- \% Tempo do processador (todos os casos) |<li> Menos de 60% consumido = Saudável</br><li> 61% a 90% consumido = Monitor ou Cuidado</br><li> 91% a 100% consumido = Crítico |

> [!NOTE]
> Se determinar que a infraestrutura é o culpado, recomendamos que desfragmente os discos regularmente para um melhor desempenho.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Causa: Outro processo ou software antivírus que interfere com a Azure Backup

Temos visto vários casos em que outros processos no sistema Windows afetaram negativamente o desempenho do processo de agente Azure Backup. Por exemplo, se utilizar tanto o agente Azure Backup como outro programa para fazer cópias de segurança de dados, ou se o software antivírus estiver em funcionamento e tiver um bloqueio nos ficheiros a ser cópia de segurança, as múltiplas fechaduras nos ficheiros podem causar contenção. Nesta situação, o apoio pode falhar, ou o trabalho pode demorar mais do que o esperado.

A melhor recomendação neste cenário é desligar o outro programa de backup para ver se o tempo de backup para o agente Azure Backup muda. Normalmente, garantir que vários postos de trabalho de reserva não estão a funcionar ao mesmo tempo é suficiente para evitar que se afetem uns aos outros.

Para programas antivírus, recomendamos que exclua os seguintes ficheiros e locais:

* C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent\bin\cbengine.exe como um processo
* C:\Ficheiros de programa\Microsoft Azure Recovery Services Agent\ pastas
* Localização de risco (se não estiver a utilizar a localização padrão)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Causa: Agente de backup a funcionar numa máquina virtual Azure

Se estiver a executar o agente de reserva num VM, o desempenho será mais lento do que quando o executa numa máquina física. Isto é esperado devido a limitações do IOPS.  No entanto, pode otimizar o desempenho mudando as unidades de dados que estão a ser apoiadas até ao Azure Premium Storage. Estamos a trabalhar para resolver este problema, e a correção estará disponível num lançamento futuro.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Causa: Backup de um grande número (milhões) de ficheiros

Mover um grande volume de dados levará mais tempo do que mover um volume menor de dados. Em alguns casos, o tempo de backup está relacionado não só com o tamanho dos dados, mas também com o número de ficheiros ou pastas. Isto é especialmente verdade quando milhões de pequenos ficheiros (alguns bytes a alguns quilobytes) estão a ser apoiados.

Este comportamento ocorre porque enquanto está a fazer o backup dos dados e a movê-lo para Azure, o Azure está simultaneamente a catalogar os seus ficheiros. Em alguns cenários raros, a operação do catálogo pode demorar mais do que o esperado.

Os seguintes indicadores podem ajudá-lo a compreender o estrangulamento e, consequentemente, trabalhar nos próximos passos:

* **A UI está a mostrar progressos para a transferência de dados.** Os dados ainda estão a ser transferidos. A largura de banda da rede ou o tamanho dos dados podem estar a causar atrasos.
* **A UI não está a mostrar progressos para a transferência de dados.** Abra os registos localizados em C:\Program Files\Microsoft Azure Recovery Services Agent\Temp e, em seguida, verifique se o FileProvider::Entrada De Fim nos registos. Esta entrada significa que a transferência de dados terminou e a operação do catálogo está a acontecer. Não cancele os trabalhos de apoio. Em vez disso, aguarde um pouco mais para a operação do catálogo terminar. Se o problema persistir, contacte o [suporte do Azure](https://portal.azure.com/#create/Microsoft.Support).

Se estiver a tentar fazer backup de discos grandes, então é aconselhável utilizar a Caixa de [Dados Azure](./offline-backup-azure-data-box.md) para a primeira cópia de segurança (Replicação Inicial).  Se não puder utilizar a Data Box, quaisquer problemas transitórios de rede que ocorram no seu ambiente durante longas transferências de dados sobre a rede podem causar falhas de backup.  Para evitar estas falhas, pode adicionar algumas pastas à sua cópia de segurança inicial e continuar a adicionar mais pastas até que todas as pastas sejam apoiadas com sucesso até ao Azure.  As cópias de segurança incrementais subsequentes serão relativamente mais rápidas.

## <a name="next-steps"></a>Passos seguintes

* [Perguntas comuns sobre o backup de ficheiros e pastas](backup-azure-file-folder-backup-faq.yml)
