---
title: Segurança operacional do Azure | Microsoft Docs
description: Saiba mais sobre Microsoft Azure logs de monitoramento, seus serviços e como ele funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 69b25f22d448436db5e906a3b56b5e50ec412b40
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934683"
---
# <a name="azure-operational-security"></a>Segurança operacional do Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Descrição geral
Sabemos que a segurança é o trabalho um na nuvem e como é importante que você encontre informações precisas e oportunas sobre a segurança do Azure. Um dos melhores motivos para usar o Azure para seus aplicativos e serviços é aproveitar a ampla variedade de ferramentas de segurança e recursos disponíveis. Essas ferramentas e recursos ajudam a tornar possível criar soluções seguras na plataforma segura do Azure. O Windows Azure deve fornecer confidencialidade, integridade e disponibilidade dos dados do cliente, ao mesmo tempo em que também permite a responsabilidade transparente.

Para ajudar os clientes a entender melhor a matriz de controles de segurança implementada em Microsoft Azure das perspectivas operacionais do cliente e da Microsoft, essa white paper, "segurança operacional do Azure", é escrita e fornece uma solução completa Examine a segurança operacional disponível com o Windows Azure.

### <a name="azure-platform"></a>Plataforma do Azure
O Azure é uma plataforma de serviço de nuvem pública que dá suporte a uma ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos. Ele pode executar contêineres do Linux com a integração do Docker; Crie aplicativos com JavaScript, Python, .NET, PHP, Java e node. js; Crie back-ends para dispositivos iOS, Android e Windows. O serviço de nuvem do Azure dá suporte às mesmas tecnologias que milhões de desenvolvedores e profissionais de ti já contam e confiam.

Ao se basear ou migrar ativos de ti para um provedor de serviços de nuvem pública, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que eles fornecem para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar milhões de clientes simultaneamente, além de fornecer uma base confiável sobre a qual as empresas podem atender aos seus requisitos de segurança. Além disso, o Azure fornece uma ampla gama de opções de segurança configuráveis e a capacidade de controlá-las para que você possa personalizar a segurança para atender aos requisitos exclusivos das implantações de sua organização. Este documento ajudará você a entender como os recursos de segurança do Azure podem ajudá-lo a atender a esses requisitos.

### <a name="abstract"></a>Abstrato
A segurança operacional do Azure refere-se aos serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Microsoft Azure. A segurança operacional do Azure é criada em uma estrutura que incorpora o conhecimento obtido por meio de vários recursos que são exclusivos à Microsoft, incluindo o SDL (Microsoft Security Development Lifecycle), o programa Microsoft Security Response Center, e conscientização profunda do panorama de ameaças do segurança cibernética.

Esta white paper descreve a abordagem da Microsoft para a segurança operacional do Azure na plataforma Microsoft Azure cloud e aborda os seguintes serviços:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Centro de Segurança do Azure](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Observador de rede do Azure](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Active Directory do Azure](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure monitorar logs

Microsoft Azure logs de monitoramento é a solução de gerenciamento de ti para a nuvem híbrida. Usado sozinho ou para estender sua implantação existente do System Center, Azure Monitor logs oferece a você o máximo de flexibilidade e controle para o gerenciamento baseado em nuvem de sua infraestrutura.

![Registos do Azure Monitor](./media/operational-security/azure-operational-security-fig1.png)

Com os logs de Azure Monitor, você pode gerenciar qualquer instância em qualquer nuvem, incluindo local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo menor do que as soluções competitivas. Criado para o mundo em nuvem, o Azure Monitor logs oferece uma nova abordagem para gerenciar sua empresa que é a maneira mais rápida e econômica de atender a novos desafios de negócios e acomodar novas cargas de trabalho, aplicativos e ambientes de nuvem.

### <a name="azure-monitor-services"></a>Serviços Azure Monitors

A principal funcionalidade dos logs de Azure Monitor é fornecida por um conjunto de serviços que são executados no Azure. Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes.

| Serviço  | Descrição|
| :------------- | :-------------|
| Registos do Azure Monitor | Monitorizar e analisar a disponibilidade e o desempenho de diferentes recursos, incluindo computadores e máquinas virtuais. |
|Automatização | Automatizar processos manuais e impor configurações para computadores e máquinas virtuais. |
| Criar cópia de segurança | Faça backup e restaure dados críticos. |
| Site Recovery | Providenciar elevada disponibilidade para aplicações críticas. |

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os logs de Azure monitor](https://azure.microsoft.com/documentation/services/log-analytics) fornecem serviços de monitoramento coletando dados de recursos gerenciados em um repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.


Esse método permite consolidar dados de várias fontes, para que você possa combinar dados de seus serviços do Azure com seu ambiente local existente. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.


![Registos do Azure Monitor](./media/operational-security/azure-operational-security-fig2.png)

O serviço de Azure Monitor gerencia seus dados baseados em nuvem com segurança usando os seguintes métodos:
-   segregação de dados
-   retenção de dados
-   segurança física
-   gerenciamento de incidentes
-   regulamenta
-   certificações de padrões de segurança

### <a name="azure-backup"></a>Azure Backup

O [backup do Azure](https://azure.microsoft.com/documentation/services/backup) fornece serviços de backup e restauração de dados e faz parte do Azure monitor pacote de produtos e serviços.
Protege os dados de aplicação e mantém-nos durante anos sem qualquer investimento de capital e com um mínimo de custos operacionais. Ele pode fazer backup de dados de servidores físicos e virtuais do Windows, além de cargas de trabalho de aplicativos, como o SQL Server e o SharePoint. Ele também pode ser usado pelo [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) para replicar dados protegidos no Azure para redundância e armazenamento de longo prazo.


Os dados protegidos no Azure Backup são armazenados num cofre de cópias de segurança localizado numa região geográfica específica. Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para maior resiliência.

### <a name="management-solutions"></a>Soluções de Gestão
[Azure monitor](../../security-center/security-center-intro.md) é a solução de gerenciamento de ti baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e na nuvem.


As [soluções de gerenciamento](../../monitoring/monitoring-solutions.md) são conjuntos predefinidos de lógicas que implementam um cenário de gerenciamento específico usando um ou mais serviços de Azure monitor. Diferentes soluções estão disponíveis da Microsoft e de parceiros que você pode adicionar facilmente à sua assinatura do Azure para aumentar o valor de seu investimento em Azure Monitor. Como parceiro, você pode criar suas próprias soluções para dar suporte aos seus aplicativos e serviços e fornecê-los aos usuários por meio dos modelos do Azure Marketplace ou Início Rápido.


![Soluções de Gestão](./media/operational-security/azure-operational-security-fig4.png)

Um bom exemplo de uma solução que usa vários serviços para fornecer funcionalidade adicional é a [solução gerenciamento de atualizações](../../automation/automation-update-management.md). Essa solução usa o agente de [logs de Azure monitor](../../log-analytics/log-analytics-queries.md) para Windows e Linux para coletar informações sobre as atualizações necessárias em cada agente. Ele grava esses dados no repositório de logs de Azure Monitor, onde você pode analisá-los com um painel incluído.

Quando você cria uma implantação, os runbooks na [automação do Azure](../../automation/automation-intro.md) são usados para instalar as atualizações necessárias. Todo este processo é gerido no portal e não tem de se preocupar com os detalhes subjacentes.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

A central de segurança do Azure ajuda a proteger seus recursos do Azure. Ele fornece monitoramento de segurança integrado e gerenciamento de políticas em suas assinaturas do Azure. Dentro do serviço, é possível definir políticas não apenas em relação às assinaturas do Azure, mas também a [grupos de recursos](../../azure-resource-manager/resource-group-overview.md#resource-groups), para que você possa ser mais granular.

### <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança

Uma política de segurança define o conjunto de controlos que são recomendados para os recursos na subscrição ou grupo de recursos especificados.

No Centro de Segurança, é possível definir políticas de acordo com os requisitos de segurança da sua empresa e com o tipo de aplicações ou sensibilidade dos dados.

![Recomendações e políticas de segurança](./media/operational-security/azure-operational-security-fig5.png)


As políticas habilitadas no nível da assinatura são propagadas automaticamente para todos os grupos de recursos dentro da assinatura, conforme mostrado no diagrama no lado direito:


### <a name="data-collection"></a>Recolha de dados

O Centro de Segurança recolhe dados das suas máquinas virtuais (VMs) para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo sobre ameaças. Quando sua primeira central de segurança de acesso, a coleta de dados é habilitada em todas as VMs em sua assinatura. A recolha de dados é recomendada, mas pode optar ativamente por não participar, desativando a recolha de dados na política do Centro de Segurança.

### <a name="data-sources"></a>Origens de dados

- O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

-   Serviços do Azure: Usa informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.

- Tráfego de rede: Usa metadados de exemplo de tráfego de rede da infraestrutura da Microsoft, como IP/porta de origem/destino, tamanho do pacote e protocolo de rede.

-   Soluções de parceiros: O usa alertas de segurança de soluções de parceiros integradas, como firewalls e soluções antimalware.

-   Suas máquinas virtuais: O usa informações de configuração e informações sobre eventos de segurança, como logs de eventos e auditoria do Windows, logs do IIS, mensagens de syslog e arquivos de despejo de memória de suas máquinas virtuais.

### <a name="data-protection"></a>Proteção de dados

Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados relacionados com segurança, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

-   Segregação de **dados**: Os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

-   **Acesso a dados**: Para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, o pessoal da Microsoft pode acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo arquivos de despejo de memória, eventos de criação de processos, instantâneos de disco de VM e artefatos, que pode incluir involuntariamente dados do cliente ou dados pessoais de suas máquinas virtuais. Seguimos os [termos e a política de privacidade do Microsoft Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), que informam que a Microsoft não usa dados do cliente nem Obtém informações sobre ele para anúncios ou para fins comerciais semelhantes.

-   **Uso de dados**: A Microsoft usa padrões e inteligência contra ameaças vistos em vários locatários para aprimorar nossos recursos de prevenção e detecção; fazemos isso de acordo com os compromissos de privacidade descritos em nossa [política de privacidade](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Localização de dados

O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida. O Centro de Segurança do Azure faz esta análise na mesma Geografia que a área de trabalho e elimina as cópias efémeras quando a análise estiver concluída. Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.

-   **Suas contas de armazenamento**: Uma conta de armazenamento é especificada para cada região em que as máquinas virtuais estão em execução. Isto permite-lhe armazenar dados na mesma região que a máquina virtual a partir da qual os dados são recolhidos.

-   **Armazenamento da central de segurança do Azure**: As informações sobre alertas de segurança, incluindo alertas de parceiros, recomendações e status de integridade de segurança são armazenadas centralmente, atualmente no Estados Unidos. Estas informações podem incluir informações de configuração relacionadas e eventos de segurança recolhidos das suas máquinas virtuais, conforme necessário para lhe fornecer o alerta de segurança, recomendação ou estado de funcionamento de segurança.


## <a name="azure-monitor"></a>Azure Monitor

A solução [Azure monitor logs de segurança](../../security-center/security-center-monitoring.md) e auditoria permite que a ti monitore ativamente todos os recursos, o que pode ajudar a minimizar o impacto dos incidentes de segurança. Azure Monitor logs Segurança e Auditoria têm domínios de segurança que podem ser usados para monitorar recursos. O domínio de segurança fornece acesso rápido às opções, para monitoramento de segurança os seguintes domínios são abordados em mais detalhes:

-   Avaliação de software maligno
-   Avaliação de atualizações
-   Identidade e acesso.

Azure Monitor fornece ponteiros para informações sobre tipos específicos de recursos. Ele oferece visualização, consulta, roteamento, alertas, dimensionamento automático e automação nos dados da infraestrutura do Azure (log de atividades) e de cada recurso individual do Azure (logs de diagnóstico).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Os aplicativos de nuvem são complexos com muitas partes móveis. O monitoramento fornece dados para garantir que seu aplicativo permaneça em funcionamento em um estado íntegro. Ele também ajuda a afastarr problemas em potencial ou a solucionar problemas anteriores.

Além disso, você pode usar dados de monitoramento para obter informações aprofundadas sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção do aplicativo, ou automatizar ações que, caso contrário, exigirão intervenção manual.

### <a name="azure-activity-log"></a>Registo de atividades do Azure


É um log que fornece informações sobre as operações que foram realizadas em recursos em sua assinatura. O log de atividades era conhecido anteriormente como "logs de auditoria" ou "logs operacionais", pois ele relata eventos de plano de controle para suas assinaturas.

![Registo de atividades do Azure](./media/operational-security/azure-operational-security-fig7.png)

Usando o log de atividades, você pode determinar "o que, quem e quando" para qualquer operação de gravação (PUT, POST, excluir) tomada nos recursos em sua assinatura. Também é possível compreender o estado da operação e outras propriedades relevantes. O log de atividades não inclui operações de leitura (GET) ou operações para recursos que usam o modelo clássico.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Esses logs são emitidos por um recurso e fornecem dados avançados e frequentes sobre a operação desse recurso. O conteúdo estes registos varia consoante o tipo de recurso.

Por exemplo, os logs do sistema de eventos do Windows são uma categoria de log de diagnóstico para VMs e logs de BLOB, tabela e fila são categorias de logs de diagnóstico para contas de armazenamento.

Os logs de diagnóstico são diferentes do [log de atividades (anteriormente conhecido como log de auditoria ou log operacional)](../../azure-monitor/platform/activity-logs-overview.md). O log de atividades fornece informações sobre as operações que foram executadas em recursos em sua assinatura. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo próprio recurso.

### <a name="metrics"></a>Métricas

Azure Monitor permite que você consuma a telemetria para obter visibilidade do desempenho e da integridade de suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do Azure são as métricas (também chamadas de contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias maneiras de configurar e consumir [](../../monitoring/monitoring-data-collection.md) essas métricas para monitoramento e solução de problemas. As métricas são uma fonte valiosa de telemetria e permitem que você realize as seguintes tarefas:

-   **Acompanhe o desempenho** do recurso (como uma VM, um site ou um aplicativo lógico) plotando suas métricas em um gráfico do portal e fixando esse gráfico a um painel.

-   **Seja notificado sobre um problema** que afete o desempenho do recurso quando uma métrica cruzar um determinado limite.

-   **Configure ações automatizadas**, como o dimensionamento automático de um recurso ou o acionamento de um runbook quando uma métrica cruzar um determinado limite.

-   **Execute análises avançadas** ou relatórios sobre desempenho ou tendências de uso de seu recurso.

-   **Arquive** o histórico de desempenho ou de integridade de seu recurso para fins de conformidade ou auditoria.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

É o recurso no Azure que permite a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. Atualmente, há suporte para [funções Web e de trabalho do serviço de nuvem do Azure](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [máquinas virtuais do Azure](../../virtual-machines/windows/overview.md) executando o Microsoft Windows e [Service Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md). Outros serviços do Azure têm seu próprio diagnóstico separado.

## <a name="azure-network-watcher"></a>Observador de rede do Azure

A auditoria da segurança de rede é vital para detectar vulnerabilidades de rede e garantir a conformidade com a segurança de ti e o modelo de governança regulatória. Com a exibição de grupo de segurança, você pode recuperar o grupo de segurança de rede e as regras de segurança configuradas e as regras de segurança em vigor. Com a lista de regras aplicadas, você pode determinar as portas que estão abertas e avaliar a vulnerabilidade da rede.

O observador de [rede](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional que permite monitorar e diagnosticar condições em um nível de rede em, para e do Azure. As ferramentas de diagnóstico e visualização de rede disponíveis com o observador de rede ajudam você a entender, diagnosticar e obter informações sobre sua rede no Azure. Esse serviço inclui captura de pacote, próximo salto, verificação de fluxo de IP, exibição de grupo de segurança, logs de fluxo de NSG. O monitoramento em nível de cenário fornece uma exibição de ponta a ponta dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

![Observador de rede do Azure](./media/operational-security/azure-operational-security-fig8.png)

O observador de rede atualmente tem os seguintes recursos:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Logs de auditoria</a>** – operações executadas como parte da configuração de redes são registradas em log. Esses logs podem ser exibidos no portal do Azure ou recuperados usando ferramentas da Microsoft, como Power BI ou ferramentas de terceiros. Os logs de auditoria estão disponíveis por meio do portal, do PowerShell, da CLI e da API REST. Para obter mais informações sobre logs de auditoria, consulte operações de auditoria com o Gerenciador de recursos. Os logs de auditoria estão disponíveis para operações realizadas em todos os recursos de rede.


-   Verificações de **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">fluxo de IP</a>** – verifica se um pacote é permitido ou negado com base em parâmetros de pacote de cinco tuplas de informações de fluxo (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote for negado por um grupo de segurança de rede, a regra e o grupo de segurança de rede que negou o pacote serão retornados.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Próximo salto</a>** – determina o próximo salto para os pacotes que estão sendo roteados na malha de rede do Azure, permitindo que você diagnostique quaisquer rotas definidas pelo usuário mal configuradas.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Exibição de grupo de segurança</a>** – Obtém as regras de segurança efetivas e aplicadas que são aplicadas em uma VM.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Log de fluxo do NSG</a>** – logs de fluxo para grupos de segurança de rede permitem capturar logs relacionados ao tráfego permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por informações de 5 tuplas – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.

## <a name="azure-storage-analytics"></a>Análise do Armazenamento do Azure

[Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade sobre solicitações para um serviço de armazenamento. As transações são relatadas no nível de operação da API e no nível do serviço de armazenamento, e a capacidade é relatada no nível do serviço de armazenamento. Os dados de métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com solicitações feitas no serviço de armazenamento e melhorar o desempenho de aplicativos que usam um serviço.

[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) executa o registro em log e fornece dados de métrica para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Análise de Armazenamento log está disponível para os [Serviços BLOB, fila e tabela](../../storage/common/storage-introduction.md). Análise de Armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha em um serviço de armazenamento.

Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. As entradas de log serão criadas somente se houver solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividade em seu ponto de extremidade de BLOB, mas não em seus pontos de extremidades de tabela ou fila, somente os logs pertencentes ao serviço blob serão criados.

Para usar Análise de Armazenamento, você deve habilitá-lo individualmente para cada serviço que deseja monitorar. Você pode habilitá-lo no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [monitorar uma conta de armazenamento no portal do Azure](../../storage/common/storage-monitor-storage-account.md). Você também pode habilitar Análise de Armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use a operação definir propriedades do serviço para habilitar Análise de Armazenamento individualmente para cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando as APIs serviço de BLOB e serviço de tabela.

Análise de Armazenamento tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para sua conta de armazenamento. Todos os logs são armazenados em blobs de [blocos](../../storage/common/storage-analytics.md) em um contêiner chamado $logs, que são criados automaticamente quando análise de armazenamento está habilitado para uma conta de armazenamento.

As seguintes ações executadas por Análise de Armazenamento são faturáveis:

-   Solicitações para criar BLOBs para registro em log
-   Solicitações para criar entidades de tabela para métricas.

> [!Note]
> Para obter mais informações sobre cobrança e políticas de retenção de dados, consulte [análise de armazenamento e cobrança](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Para obter um desempenho ideal, você deseja limitar o número de discos altamente utilizados anexados à máquina virtual para evitar uma limitação possível. Se todos os discos não estiverem sendo altamente utilizados ao mesmo tempo, a conta de armazenamento poderá dar suporte a um disco de número maior.

> [!Note]
> Para obter mais informações sobre limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](../../storage/common/storage-scalability-targets.md).


Os seguintes tipos de solicitações autenticadas e anônimas são registrados.

| Autenticados  | Anónimo|
| :------------- | :-------------|
| Pedidos com êxito | Pedidos com êxito |
|Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros | Solicitações que usam uma SAS (assinatura de acesso compartilhado), incluindo solicitações com falha e bem-sucedidas |
| Solicitações que usam uma SAS (assinatura de acesso compartilhado), incluindo solicitações com falha e bem-sucedidas |Erros de tempo limite para o cliente e o servidor |
|   Solicitações para dados de análise |    Solicitações GET com falha com o código de erro 304 (não modificado) |
| As solicitações feitas por Análise de Armazenamento em si, como criação ou exclusão de log, não são registradas. Uma lista completa dos dados registrados em log está documentada na [análise de armazenamento operações registradas e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e tópicos de [análise de armazenamento formato de log](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) . | Todas as outras solicitações anônimas com falha não são registradas. Uma lista completa dos dados registrados em log está documentada na [análise de armazenamento operações registradas e mensagens de status](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [análise de armazenamento formato de log](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

O Azure AD também inclui um pacote completo de recursos de gerenciamento de identidade, incluindo autenticação multifator, registro de dispositivo, gerenciamento de senhas de autoatendimento, gerenciamento de grupo de autoatendimento, gerenciamento de conta com privilégios, acesso baseado em função controle, monitoramento de uso de aplicativos, auditoria avançada e monitoramento e alertas de segurança.

-   Melhore a segurança do aplicativo com a autenticação multifator do Azure AD e o acesso condicional.

-   Monitore o uso do aplicativo e proteja seus negócios contra ameaças avançadas com relatórios e monitoramento de segurança.

O Azure Active Directory (Azure AD) inclui relatórios de segurança, atividade e auditoria para o diretório. [O Azure Active Directory relatório de auditoria](../../active-directory/active-directory-reporting-azure-portal.md) ajuda os clientes a identificar ações privilegiadas que ocorreram em seus Azure Active Directory. As ações privilegiadas incluem alterações de elevação (por exemplo, criação de função ou redefinições de senha), alteração das configurações de política (por exemplo, políticas de senha) ou alterações na configuração de diretório (por exemplo, alterações nas configurações de Federação de domínio).

Os relatórios fornecem o registro de auditoria para o nome do evento, o ator que realizou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os clientes podem recuperar a lista de eventos de auditoria para seus Azure Active Directory por meio do [portal do Azure](https://portal.azure.com/), conforme descrito em [exibir seus logs de auditoria](../../active-directory/reports-monitoring/overview-reports.md). Aqui está uma lista dos relatórios incluídos:

| Relatórios de segurança  | Relatórios de atividade| Relatórios de auditoria |
| :------------- | :-------------| :-------------|
|Inícios de sessão de fontes desconhecidas | Utilização da aplicação: resumo | Relatório de auditoria de diretório |
|Inícios de sessão após várias falhas | Utilização da aplicação: detalhado |   |
|Inícios de sessão de várias localizações geográficas | Dashboard de aplicações |  |
|Inícios de sessão de endereços IP com atividade suspeita |Erros de aprovisionamento de contas |  |
|Atividades irregulares de início de sessão |Dispositivos de utilizadores individuais |  |
|Inícios de sessão de dispositivos possivelmente infetados |Atividade de utilizadores individuais |   |
|Utilizadores com atividade anómala de início de sessão |Relatório de atividade de grupos |   |
| |Relatório de atividade de registo de reposição de palavra-passe |   |
| |Atividade de reposição de palavra-passe |   |



Os dados desses relatórios podem ser úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As [APIs](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) de relatório do Azure ad fornecem acesso programático aos dados por meio de um conjunto de APIs baseadas em REST. Você pode chamar essas APIs de várias ferramentas e linguagens de programação.

Os eventos no relatório de auditoria do Azure AD são mantidos por 180 dias.

> [!Note]
> Para obter mais informações sobre retenção em relatórios, consulte [Azure Active Directory relatórios políticas de retenção](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Para clientes interessados em armazenar seus [eventos de auditoria](../../active-directory/active-directory-reporting-activity-audit-logs.md) por períodos de retenção mais longos, a API de relatório pode ser usada para extrair eventos de auditoria regularmente para um armazenamento de dados separado.

## <a name="summary"></a>Resumo

Este artigo resume a proteção de sua privacidade e proteção de dados, oferecendo software e serviços que ajudam a gerenciar a infraestrutura de ti de sua organização. A Microsoft reconhece que, ao confiar em seus dados para outras pessoas, essa confiança requer uma rigorosa segurança. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Proteger e proteger dados é uma prioridade principal na Microsoft.

Este artigo explica

-   Como os dados são coletados, processados e protegidos no Azure Monitor Suite.

-   Analise rapidamente eventos em várias origens de dados. Identifique riscos de segurança e compreenda o âmbito e o impacto das ameaças e ataques para reduzir os danos provocados por falhas na segurança.

-   Identifique padrões de ataque ao ver o tráfego IP malicioso de saída e os tipos de ameaças maliciosas. Entenda a postura de segurança de todo o seu ambiente, independentemente da plataforma.

-   Capture todos os dados de log e eventos necessários para uma auditoria de segurança ou conformidade. A barra de tempo e os recursos necessários para fornecer uma auditoria de segurança com um conjunto de dados de log e evento completo, pesquisável e exportável.

<ul>
<li>Colete eventos relacionados à segurança, auditoria e análise de violação para ter um olho mais próximo de seus ativos:</li>
<ul>
<li>Postura de segurança</li>
<li>Problema notável</li>
<li>Ameaças de resumos</li>
</ul>
</ul>

## <a name="next-steps"></a>Próximos Passos

- [Design e segurança operacional](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft projeta seus serviços e software com a segurança em mente para ajudar a garantir que sua infraestrutura de nuvem seja resiliente e seja protegida contra ataques.

- [Logs de Azure Monitor | Segurança e Conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance)

Use a análise e os dados de segurança da Microsoft para realizar uma detecção de ameaças mais inteligente e eficaz.

- [Planejamento e operações da central de segurança do Azure](../../security-center/security-center-planning-and-operations-guide.md) Um conjunto de etapas e tarefas que você pode seguir para otimizar o uso da central de segurança com base nos requisitos de segurança e no modelo de gerenciamento de nuvem de sua organização.

