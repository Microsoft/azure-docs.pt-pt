---
title: Base de Dados Azure para PostgreSQL - Servidor Flexível
description: Fornece uma visão geral da Base de Dados Azure para PostgreSQL - Servidor Flexível.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 03/03/2021
ms.openlocfilehash: d36dcfccb6843947991b2548af6dd98b81adb40e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049513"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>Base de Dados Azure para PostgreSQL - Servidor Flexível

[A azure Database for PostgreSQL](../overview.md) powered by the PostgreSQL community edition está disponível em três modos de implementação:

- [Servidor Único](../overview-single-server.md)
- Servidor Flexível (Pré-visualização)
- Hyperscale (Citus)

Neste artigo, forneceremos uma visão geral e introdução aos conceitos fundamentais do modelo flexível de implementação do servidor.

> [!IMPORTANT]
> Azure Database for PostgreSQL - Flexible Server está em pré-visualização

## <a name="overview"></a>Descrição Geral

A Azure Database for PostgreSQL - Flexible Server é um serviço de base de dados totalmente gerido projetado para fornecer mais controlo granular e flexibilidade sobre funções de gestão de bases de dados e configurações de configuração. Em geral, o serviço proporciona mais flexibilidade e personalizações de configuração do servidor com base nos requisitos do utilizador. A arquitetura flexível do servidor permite que os utilizadores collocam o motor de base de dados com o nível do cliente para uma latência mais baixa, escolha alta disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. Os servidores flexíveis também fornecem controlos de otimização de custos melhores com a capacidade de parar/iniciar o seu servidor e nível de computação retável que é ideal para cargas de trabalho que não necessitam de capacidade de computação completa continuamente. O serviço suporta atualmente a versão comunitária do PostgreSQL 11 e 12. O serviço está atualmente em pré-visualização, disponível hoje em grande variedade de regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/services/)

![Servidor Flexível - Visão geral](./media/overview/overview-flexible-server.png)


Servidores flexíveis são os mais adequados para

- Desenvolvimentos de aplicações que requerem melhor controlo e personalização.
- Zona redundante alta disponibilidade
- Janelas de manutenção geridas
  
## <a name="high-availability"></a>Elevada disponibilidade

O modelo flexível de implementação do servidor foi concebido para suportar uma elevada disponibilidade dentro de uma única zona de disponibilidade e em várias zonas de disponibilidade. A arquitetura separa o cálculo e o armazenamento. O motor de base de dados funciona numa máquina virtual Linux, enquanto os ficheiros de dados residem no armazenamento do Azure. O armazenamento mantém três cópias sincronizadas localmente redundantes dos ficheiros de base de dados que garantem a durabilidade dos dados.

Durante eventos de failover planeados ou não planeados, se o servidor se desligar, o serviço mantém uma elevada disponibilidade dos servidores utilizando o seguinte procedimento automatizado:

1. É a provisionado um novo computação Linux VM.
2. O armazenamento com ficheiros de dados está mapeado para a nova Máquina Virtual
3. O motor de base de dados PostgreSQL é colocado online na nova Máquina Virtual.

A imagem abaixo mostra a transição para VM e falha de armazenamento.

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="Servidor flexível - VM e falhas de armazenamento":::

Se a zona redundante de alta disponibilidade for configurada, as disposições de serviço e mantém um servidor de espera quente em toda a zona de disponibilidade dentro da mesma região de Azure. As alterações de dados no servidor de origem são sincronizadas replicadas no servidor de espera para garantir a perda de dados zero. Com a zona redundante alta disponibilidade, uma vez que o evento de failover planeado ou não planeado é desencadeado, o servidor de espera entra imediatamente em linha e está disponível para processar transações recebidas. Isto permite a resiliência do serviço da falha da zona de disponibilidade dentro de uma região do Azure que suporta múltiplas zonas de disponibilidade, como mostrado na imagem abaixo.

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Zona redundante alta disponibilidade":::

 Consulte [o documento de alta disponibilidade](./concepts-high-availability.md) para mais detalhes.

## <a name="automated-patching-with-managed-maintenance-window"></a>Remendação automatizada com janela de manutenção gerida

O serviço executa patching automatizado do hardware subjacente, sistema operativo e motor de base de dados. O patching inclui atualizações de segurança e software. Para o motor PostgreSQL, as atualizações de versão menores também estão incluídas como parte do lançamento de manutenção planeado. Os utilizadores podem configurar o calendário de remendos para serem geridos pelo sistema ou definir o seu horário personalizado. Durante o programa de manutenção, o patch é aplicado e o servidor pode ter de ser reiniciado como parte do processo de remendação para completar a atualização. Com o horário personalizado, os utilizadores podem tornar o seu ciclo de remendos previsível e escolher uma janela de manutenção com o mínimo impacto para o negócio. Em geral, o serviço segue o horário de lançamento mensal como parte da integração e libertação contínuas.

## <a name="automatic-backups"></a>Backups automáticos

O serviço de servidor flexível cria automaticamente cópias de segurança do servidor e armazena-as no utilizador configurado localmente na zona redundante (ZRS). As cópias de segurança podem ser utilizadas para restaurar o seu servidor em qualquer ponto no tempo dentro do período de retenção de backup. O período de retenção de backup predefinido é de sete dias. A retenção pode ser configurada opcionalmente até 35 dias. Todas as cópias de segurança são encriptadas através da encriptação AES de 256 bits. Consulte [backups](./concepts-backup-restore.md) para mais detalhes.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajuste o desempenho e dimensione em segundos

O serviço de servidor flexível está disponível em três níveis de cálculo: Burstable, General Purpose e Memory Optimized. O nível Burstable é o mais adequado para o desenvolvimento de baixo custo e cargas de trabalho de baixa concordância que não necessitam de capacidade computacional completa continuamente. O Objetivo Geral e a Memória Otimizados são mais adequados para cargas de trabalho de produção que requerem alta concordância, escala e desempenho previsível. Pode construir a sua primeira aplicação numa pequena base de dados por alguns dólares por mês e, em seguida, ajustar a balança de forma perfeita para atender às necessidades da sua solução.

## <a name="stopstart-server-to-lower-tco"></a>Servidor stop/start para baixar o TCO

O serviço de servidor flexível permite-lhe parar e iniciar o servidor a pedido para baixar o seu TCO. A faturação do nível do cálculo é interrompida imediatamente quando o servidor é parado. Isto pode permitir-lhe ter poupanças significativas de custos durante o desenvolvimento, teste e para cargas de trabalho previsíveis de produção limitadas ao tempo. O servidor permanece no estado de paragem por sete dias, a menos que seja reiniciado mais cedo.

## <a name="enterprise-grade-security"></a>Segurança de nível empresarial

O serviço de servidor flexível utiliza o módulo criptográfico validado FIPS 140-2 para encriptação de armazenamento de dados em repouso. Os dados, incluindo cópias de segurança e ficheiros temporários criados durante a execução de consultas são encriptados. O serviço utiliza a cifra AES de 256 bits incluída na encriptação de armazenamento Azure, e as teclas podem ser geridas pelo sistema (padrão). O serviço encripta dados em movimento com segurança da camada de transporte (SSL/TLS) aplicada por padrão. O serviço aplica e suporta apenas as versões TLS 1.2.

Servidores flexíveis permitem o acesso privado completo aos servidores utilizando a rede virtual Azure (integração VNet). Os servidores da rede virtual Azure só podem ser alcançados e conectados através de endereços IP privados. Com a integração do VNet, o acesso público é negado e os servidores não podem ser alcançados usando pontos finais públicos.

## <a name="monitoring-and-alerting"></a>Monitorização e alertas

O serviço de servidor flexível está equipado com funcionalidades de monitorização e alerta de desempenho incorporadas. Todas as métricas Azure têm uma frequência de um minuto, e cada métrica fornece 30 dias de história. Pode configurar alertas sobre as métricas. O serviço expõe as métricas do servidor do anfitrião para monitorizar a utilização dos recursos e permite configurar registos de consulta lenta. Utilizando estas ferramentas, pode otimizar rapidamente as suas cargas de trabalho e configurar o seu servidor para um melhor desempenho.

## <a name="azure-regions"></a>Regiões do Azure

Uma das vantagens de executar a sua carga de trabalho em Azure é o seu alcance global. O servidor flexível está disponível hoje nas regiões de Azure seguintes:

| Region | Disponibilidade | Zona redundante HA | 
| --- | --- | --- |
| Europa Ocidental | :heavy_check_mark: | :heavy_check_mark: |
| Europa do Norte | :heavy_check_mark: | :heavy_check_mark: |
| Sul do Reino Unido | :heavy_check_mark: | :heavy_check_mark: | 
| E.U.A. Leste 2 | :heavy_check_mark: | :heavy_check_mark: |
| E.U.A. Oeste 2 | :heavy_check_mark: | :heavy_check_mark: |
| E.U.A. Central | :heavy_check_mark: | :heavy_check_mark: | 
| E.U.A. Leste | :heavy_check_mark: | :heavy_check_mark: | 
| Sudeste Asiático | :heavy_check_mark: | :heavy_check_mark: |
| Leste do Japão | :heavy_check_mark: | :heavy_check_mark: | 

Continuamos a adicionar novas regiões. 

## <a name="migration"></a>Migração

O serviço executa a versão comunitária do PostgreSQL. Isto permite a compatibilidade total da aplicação e requer um custo mínimo de refactor para migrar uma aplicação existente desenvolvida no motor PostgreSQL para o Flexible Server. 

- **Despejo e Restauro** – Para migrações offline, onde os utilizadores podem pagar algum tempo de inatividade, despejar e restaurar usando ferramentas comunitárias como pg_dump e pg_restore podem fornecer uma forma mais rápida de migrar. Consulte [a Migração utilizando o despejo e restaure-o](../howto-migrate-using-dump-and-restore.md) para obter mais detalhes.
- **Serviço de Migração de Bases de Dados Azure** – Para migrações perfeitas e simplificadas para servidor flexível com tempo mínimo de inatividade, o Serviço de Migração da Base de Dados Azure pode ser alavancado. Ver [DMS via portal](../../dms/tutorial-postgresql-azure-postgresql-online-portal.md) e [DMS via CLI](../../dms/tutorial-postgresql-azure-postgresql-online.md). Pode migrar da sua Base de Dados Azure para PostgreSQL - Servidor Único para Servidor Flexível. Consulte este [artigo do DMS](../../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) para mais detalhes.

## <a name="next-steps"></a>Passos seguintes

Agora que leu uma introdução à Base de Dados Azure para o modo de implementação flexível do servidor Pós-SQL, está pronto para criar o seu primeiro servidor: [Criar uma Base de Dados Azure para PostgreSQL - Servidor Flexível utilizando o portal Azure](./quickstart-create-server-portal.md)