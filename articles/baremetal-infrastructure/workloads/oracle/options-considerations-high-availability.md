---
title: Opções ou servidores de infraestruturas Oracle BareMetal
description: Conheça as opções e considerações para os servidores da Oracle BareMetal Infrastructure.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: e8a2dece11884e3a659f14b30bce51e7f0244924
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590303"
---
# <a name="options-for-oracle-baremetal-infrastructure-servers"></a>Opções para servidores de infraestruturas Oracle BareMetal

Neste artigo, consideraremos opções e recomendações para obter o mais alto nível de proteção e desempenho executando a Oracle em servidores de Infraestruturas BareMetal. 

## <a name="data-guard-protection-modes"></a>Modos de proteção da Guarda de Dados

A Data Guard oferece proteção indisponível apenas através do Oracle Real Applications Cluster (RAC), replicação lógica (como o GoldenGate) e replicação baseada no armazenamento. 

| Modo de proteção | Description |
| --- | --- |
| **Desempenho Máximo** | O modo de proteção predefinido. Fornece o mais alto nível de proteção sem afetar o desempenho da base de dados primária. Os dados são considerados comprometidos assim que foram escritos na base de dados primária redo stream. É então replicado na base de dados de espera assíncroneamente. Geralmente, a base de dados de espera recebe-a em segundos, mas não é dada nenhuma garantia nesse sentido. Este modo normalmente satisfaz os requisitos do negócio (juntamente com a monitorização do lag) sem precisar de uma conectividade de rede de baixa latência entre os locais primários e de espera.<br /><br />Proporciona a melhor persistência operacional; no entanto, não garante perda de dados zero.   |
| **Disponibilidade Máxima** | Fornece o mais alto nível de proteção sem afetar a disponibilidade da base de dados primária. Os dados nunca são considerados comprometidos com a base de dados primária até que também tenha sido comprometido com pelo menos uma base de dados de espera. Se a base de dados primária não conseguir escrever as alterações de redo para pelo menos uma base de dados de espera, ela volta ao modo De Desempenho Máximo em vez de ficar indisponível. <br /><br />Permite que o serviço continue se o site de espera não estiver disponível. Se apenas um site estiver a funcionar, apenas uma cópia dos dados será mantida até que o segundo site esteja online e a sincronização seja restabelecida. |
| **Proteção Máxima** | Fornece um nível de proteção semelhante à disponibilidade máxima. A base de dados primária desliga-se com a função adicional se não conseguir escrever as alterações de redo para pelo menos uma base de dados de espera. Isto garante que a perda de dados não pode ocorrer, mas à custa de uma disponibilidade mais frágil. |

>[!IMPORTANT]
>Se necessitar de um objetivo de ponto de recuperação (RPO) de zero, recomendamos a configuração de Disponibilidade Máxima. Em seguida, a RPO pode ser garantida mesmo quando ocorrem múltiplas falhas. Por exemplo, mesmo no caso de uma falha de rede a partir da base de dados primária, seguida da perda da base de dados primária algum tempo depois, enquanto a falha da rede ainda está em vigor.

### <a name="data-guard-deployment-patterns"></a>Padrões de implementação da Guarda de Dados

A Oracle permite-lhe configurar vários destinos para a geração redo, permitindo várias bases de dados de espera. A configuração mais comum é mostrada na seguinte figura, uma única base de dados de espera numa região diferente.

:::image type="content" source="media/oracle-high-availability/default-data-guard-deployment.png" alt-text="Diagrama mostrando a implementação padrão da Oracle data guard.":::

A Data Guard está configurada no modo de desempenho máximo para uma implementação predefinida. Esta configuração fornece quase uma replicação de dados em tempo real através do transporte de redo assíncronos. A base de dados de espera não precisa de ser executada dentro de uma implementação rac, mas recomendamos que cumpra as exigências de desempenho do site principal.

Recomendamos uma implantação como a mostrada na seguinte figura para ambientes que exijam um tempo de tempo rigoroso ou um RPO de zero. A configuração de Disponibilidade Máxima consiste numa base de dados local de espera que aplica o redo em modo sincronizado e uma segunda base de dados de espera em execução numa região remota.

:::image type="content" source="media/oracle-high-availability/max-availability-data-guard-deployment.png" alt-text="Diagrama mostrando a máxima disponibilidade de utilização da Data Guard.":::

Pode criar uma base de dados de espera local quando o desempenho da aplicação sofrerá executando a base de dados e servidores de aplicações em regiões distintas. Nesta configuração, é utilizada uma base de dados de espera local quando é necessária uma manutenção planeada ou não planeada no cluster primário. Pode executar estas bases de dados com replicação sincronizada porque estão na mesma região, garantindo que não há dados perdidos entre eles.

### <a name="data-guard-configuration-considerations"></a>Considerações de configuração da Data Guard

O Data Guard Broker deve ser implementado, uma vez que simplifica a implementação de uma configuração da Data Guard e garante que as melhores práticas são cumpridas. Fornece funcionalidade de monitorização de desempenho e simplifica consideravelmente os procedimentos de transição, falha e reinstação.

A Data Guard permite-lhe executar um processo de observador, que monitoriza todas as bases de dados numa configuração da Data Guard para determinar a disponibilidade da base de dados. Se uma base de dados primária falhar, o Data Guard Observer pode iniciar automaticamente uma falha numa base de dados de espera na configuração. Pode implementar o Data Guard Observer com vários observadores com base no número de sites físicos (até três). 

Este observador deve situar-se na infraestrutura que irá suportar o nível de candidatura. O Observador primário deve existir sempre no local onde não se encontra a base de dados principal. Recomendamos cautela na automatização das operações de failover desencadeadas por um Observador da Guarda de Dados. Primeiro certifique-se de que as suas aplicações são concebidas e testadas para fornecer um serviço aceitável quando a base de dados funciona num local separado.

Se a aplicação só puder funcionar localmente, a falha no local secundário deve ser manual. Os ambientes que exijam elevados níveis de disponibilidade (99,99% ou 99,999% de tempo de paragem) devem utilizar uma base de dados local e remota de espera, como mostra o valor anterior. Nestes casos, o parâmetro FastStartFailoverTarget será definido apenas para a base de dados de espera local.

Para todas as aplicações que suportam o acesso a aplicações/bases de dados entre sites, o FastStartFailoverTarget está definido para todas as bases de dados em espera na configuração da Data Guard.

### <a name="active-data-guard"></a>Guarda de Dados Ativos

Oracle Ative Data Guard (ADG) é um superconjunto de capacidades básicas de Guarda de Dados incluído com a Oracle Database Enterprise Edition. Fornece as seguintes funcionalidades adicionadas, que serão utilizadas em toda a implantação da Oracle Exadata:

- Deteção única de corrupção e reparação automática.
- Falha rápida na réplica sincronizada de produção – manual ou automática.
- Descarrega a carga de trabalho de produção para uma leitura aberta sincronizada.
- Atualizações de rolamento de bases de dados e standby. Primeiro remendação usando suporte físico.
- Descarrega cópias de segurança incrementais para ficar em espera.
- Proteção de recuperação de dados de perda de dados zero em qualquer distância sem afetar o desempenho.

O livro branco disponível [https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf](https://www.oracle.com/technetwork/database/availability/dg-adg-technical-overview-wp-5347548.pdf) fornece uma boa visão geral das características anteriores, tal como mostrado na figura seguinte.

:::image type="content" source="media/oracle-high-availability/active-data-guard-features.png" alt-text="Diagrama mostrando uma visão geral das funcionalidades da Oracle's Ative Data Guard.":::

## <a name="backup-recommendations"></a>Recomendações de backup

Certifique-se de fazer o back-up das suas bases de dados. Utilize as funcionalidades de restauro e recuperação para restaurar uma base de dados no mesmo ou noutro sistema, ou para recuperar ficheiros de bases de dados.

É importante criar uma estratégia de recuperação de backup para proteger as bases de dados do Oracle Database Appliance da perda de dados. Tal perda pode resultar de um problema físico com um disco que causa uma falha de uma leitura ou escrita para um ficheiro de disco necessário para executar a base de dados. O erro do utilizador também pode causar a perda de dados. A funcionalidade de backup fornece a capacidade de **pontuar no tempo restaurar a base de dados, a recuperação do Número de Alteração do Sistema (SCN) e a recuperação mais recente**. Pode criar uma política de backup na Interface do Utilizador do Navegador ou a partir da interface de linha de comando.

Estão disponíveis as seguintes opções de backup:

- Voltar ao volume de armazenamento NFS (Área de Recuperação Rápida-FRA- /u98).
- Utilização de ficheiros Azure NetApp SnapCenter – instantâneo.

Processo a considerar:

- Cópias de segurança manuais ou automáticas.
- As cópias de segurança automáticas são escritas para volumes de armazenamento NFS (por exemplo, /u98).
- As cópias de segurança funcionam entre as 00h00 e as 6h00 no fuso horário do sistema de base de dados.
- Períodos de retenção atuais: 7, 15, 30, 45 e 60 dias.

- Recuperar base de dados a partir de uma cópia de segurança armazenada no armazenamento de objetos:
  - Até ao último bom estado conhecido com a menor perda de dados possível.
  - Usando o tempotando especificado.
  - Utilizando o SCN especificado.
  - BackupReport – _utiliza o SCN a partir do relatório de backup em vez de SCN especificado_.

:::image type="content" source="media/oracle-high-availability/customer-db-backup-to-fra.png" alt-text="Diagrama que mostra a base de dados do cliente até FRA (/u98) e não-FRA (/u95).":::

### <a name="backup-policy"></a>Política de cópias de segurança

A política de backup define os detalhes de backup. Quando cria uma política de backup, define o destino para as cópias de segurança da base de dados FRA (localização NFS) e define a janela de recuperação.

Por padrão, o algoritmo de compressão BASIC é usado. Ao utilizar algoritmos de compressão LOW, MEDIUM ou HIGH para a política de backup do Disco ou NFS, existem considerações de licença.

### <a name="backup-levels"></a>Níveis de backup

Especifique o nível de backup quando fizer uma cópia de segurança.

- Nível 0 - Cheio
- Nível 1 - Incremental
- LongTerm/ Archievelog - com exceção da política de retenção de backup, utilize a localização não FRA (por exemplo, /u95).

## <a name="next-steps"></a>Passos seguintes

Saiba como recuperar a base de dados do Oráculo quando ocorrer uma falha:

> [!div class="nextstepaction"]
> [Recupere a sua base de dados Oráculo na Infraestrutura Azure BareMetal](oracle-high-availability-recovery.md)
