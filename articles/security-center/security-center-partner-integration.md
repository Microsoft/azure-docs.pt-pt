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
ms.openlocfilehash: 23a00c766dbb38853c57c91e7f59ec364390c44b
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603804"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

> [!NOTE]
> Um subconjunto de soluções de segurança foi retirado a 31 de julho de 2019. Para mais informações e serviços alternativos, consulte [funcionalidades do Centro de Aposentações (julho 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. As vantagens incluem:

- **Implementação simplificada**: o Centro de Segurança oferece aprovisionamento integrado de soluções de parceiros integradas. Para soluções como antimalware e avaliação de vulnerabilidade, o Security Center pode fornecer o agente nas suas máquinas virtuais. Para aparelhos de firewall, o Security Center pode cuidar de grande parte da configuração de rede necessária.
- **Deteções integradas**: os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Gestão e monitorização do estado de funcionamento unificadas**: os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções de parceiros rapidamente. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Atualmente, as soluções de segurança integradas incluem a avaliação de vulnerabilidade por [Qualys](https://www.qualys.com/public-cloud/#azure) e [Rapid7](https://www.rapid7.com/products/insightvm/) e Microsoft Application Gateway Web application firewall.

> [!NOTE]
> O Security Center não instala o Agente de Monitorização da Microsoft em aparelhos virtuais parceiros porque a maioria dos fornecedores de segurança proíbem agentes externos a funcionar nos seus aparelhos.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. Também pode ligar outras fontes de dados de segurança, incluindo computadores que executam no local ou em outras nuvens.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerir soluções de segurança do Azure integradas e outras origens de dados

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

3. No menu Centro de Segurança, selecione **Soluções de segurança**.

   ![Descrição geral do Centro de Segurança](./media/security-center-partner-integration/overview.png)

Em soluções de **Segurança,** pode ver a saúde de soluções de segurança integradas do Azure e executar tarefas básicas de gestão.

### <a name="connected-solutions"></a>Soluções ligadas

A secção **de soluções Connected** inclui soluções de segurança que estão atualmente ligadas ao Centro de Segurança. Mostra também o estado de saúde de cada solução.  

![Soluções ligadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

O estatuto de uma solução parceira pode ser:

* Saudável (verde) - sem problemas de saúde.
* Pouco saudável (vermelho) - há um problema de saúde que requer atenção imediata.
* Problemas de saúde (laranja) - a solução deixou de reportar a sua saúde.
* Não reportado (cinzento) - a solução ainda não reportou nada e não há dados de saúde disponíveis. O estado de uma solução pode não ser reportado se foi conectado recentemente e ainda está em funcionamento.

> [!NOTE]
> Se os dados do estado de saúde não estiverem disponíveis, o Centro de Segurança mostra a data e hora do último evento recebido para indicar se a solução está ou não a reportar. Se não houver dados de saúde disponíveis e não forem recebidos alertas nos últimos 14 dias, o Centro de Segurança indica que a solução não é saudável ou não reporta.
>
>

1. Selecione **VISTA** para informações e opções adicionais, tais como:

   - Consola de **solução.** Abre a experiência de gestão para esta solução.
   - **Link VM**. Abre a página 'Aplicações de Link'. Aqui pode ligar recursos à solução de parceiros.
   - **Eliminar a solução**.
   - **Configurar**.

   ![Detalhe de solução de parceiros](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções detetadas

O Security Center descobre automaticamente soluções de segurança em funcionamento em Azure, mas não ligadas ao Centro de Segurança e exibe as soluções na secção **Soluções Descobertas.** Estas soluções incluem soluções Azure, como a [Azure AD Identity Protection,](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)e soluções parceiras.

> [!NOTE]
> É necessário o escalão Standard do Centro de Segurança ao nível da subscrição para a funcionalidade de soluções detetadas. Consulte [o Preço](security-center-pricing.md) para saber mais sobre os níveis de preços.
>
>

Selecione **CONNECT** sob uma solução para integrar com o Centro de Segurança e seja notificado de alertas de segurança.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportação de dados para um SIEM

> [!NOTE]
> Para obter detalhes de um método mais simples (atualmente em pré-visualização) para exportar dados para um SIEM, consulte [alertas e recomendações de segurança para exportação (Pré-visualização)](continuous-export.md). O novo método não utiliza o Registo de Atividades como intermediador e permite a exportação direta do Centro de Segurança para centros de eventos (e depois para o seu SIEM), apoiatambém a exportação de Recomendações de Segurança.


Pode configurar os seus SIEMs ou outras ferramentas de monitorização para receber eventos do Centro de Segurança Azure.

Todos os eventos do Azure Security Center são publicados no registo de [atividade](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)seleção azure do Azure Monitor. O Azure Monitor utiliza [um pipeline consolidado](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) para transmitir os dados para um Hub de Eventos onde pode ser puxado para a sua ferramenta de monitorização.

As próximas secções descrevem como pode configurar dados a serem transmitidos para um centro de eventos. Os passos assumem que já tem o Azure Security Center configurado na sua subscrição Azure.

### <a name="high-level-overview"></a>Descrição geral de alto nível

![Visão geral de alto nível](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quais são os dados de segurança do Azure expostos ao SIEM?

Nesta versão, expomos os alertas de [segurança.](../security-center/security-center-managing-and-responding-alerts.md) Nos próximos lançamentos, enriqueceremos o conjunto de dados com recomendações de segurança.

### <a name="how-to-set-up-the-pipeline"></a>Como configurar o oleoduto

#### <a name="create-an-event-hub"></a>Criar um Hub de Eventos

Antes de começar, crie um espaço de [nome Sem Eventos](../event-hubs/event-hubs-create.md) - o destino para todos os seus dados de monitorização.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmita o Log de Atividade do Azure para centros de eventos

Consulte o registo de atividade de fluxo de artigos a seguir para Os Centros de [Eventos](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Instale um conector Parceiro SIEM 

Encaminhamento dos seus dados de monitorização para um Hub de Eventos com o Monitor Azure permite-lhe integrar-se facilmente com o Parceiro SIEM e ferramentas de monitorização.

Consulte o seguinte artigo para a lista de [SIEMs suportados](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

### <a name="example-for-querying-data"></a>Exemplo para dados de consulta 

Aqui estão algumas consultas Splunk que pode usar para retirar dados de alerta:

| **Descrição da Consulta** | **Consulta** |
|----|----|
| Todos os Alertas| index=principal Microsoft.Segurança/localizações/alertas|
| Resumir a contagem de operações pelo seu nome| index=main sourcetype="amal:security" \| operação da tabelaNome \| estatísticas contam por operaçãoNome|
| Obter informações de Alertas: Tempo, Nome, Estado, ID e Subscrição | index=main Microsoft.Security/locations/alerts \| tabela \_tempo, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para saber mais sobre o Centro de Segurança, consulte o seguinte artigo:

* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.