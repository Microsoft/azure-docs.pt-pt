---
title: Integrar soluções de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: 7174003485d51cf582c798c4b18404b1b72de0fb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75530957"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

> [!NOTE]
> Um subconjunto de soluções de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. Os benefícios incluem:

- **Implementação simplificada**: o Centro de Segurança oferece aprovisionamento integrado de soluções de parceiros integradas. Para soluções como antimalware e avaliação de vulnerabilidade, a central de segurança pode provisionar o agente em suas máquinas virtuais. Para dispositivos de firewall, a central de segurança pode cuidar de grande parte da configuração de rede necessária.
- **Deteções integradas**: os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Gestão e monitorização do estado de funcionamento unificadas**: os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções de parceiros rapidamente. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Atualmente, as soluções de segurança integradas incluem a avaliação de vulnerabilidades de [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e o Firewall do aplicativo Web do Microsoft Application Gateway.

> [!NOTE]
> A central de segurança não instala o Microsoft Monitoring Agent em soluções de virtualização de parceiros, pois a maioria dos fornecedores de segurança proíbe agentes externos em execução em seus dispositivos.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. Você também pode conectar outras fontes de dados de segurança, incluindo computadores executados localmente ou em outras nuvens.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerir soluções de segurança do Azure integradas e outras origens de dados

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

3. No menu Centro de Segurança, selecione **Soluções de segurança**.

   ![Descrição geral do Centro de Segurança](./media/security-center-partner-integration/overview.png)

Em **soluções de segurança**, você pode ver a integridade das soluções de segurança integradas do Azure e executar tarefas básicas de gerenciamento.

### <a name="connected-solutions"></a>Soluções ligadas

A seção **soluções conectadas** inclui soluções de segurança que estão atualmente conectadas à central de segurança. Ele também mostra o status de integridade de cada solução.  

![Soluções ligadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

O status de uma solução de parceiro pode ser:

* Íntegro (verde)-sem problemas de integridade.
* Não íntegro (vermelho) – há um problema de integridade que requer atenção imediata.
* Problemas de integridade (laranja)-a solução parou de relatar sua integridade.
* Não relatado (cinza)-a solução ainda não relatou nada e nenhum dado de integridade está disponível. O status de uma solução poderá não ser relatado se ele tiver sido conectado recentemente e ainda estiver sendo implantado.

> [!NOTE]
> Se os dados de status de integridade não estiverem disponíveis, a central de segurança mostrará a data e a hora do último evento recebido para indicar se a solução está se comunicando ou não. Se nenhum dado de integridade estiver disponível e nenhum alerta for recebido nos últimos 14 dias, a central de segurança indicará que a solução não está íntegra ou não está relatando.
>
>

1. Selecione **Exibir** para obter informações adicionais e opções, como:

   - **Console de solução**. Abre a experiência de gerenciamento para esta solução.
   - **Vincular VM**. Abre a página vincular aplicativos. Aqui pode ligar recursos à solução de parceiros.
   - **Excluir solução**.
   - **Configurar**.

   ![Detalhe de solução de parceiros](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções detetadas

A central de segurança descobre automaticamente as soluções de segurança em execução no Azure, mas não está conectada à central de segurança e exibe as soluções na seção **soluções descobertas** . Essas soluções incluem soluções do Azure, como [Azure ad Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluções de parceiros.

> [!NOTE]
> É necessário o escalão Standard do Centro de Segurança ao nível da subscrição para a funcionalidade de soluções detetadas. Consulte [preços](security-center-pricing.md) para saber mais sobre os tipos de preço.
>
>

Selecione **conectar** em uma solução para integrar com a central de segurança e ser notificado sobre alertas de segurança.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportando dados para um SIEM

> [!NOTE]
> Para obter detalhes de um método mais simples (atualmente em visualização) para exportar dados para um SIEM, consulte [Exportar alertas de segurança e recomendações (versão prévia)](continuous-export.md). O novo método não usa o log de atividades como um intermediador e permite a exportação direta da central de segurança para os hubs de eventos (e, em seguida, para o SIEM), também dá suporte à exportação de recomendações de segurança.


Você pode configurar seus SIEMs ou outras ferramentas de monitoramento para receber eventos da central de segurança do Azure.

Todos os eventos da central de segurança do Azure são publicados no [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)do Azure Azure monitor. O Azure Monitor usa [um pipeline consolidado](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) para transmitir os dados para um hub de eventos, no qual ele pode ser obtido em sua ferramenta de monitoramento.

As seções a seguir descrevem como você pode configurar os dados a serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem a central de segurança do Azure configurada em sua assinatura do Azure.

### <a name="high-level-overview"></a>Descrição geral de alto nível

![Visão geral de alto nível](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quais são os dados de segurança do Azure expostos ao SIEM?

Nesta versão, expõemos os [alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md) Em versões futuras, enriqueceremos o conjunto de dados com recomendações de segurança.

### <a name="how-to-set-up-the-pipeline"></a>Como configurar o pipeline

#### <a name="create-an-event-hub"></a>Criar um Hub de Eventos

Antes de começar, [crie um namespace de hubs de eventos](../event-hubs/event-hubs-create.md) -o destino de todos os seus dados de monitoramento.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmitir o log de atividades do Azure para os hubs de eventos

Consulte o artigo [fluxo de atividades do Stream a seguir para os hubs de eventos](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalar um conector SIEM do parceiro 

O roteamento dos dados de monitoramento para um hub de eventos com o Azure Monitor permite que você se integre facilmente às ferramentas de monitoramento e SIEM do parceiro.

Consulte o seguinte artigo para obter a lista de [Siems com suporte](../azure-monitor/platform/resource-logs-stream-event-hubs.md#what-you-can-do-with-platform-logs-sent-to-an-event-hub)

### <a name="example-for-querying-data"></a>Exemplo para consultar dados 

Aqui estão algumas consultas Splunk que você pode usar para efetuar pull de dados de alerta:

| **Descrição da consulta** | **Consulta** |
|----|----|
| Todos os Alertas| index = Microsoft. Security/Locations/alertas principais|
| Resumir contagem de operações por seu nome| index = Main SourceType = "Amal: Security" \| tabela operationName \| a contagem de estatísticas por operationName|
| Obter informações de alertas: hora, nome, estado, ID e assinatura | index = Main Microsoft. Security/Locations/Alerts \| tabela \_time, Properties. EventName, estado, Properties. operationId, am_subscriptionId |


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.
* [Blogue Azure Security](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
