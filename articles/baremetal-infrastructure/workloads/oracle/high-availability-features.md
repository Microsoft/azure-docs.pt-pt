---
title: Funcionalidades de alta disponibilidade para o Oracle em Azure BareMetal
description: Saiba mais sobre as funcionalidades disponíveis na BareMetal para uma base de dados Oracle.
ms.topic: overview
ms.subservice: workloads
ms.date: 04/16/2021
ms.openlocfilehash: b27dc4b857d553be791528cbd91aee70b2294a92
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600221"
---
# <a name="high-availability-features-for-oracle-on-azure-baremetal"></a>Funcionalidades de alta disponibilidade para o Oracle em Azure BareMetal

Neste artigo, vamos olhar para as principais características de alta disponibilidade e recuperação de desastres da Oracle.

A Oracle oferece muitas funcionalidades para construir uma plataforma resiliente para executar bases de dados oracle. Embora nenhuma característica única forneça cobertura para cada tipo de falha, combinar tecnologias de forma em camadas cria um sistema altamente disponível. Nem todas as funcionalidades são necessárias para manter a disponibilidade. Mas combinar estratégias dá-lhe a melhor proteção contra a variedade de falhas que ocorrem. 

## <a name="flashback-database"></a>Base de Dados de Flashback

A funcionalidade [Flashback Database](https://docs.oracle.com/en/database/oracle/oracle-database/21/rcmrf/FLASHBACK-DATABASE.html#GUID-584AC79A-40C5-45CA-8C63-DED3BE3A4511) é apresentada na Oracle Database Enterprise Edition. A Base de Dados do Flashback rebobina a base de dados para um ponto específico no tempo. Esta característica difere de uma recuperação pontual do [Recovery Manager (RMAN)](https://docs.oracle.com/en/cloud/paas/db-backup-cloud/csdbb/performing-general-restore-and-recovery-operations.html) na medida em que recua do tempo atual, em vez de ventos de frente após uma restauração. O resultado é que a Base de Dados do Flashback dá tempos de conclusão muito mais rápidos.
 
Pode utilizar esta funcionalidade ao lado [da Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/19/sbydb/preface.html#GUID-B6209E95-9DA8-4D37-9BAD-3F000C7E3590). A Base de Dados flashback permite que um administrador de base de dados reestem uma base de dados falhada de volta a uma configuração da Data Guard sem uma restauração e recuperação completas do RMAN. Esta funcionalidade permite-lhe restaurar a capacidade de recuperação de desastres (e quaisquer benefícios de relatórios e backup descarregados com a Ative Data Guard) muito mais rapidamente.
 
Pode utilizar esta funcionalidade em vez de um refaso atrasado no tempo na base de dados de espera. Uma base de dados de espera pode ser intermitente até um ponto antes de o problema surgir.
 
A Base de Dados Oráculo mantém registos de flashbacks na área de recuperação rápida (FRA). Estes troncos são separados dos registos de redo e requerem mais espaço dentro do FRA. Por predefinição, são mantidas 24 horas de registos de flashback, mas pode alterar esta definição de acordo com os seus requisitos.

## <a name="oracle-real-application-clusters"></a>Clusters de aplicações reais da Oracle

[O Oracle Real Application Clusters (RAC)](https://docs.oracle.com/en/database/oracle/oracle-database/19/racad/introduction-to-oracle-rac.html#GUID-5A1B02A2-A327-42DD-A1AD-20610B2A9D92) permite que vários servidores interligados apareçam como um serviço de base de dados para utilizadores finais e aplicações. Esta funcionalidade remove muitos pontos de falha e é uma solução reconhecida de alta disponibilidade ativa/ativa para bases de dados oracle.

Como mostrado na seguinte figura da [Visão Geral de Alta Disponibilidade e Boas Práticas](https://docs.oracle.com/en/database/oracle/oracle-database/19/haovw/ha-features.html)da Oracle, uma única base de dados RAC é apresentada à camada de aplicação. As aplicações ligam-se ao ouvinte scan, que direciona o tráfego para uma determinada caixa de dados. O RAC controla o acesso de várias instâncias para manter a consistência dos dados em nós de computação separados.

![Diagrama mostrando uma visão geral da arquitetura do Oracle RAC.](media/oracle-high-availability/oracle-real-application-clusters.png)

Se uma instância falhar, o serviço continua em todas as outras instâncias restantes. Cada base de dados implantada na solução será numa configuração RAC de n+1, onde n é o poder mínimo de processamento necessário para suportar o serviço.

Os serviços de base de dados da Oracle são utilizados para permitir que as ligações falhem entre nós quando um caso falha de forma transparente. Tais falhas podem ser planeadas ou não planeadas. Trabalhando com a aplicação (eventos de notificação de aplicação rápida), quando uma instância é tornada indisponível, o serviço é transferido para um nó sobrevivente. O serviço desloca-se para um nó especificado na configuração de serviço como preferido ou disponível.

Outra característica fundamental dos serviços da Oracle Database é apenas iniciar um serviço dependendo do seu papel. Esta função é utilizada quando existe uma falha da Data Guard. Todos os padrões implementados usando a Data Guard são necessários para ligar um serviço de base de dados a uma função de Data Guard.

Por exemplo, poderiam ser criados dois serviços, MY \_ DB \_ APP e MY \_ DB \_ AS. O serviço MY \_ DB \_ APP só é iniciado quando a instância da base de dados é iniciada com o papel de Guarda de Dados de PRIMARY. O \_ meu DB \_ AS só é iniciado quando o papel de Data Guard é PHYSICAL \_ STANDBY. Esta configuração permite que as aplicações aplindam para o \_ serviço APP, ao mesmo tempo que reporta, que pode ser descarregado para Ative Standby e apontado para o \_ serviço AS.

## <a name="oracle-data-guard"></a>Proteção de Dados Oracle

Com a Data Guard, pode manter uma cópia idêntica de uma base de dados em hardware físico separado. Idealmente, esse hardware deve ser geograficamente removido da base de dados primária. A Data Guard não coloca limites à distância, embora a distância tenha influência nos modos de proteção. O aumento da distância adiciona latência entre os sites, o que pode fazer com que algumas opções (como a replicação sincronizada) deixem de ser viáveis.

A Data Guard oferece vantagens em relação à replicação ao nível do armazenamento:

- Como a replicação é consciente da base de dados, apenas o tráfego relevante é replicado.
- Certas cargas de trabalho podem gerar entrada/saída elevada em espaços de mesa temporários, que não são necessários em espera e por isso não são replicados.
- A validação nos blocos replicados ocorre na base de dados de espera, por isso as corrupçãos físicas na base de dados primária não são replicadas na base de dados de espera.
- Previne corrupçãos lógicas intra-bloqueios e corrupçãos de escrita perdida. Também elimina o risco de erros cometidos pelos administradores de armazenamento de se replicarem para o standby.
O redo pode ser adiado por um período pré-determinado, pelo que os erros do utilizador não são imediatamente replicados para o standby.

## <a name="azure-netapp-files-snapshots"></a>Fotos de ficheiros Azure NetApp

A solução de armazenamento NetApp Files utilizada no BareMetal permite-lhe criar instantâneos de volumes. As imagens permitem reverter rapidamente um sistema de ficheiros para um ponto específico no tempo. As tecnologias snapshot permitem tempo de recuperação de tempo (RTO) vezes que são uma fração do tempo necessário para restaurar uma cópia de segurança da base de dados.

A funcionalidade Snapshot para bases de dados oracle está disponível através do Azure NetApp SnapCenter. O SnapCenter permite instantâneos para backup, o SnapVault dá-lhe abóbada offline e o Snap Clone permite a restauração do autosserviço e outras operações.

## <a name="recovery-manager"></a>Gestor de Recuperação

Recovery Manager (RMAN) é o utilitário preferido para a tomada de backups de bases de dados físicas. A RMAN interage com o ficheiro de controlo da base de dados (ou um catálogo de recuperação centralizado) para proteger os vários componentes fundamentais da base de dados, incluindo:

- Ficheiros de dados de bases de dados
- Registos de redo arquivados
- Ficheiros de controlo de bases de dados
- Ficheiros de inicialização da base de dados (spfile)

A RMAN permite-lhe fazer cópias de dados quentes ou frias. Pode utilizar estas cópias de segurança para criar bases de dados de espera ou para duplicar bases de dados para clonar ambientes. A RMAN também tem uma função de validação de restauro. Esta função lê um conjunto de backup e determina se pode usá-la para recuperar a base de dados para um determinado ponto no tempo.

Como a RMAN é uma utilidade fornecida pela Oracle, lê a estrutura interna dos ficheiros de base de dados. Isto permite-lhe executar controlos físicos e lógicos de corrupção durante operações de backup e restauro. Também pode recuperar os ficheiros de dados da base de dados e restaurar os ficheiros de dados individuais e os espaços de mesa para um ponto específico no tempo. Estas são as vantagens que a RMAN oferece sobre as fotos de armazenamento. Os backups RMAN fornecem uma última linha de defesa contra a perda total de dados quando não pode usar instantâneos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre opções e recomendações para otimizar a proteção e desempenho da Oracle na Infraestrutura BareMetal:

> [!div class="nextstepaction"]
> [Opções para servidores de infraestruturas Oracle BareMetal](options-considerations-high-availability.md)
