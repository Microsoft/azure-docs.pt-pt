---
title: Visão geral da gestão do Azure - Azure Governance
description: Panorâmica das áreas de gestão das aplicações e recursos da Azure com ligações aos conteúdos em ferramentas de gestão Azure.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99593930"
---
# <a name="what-are-the-azure-management-areas"></a>Quais são as áreas de Gestão Azure?

A governação em Azure é um aspeto da Gestão Azure. Este artigo abrange as diferentes áreas de gestão para implantação e manutenção dos seus recursos em Azure.

Gestão refere-se aos processos e tarefas necessários para manter as suas aplicações empresariais e os recursos que suportam esses processos e tarefas. A Azure tem muitos serviços e ferramentas que trabalham em conjunto para fornecer uma gestão completa. Estes serviços não são apenas para recursos em Azure, mas também em outras nuvens e no local. Compreender as diferentes ferramentas e como trabalham em conjunto é o primeiro passo para conceber um ambiente de gestão completo.

O diagrama seguinte ilustra as diferentes áreas de gestão necessárias para manter qualquer aplicação ou recurso. Estas diferentes áreas podem ser consideradas como um ciclo de vida. Cada área é necessária em sucessão contínua ao longo do tempo de vida de um recurso. Este ciclo de vida de recursos começa com a implantação inicial, através de uma operação contínua e, finalmente, quando se aposentado.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Diagrama das disciplinas de Gestão em Azure." border="false":::
   Diagrama que mostra os elementos migratórios, seguros, protegidos, monitores, configures e reger elementos da roda de serviços que apoiam a Gestão e Governação em Azure. A Secure tem a gestão de segurança e a proteção contra ameaças como sub-itens. Protect tem backup e recuperação de desastres como sub-itens. O Monitor tem a monitorização de aplicações, infra e rede, e log Analytics e Diagnósticos como sub-itens. A Configuração tem Configuração, Gestão de Atualização, Automação e Scripting como sub-itens. E o Govern tem a gestão de políticas e a gestão de custos como sub-itens.
:::image-end:::

Nenhum serviço Azure preenche completamente os requisitos de uma determinada área de gestão. Em vez disso, cada um é realizado por vários serviços que trabalham em conjunto. Alguns serviços, como o Application Insights, fornecem funcionalidades de monitorização direcionadas para aplicações web. Outros, como registos do Azure Monitor, dados de gestão de lojas para outros serviços. Esta funcionalidade permite analisar dados de diferentes tipos recolhidos por diferentes serviços.

As secções seguintes descrevem resumidamente as diferentes áreas de gestão e fornecem ligações para conteúdo detalhado sobre os principais serviços do Azure que visam abordar essas áreas.

## <a name="monitor"></a>Monitor

A monitorização é o ato de recolher e analisar dados para auditar o desempenho, saúde e disponibilidade dos seus recursos. Uma estratégia de monitorização eficaz ajuda-o a compreender o funcionamento dos componentes e a aumentar o seu tempo de funcionamento com notificações. Leia uma visão geral da Monitorização que abrange os diferentes serviços utilizados na [Monitorização de aplicações e recursos do Azure.](../azure-monitor/overview.md)

## <a name="configure"></a>Configurar

Configure refere-se à implantação inicial e configuração de recursos e manutenção contínua.
A automatização destas tarefas permite eliminar a redundância, minimizando o seu tempo e esforço e aumentando a sua precisão e eficiência. A [Automatização do Azure](../automation/automation-intro.md) fornece-lhe o conjunto de serviços que lhe permite automatizar as tarefas de configuração. Enquanto os runbooks lidam com automatização de processos, configuração e gestão de atualização ajudam a gerir a configuração.

## <a name="govern"></a>Governação

A governação fornece mecanismos e processos para manter o controlo sobre os recursos e aplicações no Azure. A governação envolve o planeamento das suas iniciativas e a definição de prioridades estratégicas.
A governação no Azure é implementada principalmente com dois serviços. [A Azure Policy](./policy/overview.md) permite-lhe criar, atribuir e gerir definições políticas para impor regras para os seus recursos.
Esta funcionalidade mantém esses recursos em conformidade com os seus padrões corporativos.
[A Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) permite-lhe acompanhar o uso da nuvem e as despesas para os seus recursos Azure e outros fornecedores de nuvem.

## <a name="secure"></a>Proteger

Gerencie a segurança dos seus recursos e dados. Um programa de segurança envolve avaliar ameaças, recolher e analisar dados, e cumprir as suas aplicações e recursos. A monitorização de segurança e a análise de ameaças são fornecidas pelo [Azure Security Center,](../security-center/security-center-introduction.md)que inclui a gestão unificada de segurança e a proteção avançada de ameaças através de cargas de trabalho em nuvem híbrida. Consulte [a Introdução à Segurança Azure](../security/fundamentals/overview.md) para obter informações e orientações abrangentes sobre a garantia de recursos da Azure.

## <a name="protect"></a>Proteger

Proteção refere-se a manter as suas aplicações e dados disponíveis, mesmo com falhas que estão fora do seu controlo. A proteção no Azure é fornecida por dois serviços. O [Azure Backup](../backup/backup-overview.md) garante a cópia de segurança e a recuperação dos seus dados, seja na cloud ou no local. [A Azure Site Recovery](../site-recovery/site-recovery-overview.md) proporciona continuidade de negócios e recuperação imediata durante um desastre.

## <a name="migrate"></a>Migrate

Migração refere-se à transição das cargas de trabalho atualmente em execução no local para a cloud do Azure.
[Azure Migrate](../migrate/migrate-services-overview.md) é um serviço que o ajuda a avaliar a adequação da migração de máquinas virtuais no local para Azure. A Azure Site Recovery migra máquinas virtuais [a partir do local](../site-recovery/migrate-tutorial-on-premises-azure.md) ou dos [Serviços Web da Amazon.](../site-recovery/migrate-tutorial-aws-azure.md) [A Azure Database Migration](../dms/dms-overview.md) ajuda-o na migração de fontes de dados para plataformas de Dados Azure.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a Governação do Azure, consulte estes artigos:

- Ver o centro de [governação do Azure.](./index.yml)
- Ver [Governação no Quadro de Adoção em Nuvem para Azure](/azure/cloud-adoption-framework/govern/)