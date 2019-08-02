---
title: Integrar soluções de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como é que o Centro de Segurança do Azure se integra com parceiros, para melhorar a segurança geral dos seus recursos do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 14bf7dc62bb97cb14232660db6d0649e3d77d4fa
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662945"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

> [!NOTE]
> Um subconjunto de soluções de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. As vantagens incluem:

- **Implantação simplificada**: A central de segurança oferece provisionamento simplificado de soluções de parceiros integradas. Para soluções como antimalware e avaliação de vulnerabilidades, o Centro de Segurança pode aprovisionar o agente necessário nas suas máquinas virtuais e para aplicações de firewall, o Centro de Segurança pode assegurar grande parte da configuração de rede necessária.
- **Detecções integradas**: Os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Monitoramento e gerenciamento de integridade**unificados: Os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções do parceiro de forma rápida. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Atualmente, as soluções de segurança integradas incluem a avaliação de vulnerabilidades de [Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) e [Rapid7](https://www.rapid7.com/products/insightvm/) e o Firewall do aplicativo Web do Microsoft Application Gateway.

> [!NOTE]
> O Centro de Segurança não instala o Microsoft Monitoring Agent em aplicações virtuais de parceiro, porque a maioria dos fornecedores de segurança proíbe agentes externos em execução nas suas aplicações.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. Você também pode conectar outras fontes de dados de segurança, incluindo computadores executados localmente ou em outras nuvens.

![Integração de soluções de parceiros](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Gerir soluções de segurança do Azure integradas e outras origens de dados

1. Inicie sessão no [portal do Azure](https://azure.microsoft.com/features/azure-portal/).

2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

3. No menu Centro de Segurança, selecione **Soluções de segurança**.

   ![Descrição geral do Centro de Segurança](./media/security-center-partner-integration/overview.png)

Em **Soluções de segurança**, pode ver informações sobre o estado de funcionamento da solução de segurança do Azure integrada e efetuar tarefas de gestão básicas. Também pode ligar outros tipos de origens de dados de segurança, tais como alertas do Azure Active Directory Identity Protection e registos de firewall no Common Event Format (CEF).

### <a name="connected-solutions"></a>Soluções ligadas

A secção **Soluções ligadas** inclui soluções de segurança que estão ligadas atualmente ao Centro de Segurança e informações sobre o estado de funcionamento de cada solução.  

![Soluções ligadas](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

O status de uma solução de parceiro pode ser:

* Íntegro (verde)-não há nenhum problema de integridade.
* Mau estado de funcionamento (vermelho) – existe um problema de estado de funcionamento que exige atenção imediata.
* Problemas de integridade (laranja)-a solução parou de relatar sua integridade.
* Não relatado (cinza)-a solução não reportou nada ainda, o status de uma solução poderá não ser relatado se tiver sido conectado recentemente e ainda estiver sendo implantado, ou se nenhum dado de integridade estiver disponível.

> [!NOTE]
> Se os dados de status de integridade não estiverem disponíveis, a central de segurança mostrará a data e a hora do último evento recebido para indicar se a solução está se comunicando ou não. Se nenhum dado de integridade estiver disponível e nenhum alerta for recebido nos últimos 14 dias, a central de segurança indicará que a solução não está íntegra ou não está relatando.
>
>

1. Selecione **Exibir** para obter informações e opções adicionais, que incluem:

   - **Console de solução**. Abre a experiência de gerenciamento para esta solução.
   - **Vincular VM**. Abre a folha vincular aplicativos. Aqui pode ligar recursos à solução de parceiros.
   - **Excluir solução**.
   - **Configurar**.

   ![Detalhe de solução de parceiros](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções descobertas

O Centro de Segurança deteta automaticamente as soluções de segurança em execução no Azure, mas não ligadas ao Centro de Segurança, e apresenta as soluções na secção **Soluções detetadas**. Isto inclui soluções do Azure, como o [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), bem como soluções de parceiros.

> [!NOTE]
> É necessário o escalão Standard do Centro de Segurança ao nível da subscrição para a funcionalidade de soluções detetadas. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preço de Segurança.
>
>

Selecione **LIGAR** numa solução para se integrar com o Centro de Segurança e ser notificado sobre alertas de segurança.

![Soluções descobertas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

O Centro de Segurança também deteta as soluções implementadas na subscrição que podem reencaminhar registos do Formato de Evento Comum (CEF). Saiba como [ligar uma solução de segurança](quick-security-solutions.md) que utilize registos CEF ao Centro de Segurança.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

## <a name="exporting-data-to-a-siem"></a>Exportando dados para um SIEM

Os eventos processados produzidos pela central de segurança do Azure são publicados no [log de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)do Azure, um dos tipos de log disponíveis por meio de Azure monitor. O Azure Monitor oferece um pipeline consolidado para roteamento de qualquer um dos seus dados de monitoramento para uma ferramenta SIEM. Isso é feito transmitindo esses dados para um hub de eventos, no qual ele pode ser extraído para uma ferramenta de parceiro.

Esse pipe usa o [pipeline único de monitoramento do Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) para obter acesso aos dados de monitoramento do seu ambiente do Azure. Isso permite que você configure facilmente os SIEMs e as ferramentas de monitoramento para consumir os dados.

As seções a seguir descrevem como você pode configurar os dados a serem transmitidos para um hub de eventos. As etapas pressupõem que você já tem a central de segurança do Azure configurada em sua assinatura do Azure.

Descrição geral de alto nível

![Visão geral de alto nível](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Quais são os dados de segurança do Azure expostos ao SIEM?

Nesta versão, expõemos os [alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md) Em versões futuras, enriqueceremos o conjunto de dados com recomendações de segurança.

### <a name="how-to-setup-the-pipeline"></a>Como configurar o pipeline

#### <a name="create-an-event-hub"></a>Criar um Hub de Eventos

Antes de começar, você precisa [criar um namespace de hubs de eventos](../event-hubs/event-hubs-create.md). Esse namespace e o Hub de eventos são o destino de todos os seus dados de monitoramento.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Transmitir o log de atividades do Azure para os hubs de eventos

Consulte o seguinte artigo fluxo de [atividades do Stream para hubs de eventos](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalar um conector SIEM do parceiro 

O roteamento dos dados de monitoramento para um hub de eventos com o Azure Monitor permite que você se integre facilmente às ferramentas de monitoramento e SIEM do parceiro.

Consulte o link a seguir para ver a lista de [Siems com suporte](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Exemplo para consultar dados 

Aqui estão algumas consultas Splunk que você pode usar para efetuar pull de dados de alerta:

| **Descrição da consulta** | **Consulta** |
|----|----|
| Todos os Alertas| index = Microsoft. Security/Locations/alertas principais|
| Resumir contagem de operações por seu nome| index = Main SourceType = "Amal: Security" \| tabela operationName \| stats Count por operationName|
| Obter informações de alertas: Hora, nome, estado, ID e assinatura | index = Main Microsoft. Security/Locations/ \| Alerts Table \_time, propriedades. EventName, estado, propriedades. operationId, am_subscriptionId |


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.
* [Blogue Azure Security](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
