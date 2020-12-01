---
title: Linha de segurança Azure para serviços em nuvem Azure
description: A linha de base de segurança Azure Cloud Services fornece orientações processuais e recursos para a implementação das recomendações de segurança especificadas no Azure Security Benchmark.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 7deee88210acf700916961be7c4ccaf9477accf8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353447"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Linha de segurança Azure para serviços em nuvem Azure

Esta linha de base de segurança aplica orientações da [versão 1.0 do Azure Security Benchmark](../security/benchmarks/overview-v1.md) para os Serviços cloud do Microsoft Azure. A Referência de Segurança do Azure disponibiliza recomendações para proteger as suas soluções cloud no Azure.
O conteúdo é agrupado pelos **controlos de segurança definidos** pelo Azure Security Benchmark e pela orientação conexa aplicável aos Serviços cloud. Foram excluídos **os controlos** não aplicáveis aos Serviços cloud.

 
Para ver como os Serviços cloud mapeiam completamente para o Azure Security Benchmark, consulte o [ficheiro de mapeamento de base de segurança completo dos Serviços cloud.](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)

## <a name="network-security"></a>Segurança da rede

*Para obter mais informações, consulte o [Azure Security Benchmark: Segurança da rede](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteger os recursos do Azure nas redes virtuais

**Orientação**: Crie uma rede virtual clássica do Azure com subesí redes públicas e privadas separadas para impor o isolamento com base em portas fidedignas e gamas IP. Estas redes virtuais e sub-redes devem ser os recursos clássicos da Rede Virtual (implantação clássica) e não os recursos atuais do Azure Resource Manager.  

Permitir ou negar o tráfego utilizando um grupo de segurança de rede, que contém regras de controlo de acesso baseadas na direção de tráfego, protocolo, endereço de origem e porto, e endereço de destino e porto. As regras de um grupo de segurança de rede podem ser alteradas a qualquer momento e as alterações são aplicadas a todas as instâncias associadas.

Os Serviços cloud da Microsoft Azure (Clássico) não podem ser colocados em redes virtuais do Azure Resource Manager. No entanto, as redes virtuais baseadas em Gestores de Recursos e redes virtuais clássicas baseadas em implementação podem ser conectadas através do espreitamento. 

- [Visão geral do Grupo de Segurança de Rede](../virtual-network/network-security-groups-overview.md)

- [Peering de Rede Virtual](./cloud-services-connectivity-and-networking-faq.md?amp;preserve-view=#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorizar e registar a configuração e o tráfego de redes virtuais, sub-redes e NICs

**Orientação**: Documente a configuração dos Serviços Azure Cloud e monitorize-a para alterações. Utilize o ficheiro de configuração do serviço para especificar o número de instâncias para implantar para cada função no serviço, os valores de quaisquer definições de configuração e as impressões digitais para quaisquer certificados associados a uma função. 

Se o serviço fizer parte de uma rede virtual, as informações de configuração da rede devem ser fornecidas no ficheiro de configuração de serviço, bem como no ficheiro de configuração de rede virtual. A extensão padrão para o ficheiro de configuração de serviço é .cscfg. Note que a Política Azure não é suportada para implementações clássicas para aplicação de configuração.

Desajei os valores de configuração de um serviço de nuvem no ficheiro de configuração de serviço (.cscfg) e na definição num ficheiro de definição de serviço (.csdef). Utilize o ficheiro de definição de serviço para definir o modelo de serviço para uma aplicação. Defina as funções, que estão disponíveis para um serviço de nuvem e especifique também os pontos finais do serviço. Faça o registo da configuração dos Serviços Azure Cloud com ficheiro de configuração de serviço. Qualquer reconfiguração pode ser feita através do ficheiro ServiceConfig.cscfg. 

Monitorize a definição de serviço de elementos networkTrafficRules opcionais que restringe quais as funções que podem comunicar a pontos finais internos especificados. Configure o nó Desícono NetworkTrafficRules, um elemento opcional no ficheiro de definição de serviço, para especificar como as funções devem comunicar entre si. Colocar limites aos quais as funções podem aceder aos pontos finais internos da função específica.  Note que a definição de serviço não pode ser alterada. 

Ativar os registos de fluxo do grupo de segurança da rede e enviar os registos para uma conta de Armazenamento Azure para auditoria. Envie os registos de fluxo para um espaço de trabalho do Log Analytics e use o Traffic Analytics para fornecer informações sobre os padrões de tráfego no seu inquilino Azure. Algumas vantagens do Traffic Analytics são a capacidade de visualizar a atividade da rede, identificar pontos quentes e ameaças de segurança, compreender padrões de fluxo de tráfego e identificar configurações erradas da rede.

- [Azure Resource Manager vs. implantação clássica - Compreenda os modelos de implantação e o estado dos seus recursos](../azure-resource-manager/management/deployment-models.md)

- [Ficheiro Config dos Serviços de Nuvem](schema-cscfg-file.md)

- [Lista de serviços apoiados pela Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage?amp;preserve-view=)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="13-protect-critical-web-applications"></a>1.3: Proteger aplicações web críticas

**Orientação**: A Microsoft utiliza o protocolo V1.2 de Segurança da Camada de Transporte (TLS) para proteger os dados quando viaja entre os Serviços Azure Cloud e os clientes. Os datacenters da Microsoft negoceiam uma ligação TLS com sistemas de clientes que se ligam aos serviços Azure. O TLS proporciona uma autenticação forte, privacidade e integridade da mensagem (permitindo a deteção de adulteração de mensagens, interceção e falsificação), interoperabilidade, flexibilidade de algoritmos e facilidade de implantação e utilização.

- [Fundamentos de encriptação](../security/fundamentals/encryption-overview.md)

- [Configure certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negar comunicações com endereços IP maliciosos conhecidos

**Orientação**: A Azure Cloud implementa uma segurança de rede multicamadas para proteger os seus serviços de plataforma contra ataques de negação de serviço distribuídos (DDoS). O Azure DDoS Protection faz parte do processo de monitorização contínua da Azure Cloud, que é continuamente melhorado através de testes de penetração. Esta Proteção DDoS é projetada para resistir não só a ataques vindos de fora, mas também de outros inquilinos do Azure. 

Existem algumas formas diferentes de bloquear ou negar a comunicação além da proteção ao nível da plataforma dentro dos Serviços Azure Cloud. Esses avisos são: 

-  Criar uma tarefa de arranque para bloquear seletivamente alguns endereços IP específicos
-  Restringir o acesso de uma função web Azure a um conjunto de endereços IP especificados modificando o seu ficheiro IIS web.config

Impedir a entrada de tráfego no URL predefinido ou no nome dos seus Serviços cloud, por exemplo, *.cloudapp.net. Desabrague o cabeçalho do anfitrião para um nome DNS personalizado, na configuração de ligação do site na definição de Cloud Services*(.csdef).

Configure uma regra Deny Apply para atribuições clássicas de administrador de subscrição. Por padrão, após a definição de um ponto final interno, a comunicação pode fluir de qualquer papel para o ponto final interno de uma função sem quaisquer restrições. Para restringir a comunicação, deve adicionar um elemento NetworkTrafficRules ao elemento 'Definição de Serviço' no ficheiro de definição de serviço.

- [Como posso bloquear/desativar o tráfego de entrada para o URL padrão do meu serviço na nuvem](./cloud-services-connectivity-and-networking-faq.md?amp;preserve-view=#how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service)

- [Proteção Azure DDOS](./cloud-services-connectivity-and-networking-faq.md?amp;preserve-view=#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service)

- [Bloqueie um endereço IP específico](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="15-record-network-packets"></a>1.5: Pacotes de rede de registos

**Orientação**: Utilize o Observador de Rede Azure, monitorização do desempenho da rede, serviço de diagnóstico e análise, que permite a monitorização das redes Azure. A extensão da máquina virtual do Network Watcher Agent é um requisito para capturar o tráfego de rede a pedido, e outras funcionalidades avançadas em Azure Virtual Machines. Instale a extensão da máquina virtual do Network Watcher Agent e ligue os registos de fluxo do grupo de segurança da rede.

Configurar o fluxo de registo num grupo de segurança de rede. Reveja detalhes sobre como implementar a extensão da Máquina Virtual do Observador de Rede para uma Máquina Virtual existente implantada através do modelo de implementação clássico.

- [Configure a exploração de fluxos num grupo de segurança de rede](../virtual-machines/extensions/network-watcher-linux.md)

- [Para mais informações sobre a configuração da visita de registos de fluxo](/cli/azure/azure-services-the-azure-cli-can-manage?amp;preserve-view=)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementar sistemas de deteção/prevenção de intrusões baseados em rede (IDS/IPS)

**Orientação**: O Azure Cloud Services não tem capacidade de IDS ou IPS incorporada. Os clientes podem selecionar e implementar uma solução de IDS ou IPS suplementar baseada em rede a partir do Azure Marketplace com base nos seus requisitos organizacionais. Ao utilizar soluções de terceiros, certifique-se de testar cuidadosamente a sua solução de IDS ou IPS selecionada com a Azure Cloud Services para garantir o bom funcionamento e funcionalidade.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall) 

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Gerir o tráfego para aplicações web

**Orientação**: Os certificados de serviço, que estão ligados aos Serviços Azure Cloud, permitem uma comunicação segura de e para o serviço. Estes certificados são definidos na definição dos serviços e são automaticamente implantados na máquina virtual que está executando uma instância de uma função web. Como exemplo, para uma função web, pode utilizar um certificado de serviço que pode autenticar um ponto final HTTPS exposto. 

Para atualizar o certificado, basta carregar um novo certificado e alterar o valor da impressão digital no ficheiro de configuração de serviço.

Utilize o protocolo TLS 1.2, o método mais utilizado para assegurar dados para fornecer proteção de confidencialidade e integridade. 

Geralmente, para proteger aplicações web e protegê-las contra ataques como o OWASP Top 10, pode implementar um Gateway de aplicação Azure ativado por Azure Web para proteger aplicações web. 

- [Certificados de Serviço](cloud-services-certs-create.md)

- [Configurar TLS para uma aplicação em Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Como implementar o Gateway de Aplicações](../application-gateway/quick-create-portal.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Manter configurações de segurança padrão para dispositivos de rede

**Orientação**: Endureça a configuração dos Serviços Azure Cloud e monitorize-a para alterações. O ficheiro de configuração de serviço especifica o número de instâncias de função a implementar para cada função no serviço, os valores de quaisquer definições de configuração e as impressões digitais para quaisquer certificados associados a uma função. 

Se o seu serviço fizer parte de uma rede virtual, as informações de configuração da rede devem ser fornecidas no ficheiro de configuração de serviço, bem como no ficheiro de configuração de rede virtual. A extensão padrão para o ficheiro de configuração de serviço é .cscfg.

Note que a Política Azure não é suportada com a Azure Cloud Services para aplicação da configuração.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regras de configuração do tráfego documental

**Orientação**: Os grupos de segurança da rede Azure podem ser utilizados para filtrar o tráfego da rede de e para os recursos Azure numa Rede Virtual Azure. Um grupo de segurança de rede contém regras de segurança que permitem ou negam o tráfego de rede de entrada para, ou, tráfego de rede de saída de vários tipos de recursos Azure. Para cada regra, pode especificar a origem e o destino, a porta e o protocolo.

Utilize o campo "Descrição" para as regras individuais do grupo de segurança da rede dentro dos Serviços Azure Cloud para documentar as regras, que permitem o tráfego para, ou a partir de uma rede.

- [Como filtrar o tráfego da rede com regras do grupo de segurança de rede](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilize ferramentas automatizadas para monitorizar as configurações de recursos de rede e detetar alterações

**Orientação**: Utilize as funcionalidades de monitorização de ponto final incorporada do Azure Traffic Manager e as funcionalidades automáticas de falha de falha do ponto final. Ajudam-no a fornecer aplicações de alta disponibilidade, que são resistentes às falhas da região de Endpoint e Azure. Para configurar a monitorização do ponto final, tem de especificar determinadas definições no perfil do Gestor de Tráfego.

Recolha informações do log activity, um registo de plataforma em Azure, em eventos de nível de subscrição. Inclui informações como quando um recurso é modificado ou quando uma máquina virtual é iniciada. Ver o registo de atividade no portal Azure ou recuperar entradas com PowerShell e CLI. 

Crie uma definição de diagnóstico para enviar o registo de atividade para Azure Monitor, Azure Event Hubs para encaminhar para fora de Azure, ou para O Armazenamento Azure para arquivo. Configure O Monitor Azure para alertas de notificação quando os recursos críticos nos seus Serviços Azure Cloud são alterados. 

- [Log de atividades Azure](../azure-monitor/platform/activity-log.md)

- [Criar, visualizar e gerir alertas de registo de atividades utilizando o Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

- [Monitorização do Gestor de Tráfego](../traffic-manager/traffic-manager-monitoring.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="logging-and-monitoring"></a>Início de sessão e monitorização

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Registar e monitorizar](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Utilizar fontes de sincronização de tempo aprovadas

**Orientação**: A Microsoft mantém fontes de tempo para recursos Azure para serviços Azure Cloud. Os clientes podem precisar de criar uma regra de rede para permitir o acesso a um servidor de tempo utilizado no seu ambiente, sobre a porta 123 com o protocolo UDP.

- [Acesso ao servidor NTP](../firewall/protect-windows-virtual-desktop.md#additional-considerations)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Partilhada

### <a name="22-configure-central-security-log-management"></a>2.2: Configurar a gestão central dos registos de segurança

**Orientação**: Consuma o seu serviço de streaming de dados em nuvem programáticamente com os Hubs de Eventos Azure. Integre e envie todos estes dados para o Azure Sentinel para monitorizar e rever os seus registos, ou utilizar um SIEM de terceiros. Para a gestão central de registos de segurança, configufique a exportação contínua dos dados do Seu Centro de Segurança Azure escolhido para Azure Event Hubs e crie o conector apropriado para o seu SIEM. Aqui estão algumas opções para Azure Sentinel, incluindo ferramentas de terceiros:

- Azure Sentinel - Use o centro de segurança nativo alerta o conector de dados
- Splunk - Use o addon Azure Monitor para Splunk
- IBM QRadar - Utilize uma fonte de log configurada manualmente
- ArcSight – Use SmartConnector

Reveja a documentação do Azure Sentinel para obter mais detalhes sobre os conectores disponíveis com o Azure Sentinel. 

- [Ligar a origens de dados](../sentinel/connect-data-sources.md)

- [Integrar-se com um SIEM](../security-center/continuous-export.md)

- [Armazenar dados de diagnóstico](diagnostics-extension-to-storage.md)

- [Configurar a integração do SIEM através dos Hubs de Eventos Azure](../security-center/continuous-export.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Permitir a exploração de auditorias para recursos Azure

**Orientação**: Configurar o Estúdio Visual para configurar o Azure Diagnostics para resolução de problemas dos Serviços de Nuvem Azure, que captura o sistema e regista dados em máquinas virtuais, incluindo casos de máquinas virtuais que executam os seus Serviços cloud Azure. Os dados de Diagnóstico são transferidos para uma conta de armazenamento à sua escolha. Ligue os diagnósticos nos projetos da Azure Cloud Services antes da sua implantação.

 
Veja o histórico de Alterações para alguns eventos no registo de atividades dentro do Azure Monitor. Auditar as alterações que ocorreram durante um período de tempo do evento. Escolha um evento no Registo de Atividade para uma inspeção mais profunda com o separador Alterar histórico (Preview). Envie os dados de diagnóstico para Application Insights quando publicar um Azure Cloud Services do Visual Studio. Crie o recurso Application Insights Azure nessa altura ou envie os dados para um recurso Azure existente. 

Os Serviços Azure Cloud podem ser monitorizados pela Application Insights para disponibilidade, desempenho, falhas e utilização. Os gráficos personalizados podem ser adicionados ao Application Insights para que possa ver os dados que mais importam. Os dados da instância de função podem ser recolhidos utilizando o SDK Application Insights no seu projeto Azure Cloud Services. 

- [Ligue os diagnósticos no Estúdio Visual antes da implementação](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?amp;preserve-view=#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Ver alterar história](../azure-monitor/platform/activity-log.md#view-change-history)

- [Insights de Aplicação para o serviço Azure Cloud (Clássico)](../azure-monitor/app/cloudservices.md)

- [Configurar diagnósticos para o serviço Azure Cloud (Classic) e máquinas virtuais](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?amp;preserve-view=true&toc=%2fazure%2fcloud-services%2ftoc.json)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configurar a retenção de armazenamento de registos de segurança

**Orientação**: Pode utilizar uma monitorização avançada com os Serviços Azure Cloud, que permite que métricas adicionais sejam amostradas e recolhidas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados na conta de armazenamento, nas tabelas, e são purgados após 10 dias. No entanto, a conta de armazenamento utilizada é configurada pelo papel e pode utilizar diferentes contas de armazenamento para diferentes funções. Isto é configurado com uma cadeia de ligação nos ficheiros .csdef e .cscfg.

Note que a monitorização avançada envolve a utilização da extensão Azure Diagnostics (Application Insights SDK é opcional) sobre a função que pretende monitorizar. A extensão de diagnóstico utiliza um ficheiro config (por função) chamado diagnóstico.wadcfgx para configurar as métricas de diagnóstico monitorizadas. A extensão Azure Diagnostic recolhe e armazena dados numa conta de Armazenamento Azure. Estas definições estão configuradas nos ficheiros .wadcfgx, .csdef e .cscfg.

- [Introdução à Monitorização do Serviço de Nuvem](cloud-services-how-to-monitor.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="26-monitor-and-review-logs"></a>2.6: Monitor e revisão de Registos

**Orientação**: Os modos de monitorização básicos ou avançados estão disponíveis para os Serviços Azure Cloud. O Azure Cloud Services recolhe automaticamente dados básicos de monitorização (percentagem de CPU, rede dentro/fora e leitura/gravação de disco) de uma máquina virtual hospedeira. Veja os dados de monitorização recolhidos nas páginas de visão geral e métricas de um serviço de nuvem no portal Azure. 

Permitir diagnósticos em Azure Cloud Services para recolher dados de diagnóstico como registos de aplicações, contadores de desempenho e muito mais, utilizando a extensão Azure Diagnostics. Ativar ou atualizar a configuração de diagnósticos num serviço de nuvem que já esteja a funcionar com Set-AzureServiceDiagnosticsExtension cmdlet ou implementar automaticamente um serviço de cloud com extensão de diagnóstico. Opcionalmente, instale o SDK Application Insights. Envie contadores de desempenho para o Azure Monitor.

A extensão Azure Diagnostic recolhe e armazena dados numa conta de Armazenamento Azure. Transfira os dados de diagnóstico para o Emulador de Armazenamento Microsoft Azure ou para o Azure Storage, uma vez que este não está armazenado permanentemente. Uma vez armazenado, pode ser visualizado com uma de várias ferramentas disponíveis, como Server Explorer em Visual Studio, Microsoft Azure Storage Explorer, Azure Management Studio. Configure as métricas de diagnóstico a monitorizar com um ficheiro config (por função) nomeado diagnósticos.wadcfgx na extensão de diagnóstico. 

- [Introdução à Monitorização do Serviço de Nuvem](cloud-services-how-to-monitor.md)

- [Como Permitir diagnósticos numa função de trabalhador - Integre-se com um SIEM](../security-center/continuous-export.md)

- [Ativar diagnósticos em Azure Cloud Services usando PowerShell](cloud-services-diagnostics-powershell.md)

- [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](diagnostics-extension-to-storage.md?&amp;preserve-view=true)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Permitir alertas para atividades anómalas

**Orientação**: Pode monitorizar os dados de registo do Azure Cloud Services através da integração com o Azure Sentinel, ou com um SIEM de terceiros, permitindo alertar para atividades anómalas.

- [Integrar-se com um SIEM](../security-center/continuous-export.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralizar a sessão anti-malware

**Orientação**: Microsoft Antimalware for Azure, protege a Azure Cloud Services e máquinas virtuais. Além disso, tem a opção de implementar soluções de segurança de terceiros, tais como paredes de incêndio de aplicações web, firewalls de rede, antimalware, sistemas de deteção e prevenção de intrusões (IDS ou IPS), e muito mais.

- [Quais são as funcionalidades e capacidades que a Azure básica IPS/IDS e DDOS fornece](./cloud-services-configuration-and-management-faq.md?amp;preserve-view=#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="identity-and-access-control"></a>Identidade e controlo de acesso

*Para mais informações, consulte o [Benchmark de Segurança Azure: Identidade e controlo de acessos](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Manter um inventário das contas administrativas

**Orientação**: A Microsoft recomenda que você gere o acesso aos recursos Azure usando o controlo de acesso baseado em funções Azure (Azure RBAC). A azure Cloud Services, no entanto, não suporta o modelo Azure RBAC, uma vez que não é um serviço baseado em Recursos Azure e você tem que usar uma subscrição clássica

Por padrão, Administrador de Conta, Administrador de Serviço e Co-Administrator são as três funções clássicas de administrador de subscrição no Azure. 

Os administradores de subscrição clássica têm acesso total à subscrição do Azure. Podem gerir recursos com o portal do Azure, as APIs do Azure Resource Manager e as APIs do modelo de implementação clássica. A conta utilizada para se inscrever no Azure é definida automaticamente como Administrador de Conta e Administrador de Serviços. Co-Administrators adicionais podem ser adicionados mais tarde. 

O Administrador de Serviço e o Co-Administrators têm acesso equivalente aos utilizadores a quem foi atribuída a função De proprietário (uma função Azure) no âmbito de subscrição. Gerir Co-Administrators ou ver o Administrador de Serviço utilizando o separador De administradores Clássico no portal Azure. 

Liste atribuições de funções para administrador de serviço clássico e coadministrators com PowerShell com o comando:

Get-AzRoleAssignment -Incluir Alunos Desematores

Reveja as diferenças entre as funções administrativas clássicas de subscrição. 

- [Diferenças entre três funções administrativas clássicas de subscrição](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Utilizar contas administrativas dedicadas

**Orientação**: Recomenda-se a criação de procedimentos operacionais padrão em torno da utilização de contas administrativas dedicadas, com base nas funções disponíveis e nas permissões necessárias para operar e gerir os recursos dos Serviços cloud Azure.

- [Diferenças entre as funções administrativas clássicas de subscrição](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Utilize um único sinal de sso com o Azure Ative Directory

**Orientação**: Evite gerir identidades separadas para aplicações que estão a decorrer nos Serviços Azure Cloud. Implementar um único sinal para evitar que os utilizadores gerem múltiplas identidades e credenciais.

- [O que é um único sinal (SSO)](../active-directory/manage-apps/what-is-single-sign-on.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Utilizar máquinas dedicadas (Estações de acesso privilegiadas) para todas as tarefas administrativas

**Orientação**: Recomenda-se a utilização de uma estação de trabalho segura gerida pelo Azure (também conhecida como Estação de Trabalho de Acesso Privilegiado) para tarefas administrativas, que requerem privilégios elevados.

- [Compreenda estações de trabalho seguras e geridas pelo Azure](../active-directory/devices/concept-azure-managed-workstation.md)

- [Como ativar O Azure AD MFA](../active-directory/authentication/howto-mfa-getstarted.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="data-protection"></a>Proteção de dados

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Proteção de dados](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Manter um inventário de informação sensível

**Orientação**: Utilize as APIs de REPOUSO do Serviço de Nuvem Azure para inventariar os seus recursos do Serviço Azure Cloud para obter informações sensíveis. Faça uma sondagem dos recursos de serviço na nuvem implantados para obter a configuração e .pkg recursos.

 Como exemplo, algumas APIs estão listadas abaixo:

- Obter Implementação - A operação Get Deployment devolve informações de configuração, estado e propriedades do sistema para uma implantação.
- Get Package - A operação Get Package recupera um pacote de serviço em nuvem para uma implementação e armazena os ficheiros de pacotes no armazenamento microsoft Azure Blob
- Obter Propriedades de Serviço cloud - A operação Get Cloud Service Properties recupera propriedades para o serviço de nuvem especificado

Reveja a documentação do Azure Cloud Service REST APIs e crie um processo de proteção de dados de informação sensível, com base nos seus requisitos organizacionais.

- [Obter Implementação](/rest/api/compute/cloudservices/rest-get-deployment)

- [Obtenha propriedades de serviço em nuvem](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Obter Pacote](/rest/api/compute/cloudservices/rest-get-package)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Isolar sistemas de armazenamento ou tratamento de informações sensíveis

**Orientação**: Implementar o isolamento utilizando subscrições e grupos de gestão separados para domínios de segurança individuais, tais como o tipo de ambiente e o nível de sensibilidade de dados para os Serviços Azure Cloud.

Também pode editar o "permissionLevel" no elemento Certificado do Azure Cloud Service para especificar as permissões de acesso dadas aos processos de função. Se pretender apenas processos elevados para poder aceder à chave privada, especifique a permissão elevada. a permissão limitadaOrElevated permite que todos os processos de função acedam à chave privada. Os valores possíveis são limitadosOrelevated ou elevados. O valor predefinido é limitadoOrelevated.

- [Como criar subscrições adicionais do Azure](../cost-management-billing/manage/create-subscription.md)

- [Como criar grupos de gestão](../governance/management-groups/create-management-group-portal.md)

- [Esquema de WebRole](schema-csdef-webrole.md#Certificate)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Monitor e bloquear transferência não autorizada de informações sensíveis

**Orientação**: Recomenda-se a utilização de uma solução de terceiros do Azure Marketplace em perímetros de rede para monitorizar a transferência não autorizada de informações sensíveis e bloquear tais transferências, alertando os profissionais de segurança da informação.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Criptografar todas as informações sensíveis em trânsito

**Orientação**: Configurar TLS v2 para serviços de nuvem Azure. Utilize o portal Azure para adicionar o certificado à sua implantação de Serviços Azure Cloud e adicione as informações do certificado aos ficheiros CSDEF e CSCFG dos serviços. Reembalar a sua aplicação e atualizar a sua implementação encenada para utilizar o novo pacote. 

Utilize certificados de serviço em Azure que estejam ligados aos Serviços Azure Cloud para permitir uma comunicação segura de e para o serviço. Forneça um certificado que possa autenticar um ponto final HTTPS exposto. Defina os certificados de serviço na definição de serviço do serviço de nuvem e implemente-os automaticamente na Máquina Virtual, executando uma instância da sua função.

Autenticar com a API de gestão com certificados de gestão) Os certificados de gestão permitem-lhe autenticar com o modelo de implantação clássico. Muitos programas e ferramentas (por exemplo, o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e a implementação de vários serviços do Azure. 

Para referência adicional, a Azure Service Management API proporciona acesso programático à funcionalidade Gestão de Serviços disponível através do portal Azure. O Azure SDK para Python pode ser usado para gerir os Serviços Azure Cloud e as contas de Armazenamento Azure. O Azure SDK para Python envolve a API de Gestão de Serviços, uma API REST. Todas as operações da API são realizadas através de TLS e mutuamente autenticadas utilizando certificados X.509 v3. O serviço de gestão pode ser acedido a partir de um serviço em execução em Azure. Também pode ser acedido diretamente pela Internet a partir de qualquer aplicação que possa enviar um pedido HTTPS e receber uma resposta HTTPS.

- [Configure TLS para uma aplicação em Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Utilizar gestão de serviços da Python](cloud-services-python-how-to-use-service-management.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Utilize uma ferramenta de descoberta ativa para identificar dados sensíveis

**Orientação**: Recomenda-se a utilização de uma ferramenta de descoberta ativa de terceiros para identificar todas as informações sensíveis armazenadas, processadas ou transmitidas pelos sistemas tecnológicos da organização, incluindo as localizadas no local, ou num prestador de serviços remotos, e, em seguida, atualizar o inventário de informação sensível da organização.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Utilizar a prevenção da perda de dados baseada no hospedeiro para impor o controlo do acesso

**Orientação**: Não aplicável ao serviço Cloud (Clássico). Não impõe a prevenção da perda de dados.

Recomenda-se implementar uma ferramenta de terceiros, como uma solução automatizada de prevenção de perda de dados baseada em hospedeiros, para impor controlos de acesso aos dados, mesmo quando os dados são copiados de um sistema.

Para a plataforma subjacente, gerida pela Microsoft, a Microsoft trata todos os conteúdos dos clientes como sensíveis e faz grandes esforços para se proteger contra a perda e exposição de dados dos clientes. Para garantir que os dados dos clientes no Azure permanecem seguros, a Microsoft implementou e mantém um conjunto de controlos e capacidades robustos de proteção de dados.

- [Compreender a proteção dos dados dos clientes no Azure](../security/fundamentals/protection-customer-data.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Criptografe informação sensível em repouso

**Orientação**: A azure Cloud Services não suporta encriptação em repouso. Isto porque os Serviços Azure Cloud são projetados para serem apátridas. Os Azure Cloud Services suportam armazenamento externo, por exemplo, Azure Storage, que é por padrão, encriptado em repouso.  

Os dados da aplicação armazenados em discos temporários não são encriptados. O cliente é responsável por gerir e encriptar estes dados, conforme necessário.  

- [Compreender a encriptação de dados inativos no Azure](../security/fundamentals/encryption-atrest.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registar e alertar sobre alterações aos recursos críticos do Azure

**Orientação**: Pode utilizar alertas métricos clássicos no Azure Monitor para ser notificado quando uma das suas métricas aplicadas a recursos críticos cruza um limiar. Os alertas métricos clássicos são uma funcionalidade mais antiga que permite alertar apenas em métricas não dimensionais. Existe uma funcionalidade mais recente existente chamada alertas métricos que tem melhorado a funcionalidade em relação aos alertas métricos clássicos. 

Além disso, o Application Insights pode monitorizar as aplicações do Azure Cloud Services para disponibilidade, desempenho, falhas e utilização. Isto utiliza dados combinados de SDKs Application Insights com dados de Diagnóstico Azure dos seus Serviços de Nuvem Azure.

- [Criar, ver e gerir alertas métricos clássicos usando o Azure Monitor](../azure-monitor/platform/alerts-classic-portal.md)

- [Métrica alertas visão geral](../azure-monitor/platform/alerts-metric-overview.md) 

- [Insights de Aplicação para o serviço Azure Cloud (Clássico)](../azure-monitor/app/cloudservices.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades

*Para obter mais informações, consulte o [Azure Security Benchmark: Gestão de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementar solução automatizada de gestão de correção do sistema operativo

**Orientação**: Note que esta informação diz respeito ao sistema operativo Azure Guest para trabalhadores da Azure Cloud Services e funções web com plataforma como serviço (PaaS). No entanto, não se aplica a Máquinas Virtuais com Infraestruturas como serviço (IaaS).

Por padrão, o Azure atualiza periodicamente o sistema operativo do cliente para a mais recente imagem suportada dentro da família do sistema operativo que especificaram na sua configuração de serviço (.cscfg), como, por exemplo, o Windows Server 2016.

Quando um cliente escolhe uma versão específica do sistema operativo para a sua implementação do Azure Cloud Services, desativa as atualizações automáticas do sistema operativo e faz com que a correção seja responsável. O cliente deve garantir que as suas instâncias de função estão a receber atualizações ou que podem expor a sua aplicação a vulnerabilidades de segurança.

- [Hóspede Azure OS](cloud-services-guestos-msrc-releases.md)

- [Azure Guest OS apoia a política de apoio e reforma](cloud-services-guestos-retirement-policy.md)

- [Como configurar o serviço Cloud (Clássico)](cloud-services-how-to-configure-portal.md)

- [Gerir a versão OS do Guest](cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementar uma solução automatizada de gestão de patchs para títulos de software de terceiros

**Orientação**: Utilize uma solução de gestão de patchs de terceiros. Os clientes que já utilizam o Gestor de Configuração no seu ambiente também podem utilizar o Editor de Atualizações do Sistema Center, permitindo-lhes publicar atualizações personalizadas no Serviço de Atualização do Servidor do Windows Server. 

Isto permite que a Gestão de Atualização remendo máquinas que usam o Gestor de Configuração como o seu repositório de atualização com software de terceiros.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Utilize um processo de classificação de risco para priorizar a reparação de vulnerabilidades descobertas

**Orientação**: Recomenda-se que um cliente compreenda o âmbito do seu risco de um ataque DDoS numa base contínua. 

Sugerimos pensar através destes cenários:

- Que novos recursos azure publicamente disponíveis precisam de proteção?
- Há um único ponto de falha no serviço?
- Como é que os serviços podem ser isolados para limitar o impacto de um ataque, ao mesmo tempo que disponibilizam serviços a clientes válidos?
- Existem redes virtuais onde o DDoS Protection Standard deve ser ativado mas não é?
- Os meus serviços estão ativos/ativos com falhas em várias regiões?

Documentação de apoio:

- [Avaliação de risco dos seus recursos Azure](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="inventory-and-asset-management"></a>Gestão de recursos e inventário

*Para mais informações, consulte o [Azure Security Benchmark: Inventário e gestão de ativos.](../security/benchmarks/security-control-inventory-asset-management.md)*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Utilize uma solução automatizada de descoberta de ativos

**Orientação**: Não aplicável aos Serviços Azure Cloud. Esta recomendação aplica-se aos recursos de computação IaaS.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminar recursos azure não autorizados

**Orientação**: Recomenda-se conciliar o inventário regularmente e garantir que os recursos não autorizados sejam eliminados da subscrição em tempo útil.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definir e manter um inventário dos recursos aprovados do Azure

**Orientação**: O cliente deve definir recursos Azure aprovados e software aprovado para recursos de computação.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor para recursos Azure não aprovados

**Orientação**: Utilize a função de Controlo de Aplicações Adaptativas, disponível no Centro de Segurança Azure. É uma solução inteligente, automatizada e de ponta a ponta do Security Center que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Windows e Linux, Azure e non-Azure. Também ajuda a endurecer as suas máquinas contra malware. 

Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:
- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

Mais detalhes estão disponíveis nos links referenciados.

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor para aplicações de software não aprovadas dentro dos recursos computacional

**Orientação**: Utilize a função de Controlo de Aplicações Adaptativas, disponível no Centro de Segurança Azure. É uma solução inteligente, automatizada e de ponta a ponta do Security Center que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Windows e Linux, Azure e non-Azure. Também ajuda a endurecer as suas máquinas contra malware. 

Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:

- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.

- Evitar a utilização de software não desejado no seu ambiente.

- Evitar a execução de aplicações antigas e não suportadas.

- Impedir ferramentas de software específicas que não são permitidas na sua organização.

- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

Mais detalhes estão disponíveis nos links referenciados.

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Remover recursos e aplicações de software não aprovados

**Orientação**: Utilize a função de Controlo de Aplicações Adaptativas, disponível no Centro de Segurança Azure. É uma solução inteligente, automatizada e de ponta a ponta do Security Center que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Windows e Linux, Azure e non-Azure. Também ajuda a endurecer as suas máquinas contra malware. 

Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:

- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.

- Evitar a utilização de software não desejado no seu ambiente.

- Evitar a execução de aplicações antigas e não suportadas.

- Impedir ferramentas de software específicas que não são permitidas na sua organização.

- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

Mais detalhes estão disponíveis nos links referenciados.

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="68-use-only-approved-applications"></a>6.8: Utilizar apenas aplicações aprovadas

**Orientação**: Utilize a função de Controlo de Aplicações Adaptativas, disponível no Centro de Segurança Azure. É uma solução inteligente, automatizada e de ponta a ponta do Security Center que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Windows e Linux, Azure e non-Azure. Também ajuda a endurecer as suas máquinas contra malware. 

Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:

- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.

- Evitar a utilização de software não desejado no seu ambiente.

- Evitar a execução de aplicações antigas e não suportadas.

- Impedir ferramentas de software específicas que não são permitidas na sua organização.

- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

Mais detalhes estão disponíveis nos links referenciados.

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Manter um inventário de títulos de software aprovados

**Orientação**: Utilize a função de Controlo de Aplicações Adaptativas, disponível no Centro de Segurança Azure. É uma solução inteligente, automatizada e de ponta a ponta do Security Center que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Windows e Linux, Azure e non-Azure. Também ajuda a endurecer as suas máquinas contra malware. 

Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:
- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.
- Evitar a utilização de software não desejado no seu ambiente.
- Evitar a execução de aplicações antigas e não suportadas.
- Impedir ferramentas de software específicas que não são permitidas na sua organização.
- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

Mais detalhes estão disponíveis nos links referenciados.

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitar a capacidade dos utilizadores de executar scripts em recursos computacional

**Orientação**: Utilize a função de Controlo de Aplicações Adaptativas, disponível no Centro de Segurança Azure. É uma solução inteligente, automatizada e de ponta a ponta do Security Center que o ajuda a controlar quais aplicações podem funcionar nas suas máquinas Windows e Linux, Azure e non-Azure. Também ajuda a endurecer as suas máquinas contra malware. 

Esta funcionalidade encontra-se disponível tanto para as máquinas Azure como para o Windows não-Azure (todas as versões, classic ou Azure Resource Manager) e linux.

O Security Center utiliza machine learning para analisar as aplicações em execução nas suas máquinas e cria uma lista de permitis a partir desta inteligência. Esta capacidade simplifica muito o processo de configuração e manutenção da aplicação, permitindo políticas de listas, permitindo-lhe:

- Bloqueie ou alerte para tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- Estar em conformidade com a política de segurança da sua organização que estabelece que só pode ser utilizado software licenciado.

- Evitar a utilização de software não desejado no seu ambiente.

- Evitar a execução de aplicações antigas e não suportadas.

- Impedir ferramentas de software específicas que não são permitidas na sua organização.

- Permitir que a equipa de TI controle o acesso a dados confidenciais através da utilização de aplicações.

Mais detalhes estão disponíveis nos links referenciados.

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregar física ou logicamente aplicações de alto risco

**Orientação**: Para aplicações sensíveis ou de alto risco com a Azure Cloud Services, implemente subscrições separadas ou grupos de gestão para proporcionar isolamento.

Use um grupo de segurança de rede, crie uma regra de segurança de entrada, escolha um serviço como http, escolha uma porta personalizada também, dê-lhe uma prioridade e um nome. A prioridade afeta a ordem em que as regras são aplicadas, quanto menor o valor numérico, quanto mais cedo a regra for aplicada. Terá de associar o seu grupo de segurança de rede a uma sub-rede ou a uma interface de rede específica para isolar ou segmentar o tráfego de rede com base nas necessidades do seu negócio.

Mais detalhes estão disponíveis nos links referenciados.

- [Tutorial - Filtrar o tráfego da rede com um grupo de segurança de rede utilizando o portal Azure](../virtual-network/tutorial-filter-network-traffic.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="secure-configuration"></a>Configuração segura

*Para obter mais informações, consulte o [Benchmark de Segurança Azure: Configuração segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Estabelecer configurações seguras para todos os recursos da Azure

**Orientação**: Utilize as recomendações do Azure Security Center como base de configuração segura para os seus recursos Azure Cloud Services. 

No portal Azure, escolha o Security Center, depois as aplicações Compute &amp; e a Azure Cloud Services para ver as recomendações aplicáveis aos seus recursos de serviço.

- [Recomendações de segurança: um guia de referência](../security-center/recommendations-reference.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Manter configurações seguras de recursos Azure

**Orientação**: Não aplicável aos Serviços Azure Cloud. Baseia-se no modelo clássico de implantação. Recomenda-se a utilização de uma solução de terceiros para manter configurações seguras de recursos Azure

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Armazenar de forma segura a configuração dos recursos Azure

**Orientação**: O ficheiro de configuração do Azure Cloud Service armazena os atributos operacionais de um recurso. Pode armazenar uma cópia dos ficheiros de configuração numa conta de armazenamento segura.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementar ferramentas de gestão de configuração para recursos Azure

**Orientação**: Não aplicável aos Serviços Azure Cloud. Baseia-se no modelo clássico de implementação e não pode ser gerido por ferramentas de configuração baseadas em recursos Azure Resource Manager.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementar ferramentas de gestão de configuração para sistemas operativos

**Orientação**: Não aplicável aos Serviços Azure Cloud. Esta recomendação aplica-se à Infraestrutura como um serviço de recursos computacional baseados em iaas.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementar monitorização automatizada de configuração para recursos Azure

**Orientação**: Utilize o Centro de Segurança Azure para efetuar análises de base para os seus Recursos Azure.  

- [Como remediar recomendações no Centro de Segurança Azure](../security-center/security-center-remediate-recommendations.md)

**Monitorização do Centro de Segurança do Azure**: Sim

**Responsabilidade**: Cliente

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implementar monitorização automatizada de configuração para sistemas operativos

**Orientação**: No Azure Security Center, escolha o recurso Compute &amp; Apps e siga as recomendações para máquinas virtuais, servidores e contentores.

- [Compreender as recomendações do contentor do Centro de Segurança do Azure](../security-center/container-security.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="711-manage-azure-secrets-securely"></a>7.11: Gerir os segredos do Azure de forma segura

**Orientação**: A azure Cloud Services baseia-se num modelo de implementação clássico e não se integra com o Azure Key Vault.

Pode proteger segredos como credenciais que são usadas nos Serviços Azure Cloud para que não tenha de escrever uma palavra-passe de cada vez. Para começar, especifique uma palavra-passe de texto simples, converta-a numa cadeia segura utilizando o comando ConvertTo-SecureString, PowerShell. Em seguida, converta esta cadeia de segurança numa cadeia padrão encriptada utilizando ConvertFrom-SecureString.  Agora pode guardar esta cadeia padrão encriptada para um ficheiro utilizando O Conteúdo Definido.

Além disso, recomenda-se guardar as chaves privadas para os certificados utilizados nos Serviços Azure Cloud para um armazenamento seguro.

- [Configurar o ambiente de trabalho remoto da PowerShell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Eliminar a exposição credencial não intencional

**Orientação**: Proteja segredos como credenciais usadas nos Serviços Azure Cloud para que não tenha de escrever uma palavra-passe de cada vez. 
 

Para começar, especifique uma palavra-passe de texto simples, altere-a para uma cadeia segura utilizando o comando ConvertTo-SecureString, PowerShell. Em seguida, converta esta cadeia de segurança numa cadeia padrão encriptada utilizando ConvertFrom-SecureString. Agora guarde esta cadeia padrão encriptada para um ficheiro usando Set-Content comando.

Guarde as chaves privadas para os certificados utilizados nos Serviços Azure Cloud para um local de armazenamento seguro.

- [Configurar o ambiente de trabalho remoto da PowerShell](cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="malware-defense"></a>Defesa contra malware

*Para mais informações, consulte o [Azure Security Benchmark: Malware defense](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Utilizar software antimalware gerido centralmente

**Orientação**: O Microsoft Antimalware for Azure está disponível para serviços em nuvem azure e máquinas virtuais. É uma proteção gratuita em tempo real que ajuda a identificar e remover vírus, spyware e outros softwares maliciosos. Gera alertas quando um software malicioso ou indesejado conhecido tenta instalar-se ou funcionar nos seus sistemas Azure. 

Utilize o cmdlet Antimalware baseado em PowerShell para obter a configuração antimalware, com "Get-AzureServiceAntimalwareConfig".

Ative a extensão antimalware com um script PowerShell na Tarefa de Arranque nos Serviços de Nuvem Azure.

Escolha a função de controlo de aplicações adaptáveis no Azure Security Center, uma solução inteligente, automatizada e de ponta a ponta. Ajuda a endurecer as suas máquinas contra malware e permite bloquear ou alertar sobre tentativas de executar aplicações maliciosas, incluindo aquelas que de outra forma poderiam ser perdidas por soluções antimalware.

- [Como posso adicionar uma extensão antimalware para os meus Serviços Azure Cloud de forma automatizada](./cloud-services-configuration-and-management-faq.md?amp;preserve-view=#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)

- [Cenários de implantação de antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Controlos de aplicações adaptáveis](../security-center/security-center-adaptive-application.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="incident-response"></a>Resposta a incidentes

*Para mais informações, consulte o [Benchmark de Segurança Azure: Resposta a incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Criar um guia de resposta a incidentes

**Orientação**: desenvolva um guia de respostas a incidentes para a sua organização. Confirme que existem planos escritos de resposta a incidentes, que definem todas as funções do pessoal, assim como as fases de manipulação/gestão de incidentes desde a deteção até à análise pós-incidente.

- [Como configurar automatizações de fluxo de trabalho dentro do Centro de Segurança Azure](../security-center/security-center-planning-and-operations-guide.md)

- [Orientação para a construção do seu próprio processo de resposta a incidentes de segurança](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomia de um incidente do Microsoft Security Response Center](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [O cliente também pode aproveitar o Guia de Tratamento de Incidentes de Segurança Informática da NIST para ajudar na criação do seu próprio plano de resposta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Criar um procedimento de pontuação e priorização de incidentes

**Orientação**: O Centro de Segurança Azure atribui uma gravidade a cada alerta para ajudá-lo a priorizar quais os alertas que devem ser investigados primeiro. A gravidade baseia-se na confiança que o Centro de Segurança está na descoberta ou no analítico utilizado para emitir o alerta, bem como no nível de confiança de que havia intenção maliciosa por trás da atividade que levou ao alerta. 

Marque claramente as subscrições (por exemplo, produção, não produção) e crie um sistema de nomeação para identificar e categorizar claramente os recursos da Azure.

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="103-test-security-response-procedures"></a>10.3: Procedimentos de resposta à segurança do teste

**Orientação**: Realize exercícios para testar as capacidades de resposta a incidentes dos seus sistemas numa cadência regular. Identifique pontos fracos e lacunas e reavalie o plano, conforme necessário. 

- [Consulte a publicação do NIST: Guia para testar, treinar e exercitar programas para planos e capacidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Fornecer dados de contacto com incidentes de segurança e configurar notificações de alerta para incidentes de segurança

**Orientação**: As informações de contacto com incidentes de segurança serão utilizadas pela Microsoft para o contactar se o Microsoft Security Response Center (MSRC) descobrir que os dados do cliente foram acedidos por uma parte ilegal ou não autorizada. Reveja os incidentes após o facto de garantir que as questões sejam resolvidas. 

- [Como definir o Contacto de Segurança do Centro de Segurança Azure](../security-center/security-center-provide-security-contact-details.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporar alertas de segurança no seu sistema de resposta a incidentes

**Orientação**: Exporte os alertas e recomendações do Centro de Segurança Azure utilizando a função exportação contínua. A Exportação Contínua permite-lhe exportar alertas e recomendações manualmente ou de forma contínua e contínua. Pode utilizar o conector de dados do Centro de Segurança para transmitir os alertas ao Azure Sentinel. 

- [Como configurar a exportação contínua](../security-center/continuous-export.md) 

- [Como transmitir alertas para o Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatizar a resposta aos alertas de segurança

**Orientação**: Utilize a função de automatização do fluxo de trabalho no Centro de Segurança Azure para desencadear automaticamente respostas através de "Aplicações Lógicas" em alertas e recomendações de segurança.

- [Como configurar a automatização do fluxo de trabalho e as aplicações lógicas](../security-center/workflow-automation.md)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Cliente

## <a name="penetration-tests-and-red-team-exercises"></a>Testes de penetração e exercícios da Red Team

*Para obter mais informações, consulte o [Azure Security Benchmark: Testes de penetração e exercícios de equipa vermelha](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realizar testes regulares de penetração dos seus recursos Azure e garantir a reparação de todas as conclusões críticas de segurança

**Orientação**: Siga as Regras de Engajamento da Microsoft para garantir que os seus Testes de Penetração não violam as políticas da Microsoft: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1 

- [Você pode encontrar mais informações sobre a estratégia da Microsoft e execução de Red Teaming e testes de penetração em sites ao vivo contra infraestruturas, serviços e aplicações geridas pela Microsoft, aqui](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Monitorização do Centro de Segurança do Azure**: Não aplicável

**Responsabilidade**: Partilhada

## <a name="next-steps"></a>Passos seguintes

- Consulte a [referência de segurança Azure](../security/benchmarks/overview.md)
- Saiba mais sobre as [linhas de base de segurança do Azure](../security/benchmarks/security-baselines-overview.md)