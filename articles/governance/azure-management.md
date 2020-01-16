---
title: Visão geral do gerenciamento do Azure-governança do Azure
description: Descrição geral das áreas de gestão para aplicações do Azure e de recursos com ligações para conteúdo em ferramentas de gestão do Azure.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: 644f4fc3a04f86426c2eb04b02c29882fa39fb88
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980907"
---
# <a name="overview-of-management-services-in-azure"></a>Visão geral dos serviços de gerenciamento no Azure

Governação no Azure é um aspecto de gestão do Azure. Este artigo aborda as diferentes áreas de gestão para implementar e manter os seus recursos no Azure.

Gestão refere-se aos processos e tarefas necessários para manter as suas aplicações empresariais e os recursos que suportam esses processos e tarefas. O Azure tem muitos serviços e ferramentas que funcionam em conjunto para assegurar uma gestão completa. Estes serviços não são apenas para recursos no Azure, mas também noutras clouds e no local. Compreender as diferentes ferramentas e como elas funcionam em conjunto é a primeira etapa na criação de um ambiente de gestão completa.

O diagrama seguinte ilustra as diferentes áreas de gestão necessárias para manter qualquer aplicação ou recurso. Essas áreas diferentes podem ser consideradas como um ciclo de vida. Cada área é necessário em sucessão contínua ao longo do tempo de vida de um recurso. Este ciclo de vida do recurso começa com a implementação inicial, por meio de operação continuada e, finalmente, quando extinto.

![Disciplinas de gerenciamento no Azure](../monitoring/media/management-overview/management-capabilities.png)

Nenhum serviço do Azure individual preenche completamente os requisitos de uma área de gestão específico. Em vez disso, cada um é realizado por vários serviços, trabalhando em conjunto. Alguns serviços, como o Application Insights, fornecem funcionalidade direcionada de monitorização para aplicações web. Outros, como logs de Azure Monitor, armazenam dados de gerenciamento para outros serviços. Esta funcionalidade permite-lhe analisar os dados de vários tipos recolhidos por diferentes serviços.

As secções seguintes descrevem resumidamente as diferentes áreas de gestão e fornecem ligações para conteúdo detalhado sobre os principais serviços do Azure que visam abordar essas áreas.

## <a name="monitor"></a>Monitorizar

A monitorização é o ato de recolha e análise de dados para o desempenho, o estado de funcionamento e a disponibilidade dos seus recursos de auditoria. Uma estratégia de monitorização eficaz ajuda-o a compreender a operação dos componentes e para aumentar o tempo de atividade com notificações. Leia uma visão geral de monitorização que abrange os diferentes serviços utilizados no [aplicações de monitorização do Azure e recursos](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Configurar

Configurar refere-se para a implementação inicial e a configuração de recursos e a manutenção em curso.
Automatização destas tarefas permite-lhe eliminar a redundância, minimizar o tempo e esforço e aumentar a precisão e eficiência. A [Automatização do Azure](../automation/automation-intro.md) fornece-lhe o conjunto de serviços que lhe permite automatizar as tarefas de configuração. Enquanto os runbooks lidar com a automatização de processos, configuração e gestão de atualizações de auxiliam no gerenciamento de configuração.

## <a name="govern"></a>Governação

A governação fornece mecanismos e processos para manter o controlo sobre os recursos e aplicações no Azure. A governação envolve o planeamento das suas iniciativas e a definição de prioridades estratégicas.
A governação no Azure é implementada principalmente com dois serviços. [O Azure Policy](./policy/overview.md) permite-lhe criar, atribuir e gerir definições de política para impor regras para os seus recursos. Esta funcionalidade mantém esses recursos em conformidade com os seus padrões empresariais. O [Gerenciamento de custos do Azure](../cost-management-billing/cost-management-billing-overview.md) permite que você acompanhe o uso e as despesas da nuvem para seus recursos do Azure e outros provedores de nuvem.

## <a name="secure"></a>Segura

Gerir a segurança dos seus recursos e dados. Um programa de segurança envolve a avaliação de ameaças, recolha e análise de dados e a conformidade das suas aplicações e recursos. Análise de ameaças e monitorização de segurança são fornecidos pela [Centro de segurança do Azure](../security-center/security-center-intro.md), que inclui segurança gestão unificada e proteção avançada contra ameaças entre cargas de trabalho híbrida na cloud. Ver [introdução à segurança do Azure](../security/fundamentals/overview.md) para informações abrangentes e orientações sobre como proteger recursos do Azure.

## <a name="protect"></a>Proteger

Proteção refere-se para manter as suas aplicações e dados disponíveis, mesmo com falhas que estejam fora do seu controle. A proteção no Azure é fornecida por dois serviços. O [Azure Backup](../backup/backup-introduction-to-azure-backup.md) garante a cópia de segurança e a recuperação dos seus dados, seja na cloud ou no local. [O Azure Site Recovery](../site-recovery/site-recovery-overview.md) proporciona continuidade do negócio e recuperação imediata durante um desastre.

## <a name="migrate"></a>Migração

Migração refere-se à transição das cargas de trabalho atualmente em execução no local para a cloud do Azure.
[O Azure Migrate](../migrate/migrate-overview.md) é um serviço que o ajuda a avaliar a adequabilidade de migração de máquinas de virtuais no local para o Azure. O Azure Site Recovery migra máquinas virtuais [locais](../site-recovery/migrate-tutorial-on-premises-azure.md) ou [do Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md). [Migração de base de dados do Azure](../dms/dms-overview.md) ajuda a migrar origens de base de dados para plataformas de dados do Azure.
