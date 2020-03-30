---
title: Segurança Operacional Azure Microsoft Docs
description: Saiba mais sobre os registos do Microsoft Azure Monitor, os seus serviços e como funciona.
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
ms.openlocfilehash: 34c0c52945abc6e0ab74b1cb180581c76464bee8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749952"
---
# <a name="azure-operational-security"></a>Segurança Operacional Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Descrição geral
Sabemos que a segurança é o trabalho um na nuvem e como é importante que encontre informações precisas e oportunas sobre a segurança do Azure. Uma das melhores razões para utilizar o Azure para as suas aplicações e serviços é tirar partido da grande variedade de ferramentas e capacidades de segurança disponíveis. Estas ferramentas e capacidades ajudam a tornar possível criar soluções seguras na plataforma Segura Azure. O Windows Azure deve fornecer confidencialidade, integridade e disponibilidade de dados dos clientes, permitindo também uma responsabilização transparente.

Para ajudar os clientes a entender melhor a gama de controlos de segurança implementados dentro do Microsoft Azure a partir das perspetivas operacionais do cliente e da Microsoft, este livro branco, "Azure Operational Security", é escrito que fornece um abrangente Veja a segurança operacional disponível com o Windows Azure.

### <a name="azure-platform"></a>Plataforma Azure
O Azure é uma plataforma pública de serviço na nuvem que suporta uma ampla seleção de sistemas operativos, linguagens de programação, quadros, ferramentas, bases de dados e dispositivos. Pode executar contentores Linux com integração Docker; construir aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js; construir back-ends para dispositivos iOS, Android e Windows. O serviço Azure Cloud suporta as mesmas tecnologias que milhões de desenvolvedores e profissionais de TI já confiam e confiam.

Quando você constrói, ou migra ativos de TI para, um fornecedor público de serviços na nuvem você está confiando nas capacidades dessa organização para proteger suas aplicações e dados com os serviços e os controles que eles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

A infraestrutura da Azure é projetada desde a instalação até aplicações para hospedar milhões de clientes simultaneamente, e fornece uma base de confiança sobre a qual as empresas podem satisfazer os seus requisitos de segurança. Além disso, o Azure oferece-lhe um vasto leque de opções de segurança configuráveis e a capacidade de controlá-las para que possa personalizar a segurança para satisfazer os requisitos únicos das implementações da sua organização. Este documento irá ajudá-lo a entender como as capacidades de segurança do Azure podem ajudá-lo a cumprir estes requisitos.

### <a name="abstract"></a>Abstract
A Segurança Operacional Azure refere-se aos serviços, controlos e funcionalidades disponíveis para os utilizadores para proteger os seus dados, aplicações e outros ativos no Microsoft Azure. A Segurança Operacional Azure baseia-se num quadro que incorpora o conhecimento adquirido através de várias capacidades exclusivas da Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center, e uma profunda consciência do cenário de ameaça à cibersegurança.

Este livro branco descreve a abordagem da Microsoft à Segurança Operacional Azure dentro da plataforma cloud do Microsoft Azure e abrange os seguintes serviços:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Centro de Segurança do Azure](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Observador da Rede Azure](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Análise de Armazenamento Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Diretório Azure Ative](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Registos do Microsoft Azure Monitor

Os registos do Microsoft Azure Monitor são a solução de gestão de TI para a nuvem híbrida. Utilizados sozinhos ou para alargar a implementação do Centro de Sistemas existente, os registos do Azure Monitor conferem-lhe a máxima flexibilidade e controlo para a gestão baseada na nuvem da sua infraestrutura.

![Registos do Azure Monitor](./media/operational-security/azure-operational-security-fig1.png)

Com os registos do Monitor Azure, pode gerir qualquer instância em qualquer nuvem, incluindo no local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo mais baixo do que soluções competitivas. Construído para o mundo em primeira nuvem, os registos do Azure Monitor oferecem uma nova abordagem para gerir a sua empresa que é a forma mais rápida e rentável de enfrentar novos desafios de negócio e acomodar novas cargas de trabalho, aplicações e ambientes em nuvem.

### <a name="azure-monitor-services"></a>Serviços Azure Monitor

A funcionalidade principal dos registos do Monitor Azure é fornecida por um conjunto de serviços que funcionam no Azure. Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes.

| Serviço  | Descrição|
| :------------- | :-------------|
| Registos do Azure Monitor | Monitorizar e analisar a disponibilidade e o desempenho de diferentes recursos, incluindo computadores e máquinas virtuais. |
|Automatização | Automatizar processos manuais e impor configurações para computadores e máquinas virtuais. |
| Cópia de segurança | Volte e restaure dados críticos. |
| Site Recovery | Providenciar elevada disponibilidade para aplicações críticas. |

### <a name="azure-monitor-logs"></a>Registos do Azure Monitor

[Os registos do Azure Monitor](https://azure.microsoft.com/documentation/services/log-analytics) fornecem serviços de monitorização recolhendo dados de recursos geridos para um repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.


Este método permite-lhe consolidar dados de várias fontes, para que possa combinar dados dos seus serviços Azure com o ambiente existente no local. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.


![Registos do Azure Monitor](./media/operational-security/azure-operational-security-fig2.png)

O serviço Azure Monitor gere os seus dados baseados na nuvem de forma segura utilizando os seguintes métodos:
-   segregação de dados
-   retenção de dados
-   segurança física
-   gestão de incidentes
-   conformidade
-   certificações de normas de segurança

### <a name="azure-backup"></a>Azure Backup

A [Azure Backup](https://azure.microsoft.com/documentation/services/backup) fornece serviços de backup de dados e restauro e faz parte do conjunto de produtos e serviços do Azure Monitor.
Protege os dados de aplicação e mantém-nos durante anos sem qualquer investimento de capital e com um mínimo de custos operacionais. Pode fazer cópias de segurança de dados de servidores do Windows físicos e virtuais, além de cargas de trabalho de aplicações como o SQL Server e o SharePoint. Também pode ser usado pelo [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) para replicar dados protegidos para o Azure para redundância e armazenamento a longo prazo.


Os dados protegidos no Azure Backup são armazenados num cofre de cópias de segurança localizado numa região geográfica específica. Os dados são replicados dentro da mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para maior resiliência.

### <a name="management-solutions"></a>Soluções de Gestão
[O Azure Monitor](../../security-center/security-center-intro.md) é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e proteger as suas infraestruturas no local e na nuvem.


[As Soluções](../../monitoring/monitoring-solutions.md) de Gestão são conjuntos de lógicas pré-embalados que implementam um determinado cenário de gestão utilizando um ou mais serviços do Azure Monitor. Diferentes soluções estão disponíveis da Microsoft e de parceiros que pode facilmente adicionar à sua subscrição Azure para aumentar o valor do seu investimento no Monitor Azure. Como parceiro, pode criar as suas próprias soluções para apoiar as suas aplicações e serviços e fornecê-las aos utilizadores através do Azure Marketplace ou modelos de quickstart.


![Soluções de Gestão](./media/operational-security/azure-operational-security-fig4.png)

Um bom exemplo de uma solução que utiliza vários serviços para fornecer funcionalidadeadicional é a [solução De Gestão de Atualizações.](../../automation/automation-update-management.md) Esta solução utiliza o agente de [registos Do Monitor Azure](../../log-analytics/log-analytics-queries.md) para Windows e Linux para recolher informações sobre as atualizações necessárias em cada agente. Escreve estes dados para o repositório de registos do Monitor Azure onde pode analisá-los com um dashboard incluído.

Quando cria uma implementação, os livros de execução em [Automatização Azure](../../automation/automation-intro.md) são utilizados para instalar as atualizações necessárias. Todo este processo é gerido no portal e não tem de se preocupar com os detalhes subjacentes.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

O Azure Security Center ajuda a proteger os seus recursos Azure. Fornece monitorização integrada de segurança e gestão de políticas em todas as suas subscrições do Azure. Dentro do serviço, você é capaz de definir policiais não só contra as suas subscrições Azure, mas também contra [Grupos de Recursos](../../azure-resource-manager/management/overview.md#resource-groups), para que você possa ser mais granular.

### <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança

Uma política de segurança define o conjunto de controlos que são recomendados para os recursos na subscrição ou grupo de recursos especificados.

No Centro de Segurança, é possível definir políticas de acordo com os requisitos de segurança da sua empresa e com o tipo de aplicações ou sensibilidade dos dados.

![Recomendações e políticas de segurança](./media/operational-security/azure-operational-security-fig5.png)


As políticas que são ativadas no nível de subscrição propagam-se automaticamente a todos os grupos de recursos dentro da subscrição, como mostra o diagrama do lado direito:


### <a name="data-collection"></a>Recolha de dados

O Centro de Segurança recolhe dados das suas máquinas virtuais (VMs) para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo sobre ameaças. Quando o seu primeiro access Security Center, a recolha de dados está ativada em todos os VMs da sua subscrição. A recolha de dados é recomendada, mas pode optar ativamente por não participar, desativando a recolha de dados na política do Centro de Segurança.

### <a name="data-sources"></a>Origens de dados

- O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

-   Serviços do Azure: utiliza informações sobre a configuração dos serviços do Azure que implementou através da comunicação com o fornecedor de recursos desses serviços.

- Tráfego de Rede: utiliza metadados de tráfego de rede de amostragem da infraestrutura da Microsoft, tais como porta/IP de origem/destino, tamanho do pacote e protocolo de rede.

-   Soluções de Parceiros: utiliza alertas de segurança das soluções de parceiros integradas, tais como firewalls e soluções antimalware.

-   As suas Máquinas Virtuais: utiliza informações de configuração e informações sobre eventos de segurança, tais como eventos do Windows e registos de auditoria, registos do IIS, mensagens do syslog e ficheiros de informação de falha de sistema das suas máquinas virtuais.

### <a name="data-protection"></a>Proteção de dados

Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados relacionados com segurança, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

-   **Segregação de dados**: os dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

-   **Acesso**a dados : Para fornecer recomendações de segurança e investigar potenciais ameaças à segurança, o pessoal da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços do Azure, incluindo ficheiros de despejo de falhas, eventos de criação de processos, instantâneos e artefactos de disco VM, que podem incluir involuntariamente dados do Cliente ou dados pessoais das suas máquinas virtuais. Aderimos à Declaração de Termos e Privacidade dos [Serviços Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)da Microsoft, que declara que a Microsoft não utiliza dados do cliente nem obtém informações dos mesmos para qualquer publicidade ou fins comerciais similares.

-   **Utilização de dados**: a Microsoft utiliza os padrões e as informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção. Fazemo-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de Privacidade](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Localização dos dados

O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida. O Centro de Segurança do Azure faz esta análise na mesma Geografia que a área de trabalho e elimina as cópias efémeras quando a análise estiver concluída. Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.

-   **Contas de Armazenamento**: É especificada uma conta de armazenamento para cada região onde as máquinas virtuais estão em funcionamento. Isto permite-lhe armazenar dados na mesma região que a máquina virtual a partir da qual os dados são recolhidos.

-   **Armazenamento do Centro de Segurança do Azure**: as informações sobre alertas de segurança, incluindo alertas de parceiros, recomendações e estado de funcionamento da segurança, são armazenadas de forma central, atualmente nos Estados Unidos. Estas informações podem incluir informações de configuração relacionadas e eventos de segurança recolhidos das suas máquinas virtuais, conforme necessário para lhe fornecer o alerta de segurança, recomendação ou estado de funcionamento de segurança.


## <a name="azure-monitor"></a>Azure Monitor

A solução de [registos de Segurança](../../security-center/security-center-monitoring.md) e Auditoria do Azure Monitor permite que a TI monitorize ativamente todos os recursos, o que pode ajudar a minimizar o impacto dos incidentes de segurança. Os registos do Azure Monitor Segurança e Auditoria têm domínios de segurança que podem ser usados para monitorizar recursos. O domínio de segurança proporciona acesso rápido às opções, para monitorização de segurança os seguintes domínios são cobertos por mais detalhes:

-   Avaliação de software maligno
-   Avaliação de atualização
-   Identidade e Acesso.

O Monitor Azure fornece indicações sobre tipos específicos de recursos. Oferece visualização, consulta, encaminhamento, alerta, escala automática e automatização em dados tanto da infraestrutura Azure (Registo de Atividade) como de cada recurso Azure individual (Registos de Diagnóstico).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


As aplicações em nuvem são complexas com muitas partes móveis. A monitorização fornece dados para garantir que a sua aplicação permanece em funcionamento num estado saudável. Também o ajuda a evitar potenciais problemas ou problemas para além dos.

Além disso, pode utilizar dados de monitorização para obter informações profundas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a manutenção da aplicação, ou automatizar ações que de outra forma exigiriam intervenção manual.

### <a name="azure-activity-log"></a>Registo de Atividades do Azure


É um registo que fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição. O Registo de Atividades era anteriormente conhecido como "Registos de Auditoria" ou "Registos Operacionais", uma vez que reporta eventos de control-plane para as suas subscrições.

![Registo de Atividades do Azure](./media/operational-security/azure-operational-security-fig7.png)

Utilizando o Registo de Atividades, pode determinar o "quê, quem e quando" para quaisquer operações de escrita (PUT, POST, DELETE) adquiridas com os recursos da sua subscrição. Também pode compreender o estado da operação e outras propriedades relevantes. O Registo de Atividades não inclui operações ou operações de leitura (GET) para recursos que utilizem o modelo Classic.

### <a name="azure-diagnostic-logs"></a>Registos de Diagnóstico Azure

Estes registos são emitidos por um recurso e fornecem dados ricos e frequentes sobre o funcionamento desse recurso. O conteúdo destes registos varia por tipo de recurso.

Por exemplo, os registos do sistema de eventos windows são uma categoria de Registo de Diagnóstico para VMs e os registos de blob, mesa e fila são categorias de Registos de Diagnóstico para contas de armazenamento.

Os registos de diagnóstico diferem do Registo de [Atividade (anteriormente conhecido como Registo](../../azure-monitor/platform/platform-logs-overview.md)de Auditoria ou Registo Operacional) . O registo de Atividades fornece informações sobre as operações que foram realizadas sobre os recursos na sua subscrição. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo próprio recurso.

### <a name="metrics"></a>Métricas

O Azure Monitor permite-lhe consumir telemetria para ganhar visibilidade no desempenho e saúde das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria Azure são as métricas (também chamadas contadores de desempenho) emitidas pela maioria dos recursos do Azure. O Azure Monitor fornece várias formas de configurar e consumir estas [métricas](../../monitoring/monitoring-data-collection.md) para monitorização e resolução de problemas. As métricas são uma valiosa fonte de telemetria e permitem-lhe fazer as seguintes tarefas:

-   **Acompanhe o desempenho** do seu recurso (como uma VM, website ou aplicação lógica) traçando as suas métricas num gráfico de portal e fixando esse gráfico num dashboard.

-   **Seja notificado de um problema** que impacte o desempenho do seu recurso quando uma métrica cruza um determinado limiar.

-   **Configure as ações automatizadas**, tais como a escala automática de um recurso ou o disparo de um livro de execução quando uma métrica cruza um determinado limiar.

-   **Execute análises avançadas** ou relatórios sobre as tendências de desempenho ou utilização do seu recurso.

-   **Arquivar** o desempenho ou histórico de saúde do seu recurso para efeitos de conformidade ou auditoria.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

É a capacidade dentro do Azure que permite a recolha de dados de diagnóstico numa aplicação implementada. Pode utilizar a extensão de diagnóstico de várias fontes diferentes. Atualmente suportados estão [azure Cloud Service Web e Funções de Trabalhador,](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) [Máquinas Virtuais Azure](../../virtual-machines/windows/overview.md) que executam o Microsoft Windows e o Tecido de [Serviço.](../../azure-monitor/platform/diagnostics-extension-overview.md) Outros serviços Azure têm os seus próprios diagnósticos separados.

## <a name="azure-network-watcher"></a>Observador de Rede do Azure

A auditoria à segurança da sua rede é vital para detetar vulnerabilidades de rede e garantir o cumprimento do seu modelo de segurança e governança regulamentar. Com a visão do Grupo de Segurança, pode recuperar as regras configuradas do Grupo de Segurança da Rede e as regras de segurança e as regras de segurança eficazes. Com a lista de regras aplicadas, pode determinar as portas abertas e avaliar a vulnerabilidade da rede.

[O Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional que lhe permite monitorizar e diagnosticar condições a nível de rede, de e para o Azure. As ferramentas de diagnóstico e visualização de rede disponíveis com o Network Watcher ajudam-no a compreender, diagnosticar e obter informações sobre a sua rede em Azure. Este serviço inclui a captura de pacotes, o próximo salto, verificação de fluxo IP, vista de grupo de segurança, registos de fluxo NSG. A monitorização do nível dos cenários proporciona uma visão final dos recursos de rede em contraste com a monitorização individual dos recursos da rede.

![Observador de Rede do Azure](./media/operational-security/azure-operational-security-fig8.png)

O Network Watcher tem atualmente as seguintes capacidades:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Registos de Auditoria</a>**- As operações realizadas como parte da configuração das redes são registadas. Estes registos podem ser vistos no portal Azure ou recuperados utilizando ferramentas da Microsoft, como o Power BI ou ferramentas de terceiros. Os registos de auditoria estão disponíveis através do portal PowerShell, CLI e Rest API. Para obter mais informações sobre registos de Auditoria, consulte operações de Auditoria com O Gestor de Recursos. Os registos de auditoria estão disponíveis para operações realizadas em todos os recursos da rede.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Ip flow verifica</a>** - Verifica se um pacote é permitido ou negado com base em parâmetros de pacote de 5 tuple (Destination IP, Source IP, Destination Port, Source Port, Source Port e Protocol). Se o pacote for negado por um Grupo de Segurança da Rede, a regra e o Grupo de Segurança da Rede que negou o pacote são devolvidos.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Próximo salto</a>** - Determina o próximo salto para os pacotes que estão a ser encaminhados no Tecido da Rede Azure, permitindo-lhe diagnosticar quaisquer rotas mal configuradas pelo utilizador.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Visão</a>** de grupo de segurança - Obtém as regras de segurança eficazes e aplicadas que são aplicadas num VM.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Registos de fluxo NSG</a>** - Registos de fluxo para Grupos de Segurança de Rede permitem-lhe capturar registos relacionados com o tráfego que são permitidos ou negados pelas regras de segurança do grupo. O fluxo é definido por uma informação de 5 tuple – SOURCE IP, Destination IP, Source Port, Destination Port e Protocol.

## <a name="azure-storage-analytics"></a>Análise de Armazenamento Azure

[O Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluam estatísticas de transações agregadas e dados de capacidade sobre pedidos a um serviço de armazenamento. As transações são reportadas tanto ao nível da operação API como ao nível do serviço de armazenamento, e a capacidade é reportada ao nível do serviço de armazenamento. Os dados das métricas podem ser usados para analisar o uso do serviço de armazenamento, diagnosticar problemas com pedidos feitos contra o serviço de armazenamento, e melhorar o desempenho de aplicações que utilizam um serviço.

[O Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) realiza a exploração madeireira e fornece dados métricos para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas com a sua conta de armazenamento. O registo de armazenamento Analytics está disponível para os [serviços Blob, Queue e Table.](../../storage/common/storage-introduction.md) A Análise de Armazenamento regista informações detalhadas sobre os pedidos com êxito e com falha feitos a um serviço de armazenamento.

Estas informações podem ser utilizadas para monitorizar os pedidos individuais e diagnosticar problemas num serviço de armazenamento. Os pedidos são registados com o melhor esforço. As entradas de registo só são criadas se houver pedidos contra o ponto final do serviço. Por exemplo, se uma conta de armazenamento tiver atividade no seu ponto final blob, mas não nos seus pontos finais de Tabela ou Fila, apenas são criados registos relativos ao serviço Blob.

Para utilizar o Storage Analytics, deve ative-o individualmente para cada serviço que pretende monitorizar. Pode permitir no [portal Azure;](https://portal.azure.com/) para mais detalhes, consulte Monitor uma conta de [armazenamento no portal Azure](../../storage/common/storage-monitor-storage-account.md). Também pode ativar o Storage Analytics programaticamente através da API REST ou da biblioteca do cliente. Utilize a operação set Service Properties para ativar o Storage Analytics individualmente para cada serviço.

Os dados agregados são armazenados numa bolha bem conhecida (para exploração madeireira) e em tabelas bem conhecidas (para métricas), que podem ser acedidas através do serviço Blob e das APIs do serviço de mesa.

O Storage Analytics tem um limite de 20 TB na quantidade de dados armazenados que é independente do limite total para a sua conta de armazenamento. Todos os registos são armazenados em [blocos num](../../storage/common/storage-analytics.md) recipiente chamado $logs, que são automaticamente criados quando o Storage Analytics está ativado para uma conta de armazenamento.

As seguintes ações realizadas pelo Storage Analytics são faturadas:

-   Pedidos para criar bolhas para exploração madeireira
-   Pedidos para criar entidades de tabela para métricas.

> [!Note]
> Para obter mais informações sobre as políticas de faturação e retenção de dados, consulte [Storage Analytics e Billing](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Para um desempenho ótimo, pretende limitar o número de discos altamente utilizados ligados à máquina virtual para evitar uma possível aceleração. Se todos os discos não estiverem a ser altamente utilizados ao mesmo tempo, a conta de armazenamento pode suportar um disco de número maior.

> [!Note]
> Para obter mais informações sobre os limites da conta de armazenamento, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](../../storage/common/scalability-targets-standard-account.md).


Registam-se os seguintes tipos de pedidos autenticados e anónimos.

| Autenticado  | Anónimo|
| :------------- | :-------------|
| Pedidos com êxito | Pedidos com êxito |
|Pedidos falhados, incluindo tempo limite, limitação, rede, autorização e outros erros | Pedidos que utilizem uma Assinatura de Acesso Partilhado (SAS), incluindo pedidos falhados e bem sucedidos |
| Pedidos que utilizem uma Assinatura de Acesso Partilhado (SAS), incluindo pedidos falhados e bem sucedidos |Erros de tempo limite para o cliente e o servidor |
|   Pedidos de dados de análise |    Pedidos GET falhados com código de erro 304 (Não Modificado) |
| Os pedidos feitos pela própria Storage Analytics, tais como criação de registo ou eliminação, não são registados. Uma lista completa dos dados registados está documentada nos tópicos de [Operações e Mensagens](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) de Estado de Armazenamento Analytics e de Formato de [Registo de Armazenamento.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) | Todos os outros pedidos anónimos falhados não estão registados. Uma lista completa dos dados registados está documentada no [Armazenamento Analytics Logged Operations and Status Messages and](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) Storage Analytics Log [Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

A Azure AD também inclui um conjunto completo de capacidades de gestão de identidade, incluindo autenticação multi-factor, registo de dispositivos, gestão de passwords self-service, gestão de grupode self-service, gestão de conta privilegiada, acesso baseado em papéis controlo, monitorização do uso de aplicações, auditoria rica e monitorização e alerta de segurança.

-   Melhorar a segurança da aplicação com a autenticação multifactor Azure AD e acesso condicional.

-   Monitorize o uso da aplicação e proteja o seu negócio de ameaças avançadas com relatórios e monitorização de segurança.

O Azure Active Directory (Azure AD) inclui relatórios de segurança, atividade e auditoria para o diretório. O Relatório de [Auditoria do Diretório Ativo azure](../../active-directory/active-directory-reporting-azure-portal.md) ajuda os clientes a identificar ações privilegiadas que ocorreram no seu Diretório Ativo Azure. As ações privilegiadas incluem alterações de elevação (por exemplo, criação de papéis ou resets de passwords), alterações nas configurações de políticas (por exemplo, políticas de passwords), ou alterações na configuração do diretório (por exemplo, alterações nas definições da federação de domínio).

Os relatórios fornecem o registo de auditoria para o nome do evento, o ator que realizou a ação, o recurso-alvo afetado pela mudança, e a data e hora (na UTC). Os clientes são capazes de recuperar a lista de eventos de auditoria para o seu Diretório Ativo Azure através do [portal Azure](https://portal.azure.com/), como descrito no [View your Audit Logs](../../active-directory/reports-monitoring/overview-reports.md). Aqui está uma lista dos relatórios incluídos:

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



Os dados destes relatórios podem ser úteis para as suas aplicações, tais como sistemas SIEM, auditoria e ferramentas de inteligência empresarial. As [APIs](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) de reporte da AD Azure fornecem acesso programático aos dados através de um conjunto de APIs baseados em REST. Pode chamar estas APIs de várias linguagens e ferramentas de programação.

Os eventos do relatório de auditoria da AD Azure são retidos por 180 dias.

> [!Note]
> Para obter mais informações sobre a retenção nos relatórios, consulte as Políticas de Retenção do Relatório de Relatório de [Diretório Ativo do Azure](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

Para os clientes interessados em armazenar os seus [eventos de auditoria](../../active-directory/active-directory-reporting-activity-audit-logs.md) por períodos de retenção mais longos, a API reporte pode ser usada para puxar regularmente eventos de auditoria para uma loja de dados separada.

## <a name="summary"></a>Resumo

Este artigo resume a proteção da sua privacidade e a segurança dos seus dados, enquanto fornece software e serviços que o ajudam a gerir a infraestrutura de TI da sua organização. A Microsoft reconhece que quando confiam os seus dados a outros, essa confiança requer uma segurança rigorosa. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Proteger e proteger dados é uma prioridade máxima na Microsoft.

Este artigo explica

-   Como os dados são recolhidos, processados e protegidos na suite Azure Monitor.

-   Analise rapidamente os eventos através de várias fontes de dados. Identifique os riscos de segurança e compreenda o alcance e o impacto de ameaças e ataques para mitigar os danos de uma falha de segurança.

-   Identifique padrões de ataque visualizando tráfego ip malicioso de saída e tipos de ameaças maliciosas. Compreenda a postura de segurança de todo o seu ambiente, independentemente da plataforma.

-   Capture todos os dados de registo e evento necessários para uma auditoria de segurança ou conformidade. Reduza o tempo e os recursos necessários para fornecer uma auditoria de segurança com um conjunto completo, pesquisável e exportável de dados de registo e evento.

<ul>
<li>Colete eventos relacionados com a segurança, auditoria e análise de violação para manter um olho atento aos seus ativos:</li>
<ul>
<li>Postura de segurança</li>
<li>Questão notável</li>
<li>Resumos ameaças</li>
</ul>
</ul>

## <a name="next-steps"></a>Passos Seguintes

- [Conceção e segurança operacional](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft projeta os seus serviços e software com segurança em mente para ajudar a garantir que a sua infraestrutura em nuvem é resiliente e defendida de ataques.

- [Registos do Monitor Azure / Conformidade & de segurança](https://www.microsoft.com/cloud-platform/security-and-compliance)

Utilize dados e análises de segurança da Microsoft para realizar uma deteção de ameaças mais inteligente e eficaz.

- [Planeamento e operações do Centro](../../security-center/security-center-planning-and-operations-guide.md) de Segurança Azure Um conjunto de etapas e tarefas que pode seguir para otimizar o seu uso do Security Center com base nos requisitos de segurança da sua organização e no modelo de gestão de nuvens.

