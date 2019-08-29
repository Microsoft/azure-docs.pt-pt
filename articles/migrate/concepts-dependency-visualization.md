---
title: Visualização de dependência em migrações para Azure | Microsoft Docs
description: Fornece uma visão geral dos cálculos de avaliação no serviço de avaliação do servidor nas migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: hamusa
ms.openlocfilehash: 4b6a140ec428ce3b053c41074f02f65f19b8dc72
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102896"
---
# <a name="dependency-visualization"></a>Visualização de dependência

Azure Migrate: A avaliação do servidor avalia grupos de computadores locais para migração para o Azure. Você pode usar a funcionalidade de visualização de dependência na avaliação do servidor para criar grupos. Este artigo fornece informações sobre esse recurso.

> [!NOTE]
> A funcionalidade de visualização de dependência não está disponível no Azure governamental.

## <a name="overview"></a>Descrição geral

A visualização de dependência na avaliação do servidor permite que você crie grupos de alta confiança para avaliações de migração. Usando a visualização de dependência, você pode exibir as dependências de rede de computadores e identificar os computadores relacionados que precisam ser migrados juntos para o Azure. Essa funcionalidade é útil em cenários em que você não está totalmente ciente dos computadores que constituem seu aplicativo e precisam ser migrados juntos para o Azure.

## <a name="before-you-start"></a>Antes de começar

- Certifique-se de ter [criado](how-to-add-tool-first-time.md) um projeto de migrações para Azure.
- Se você já tiver criado um projeto, certifique-se de ter [adicionado](how-to-assess.md) a migração do Azure: Ferramenta de avaliação do servidor.
- Verifique se você descobriu seus computadores nas migrações para Azure; Você pode fazer isso Configurando um dispositivo de migrações para Azure para [VMware](how-to-set-up-appliance-vmware.md) ou [Hyper-V](how-to-set-up-appliance-hyper-v.md). O dispositivo descobre computadores locais e envia metadados e dados de desempenho para migrações para o Azure: Avaliação do Servidor. [Saiba mais](migrate-appliance.md).

## <a name="how-does-it-work"></a>Como funciona?

As migrações para Azure usam a solução [mapa do serviço](../operations-management-suite/operations-management-suite-service-map.md) em [logs de Azure monitor](../log-analytics/log-analytics-overview.md) para visualização de dependência.
- Para aproveitar a visualização de dependência, você precisa associar um espaço de trabalho Log Analytics, novo ou existente, a um projeto de migrações para Azure.
- Você só pode criar ou anexar um espaço de trabalho na mesma assinatura em que o projeto de migrações para Azure é criado.
- Para anexar um espaço de trabalho Log Analytics a um projeto:
    1. Na guia **servidores** , em **migrações para Azure: Bloco avaliação** do servidor, clique em **visão geral**.
    2. Em **visão geral**, clique na seta para baixo para expandir o **Essentials**.
    3. No **espaço de trabalho do OMS**, clique em **requer configuração**.
    4. Em **Configurar espaço de trabalho**, especifique se deseja criar um novo espaço de trabalho ou usar um existente:
    
    ![Adicionar área de trabalho](./media/how-to-create-group-machine-dependencies/workspace.png)

- Ao associar um espaço de trabalho, você terá a opção de criar um novo espaço de trabalho ou anexar um existente:
  - Ao criar um novo espaço de trabalho, você precisa especificar um nome para o espaço de trabalho. Em seguida, o espaço de trabalho é criado em uma região na mesma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que o projeto de migração.
  - Ao anexar um espaço de trabalho existente, você pode escolher entre todos os espaços de trabalho disponíveis na mesma assinatura que o projeto de migração. Observe que somente os espaços de trabalho são listados, que foram criados em uma região onde [mapa do serviço tem suporte](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Para poder anexar um espaço de trabalho, verifique se você tem acesso de ' leitor ' ao espaço de trabalho.

  > [!NOTE]
  > Depois de anexar um espaço de trabalho a um projeto, você não poderá alterá-lo mais tarde.

- O espaço de trabalho associado é marcado com o **projeto de migração**de chave e o nome do **projeto**de valor, que você pode usar para pesquisar na portal do Azure.
- Para navegar até o espaço de trabalho associado ao projeto, você pode ir para a seção **Essentials** da página **visão geral** do projeto e acessar o espaço de trabalho

    ![Navegar Log Analytics espaço de trabalho](./media/concepts-dependency-visualization/oms-workspace.png)

Para utilizar a visualização das dependências, precisa de transferir e instalar os agentes em cada computador no local que pretende analisar.  

- [O MMA (Microsoft Monitoring Agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) precisa ser instalado em cada computador. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) sobre como instalar o agente do MMA.
- O [agente de dependência](../azure-monitor/platform/agents-overview.md#dependency-agent) precisa ser instalado em cada computador. [Saiba mais](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) sobre como instalar o Dependency Agent.
- Além disso, caso tenha computadores sem conectividade à Internet, terá de transferir e instalar o gateway do Log Analytics.

Você não precisa desses agentes em computadores que deseja avaliar, a menos que esteja usando a visualização de dependência.

## <a name="do-i-need-to-pay-for-it"></a>Preciso pagar por ele?

O recurso de visualização de dependência está disponível sem custo adicional. O uso do recurso de visualização de dependência na avaliação do servidor requer Mapa do Serviço e exige que você associe um espaço de trabalho Log Analytics, novo ou existente, ao projeto migrações para Azure. A funcionalidade de visualização de dependência na avaliação do servidor é gratuita para os primeiros 180 dias.

1. O uso de qualquer solução que não Mapa do Serviço nesse espaço de trabalho Log Analytics incorrerá em encargos de [log Analytics padrão](https://azure.microsoft.com/pricing/details/log-analytics/) .
2. Para dar suporte a cenários de migração sem custo adicional, a solução Mapa do Serviço não incorrerá em nenhum encargo pelos primeiros 180 dias a partir do dia da associação do espaço de trabalho Log Analytics com o projeto de migrações para Azure. Após 180 dias, os encargos de Log Analytics padrão serão aplicados.

Quando você registra agentes no espaço de trabalho, use a ID e a chave fornecida pelo projeto na página de etapas do agente de instalação.

Quando o projeto de migrações para Azure é excluído, o espaço de trabalho não é excluído junto com ele. Poste a exclusão do projeto, o uso de Mapa do Serviço não será gratuito e cada nó será cobrado de acordo com a camada paga do espaço de trabalho Log Analytics.

> [!NOTE]
> O recurso de visualização de dependência usa Mapa do Serviço por meio de um espaço de trabalho Log Analytics. Desde 28 de fevereiro de 2018, com o anúncio da disponibilidade geral de migrações para Azure, o recurso agora está disponível sem custo adicional. Você precisará criar um novo projeto para fazer uso do espaço de trabalho de uso gratuito. Os espaços de trabalho existentes antes da disponibilidade geral ainda são passíveis de cobrança, portanto, recomendamos que você mova para um novo projeto.

Saiba mais sobre os preços do Azure Migrate [aqui](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="how-do-i-manage-the-workspace"></a>Como fazer gerenciar o espaço de trabalho?

Você pode usar o espaço de trabalho Log Analytics fora das migrações para Azure. Ele não será excluído se você excluir o projeto de migrações para Azure no qual ele foi criado. Se você não precisar mais do espaço de trabalho, [exclua-](../azure-monitor/platform/manage-access.md) o manualmente.

Não exclua o espaço de trabalho criado pela migração do Azure, a menos que você exclua o projeto de migrações para Azure. Se você fizer isso, a funcionalidade de visualização de dependência não funcionará conforme o esperado.

## <a name="next-steps"></a>Passos seguintes
- [Agrupar computadores usando dependências de máquina](how-to-create-group-machine-dependencies.md)
- [Saiba mais](https://docs.microsoft.com/azure/migrate/resources-faq#what-is-dependency-visualization) sobre as perguntas frequentes sobre a visualização de dependência.
