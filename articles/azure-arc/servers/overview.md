---
title: Azure Arc para servidores (pré-visualização) Visão geral
description: Aprenda a usar o Azure Arc para servidores para gerir máquinas que estão hospedadas fora do Azure como se fosse um recurso Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: automação azul, DSC, powershell, configuração de estado desejada, gestão de atualizações, rastreio de alterações, inventário, livros de execução, pitão, gráfico, híbrido
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 95a01db7d4d889df4695390bfd0d01510d83a817
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648055"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>O que é O Arco Azure para servidores (pré-visualização)

O Azure Arc para servidores (pré-visualização) permite-lhe gerir as suas máquinas Windows e Linux alojadas fora do Azure na sua rede corporativa ou outro fornecedor de nuvem, à semelhança da forma como gere as máquinas virtuais nativas do Azure. Quando uma máquina híbrida está ligada ao Azure, torna-se uma máquina conectada e é tratada como um recurso em Azure. Cada máquina conectada tem um ID de recursos, é gerida como parte de um grupo de recursos dentro de uma subscrição, e beneficia de construções padrão do Azure, como a Política Azure e etiquetas de aplicação.

Para oferecer esta experiência com as suas máquinas híbridas alojadas fora do Azure, o agente Azure Connected Machine precisa de ser instalado em cada máquina que planeia ligar ao Azure. Este agente não fornece qualquer outra funcionalidade e não substitui o agente Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proativamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-la utilizando livros de execução automation ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)

>[!NOTE]
>Este lançamento de pré-visualização destina-se a fins de avaliação e recomendamos que não gere máquinas de produção críticas.
>

## <a name="supported-scenarios"></a>Cenários suportados

O Azure Arc para servidores (pré-visualização) suporta os seguintes cenários com máquinas conectadas:

- Atribuir [configurações de hóspedes da Política Azure](../../governance/policy/concepts/guest-configuration.md) utilizando a mesma experiência que a atribuição de políticas para máquinas virtuais Azure.
- Registo de dados recolhidos pelo agente Log Analytics, armazenados no espaço de trabalho do Log Analytics, a máquina está registada. Os dados de registo da máquina híbrida contêm agora propriedades específicas da máquina, como um ID de recurso, que pode ser usado para apoiar o acesso ao registo [de contexto de recursos.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

## <a name="supported-regions"></a>Regiões suportadas

Com o Azure Arc para servidores (pré-visualização), apenas algumas regiões são suportadas:

- WestUS2
- Europa Ocidental
- Ásia Ocidental

Na maioria dos casos, a localização que seleciona quando criar o script de instalação deve ser a região de Azure geograficamente mais próxima da localização da sua máquina. Os dados em repouso serão armazenados dentro da geografia Azure que contenha a região que especifica, o que também pode afetar a sua escolha de região se tiver requisitos de residência de dados. Se a região azure a que a sua máquina estiver ligada for afetada por uma paragem, a máquina conectada não é afetada, mas as operações de gestão utilizando o Azure podem não conseguir completar. Para resiliência em caso de paragem regional, se tiver vários locais que fornecem um serviço geograficamente redundante, o melhor é ligar as máquinas em cada local a uma região azure diferente.

### <a name="agent-status"></a>Estado do agente

O agente Máquina Conectada envia uma mensagem regular de batimentocardíaco ao serviço a cada 5 minutos. Se o serviço deixar de receber estas mensagens de batimentocardíaco de uma máquina, essa máquina é considerada offline e o estado será automaticamente alterado para **Desligado** no portal dentro de 15 a 30 minutos. Ao receber uma mensagem de batimento cardíaco subsequente do agente Máquina Conectada, o seu estado será automaticamente alterado para **Connected**.

## <a name="next-steps"></a>Próximos passos

Antes de avaliar ou ativar o Arc para servidores (pré-visualização) através de várias máquinas híbridas, reveja o artigo de visão geral do [agente Connected Machine](agent-overview.md) para entender o que é necessário, detalhes técnicos sobre o agente e métodos de implementação.
