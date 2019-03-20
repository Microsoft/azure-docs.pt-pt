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
ms.date: 1/3/2019
ms.author: rkarlin
ms.openlocfilehash: 5a25ba889ebde750937f88e447123263a82d5627
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896828"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrar soluções de segurança no Centro de Segurança do Azure
Este documento ajuda-o a gerir soluções de segurança já ligadas ao Centro de Segurança do Azure e adicionar novas.

## <a name="integrated-azure-security-solutions"></a>Soluções de segurança do Azure integradas
O Centro de Segurança facilita a ativação de soluções de segurança integradas no Azure. As vantagens incluem:

- **Implementação simplificada**: Centro de segurança oferece aprovisionamento integrado de soluções de parceiros integradas. Para soluções como antimalware e avaliação de vulnerabilidades, o Centro de Segurança pode aprovisionar o agente necessário nas suas máquinas virtuais e para aplicações de firewall, o Centro de Segurança pode assegurar grande parte da configuração de rede necessária.
- **Deteções integradas**: Os eventos de segurança das soluções de parceiros são automaticamente recolhidos, agregados e apresentados como parte dos alertas e incidentes do Centro de Segurança. Estes eventos também são combinados com deteções de outras origens, para disponibilizarem capacidades avançadas de deteção de ameaças.
- **Monitorização de estado de funcionamento e gestão unificadas**: Os clientes podem utilizar eventos de estado de funcionamento integrados para monitorizar todas as soluções do parceiro de forma rápida. Está disponível uma gestão básica, com acesso fácil a configuração avançada mediante a utilização da solução do parceiro.

Atualmente, as soluções de segurança integradas incluem:

- Firewall de aplicações Web ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) e [Gateway de Aplicação do Azure](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewalls de próxima geração ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](https://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) e [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Avaliação de vulnerabilidades ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) e [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> O Centro de Segurança não instala o Microsoft Monitoring Agent em aplicações virtuais de parceiro, porque a maioria dos fornecedores de segurança proíbe agentes externos em execução nas suas aplicações.
>
>

## <a name="how-security-solutions-are-integrated"></a>Como são integradas as soluções de segurança
As soluções de segurança do Azure implementadas a partir do Centro de Segurança são ligadas automaticamente. Também pode ligar outras origens de dados de segurança, incluindo:

- Azure AD Identity Protection
- Computadores em execução no local ou noutras clouds
- Solução de segurança que suporte o Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

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

O estado de uma solução de parceiro pode ser:

* Bom estado de funcionamento (verde) – não há nenhum problema de estado de funcionamento.
* Mau estado de funcionamento (vermelho) – existe um problema de estado de funcionamento que exige atenção imediata.
* Problemas de estado de funcionamento (cor de laranja), a solução deixou seu estado de funcionamento de relatórios.
* Não reportada (cinzento) – a solução não comunicou qualquer coisa, mas o estado de uma solução pode ser não reportado se ele recentemente foi ligado e ainda está a ser implementado, ou não existem dados de estado de funcionamento estão disponíveis.

> [!NOTE]
> Se os dados de estado de funcionamento não estiverem disponíveis, o Centro de segurança mostra a data e hora do último evento recebido para indicar se a solução está a comunicar ou não. Se não existem dados de estado de funcionamento estão disponíveis e não existem alertas são recebidas dentro dos últimos 14 dias, o Centro de segurança indica que a solução está danificado ou não de relatórios.
>
>

1. Selecione **vista** para obter informações adicionais e opções, que inclui:

   - **Consola de soluções**. Abre a experiência de gestão para esta solução.
   - **Associar VM**. Abre o painel de ligar aplicações. Aqui pode ligar recursos à solução de parceiros.
   - **Eliminar solução**.
   - **Configurar**.

   ![Detalhe de solução de parceiros](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Soluções detetadas

O Centro de Segurança deteta automaticamente as soluções de segurança em execução no Azure, mas não ligadas ao Centro de Segurança, e apresenta as soluções na secção **Soluções detetadas**. Isto inclui soluções do Azure, como o [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), bem como soluções de parceiros.

> [!NOTE]
> É necessário o escalão Standard do Centro de Segurança ao nível da subscrição para a funcionalidade de soluções detetadas. Veja [Preços](security-center-pricing.md) para saber mais sobre os escalões de preço de Segurança.
>
>

Selecione **LIGAR** numa solução para se integrar com o Centro de Segurança e ser notificado sobre alertas de segurança.

![Soluções detetadas](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

O Centro de Segurança também deteta as soluções implementadas na subscrição que podem reencaminhar registos do Formato de Evento Comum (CEF). Saiba como [ligar uma solução de segurança](quick-security-solutions.md) que utilize registos CEF ao Centro de Segurança.

### <a name="add-data-sources"></a>Adicionar origens de dados

A secção **Adicionar origens de dados** inclui outras origens de dados disponíveis que podem ser ligadas. Para obter instruções sobre como adicionar dados a partir de qualquer uma destas origens, clique em **ADICIONAR**.

![Origens de dados](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

### <a name="connect-external-solutions"></a>Ligar soluções externas

Além de recolher dados de segurança dos seus computadores, pode integrar os dados de segurança de várias outras soluções de segurança, incluindo as que suportarem o Formato de Evento Comum (CEF). O CEF é um formato padrão da indústria sobre as mensagens Syslog, utilizado por vários fornecedores de segurança para permitir a integração de eventos entre diferentes plataformas.

Este manual de início rápido mostra-lhe como:
- Ligar uma solução de segurança ao Centro de Segurança com Registos CEF
- Validar a ligação com a solução de segurança

#### <a name="prerequisites"></a>Pré-requisitos
Para começar a utilizar o Centro de Segurança, tem de possuir uma subscrição do Microsoft Azure. Se não tiver uma subscrição, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).

Para acompanhar este manual de início rápido, tem de estar no escalão de preço Standard do Centro de Segurança. Pode experimentar o Centro de segurança Standard sem encargos. O início rápido [Onboard your Azure subscription to Security Center Standard](security-center-get-started.md) (Incluir a sua subscrição do Azure no Centro de Segurança Standard) explica-lhe como atualizar para Standard. Para saber mais, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).

Também precisa de uma [máquina Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux), com o serviço de Syslog já ligado ao seu Centro de Segurança.

#### <a name="connect-solution-using-cef"></a>Ligar a solução com CEF

1. Inicie sessão no [Portal do Azure](https://azure.microsoft.com/features/azure-portal/).
2. No menu **Microsoft Azure**, selecione **Centro de Segurança**. **Centro de Segurança - Descrição Geral** é aberto.

    ![Selecionar centro de segurança](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. No menu principal do Centro de Segurança, selecione **Soluções de Segurança**.
4. Na página Soluções de Segurança, em **Adicionar origens de dados (3)**, clique em **Adicionar** em **Formato de Evento Comum**.

    ![Adicionar origem de dados](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. Na página Registos do Formato de Evento Comum, expanda o segundo passo **Configurar o reencaminhamento do Syslog para enviar os registos necessários para o agente na porta UDP 25226**e siga as instruções abaixo no seu computador Linux:

    ![Configurar o syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. Expanda o terceiro passo **Colocar o ficheiro de configuração do agente no computador do agente** e siga as instruções abaixo no seu computador Linux:

    ![Configuração do agente](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. Expanda o quarto passo **Reiniciar o syslog daemon e o agente**e siga as instruções abaixo no seu computador Linux:

    ![Reiniciar o syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


#### <a name="validate-the-connection"></a>Validar a ligação

Antes de avançar para os passos abaixo, terá de aguardar até que o syslog comece a reportar para o Centro de Segurança. Esta operação pode demorar algum tempo, que irá variar consoante o tamanho do ambiente.

1.  No painel esquerdo do dashboard do Centro de Segurança, clique em **Procurar**.
2.  Selecione a área de trabalho à qual o Syslog (Máquina Linux) está ligado.
3.  Escreva *CommonSecurityLog* e clique no botão **Procurar**.

O exemplo seguinte mostra o resultado destes passos: ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

#### <a name="clean-up-resources"></a>Limpar recursos
Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se pretender continuar a trabalhar com inícios rápidos e tutoriais posteriores, continue a executar o escalão Standard e mantenha o aprovisionamento automático ativado. Se não pretender continuar ou quiser voltar para o Escalão gratuito:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de Segurança**.
2. Selecione a subscrição ou a política para a qual pretende voltar como Gratuita. **Política de segurança** abre-se.
3. Em **COMPONENTES DA POLÍTICA**, selecione **Escalão de preço**.
4. Selecione **Gratuito** para alterar a subscrição, do Escalão standard para o Escalão gratuito.
5. Selecione **Guardar**.

Se pretender desativar aprovisionamento automático:

1. Regresse ao menu principal do Centro de Segurança e selecione **Política de segurança**.
2. Selecione a subscrição para a qual pretende desativar o aprovisionamento automático.
3. Em **Política de segurança – Recolha de Dados**, selecione **Desativar** em **Inclusão** para desativar o aprovisionamento automático.
4. Selecione **Guardar**.

>[!NOTE]
> Desativar o aprovisionamento automático não remove o Microsoft Monitoring Agent das VMs do Azure onde o agente tiver sido aprovisionado. Desativar o aprovisionamento automático limita a monitorização da segurança dos seus recursos.
>

## <a name="exporting-data-to-a-siem"></a>Exportar dados para um SIEM

Os eventos processados produzidos pelo centro de segurança do Azure são publicados para o Azure [registo de atividades](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md), um registo de tipos disponíveis através do Azure Monitor. Monitor do Azure oferece um pipeline consolidado para o encaminhamento qualquer um dos seus dados de monitorização para uma ferramenta SIEM. Isso é feito por transmissão em fluxo de dados para um Hub de eventos em que ele, em seguida, pode ser extraído para uma ferramenta de parceiro.

Este pipe utiliza a [monitorização do Azure único pipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) para obter acesso aos dados de monitorização do seu ambiente do Azure. Isto permite-lhe configurar facilmente SIEMs e ferramentas de monitorização para consumir os dados.

As secções seguintes descrevem como configurar dados sejam transmitidos para um hub de eventos. Os passos partem do princípio de que já tenha configurado na sua subscrição do Azure no Centro de segurança do Azure.

Descrição geral de alto nível

![Descrição geral de alto nível](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>O que é expostos para o SIEM os dados de segurança do Azure?

Nesta versão expomos a [alertas de segurança.](../security-center/security-center-managing-and-responding-alerts.md) Em versões futuras, podemos irá melhorar o conjunto de dados com recomendações de segurança.

### <a name="how-to-setup-the-pipeline"></a>Como configurar o pipeline

#### <a name="create-an-event-hub"></a>Criar um Hub de Eventos

Antes de começar, precisa [criar um espaço de nomes de Hubs de eventos](../event-hubs/event-hubs-create.md). Este espaço de nomes e o Hub de eventos é o destino para todos os seus dados de monitorização.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>O registo de atividades do Azure para os Hubs de eventos do Stream

Veja o artigo seguinte [registo de atividades de fluxo para os Hubs de eventos](../azure-monitor/platform/activity-logs-stream-event-hubs.md)

#### <a name="install-a-partner-siem-connector"></a>Instalar um conector SIEM parceiro 

Encaminhamento seus dados de monitorização para um Hub de eventos com o Azure Monitor permite-lhe integrar facilmente com parceiros SIEM e ferramentas de monitorização.

Veja a seguinte ligação para ver a lista de [suportado SIEMs](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub)

### <a name="example-for-querying-data"></a>Exemplo para consultar dados 

Eis algumas consultas de Splunk que pode utilizar para extrair dados de alertas:

| **Descrição da consulta** | **Consulta** |
|----|----|
| Todos os Alertas| índice = Microsoft.Security/locations/alerts principal|
| Resumir contagem das operações pelo respetivo nome| índice = sourcetype principal = "amal: segurança" \| operationName tabela \| estatísticas contagem por operationName|
| Obter as informações de alertas: Tempo, o nome, o estado, o ID e o subscrição | índice = principal Microsoft.Security/locations/alerts \| tabela \_tempo, properties.eventName, estado, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu a integrar soluções de parceiros no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Ligar o Microsoft Advanced Threat Analytics ao Centro de Segurança do Azure](security-center-ata-integration.md)
* [Ligar o Azure Active Directory Identity Protection ao Centro de Segurança do Azure](security-center-aadip-integration.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Monitor partner solutions with Security Center](security-center-partner-solutions.md) (Monitorizar soluções de parceiros com o Centro de Segurança). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Azure Security Center FAQs](security-center-faq.md) (FAQ do Centro de Segurança do Azure). Encontre respostas para as perguntas mais frequentes sobre o Centro de Segurança.
* [Blogue Azure Security](https://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
