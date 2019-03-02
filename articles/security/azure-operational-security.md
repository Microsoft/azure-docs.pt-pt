---
title: Segurança operacional do Azure | Documentos da Microsoft
description: Saiba mais sobre os registos do Microsoft Azure Monitor, seus serviços e como ele funciona.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 2f68156e593281acb46dbeb132f0f45db4b3dfb9
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244779"
---
# <a name="azure-operational-security"></a>Segurança operacional do Azure
## <a name="introduction"></a>Introdução

### <a name="overview"></a>Descrição geral
Sabemos que a segurança é terreno na cloud e como é importante que encontrará informações precisas e atualizadas sobre a segurança do Azure. Uma das razões para utilizar o Azure para as suas aplicações e serviços melhores é aproveitar o vasto leque de ferramentas de segurança e os recursos disponíveis. Essas ferramentas e recursos ajudam a que seja possível criar soluções seguras na plataforma do Azure segura. Windows Azure tem de fornecer confidencialidade, integridade e disponibilidade dos dados dos clientes, ao ativar também a responsabilidade transparente.

Para ajudar os clientes a compreender melhor a matriz de controlos de segurança implementada dentro do Microsoft Azure de ambos os o cliente e perspectivas operacionais do Microsoft, este white paper, "Segurança operacionais do Azure", é escrito que fornece uma abrangente ver a segurança operacional disponível com o Windows Azure.

### <a name="azure-platform"></a>Plataforma do Azure
O Azure é uma plataforma de serviço de nuvem pública que suporta uma seleção ampla de sistemas operativos, linguagens de programação, arquiteturas, ferramentas, bases de dados e dispositivos. Ele pode executar contentores do Linux com a integração de Docker; Crie aplicações com JavaScript, Python, .NET, PHP, Java e node. js; Crie back-ends para dispositivos iOS, Android e Windows dispositivos. Serviço Cloud do Azure suporta as mesmas tecnologias milhões de desenvolvedores e profissionais de TI confiam e confiam.

Quando criar ou migrar ativos de TI para, um fornecedor de serviços de nuvem pública está a depender capacidades dessa organização para proteger as suas aplicações e dados com os serviços e os controles fornecem para gerir a segurança dos seus ativos baseados na nuvem.

Infraestrutura do Azure foi concebida a partir do recurso para aplicações para alojar milhões de clientes em simultâneo, e fornece uma Fundação fidedigna na qual as empresas podem satisfazer os seus requisitos de segurança. Além disso, Azure disponibiliza um vasto leque de opções de segurança configuráveis e a capacidade para controlá-las para que possa personalizar a segurança para satisfazer os requisitos únicos das implementações da sua organização. Este documento irá ajuda-o a compreender como o Azure segurança recursos podem ajudá-lo a cumprir estes requisitos.

### <a name="abstract"></a>Abstrato
Segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos utilizadores para proteger seus dados, aplicações e outros ativos no Microsoft Azure. Segurança operacional do Azure baseia-se uma estrutura que incorpore os dados recolhidos através de vários recursos que são exclusivos da Microsoft, incluindo o Microsoft Security Development Lifecycle (SDL), o programa Microsoft Security Response Center, e o conhecimento profundo do campo das ameaças de cibersegurança.

Este white paper descreve a abordagem da Microsoft para segurança operacionais do Azure dentro da plataforma de cloud do Microsoft Azure e aborda seguintes serviços:
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Centro de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Observador de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-monitor-logs"></a>Registos do Microsoft Azure Monitor

Registos do Microsoft Azure Monitor é a solução de gestão de TI para a cloud híbrida. Usada sozinha ou para expandir a sua implementação existente do System Center, os registos de Monitor do Azure dá-lhe a máxima flexibilidade e o controle para a gestão baseada na nuvem da sua infraestrutura.

![Registos de Monitor do Azure](./media/azure-operational-security/azure-operational-security-fig1.png)

Com os registos do Azure Monitor, pode gerenciar qualquer instância em qualquer cloud, incluindo no local, Azure, AWS, Windows Server, Linux, VMware e OpenStack, a um custo menor do que soluções competitivas. Criado para o mundo de cloud-first, registos do Azure Monitor oferece uma nova abordagem para gerir a sua empresa que é a forma mais rápida e rentável para atender aos novos desafios de negócios e acomodar novas cargas de trabalho, aplicações e ambientes na cloud.

### <a name="azure-monitor-services"></a>Monitorizar os serviços do Azure

A funcionalidade principal dos logs de Monitor do Azure é fornecida por um conjunto de serviços que são executados no Azure. Cada serviço proporciona uma função de gestão específica e pode combinar serviços para alcançar cenários de gestão diferentes.

| Serviço  | Descrição|
| :------------- | :-------------|
| Registos de Monitor do Azure | Monitorizar e analisar a disponibilidade e o desempenho de diferentes recursos, incluindo computadores e máquinas virtuais. |
|Automatização | Automatizar processos manuais e impor configurações para computadores e máquinas virtuais. |
| Cópia de segurança | Criar cópias de segurança e restaurar dados críticos. |
| Site Recovery | Providenciar elevada disponibilidade para aplicações críticas. |

### <a name="azure-monitor-logs"></a>Registos de Monitor do Azure

[Registos de Monitor do Azure](https://azure.microsoft.com/documentation/services/log-analytics) fornece serviços de monitorização através da recolha de dados de recursos geridos num repositório central. Estes dados podem incluir dados de eventos ou de desempenho ou dados personalizados fornecidos através da API. Depois de recolhidos, os dados estão disponíveis para alertas, análises e exportação.


Este método permite-lhe consolidar os dados de várias origens, para que pode combinar dados dos seus serviços do Azure com os seus existentes ambiente no local. Também separa claramente a recolha dos dados das ações tomadas em relação aos mesmos, para que todas as ações estejam disponíveis para todos os tipos de dados.


![Registos de Monitor do Azure](./media/azure-operational-security/azure-operational-security-fig2.png)

O serviço do Azure Monitor gere os seus dados com base na cloud em segurança, utilize os seguintes métodos:
-   segregação de dados
-   Retenção de dados
-   segurança física
-   gestão de incidentes
-   conformidade
-   certificações de normas de segurança

### <a name="azure-backup"></a>Azure Backup

[O Azure Backup](https://azure.microsoft.com/documentation/services/backup) fornece dados de cópia de segurança e restaurar os serviços e faz parte do conjunto de produtos e serviços do Azure Monitor.
Protege os dados de aplicação e mantém-nos durante anos sem qualquer investimento de capital e com um mínimo de custos operacionais. Pode criar cópias de segurança dados a partir de servidores Windows físicos e virtuais, além de cargas de trabalho de aplicação, como o SQL Server e SharePoint. Também pode ser utilizado pelo [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) para replicar dados protegidos para o Azure para redundância e armazenamento de longa duração.


Os dados protegidos no Azure Backup são armazenados num cofre de cópias de segurança localizado numa região geográfica específica. Os dados são replicados na mesma região e, dependendo do tipo de cofre, também podem ser replicados para outra região para uma maior resiliência.

### <a name="management-solutions"></a>Soluções de Gestão
[O Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) é a solução da Microsoft com base na cloud IT management que ajuda a gerenciar e proteger no local e a infraestrutura na cloud.


[Soluções de gestão](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) são conjuntos pré-embalados de demonstração de Teoremas que implementam um cenário de gestão específico através de um ou mais serviços do Azure Monitor. Soluções diferentes estão disponíveis da Microsoft e de parceiros que pode adicionar facilmente a sua subscrição do Azure para aumentar o valor do seu investimento no Azure Monitor. Como parceiro, pode criar suas próprias soluções para seus aplicativos e serviços de suporte e forneça aos utilizadores através do Azure Marketplace ou modelos de início rápido.


![Soluções de Gestão](./media/azure-operational-security/azure-operational-security-fig4.png)

Um bom exemplo de uma solução que utiliza vários serviços para fornecer funcionalidade adicional é o [solução de gestão de atualizações](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Esta solução utiliza o [registos do Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agente para Windows e Linux recolher informações sobre atualizações necessárias em cada agente. Ele grava esses dados para o repositório de registos do Azure Monitor, onde pode analisá-los com um dashboard incluído.

Quando cria uma implementação, os runbooks do [automatização do Azure](https://docs.microsoft.com/azure/automation/automation-intro) são utilizados para instalar atualizações necessárias. Todo este processo é gerido no portal e não tem de se preocupar com os detalhes subjacentes.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

Centro de segurança do Azure ajuda a proteger os seus recursos do Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure. No serviço, é possível definir políticas de não apenas em relação a subscrições do Azure, mas também contra [grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), pelo que pode ser mais detalhado.

### <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança

Uma política de segurança define o conjunto de controlos que são recomendados para os recursos na subscrição ou grupo de recursos especificados.

No Centro de Segurança, é possível definir políticas de acordo com os requisitos de segurança da sua empresa e com o tipo de aplicações ou sensibilidade dos dados.

![Recomendações e políticas de segurança](./media/azure-operational-security/azure-operational-security-fig5.png)


As políticas ativadas ao nível da subscrição automaticamente propagam a todos os grupos de recursos na subscrição, como mostrado no diagrama no lado direito:


### <a name="data-collection"></a>Recolha de dados

O Centro de Segurança recolhe dados das suas máquinas virtuais (VMs) para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo sobre ameaças. Quando o seu primeiro acesso Centro de segurança, a recolha de dados está ativado em todas as VMs na sua subscrição. A recolha de dados é recomendada, mas pode optar ativamente por não participar, desativando a recolha de dados na política do Centro de Segurança.

### <a name="data-sources"></a>Origens de dados

- O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

-   Serviços do Azure: Utiliza informações sobre a configuração dos serviços do Azure que implementou através da comunicação com o fornecedor de recursos desses serviços.

- Tráfego de rede: Amostragem a utiliza metadados de tráfego de rede da infraestrutura da Microsoft, tais como porta/IP de origem/destino, tamanho do pacote e protocolo de rede.

-   Soluções de parceiros: Utiliza alertas de segurança de soluções de parceiros integradas, tais como firewalls e soluções antimalware.

-   As máquinas virtuais: Utiliza informações de configuração e informações sobre eventos de segurança, tais como o Windows eventos e registos de auditoria, registos do IIS, mensagens do syslog e ficheiros de informação de falha das suas máquinas virtuais.

### <a name="data-protection"></a>Proteção de dados

Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados relacionados com segurança, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

-   **Segregação de dados**: Dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

-   **Acesso a dados**: Para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, o pessoal técnico da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços do Azure, incluindo os ficheiros de informação de falha, processar eventos de criação, instantâneos de disco da VM e artefactos, que Pode incluir, involuntariamente, dados do cliente ou dados pessoais das suas máquinas virtuais. Respeitamos a [declaração de privacidade e termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), qual estado em que a Microsoft não se utiliza dados do cliente ou derivará informações dos mesmos para fins publicitários ou comerciais.

-   **A utilização de dados**: A Microsoft utiliza os padrões e informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção; podemos fazê-lo de acordo com os compromissos de privacidade descritos na nossa [declaração de privacidade](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Localização dos dados

O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida. O Centro de Segurança do Azure faz esta análise na mesma Geografia que a área de trabalho e elimina as cópias efémeras quando a análise estiver concluída. Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.

-   **As contas de armazenamento**: É especificada uma conta de armazenamento para cada região em que as máquinas virtuais são executadas. Isto permite-lhe armazenar dados na mesma região que a máquina virtual a partir da qual os dados são recolhidos.

-   **Armazenamento do Centro de segurança do Azure**: Informações sobre alertas de segurança, incluindo alertas de parceiros, recomendações e estado de funcionamento de segurança são armazenadas centralmente, atualmente nos Estados Unidos. Estas informações podem incluir informações de configuração relacionadas e eventos de segurança recolhidos das suas máquinas virtuais, conforme necessário para lhe fornecer o alerta de segurança, recomendação ou estado de funcionamento de segurança.


## <a name="azure-monitor"></a>Azure Monitor

O [Log Analytics segurança](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) e permite que a solução TI ativamente monitorizar todos os recursos, o que podem ajudar a minimizar o impacto de incidentes de segurança de auditoria. Auditoria e segurança do log Analytics têm domínios de segurança que podem ser utilizados para monitorizar os recursos. O domínio de segurança fornece acesso rápido às opções, para monitorização de segurança, os seguintes domínios são abordados em mais detalhes:

-   Avaliação de software maligno
-   Avaliação de atualização
-   Identidade e acesso.

O Azure Monitor fornece indicadores para informações sobre tipos específicos de recursos. Ele oferece a visualização, consulta, encaminhamento, alertas, dimensionamento automático e automatização nos dados da infraestrutura do Azure (registo de atividade) e cada recurso do Azure individual (registos de diagnóstico).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Aplicações na cloud são complexas com muitas partes móveis. A monitorização fornece dados para garantir que seu aplicativo mantém-se e em execução em bom estado. Também ajuda-o a protele potenciais problemas ou resolução de problemas anteriores são.

Além disso, pode utilizar dados de monitorização para obter informações aprofundadas sobre a sua aplicação. Esse conhecimento pode ajudá-lo a melhorar o desempenho da aplicação ou a capacidade de manutenção, ou automatize ações que caso contrário, requer intervenção manual.

### <a name="azure-activity-log"></a>Registo de atividades do Azure


É um registo que fornece informações sobre as operações executadas nos recursos na sua subscrição. O registo de atividades anteriormente era conhecido como "Registos de auditoria" ou "Registos operacionais", uma vez que comunica eventos de plano de controlo para as suas subscrições.

![Registo de atividades do Azure](./media/azure-operational-security/azure-operational-security-fig7.png)

Utilizar o registo de atividades, pode determinar o "o quê, quem e quando" para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também é possível compreender o estado da operação e outras propriedades relevantes. O registo de Atividades não incluem operações de leitura (GET) nem operações para os recursos que utilizam o modelo clássico.

### <a name="azure-diagnostic-logs"></a>Registos de diagnóstico do Azure

Estes registos são emitidos por um recurso e fornecem dados avançados e frequentes sobre o funcionamento desse recurso. O conteúdo estes registos varia consoante o tipo de recurso.

Por exemplo, registos de sistema de eventos do Windows são uma categoria de registo de diagnóstico para VMs e blob, tabela e registos de fila são categorias dos registos de diagnóstico para contas de armazenamento.

Os registos de diagnóstico diferem da [registo de Atividades (anteriormente conhecido como registo de auditoria ou Log operacional)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). O registo de atividades fornece informações sobre as operações executadas nos recursos na sua subscrição. Os registos de diagnóstico fornecem informações aprofundadas sobre as operações executadas pelo próprio recurso.

### <a name="metrics"></a>Métricas

O Azure Monitor permite-lhe consumir telemetria ganhar visibilidade sobre o desempenho e estado de funcionamento das suas cargas de trabalho no Azure. O tipo mais importante de dados de telemetria do Azure é as métricas (também chamadas de contadores de desempenho) emitidas pelos recursos mais do Azure. O Azure Monitor proporciona várias formas de configurar e consumi-las [métricas](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) para monitorização e resolução de problemas. As métricas são uma fonte valiosa de telemetria e permitem que faça as seguintes tarefas:

-   **Controlar o desempenho** do seu recurso (por exemplo, uma VM, Web site ou aplicação lógica) ao desenhar as suas métricas num gráfico de portal e afixar esse gráfico a um dashboard.

-   **Receber notificações de um problema** isso afeta o desempenho do seu recurso quando uma métrica ultrapassa um determinado limiar.

-   **Configure ações automatizadas**, tais como automática para dimensionar um recurso ou disparando um runbook quando uma métrica ultrapassa um determinado limiar.

-   **Realizar análises avançadas** ou relatórios sobre as tendências de desempenho ou na utilização do seu recurso.

-   **Arquivo** o histórico de desempenho ou o estado de funcionamento do seu recurso de conformidade ou de fins de auditoria.

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

É a capacidade no Azure que permite a recolha de dados de diagnóstico num aplicativo implantado. Pode usar a extensão de diagnóstico a partir de várias origens diferentes. São suportados atualmente [Web do serviço de nuvem do Azure e funções de trabalho](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/overview) com o Microsoft Windows, e [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Outros serviços do Azure têm suas próprias diagnóstico separado.

## <a name="azure-network-watcher"></a>Observador de rede do Azure

A auditoria de segurança de rede é vital para detectar vulnerabilidades de rede e assegurar a conformidade com o modelo de normas de governação e segurança de TI. Com a vista de grupo de segurança, pode recuperar o grupo de segurança de rede configurado e regras de segurança e as regras de segurança efetivas. Com a lista de regras aplicadas, é possível determinar as portas que estão abertas e avaliar a vulnerabilidade de rede.

[Observador de rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço regional que lhe permite monitorizar e diagnosticar condições a um nível de rede no, a e do Azure. Diagnóstico de rede e ferramentas de visualização disponíveis com o observador de rede ajudam a compreender, diagnosticar e obter informações da sua rede no Azure. Este serviço inclui a captura de pacotes, próximo salto, fluxo de IP verificar, a vista de grupo de segurança, os registos de fluxo do NSG. A monitorização ao nível do cenário fornece uma vista de ponta a ponta dos recursos de rede em comparação com a monitorização de recursos de rede individuais.

![Observador de rede do Azure](./media/azure-operational-security/azure-operational-security-fig8.png)

Observador de rede atualmente tem as seguintes capacidades:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Registos de auditoria</a>**-operações efetuadas como parte da configuração de redes com sessão iniciadas. Estes registos podem ser visualizados no portal do Azure ou obtido com ferramentas da Microsoft, como o Power BI ou ferramentas de terceiros. Registos de auditoria estão disponíveis através do portal, PowerShell, CLI e Rest API. Para obter mais informações sobre os registos de auditoria, consulte a operações de auditoria com o Resource Manager. Registos de auditoria estão disponíveis para operações feitas em todos os recursos de rede.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">Fluxo de IP verifica </a>**  -verifica se um pacote é permitido ou negado com base nos parâmetros de pacote de 5 cadeias de identificação do fluxo informações (IP de destino, IP de origem, porta de destino, porta de origem e protocolo). Se o pacote for recusado por um grupo de segurança de rede, a regra e o grupo de segurança de rede negou o pacote é devolvido.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Próximo salto</a>**  -determina o próximo salto para pacotes que está a ser encaminhados a infraestrutura de rede do Azure, permitindo-lhe diagnosticar qualquer configurado incorretamente rotas definidas pelo utilizador.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Vista de grupo de segurança</a>**  -obtém as regras de segurança aplicada e eficiente que são aplicadas numa VM.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">Registo de fluxo de NSG</a>**  -registos de fluxo para grupos de segurança de rede permitem-lhe capturar registos relacionados com o tráfego que é permitido ou negado pelas regras de segurança no grupo. O fluxo é definido por uma informações de 5 cadeias de identificação – IP de origem, IP de destino, porta de origem, porta de destino e protocolo.

## <a name="azure-storage-analytics"></a>Análise do Armazenamento do Azure

[Análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) pode armazenar métricas que incluem agregadas de transações estatísticas e dados de capacidade sobre pedidos para um serviço de armazenamento. Transações são comunicadas ao nível da operação ambas API e o nível de serviço de armazenamento e capacidade é comunicada o nível de serviço de armazenamento. Dados de métricas podem ser utilizados para analisar a utilização do serviço de armazenamento, diagnóstico de problemas com pedidos efetuados para o serviço de armazenamento e para melhorar o desempenho das aplicações que utilizam um serviço.

[Análise de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) efetua o registo e fornece dados de métricas para uma conta de armazenamento. Pode utilizar estes dados para rastrear pedidos, analisar tendências de utilização e diagnosticar problemas relacionados com a sua conta de armazenamento. Registo de análise de armazenamento está disponível para o [serviços Blob, fila e tabela](https://docs.microsoft.com/azure/storage/storage-introduction). Análise de armazenamento regista informações detalhadas sobre pedidos com êxito ou falhadas para um serviço de armazenamento.

Estas informações podem ser utilizadas para monitorizar pedidos individuais e para diagnosticar problemas com um serviço de armazenamento. Pedidos são registados numa base de melhor esforço. Entradas de registo são criadas apenas se existirem pedidos feitos contra o ponto final de serviço. Para exemplo se uma conta de armazenamento tiver uma atividade em seu ponto final do Blob, mas não em seus pontos de extremidade tabelas ou filas, apenas registos relativos ao serviço de BLOBs é criado.

Para utilizar a análise de armazenamento, terá de a ativar individualmente para cada serviço que pretende monitorizar. Pode ativá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, veja [monitorizar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Também pode ativar a análise de armazenamento através de programação com a API REST ou a biblioteca de cliente. Utilize a operação definir propriedades de serviço para ativar a análise de armazenamento individualmente para cada serviço.

Os dados agregados são armazenados num blob bem conhecido (para o registo) e em tabelas bem conhecidas (para métricas), que podem ser acessadas utilizando o serviço de BLOBs e as APIs de serviço de tabela.

Análise de armazenamento tem um limite de 20 TB na quantidade de dados armazenados, que é independentes do limite total para a sua conta de armazenamento. Todos os registos são armazenados no [blobs de blocos](https://docs.microsoft.com/azure/storage/storage-analytics) num contentor com o nome $logs, que é criado automaticamente quando a análise de armazenamento é ativada para uma conta de armazenamento.

As seguintes ações realizadas por análise de armazenamento são a cobrar:

-   Pedidos para criar blobs para o registo
-   Pedidos para criar entidades da tabela de métricas.

> [!Note]
> Para obter mais informações sobre faturação e políticas de retenção de dados, consulte [a análise de armazenamento e de faturação](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> Para um desempenho ideal, pretende limitar o número de discos muito utilizados anexados à máquina virtual para evitar uma possível limitação. Se todos os discos não estão a ser muito utilizados ao mesmo tempo, a conta de armazenamento pode suportar um maior disco número.

> [!Note]
> Para obter mais informações sobre os limites de conta de armazenamento, consulte [metas de desempenho e escalabilidade do armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Os seguintes tipos de pedidos autenticados e anónimos são registados.

| Autenticados  | Anónimo|
| :------------- | :-------------|
| Pedidos com êxito | Pedidos com êxito |
|Pedidos falhados, incluindo o tempo limite, limitação, rede, autorização e outros erros | Pedidos com um acesso assinatura partilhado (SAS), incluindo pedidos de falhadas e bem-sucedidas |
| Pedidos com um acesso assinatura partilhado (SAS), incluindo pedidos de falhadas e bem-sucedidas |Erros de tempo limite para o cliente e servidor |
|   Pedidos de dados de análise |    Pedidos falhados de GET com código de erro 304 (não modificado) |
| Pedidos efetuados por análise de armazenamento em si, como o registo é criada ou eliminada, não tem sessão iniciados. Uma lista completa dos dados com sessão iniciada está documentada no [operações com sessão iniciada da análise de armazenamento e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) tópicos. | Todos os outros pedidos anónimos com falhas não são registados. Uma lista completa dos dados com sessão iniciada está documentada no [operações com sessão iniciada da análise de armazenamento e as mensagens de estado](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [formato de registo de análise de armazenamento](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

O Azure AD também inclui um conjunto completo de capacidades de gestão de identidade incluindo a autenticação multifator, registo de dispositivos, gestão de palavras-passe self-service, gestão de grupos self-service, gestão de contas com privilégios, acesso baseado em funções controlo, monitorização da utilização de aplicações, avançado auditoria e monitorização de segurança e alertas.

-   Melhore a segurança das aplicações com autenticação multifator do Azure AD e o acesso condicional.

-   Monitorizar a utilização da aplicação e proteger a sua empresa contra ameaças avançadas com segurança de relatórios e monitoramento.

O Azure Active Directory (Azure AD) inclui relatórios de segurança, atividade e auditoria para o diretório. [O relatório de auditoria de diretório Active Directory do Azure](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda os clientes para identificar ações privilegiadas que ocorreram no seu Azure Active Directory. Ações privilegiadas incluem alterações de elevação (por exemplo, a criação da função ou a reposições de palavra-passe), alterar as configurações da política (por exemplo políticas de palavra-passe) ou as alterações à configuração de diretório (por exemplo, alterações às definições de Federação do domínio).

Os relatórios fornecem o registo de auditoria para o nome do evento, o ator que efetuou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os clientes podem obter a lista de eventos de auditoria para o Azure Active Directory através do [portal do Azure](https://portal.azure.com/), conforme descrito nas [ver os registos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Aqui está uma lista dos relatórios incluídos:

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
| |Atividade de reposição de palavra-passe |   | |



Os dados destes relatórios podem ser úteis para seus aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. Os relatórios do Azure AD [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) proporcionam acesso programático aos dados através de um conjunto de APIs baseadas em REST. Pode chamar essas APIs de vários idiomas e ferramentas de programação.

Eventos no relatório de auditoria do Azure AD são mantidos durante 180 dias.

> [!Note]
> Para obter mais informações sobre a retenção de relatórios, consulte [políticas de retenção de relatórios do Azure Active Directory do](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

Para clientes interessados em armazenar seus [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) por períodos de retenção mais longos, a API de relatórios pode servir para extrair regularmente os eventos de auditoria para um arquivo de dados separado.

## <a name="summary"></a>Resumo

Este resumos de artigo proteger a sua privacidade e proteção de seus dados, ao mesmo tempo, software e serviços que o ajudam a gerir a infraestrutura de TI da sua organização. A Microsoft reconhece que, quando eles entrust seus dados para outras pessoas, essa confiança requer segurança rigorosa. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Proteger e a proteção dos dados são uma prioridade de segurança da Microsoft.

Este artigo explica

-   Como dados são recolhidos, processados e protegidos no pacote do Azure Monitor.

-   Analise rapidamente eventos em várias origens de dados. Identifique riscos de segurança e compreenda o âmbito e o impacto das ameaças e ataques para reduzir os danos provocados por falhas na segurança.

-   Identifique padrões de ataque ao ver o tráfego IP malicioso de saída e os tipos de ameaças maliciosas. Compreenda a postura de segurança de todo o seu ambiente, independentemente da plataforma.

-   Capture todos os dados de registos e eventos necessários para uma auditoria de segurança ou conformidade. Barra o tempo e os recursos necessários para fornecer uma auditoria de segurança com um conjunto de dados de evento e o registo completo, pesquisável e exportável.

<ul>
<li>Recolha eventos relacionados com segurança, auditoria e análise da violação para manter um olho seus ativos:</li>
<ul>
<li>Postura de segurança</li>
<li>Problema notável</li>
<li>Ameaças de resumos</li>
</ul>
</ul>

## <a name="next-steps"></a>Próximos Passos

- [Estrutura e segurança operacional](https://www.microsoft.com/trustcenter/security/designopsecurity)

A Microsoft desenvolve o seu software e serviços com segurança para ajudar a garantir que a sua infraestrutura de nuvem é resiliente e defendido contra ataques.

- [Registos de Monitor do Azure | Segurança e conformidade](https://www.microsoft.com/cloud-platform/security-and-compliance)

Utilize dados de segurança da Microsoft e de análise para executar mais a deteção de ameaças inteligente e eficiente.

- [Operações e planeamento do Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) um conjunto de passos e tarefas que pode seguir para otimizar a utilização do Centro de segurança com base nos requisitos de segurança e o modelo de gestão de nuvem da sua organização.

