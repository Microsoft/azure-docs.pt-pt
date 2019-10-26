---
title: Segurança de dados do Log Analytics | Microsoft Docs
description: Saiba mais sobre como Log Analytics protege sua privacidade e protege seus dados.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 03/04/2019
ms.openlocfilehash: 3ff69928f4d6aa1692cdb1d4fd7e846b3a6b7a5c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932541"
---
# <a name="log-analytics-data-security"></a>Segurança de dados do Log Analytics
Este documento destina-se a fornecer informações específicas para Log Analytics, que é um recurso do Azure Monitor, para complementar as informações sobre [central de confiabilidade do Azure](../../security/fundamentals/trust-center.md).  

Este artigo explica a forma como os dados são recolhidos, processados e protegidos pelo Log Analytics. Você pode usar agentes para se conectar ao serviço Web, usar System Center Operations Manager para coletar dados operacionais ou recuperar dados do diagnóstico do Azure para uso pelo Log Analytics. 

O serviço de Log Analytics gerencia seus dados baseados em nuvem com segurança usando os seguintes métodos:

* Segregação de dados
* Retenção de dados
* Segurança física
* Gerenciamento de incidentes
* Conformidade
* Certificações de padrões de segurança

Entre em contato conosco com perguntas, sugestões ou problemas sobre qualquer uma das informações a seguir, incluindo nossas políticas de segurança nas [Opções de suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Enviando dados com segurança usando o TLS 1,2 

Para garantir a segurança dos dados em trânsito para Log Analytics, recomendamos que você configure o agente para usar pelo menos o protocolo TLS 1,2. Versões mais antigas do TLS/protocolo SSL (SSL) foram considerados vulneráveis e, embora ainda funcionem para permitir a compatibilidade com versões anteriores, elas não são **recomendadas**e o setor está mudando rapidamente para abandonar o suporte para esses protocolos. 

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) definiu um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desabilitar versões mais antigas de TLS/SSL e atualizar para protocolos mais seguros. Quando o Azure descartar o suporte herdado, se os agentes não puderem se comunicar por pelo menos o TLS 1,2, você não poderá enviar dados para Log Analytics. 

Não recomendamos definir explicitamente seu agente para usar apenas o TLS 1,2, a menos que seja absolutamente necessário, pois ele pode interromper os recursos de segurança no nível da plataforma que permitem detectar e aproveitar automaticamente os protocolos mais seguros à medida que eles se tornam disponíveis, como como TLS 1,3. 

### <a name="platform-specific-guidance"></a>Diretrizes específicas da plataforma

|Plataforma/idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições do Linux tendem a confiar no [OpenSSL](https://www.openssl.org) para suporte a TLS 1,2.  | Verifique o [changelog OpenSSL](https://www.openssl.org/news/changelog.html) para confirmar se há suporte para sua versão do OpenSSL.|
| Windows 8,0-10 | Com suporte e habilitado por padrão. | Para confirmar que você ainda está usando as [configurações padrão](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012-2016 | Com suporte e habilitado por padrão. | Para confirmar que você ainda está usando as [configurações padrão](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Com suporte, mas não habilitado por padrão. | Consulte a página [configurações do registro TLS (Transport Layer Security)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) para obter detalhes sobre como habilitar o.  |

## <a name="data-segregation"></a>Segregação de dados
Depois que os dados são ingeridos pelo serviço de Log Analytics, os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são marcados por espaço de trabalho. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. Seus dados são armazenados em um banco de dado dedicado no cluster de armazenamento na região que você selecionou.

## <a name="data-retention"></a>Retenção de dados
Os dados de pesquisa de log indexados são armazenados e mantidos de acordo com seu plano de preços. Para obter mais informações, consulte [preços de log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Como parte do seu [contrato de assinatura](https://azure.microsoft.com/support/legal/subscription-agreement/), a Microsoft manterá seus dados de acordo com os termos do contrato.  Quando os dados do cliente são removidos, nenhuma unidade física é destruída.  

A tabela a seguir lista algumas das soluções disponíveis e fornece exemplos do tipo de dados coletados.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Capacidade e Desempenho |Metadados e dados de desempenho |
| Gestão de Atualizações |Metadados e dados de estado |
| Gestão de Registos |Logs de eventos definidos pelo usuário, logs de eventos do Windows e/ou logs do IIS |
| Monitorização de Alterações |Inventário de software, serviços do Windows e metadados do daemon do Linux e metadados de arquivo do Windows/Linux |
| SQL e Avaliação do Active Directory |Dados do WMI, dados do registro, dados de desempenho e SQL Server resultados da exibição de gerenciamento dinâmico |

A tabela a seguir mostra exemplos de tipos de dados:

| **Tipo de dados** | **Campo** |
| --- | --- |
| Alerta |Nome do alerta, descrição do alerta, BaseManagedEntityId, ID do problema, IsMonitorAlert, RuleId, ResolutionState, prioridade, severidade, categoria, proprietário, ResolvedBy, timeelevated, timeadded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, Timeresolver, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração |CustomerID, AgentId, ID da entidade, ManagedTypeID, ManagedTypePropertyID, CurrentValue, alterado |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, ID de regra, PublisherID, PublisherName, FullNumber, número, categoria, ChannelLevel, LoggingComputer, EventData, Eventparameters, tempo gerado, timeadded <br>**Observação:** Quando você grava eventos com campos personalizados no log de eventos do Windows, Log Analytics os coleta. |
| Metadados |BaseManagedEntityId, objectstatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Endereço, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, Samplevalue, timeamostrated, timeadded |
| Estado |StateChangeEventId, stateId, NewHealthState, OldHealthState, contexto, TimeGenerated, timeadicionad, StateId2, BaseManagedEntityId, Monitorid, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física
O serviço de Log Analytics é gerenciado pelo pessoal da Microsoft e todas as atividades são registradas e podem ser auditadas. Log Analytics é operado como um serviço do Azure e atende a todos os requisitos de conformidade e segurança do Azure. Você pode exibir detalhes sobre a segurança física dos ativos do Azure na página 18 da [visão geral de segurança do Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Os direitos de acesso físico a áreas seguras são alterados em um dia útil para qualquer pessoa que não tenha mais responsabilidade pelo serviço de Log Analytics, incluindo transferência e rescisão. Você pode ler sobre a infraestrutura física global que usamos em [data centers da Microsoft](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Gerenciamento de incidentes
O Log Analytics tem um processo de gerenciamento de incidentes que todos os serviços da Microsoft aderem. Para resumir, nós:

* Usar um modelo de responsabilidade compartilhada em que uma parte da responsabilidade de segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerenciar incidentes de segurança do Azure:
  * Iniciar uma investigação após a detecção de um incidente
  * Avalie o impacto e a gravidade de um incidente por um membro da equipe de resposta a incidentes em chamada. Com base nas evidências, a avaliação pode ou não resultar em maior escalonamento para a equipe de resposta de segurança.
  * Diagnostique um incidente por especialistas de resposta de segurança para conduzir a investigação técnica ou forense, identificar estratégias de contenção, mitigação e solução alternativa. Se a equipe de segurança acreditar que os dados do cliente podem ter sido expostos a um indivíduo ilegal ou não autorizado, a execução paralela do processo de notificação de incidentes do cliente começa em paralelo.  
  * Estabilizar e recuperar do incidente. A equipe de resposta a incidentes cria um plano de recuperação para atenuar o problema. Etapas de contenção de crise como a quarentena de sistemas afetados podem ocorrer imediatamente e em paralelo com o diagnóstico. As mitigações de prazo mais longo podem ser planejadas, que ocorrem após o risco imediato ter passado.  
  * Feche o incidente e realize um post-mortem. A equipe de resposta a incidentes cria um post-mortem que descreve os detalhes do incidente, com a intenção de revisar políticas, procedimentos e processos para evitar uma recorrência do evento.
* Notifique os clientes sobre incidentes de segurança:
  * Determine o escopo dos clientes afetados e forneça qualquer pessoa afetada como um aviso mais detalhado possível
  * Crie um aviso para fornecer aos clientes informações detalhadas o suficiente para que eles possam realizar uma investigação em seus finais e atender a qualquer compromisso que eles tenham feito com seus usuários finais, sem indevidamente atrasar o processo de notificação.
  * Confirme e declare o incidente, conforme necessário.
  * Notifique os clientes com uma notificação de incidente sem atraso inaceitável e de acordo com qualquer compromisso legal ou contratual. As notificações de incidentes de segurança são entregues a um ou mais administradores do cliente por qualquer meio que a Microsoft selecionar, incluindo por email.
* Realize treinamento e preparação para a equipe:
  * O pessoal da Microsoft precisa concluir o treinamento de segurança e conscientização, o que ajuda a identificar e relatar problemas de segurança suspeitos.  
  * Os operadores que trabalham com o serviço de Microsoft Azure têm obrigações de treinamento adicionais que envolvem o acesso a sistemas confidenciais que hospedam dados do cliente.
  * A equipe de resposta de segurança da Microsoft recebe treinamento especializado para suas funções

Se ocorrer perda de dados do cliente, notificaremos cada cliente dentro de um dia. No entanto, a perda de dados do cliente nunca ocorreu com o serviço. 

Para obter mais informações sobre como a Microsoft responde a incidentes de segurança, consulte [Microsoft Azure resposta de segurança na nuvem](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Conformidade
O programa de controle e segurança de informações da equipe de serviços Log Analytics e desenvolvimento de software dá suporte a seus requisitos de negócios e obedece às leis e regulamentos, conforme descrito na [central de confiabilidade Microsoft Azure](https://azure.microsoft.com/support/trust-center/) e na [central de confiabilidade da Microsoft Conformidade](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Como Log Analytics estabelece os requisitos de segurança, identifica os controles de segurança, gerencia e monitora os riscos também são descritos lá. Anualmente, examinamos as políticas, os padrões, os procedimentos e as diretrizes.

Cada membro da equipe de desenvolvimento recebe treinamento formal de segurança de aplicativos. Internamente, usamos um sistema de controle de versão para o desenvolvimento de software. Cada projeto de software é protegido pelo sistema de controle de versão.

A Microsoft tem uma equipe de segurança e conformidade que supervisiona e avalia todos os serviços da Microsoft. Os responsáveis pela segurança da informação compõem a equipe e não estão associados às equipes de engenharia que desenvolve Log Analytics. Os responsáveis pela segurança têm sua própria cadeia de gerenciamento e realizam avaliações independentes de produtos e serviços para garantir a segurança e a conformidade.

O Conselho de diretores da Microsoft é notificado por um relatório anual sobre todos os programas de segurança de informações da Microsoft.

O Log Analytics equipe de serviços e desenvolvimento de software está trabalhando ativamente com as equipes legais e de conformidade da Microsoft e com outros parceiros do setor para adquirir várias certificações.

## <a name="certifications-and-attestations"></a>Certificações e atestado
O Azure Log Analytics atende aos seguintes requisitos:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Padrão de segurança de dados (PCI DSS) do setor de cartão de pagamento (compatível com PCI)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) pelo PCI Security Standards Council.
* Compatível com [SOC (controle de organização de serviço) 1 tipo 1 e SOC 2 tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA e alta tecnologia](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) para empresas que têm um contrato de parceiro comercial da HIPAA
* Critérios de engenharia comum do Windows
* Informática Fidedigna da Microsoft
* Como um serviço do Azure, os componentes que Log Analytics usam aderem aos requisitos de conformidade do Azure. Você pode ler mais em [conformidade da central de confiabilidade da Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> Em algumas certificações/atestado, Log Analytics está listado sob seu nome antigo de *insights operacionais*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de computação em nuvem
O diagrama a seguir mostra uma arquitetura de segurança de nuvem como o fluxo de informações de sua empresa e como elas são protegidas à medida que são movidas para o serviço de Log Analytics, visto por você no portal do Azure. Mais informações sobre cada etapa seguem o diagrama.

![Imagem de coleta de dados de Log Analytics e segurança](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscreva-se para Log Analytics e colete dados
Para que sua organização envie dados para Log Analytics, configure um agente do Windows ou Linux em execução em máquinas virtuais do Azure ou em computadores físicos ou virtuais em seu ambiente ou em outro provedor de nuvem.  Se você usar Operations Manager, no grupo de gerenciamento, você configurará o agente de Operations Manager. Os usuários (que podem ser você, outros usuários individuais ou um grupo de pessoas) criam um ou mais espaços de trabalho do Log Analytics e registram agentes usando uma das seguintes contas:

* [ID organizacional](../../active-directory/fundamentals/sign-up-organization.md)
* [Conta da Microsoft-Outlook, Office Live, MSN](https://account.microsoft.com/account)

Um espaço de trabalho Log Analytics é onde os dados são coletados, agregados, analisados e apresentados. Um espaço de trabalho é usado principalmente como um meio de particionar dados, e cada espaço de trabalho é exclusivo. Por exemplo, talvez você queira ter seus dados de produção gerenciados com um espaço de trabalho e seus dados de teste gerenciados com outro espaço de trabalho. Os espaços de trabalho também ajudam um administrador a controlar o acesso dos usuários aos dados. Cada espaço de trabalho pode ter várias contas de usuário associadas a ele, e cada conta de usuário pode acessar vários espaços de trabalho do Log Analytics. Você cria espaços de trabalho com base na região do datacenter.

Por Operations Manager, o grupo de gerenciamento de Operations Manager estabelece uma conexão com o serviço de Log Analytics. Em seguida, você configura quais sistemas gerenciados por agente no grupo de gerenciamento têm permissão para coletar e enviar dados para o serviço. Dependendo da solução que você habilitou, os dados dessas soluções são enviados diretamente de um servidor de gerenciamento de Operations Manager para o serviço Log Analytics, ou devido ao volume de dados coletados pelo sistema gerenciado por agente, são enviados diretamente do o agente para o serviço. Para sistemas não monitorados pelo Operations Manager, cada um se conecta com segurança ao serviço de Log Analytics diretamente.

Toda a comunicação entre sistemas conectados e o serviço de Log Analytics é criptografada. O protocolo TLS (HTTPS) é usado para criptografia.  O processo de SDL da Microsoft é seguido para garantir que Log Analytics esteja atualizado com os avanços mais recentes nos protocolos criptográficos.

Cada tipo de agente coleta dados para Log Analytics. O tipo de dados coletados depende dos tipos de soluções usadas. Você pode ver um resumo da coleta de dados em [adicionar log Analytics soluções do Galeria de soluções](../../azure-monitor/insights/solutions.md). Além disso, informações de coleta mais detalhadas estão disponíveis para a maioria das soluções. Uma solução é um pacote de exibições predefinidas, consultas de pesquisa de log, regras de coleta de dados e lógica de processamento. Somente os administradores podem usar Log Analytics para importar uma solução. Depois que a solução for importada, ela será movida para os servidores de gerenciamento de Operations Manager (se usado) e, em seguida, para todos os agentes escolhidos. Depois disso, os agentes coletam os dados.

## <a name="2-send-data-from-agents"></a>2. enviar dados de agentes
Você registra todos os tipos de agente com uma chave de registro e uma conexão segura é estabelecida entre o agente e o serviço de Log Analytics usando a autenticação baseada em certificado e o SSL com a porta 443. Log Analytics usa um repositório secreto para gerar e manter chaves. As chaves privadas são giradas a cada 90 dias e são armazenadas no Azure e são gerenciadas pelas operações do Azure que seguem práticas de conformidade e regulamentações estritas.

Com o Operations Manager, o grupo de gerenciamento registrado com um espaço de trabalho do Log Analytics estabelece uma conexão HTTPS segura com um servidor de gerenciamento do Operations Manager.

Para agentes do Windows ou Linux em execução em máquinas virtuais do Azure, uma chave de armazenamento somente leitura é usada para ler eventos de diagnóstico em tabelas do Azure.  

Com qualquer agente que se reporte a um grupo de gerenciamento de Operations Manager integrado com Log Analytics, se o servidor de gerenciamento não puder se comunicar com o serviço por qualquer motivo, os dados coletados serão armazenados localmente em um cache temporário no gerenciamento servidor.   Eles tentam reenviar os dados a cada oito minutos por duas horas.  Para dados que ignoram o servidor de gerenciamento e são enviados diretamente para Log Analytics, o comportamento é consistente com o agente do Windows.  

Os dados armazenados em cache do Windows ou do agente do servidor de gerenciamento são protegidos pelo armazenamento de credenciais do sistema operacional. Se o serviço não puder processar os dados após duas horas, os agentes colocarão em fila os dados. Se a fila ficar cheia, o agente começará a descartar os tipos de dados, começando com os dados de desempenho. O limite de fila do agente é uma chave do registro para que você possa modificá-lo, se necessário. Os dados coletados são compactados e enviados para o serviço, ignorando o Operations Manager de bancos do grupo de gerenciamento, portanto, ele não adiciona nenhuma carga a eles. Depois que os dados coletados são enviados, eles são removidos do cache.

Conforme descrito acima, os dados do servidor de gerenciamento ou agentes conectados diretamente são enviados por SSL para Microsoft Azure data centers. Opcionalmente, você pode usar o ExpressRoute para fornecer segurança adicional aos dados. O ExpressRoute é uma maneira de se conectar diretamente ao Azure de sua rede WAN existente, como uma VPN MPLS (comutação de rótulo de vários protocolos), fornecida por um provedor de serviços de rede. Para obter mais informações, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. o serviço de Log Analytics recebe e processa dados
O serviço de Log Analytics garante que os dados de entrada sejam de uma fonte confiável Validando certificados e a integridade dos dados com a autenticação do Azure. Os dados brutos não processados são então armazenados em um hub de eventos do Azure na região em que os dados eventualmente serão armazenados em repouso. O tipo de dados que é armazenado depende dos tipos de soluções que foram importados e usados para coletar dados. Em seguida, o serviço de Log Analytics processa os dados brutos e os insome no banco de dado.

O período de retenção dos dados coletados armazenados no banco de dado depende do plano de preços selecionado. Para a camada *gratuita* , os dados coletados ficam disponíveis por sete dias. Para a camada *paga* , os dados coletados ficam disponíveis por 31 dias por padrão, mas podem ser estendidos para 730 dias. Os dados são armazenados criptografados em repouso no armazenamento do Azure, para garantir a confidencialidade dos dados e os dados são replicados dentro da região local usando o LRS (armazenamento com redundância local). As duas últimas semanas de dados também são armazenadas em cache baseado em SSD e esse cache é criptografado.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. usar Log Analytics para acessar os dados
Para acessar seu espaço de trabalho do Log Analytics, você entra no portal do Azure usando a conta institucional ou conta Microsoft que você configurou anteriormente. Todo o tráfego entre o portal e o serviço de Log Analytics é enviado por um canal HTTPS seguro. Ao usar o portal, uma ID de sessão é gerada no cliente do usuário (navegador da Web) e os dados são armazenados em um cache local até que a sessão seja encerrada. Quando encerrado, o cache é excluído. Os cookies do lado do cliente, que não contêm informações de identificação pessoal, não são removidos automaticamente. Os cookies de sessão são marcados como HTTPOnly e são protegidos. Após um período ocioso predeterminado, a sessão de portal do Azure é encerrada.

## <a name="next-steps"></a>Passos seguintes
* Saiba como coletar dados com Log Analytics para suas VMs do Azure seguindo o [início rápido da VM do Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Se você estiver procurando coletar dados de computadores físicos ou virtuais do Windows ou Linux em seu ambiente, consulte o guia de [início rápido para computadores Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) ou [início rápido para computadores Windows](../../azure-monitor/learn/quick-collect-windows-computer.md)

