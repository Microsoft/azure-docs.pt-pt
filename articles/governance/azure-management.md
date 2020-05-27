---
title: Visão geral da Gestão Azure - Governança Azure
description: Visão geral das áreas de gestão das aplicações e recursos do Azure com ligações a conteúdos em ferramentas de gestão Azure.
ms.date: 05/22/2020
ms.topic: overview
ms.openlocfilehash: e293ab743b7c3b49a13cab97200c136a9bd044f5
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828152"
---
# <a name="overview-of-management-services-in-azure"></a>Visão geral dos serviços de Gestão em Azure

A governação em Azure é um aspeto da Azure Management. Este artigo abrange as diferentes áreas de gestão para a implantação e manutenção dos seus recursos no Azure.

Gestão refere-se aos processos e tarefas necessários para manter as suas aplicações empresariais e os recursos que suportam esses processos e tarefas. A Azure tem muitos serviços e ferramentas que trabalham em conjunto para fornecer uma gestão completa. Estes serviços não são apenas para recursos em Azure, mas também em outras nuvens e no local. Compreender as diferentes ferramentas e como trabalham em conjunto é o primeiro passo para a conceção de um ambiente de gestão completo.

O diagrama seguinte ilustra as diferentes áreas de gestão necessárias para manter qualquer aplicação ou recurso. Estas diferentes áreas podem ser consideradas como um ciclo de vida. Cada área é necessária em sucessão contínua ao longo do tempo de vida de um recurso. Este ciclo de vida de recursos começa com a implantação inicial, através da operação contínua, e finalmente quando aposentado.

:::image type="content" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Disciplinas de Gestão em Azure" border="false":::

Nenhum serviço Azure preenche completamente os requisitos de uma determinada área de gestão. Em vez disso, cada um é realizado por vários serviços trabalhando em conjunto. Alguns serviços, como o Application Insights, fornecem uma funcionalidade de monitorização direcionada para aplicações web. Outros, como os registos do Azure Monitor, armazenam dados de gestão de outros serviços. Esta funcionalidade permite-lhe analisar dados de diferentes tipos recolhidos por diferentes serviços.

As secções seguintes descrevem resumidamente as diferentes áreas de gestão e fornecem ligações para conteúdo detalhado sobre os principais serviços do Azure que visam abordar essas áreas.

## <a name="monitor"></a>Monitorizar

A monitorização é o ato de recolher e analisar dados para auditar o desempenho, saúde e disponibilidade dos seus recursos. Uma estratégia de monitorização eficaz ajuda-o a compreender o funcionamento dos componentes e a aumentar o seu tempo de funcionamento com notificações. Leia uma visão geral da Monitorização que abrange os diferentes serviços utilizados na Monitorização de [aplicações e recursos do Azure.](../monitoring/monitoring-overview.md)

## <a name="configure"></a>Configurar

Configurar refere-se à implementação e configuração inicial de recursos e manutenção contínua.
A automatização destas tarefas permite eliminar o despedimento, minimizando o seu tempo e esforço e aumentando a sua precisão e eficiência. A [Automatização do Azure](../automation/automation-intro.md) fornece-lhe o conjunto de serviços que lhe permite automatizar as tarefas de configuração. Enquanto os livros de execução lidam com a automatização do processo, a configuração e a gestão da atualização ajudam na gestão da configuração.

## <a name="govern"></a>Governação

A governação fornece mecanismos e processos para manter o controlo sobre os recursos e aplicações no Azure. A governação envolve o planeamento das suas iniciativas e a definição de prioridades estratégicas.
A governação no Azure é implementada principalmente com dois serviços. [A Política Azure](./policy/overview.md) permite-lhe criar, atribuir e gerir definições políticas para impor regras para os seus recursos.
Esta funcionalidade mantém esses recursos em conformidade com os seus padrões corporativos.
[A Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) permite-lhe acompanhar o uso e as despesas em nuvem para os seus recursos Azure e outros fornecedores de nuvem.

## <a name="secure"></a>Proteger

Gerencie a segurança dos seus recursos e dados. Um programa de segurança envolve avaliar ameaças, recolher e analisar dados e o cumprimento das suas aplicações e recursos. A monitorização de segurança e a análise de ameaças são fornecidas pelo [Azure Security Center,](../security-center/security-center-intro.md)que inclui a gestão unificada de segurança e a proteção avançada de ameaças através de cargas de trabalho híbridas em nuvem. Consulte [a Introdução à Segurança Azure](../security/fundamentals/overview.md) para obter informações e orientações abrangentes sobre a segurança dos recursos do Azure.

## <a name="protect"></a>Proteger

A proteção refere-se a manter as suas aplicações e dados disponíveis, mesmo com interrupções que estão fora do seu controlo. A proteção no Azure é fornecida por dois serviços. O [Azure Backup](../backup/backup-introduction-to-azure-backup.md) garante a cópia de segurança e a recuperação dos seus dados, seja na cloud ou no local. [A Azure Site Recovery](../site-recovery/site-recovery-overview.md) proporciona continuidade de negócios e recuperação imediata durante um desastre.

## <a name="migrate"></a>Migrar

Migração refere-se à transição das cargas de trabalho atualmente em execução no local para a cloud do Azure.
[A Azure Migrate](../migrate/migrate-overview.md) é um serviço que o ajuda a avaliar a adequação da migração das máquinas virtuais no local para o Azure. A Azure Site Recovery migra máquinas virtuais [a partir de instalações](../site-recovery/migrate-tutorial-on-premises-azure.md) ou de [Serviços Web da Amazon.](../site-recovery/migrate-tutorial-aws-azure.md) [A Migração](../dms/dms-overview.md) de Bases de Dados Azure ajuda-o em fontes de base de dados migratórias para plataformas De Dados Azure.
