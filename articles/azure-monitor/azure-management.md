---
title: Gestão do Azure e Operations Management Suite (OMS) | Microsoft Docs
description: Descrição geral das áreas de gestão para aplicações e recursos do Azure com ligações a conteúdo em ferramentas de gestão do Azure que anteriormente foram agrupadas como o Operations Management Suite (OMS).
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/07/2018
ms.author: bwren
ms.openlocfilehash: b56993b9ad03f2ab50fe3954ab5e8855d0d8bc0f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770022"
---
# <a name="azure-management---monitoring"></a>Gestão do Azure - Monitorização

A monitorização no Azure é um aspeto da Gestão do Azure.  Este artigo descreve resumidamente as diferentes áreas de gestão necessárias para implementar e manter as suas aplicações e recursos no Azure com ligações para documentação que irá ajudá-lo a começar.

## <a name="management-in-azure"></a>Gestão no Azure

Gestão refere-se aos processos e tarefas necessários para manter as suas aplicações empresariais e os recursos que suportam esses processos e tarefas.  O Azure tem vários serviços e ferramentas que funcionam em conjunto para assegurar uma gestão completa não só das suas aplicações em execução no Azure, como também noutras clouds e no local.  Compreender as diferentes ferramentas disponíveis e como pode utilizá-las em conjunto num vasto leque de cenários de gestão é o primeiro passo na conceção de um ambiente de gestão completo.

O diagrama seguinte ilustra as diferentes áreas de gestão necessárias para manter qualquer aplicação ou recurso.  Pode pensar nestas diferentes áreas em termos de um ciclo de vida em que cada um é necessário em sucessão contínua ao longo do tempo de vida de um recurso.  Começa com a implementação inicial, passando pela respetivo funcionamento contínuo, até que, por fim, é descontinuado.

![Capacidades de gestão](media/management-overview/management-capabilities.png)


As secções seguintes descrevem resumidamente as diferentes áreas de gestão e fornecem ligações para conteúdo detalhado sobre os principais serviços do Azure que visam abordar essas áreas.

## <a name="monitor"></a>Monitorizar
A monitorização é o ato de recolha e análise de dados para determinar o desempenho, o estado de funcionamento e a disponibilidade da sua aplicação empresarial e dos recursos de que esta depende. Uma estratégia de monitorização eficaz irá ajudá-lo a compreender o funcionamento detalhado dos diferentes componentes da sua aplicação e a aumentar o tempo de atividade ao notificá-lo proativamente dos problemas críticos, para que possa resolvê-los antes que se tornem realmente graves. A monitorização no Azure é principalmente fornecida pelo [Azure Monitor](../azure-monitor/overview.md) que fornece os meios comuns para armazenar dados de monitorização, várias origens de dados para a recolha de dados das diferentes camadas que suportam a sua aplicação e funcionalidades para analisar e responder aos dados recolhidos.

## <a name="configure"></a>Configurar
Configurar refere-se à implementação inicial e à configuração dos recursos e aplicações e à respetiva manutenção contínua com correções de erros e atualizações.  A automatização destas tarefas através de scripts e políticas permite-lhe eliminar a redundância, minimizar o tempo e esforço que despende e aumentar a precisão e eficiência.  A [Automatização do Azure](../automation/automation-intro.md) fornece-lhe o conjunto de serviços que lhe permite automatizar as tarefas de configuração.  Para além dos runbooks para a automatização de processos, este serviço fornece a gestão de atualizações e configuração, o que o ajuda a gerir a configuração através de políticas e a identificar e implementar atualizações.

## <a name="govern"></a>Governação
A governação fornece mecanismos e processos para manter o controlo sobre os recursos e aplicações no Azure.  A governação envolve o planeamento das suas iniciativas e a definição de prioridades estratégicas.  A governação no Azure é implementada principalmente com dois serviços.  O [Azure Policy](../governance/policy/overview.md) permite-lhe criar, atribuir e gerir definições de política que impõem diferentes regras e ações aos recursos, para que esses recursos mantenham a conformidade face às normas empresariais e aos contratos de nível de serviço. O [Azure Cost Management da Cloudyn](../cost-management/overview.md) permite-lhe controlar a utilização da cloud e as despesas dos seus recursos do Azure e outros fornecedores de cloud, incluindo AWS e Google.

## <a name="secure"></a>Proteger
Gerir a segurança dos seus recursos, aplicações e dados passa por combinar a avaliação de ameaças, a recolha e análise de dados de segurança e garantir que os seus recursos e aplicações são concebidos e configurados com toda a segurança.  A monitorização da segurança e a análise de ameaças são fornecidas pelo [Centro de Segurança do Azure](../security-center/security-center-intro.md), o que inclui gestão de segurança unificada e proteção avançada contra ameaças entre cargas de trabalho na cloud híbrida.  Também deve ver a [Introdução à Segurança do Azure](../security/azure-security.md) para obter informações abrangentes sobre segurança no Azure e orientação sobre como configurar recursos do Azure com segurança.


## <a name="protect"></a>Proteger
Proteção passa por se certificar de que os seus dados e aplicações estão sempre disponíveis, mesmo em caso de falhas que estejam para além do seu controlo.  A proteção no Azure é fornecida por dois serviços.  O [Azure Backup](../backup/backup-introduction-to-azure-backup.md) garante a cópia de segurança e a recuperação dos seus dados, seja na cloud ou no local.    O [Azure Site Recovery](../site-recovery/site-recovery-overview.md) garante a elevada disponibilidade da sua aplicação ao assegurar a continuidade do negócio e a recuperação imediata em caso de desastre.

## <a name="migrate"></a>Migrar 
Migração refere-se à transição das cargas de trabalho atualmente em execução no local para a cloud do Azure.  O [Azure Migrate](../migrate/migrate-overview.md) é um serviço que o ajuda a avaliar a adequação da migração, incluindo o dimensionamento com base no desempenho e estimativas de custos, de máquinas virtuais no local para o Azure.  O Azure Site Recovery pode ajudá-lo a fazer a migração real de máquinas virtuais a partir [do local](../site-recovery/migrate-tutorial-on-premises-azure.md) ou [do Amazon Web Services](../site-recovery/migrate-tutorial-aws-azure.md).  O [Azure Database Migration](../dms/dms-overview.md) irá ajudá-lo a migrar várias origens de dados para as Plataformas de Dados do Azure.

