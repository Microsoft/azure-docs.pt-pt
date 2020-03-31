---
title: Segurança de dados do Log Analytics [ Microsoft Docs
description: Saiba como o Log Analytics protege a sua privacidade e protege os seus dados.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2019
ms.openlocfilehash: 63d8d8d3701a9adca4bd01e6e061877f5d0bd245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333350"
---
# <a name="log-analytics-data-security"></a>Segurança de dados do Log Analytics
Este documento destina-se a fornecer informações específicas ao Log Analytics, que é uma característica do Monitor Azure, para complementar a informação sobre o [Azure Trust Center.](../../security/fundamentals/trust-center.md)  

Este artigo explica a forma como os dados são recolhidos, processados e protegidos pelo Log Analytics. Pode utilizar agentes para se conectar em função do serviço web, utilizar o System Center Operations Manager para recolher dados operacionais ou recuperar dados de diagnósticos do Azure para utilização através do Log Analytics. 

O serviço Log Analytics gere os seus dados baseados na nuvem de forma segura, utilizando os seguintes métodos:

* Segregação de dados
* Retenção de dados
* Segurança física
* Gestão de Incidentes
* Conformidade
* Certificações de normas de segurança

Contacte-nos com quaisquer questões, sugestões ou questões sobre qualquer uma das seguintes informações, incluindo as nossas políticas de segurança nas opções de [suporte do Azure.](https://azure.microsoft.com/support/options/)

## <a name="sending-data-securely-using-tls-12"></a>Envio de dados de forma segura utilizando TLS 1.2 

Para garantir a segurança dos dados em trânsito para o Log Analytics, encorajamo-lo vivamente a configurar o agente para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. As versões mais antigas da Camada de Tomadas Seguras (SSL) foram consideradas vulneráveis e, embora ainda trabalhem para permitir a retrocompatibilidade, não são **recomendadas**, e a indústria está rapidamente a mover-se para abandonar o suporte a estes protocolos mais antigos. 

O Conselho de Normas de Segurança do [PCI](https://www.pcisecuritystandards.org/) fixou um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar versões mais antigas de TLS/SSL e atualizar para protocolos mais seguros. Uma vez que o Azure deixe cair o suporte do legado, se os seus agentes não conseguirem comunicar pelo menos TLS 1.2 não poderá enviar dados para o Log Analytics. 

Não recomendamos que o seu agente utilize explicitamente o TLS 1.2 a menos que seja absolutamente necessário, pois pode quebrar funcionalidades de segurança ao nível da plataforma que lhe permitem detetar e tirar partido de protocolos mais recentes e seguros à medida que se tornam disponíveis, tais como como TLS 1.3. 

### <a name="platform-specific-guidance"></a>Orientação específica da plataforma

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte TLS 1.2.  | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows 8.0 - 10 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por padrão. | Consulte a página de definições de registo de Segurança da Camada de [Transporte (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) para obter mais detalhes sobre como ativar.  |

## <a name="data-segregation"></a>Segregação de dados
Depois de os seus dados terem sido ingeridos pelo serviço Log Analytics, os dados são mantidos logicamente separados em cada componente ao longo do serviço. Todos os dados estão marcados por espaço de trabalho. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. Os seus dados são armazenados numa base de dados dedicada no cluster de armazenamento na região que selecionou.

## <a name="data-retention"></a>Retenção de dados
Os dados de pesquisa de registo indexados são armazenados e conservados de acordo com o seu plano de preços. Para mais informações, consulte [o Log Analytics Pricing](https://azure.microsoft.com/pricing/details/log-analytics/).

Como parte do seu contrato de [subscrição,](https://azure.microsoft.com/support/legal/subscription-agreement/)a Microsoft conservará os seus dados nos termos do acordo.  Quando os dados do cliente são removidos, não são destruídos os impulsos físicos.  

A tabela que se segue lista algumas das soluções disponíveis e fornece exemplos do tipo de dados que recolhem.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Capacidade e Desempenho |Dados de desempenho e metadados |
| Gestão de Atualizações |Metadados e dados do Estado |
| Gestão de Registos |Registos de eventos definidos pelo utilizador, registos de eventos do Windows e/ou registos iIS |
| Monitorização de Alterações |Inventário de software, serviço Windows e metadados de daemon Linux, e metadados de ficheiros Windows/Linux |
| Avaliação do Diretório SQL e Ativo |Dados do WMI, dados de registo, dados de desempenho e resultados de visão dinâmica de gestão do SQL Server |

O quadro que se segue apresenta exemplos de tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta |Nome de alerta, Descrição do alerta, BaseManagedEntityId, ID de problemas, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModificadoReRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota:** Quando escreve eventos com campos personalizados no registo de eventos do Windows, o Log Analytics recolhe-os. |
| Metadados |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Endereço, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, MainName, OffsetInMinuteFromGreenwichTime |
| Desempenho |Nome de objeto, ContraNome, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física
O serviço Log Analytics é gerido pelo pessoal da Microsoft e todas as atividades são registadas e podem ser auditadas. O Log Analytics é operado como um Serviço Azure e cumpre todos os requisitos de Conformidade e Segurança do Azure. Pode visualizar detalhes sobre a segurança física dos ativos do Azure na página 18 da visão geral da [Segurança do Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Os direitos de acesso físico às áreas seguras são alterados num dia útil para quem já não tem responsabilidade pelo serviço Log Analytics, incluindo transferência e rescisão. Pode ler sobre a infraestrutura física global que usamos nos Datacenters da [Microsoft.](https://azure.microsoft.com/global-infrastructure/)

## <a name="incident-management"></a>Gestão de Incidentes
O Log Analytics tem um processo de gestão de incidentes a que todos os serviços da Microsoft aderem. Resumindo, nós:

* Use um modelo de responsabilidade partilhada onde uma parte da responsabilidade de segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerir incidentes de segurança do Azure:
  * Inicie uma investigação sobre a deteção de um incidente
  * Avalie o impacto e a gravidade de um incidente por um membro da equipa de resposta a incidentes. Com base em provas, a avaliação pode ou não resultar numa nova escalada para a equipa de resposta à segurança.
  * Diagnosticar um incidente por peritos em resposta à segurança para conduzir a investigação técnica ou forense, identificar estratégias de contenção, mitigação e sonoridade. Se a equipa de segurança acreditar que os dados do cliente podem ter ficado expostos a um indivíduo ilegal ou não autorizado, a execução paralela do processo de notificação de incidentes do cliente começa em paralelo.  
  * Estabilizar e recuperar do incidente. A equipa de resposta a incidentes cria um plano de recuperação para mitigar o problema. As etapas de contenção de crises, tais como os sistemas impactados em quarentena, podem ocorrer imediatamente e em paralelo com o diagnóstico. Podem ser previstas atenuações a longo prazo que ocorram após a aprovação do risco imediato.  
  * Feche o incidente e conduza uma autópsia. A equipa de resposta a incidentes cria uma autópsia que descreve os detalhes do incidente, com a intenção de rever políticas, procedimentos e processos para evitar uma recorrência do evento.
* Notificar os clientes de incidentes de segurança:
  * Determine o âmbito dos clientes com impacto e forneça a qualquer pessoa que seja impactada o mais detalhadamente possível
  * Crie um aviso para fornecer aos clientes informações suficientemente detalhadas para que possam realizar uma investigação sobre o seu fim e cumprir quaisquer compromissos que tenham assumido aos seus utilizadores finais, sem atrasar indevidamente o processo de notificação.
  * Confirme e declare o incidente, se necessário.
  * Notifique os clientes com uma notificação de incidente sem demora razoável e de acordo com qualquer compromisso legal ou contratual. As notificações de incidentes de segurança são entregues a um ou mais administradores de um cliente por qualquer meio que a Microsoft selecione, incluindo por e-mail.
* Conduzir prontidão e treino da equipa:
  * O pessoal da Microsoft é obrigado a completar o treino de segurança e sensibilização, o que os ajuda a identificar e reportar suspeitas de problemas de segurança.  
  * Os operadores que trabalham no serviço Microsoft Azure têm obrigações de formação de adição em torno do seu acesso a sistemas sensíveis que hospedam dados dos clientes.
  * Pessoal de resposta à segurança da Microsoft recebe formação especializada para as suas funções

Se ocorrer a perda de dados do cliente, notificamos cada cliente no prazo de um dia. No entanto, a perda de dados dos clientes nunca ocorreu com o serviço. 

Para obter mais informações sobre como a Microsoft reage a incidentes de segurança, consulte [o Microsoft Azure Security Response na Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Conformidade
O programa de desenvolvimento e governança de software log Analytics suporta os seus requisitos de negócio e adere a leis e regulamentos como descrito no [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e no Microsoft Trust Center [Compliance.](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx) Como o Log Analytics estabelece requisitos de segurança, identifica controlos de segurança, gere e monitoriza os riscos também são descritos lá. Anualmente, revemos polícias, normas, procedimentos e orientações.

Cada membro da equipa de desenvolvimento recebe formação formal de segurança de aplicações. Internamente, usamos um sistema de controlo de versão para desenvolvimento de software. Cada projeto de software está protegido pelo sistema de controlo da versão.

A Microsoft tem uma equipa de segurança e conformidade que supervisiona e avalia todos os serviços da Microsoft. Os agentes de segurança da informação compõem a equipa e não estão associados às equipas de engenharia que desenvolvem o Log Analytics. Os agentes de segurança têm a sua própria cadeia de gestão e realizam avaliações independentes de produtos e serviços para garantir a segurança e o cumprimento.

O conselho de administração da Microsoft é notificado por um relatório anual sobre todos os programas de segurança da informação na Microsoft.

A equipa de desenvolvimento e serviço de software Log Analytics está a trabalhar ativamente com as equipas microsoft Legal e Compliance e outros parceiros do setor para adquirir várias certificações.

## <a name="certifications-and-attestations"></a>Certificações e atestados
O Azure Log Analytics satisfaz os seguintes requisitos:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* Norma de Segurança de [Dados (Conformidade com pci)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) pelo Conselho de Normas de Segurança do PCI.
* [Controlos de organização de serviços (SOC) 1 Tipo 1 e SOC 2 Tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2)
* [HIPAA e HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) para empresas que tenham um Acordo Associado DE NEGÓCIOS HIPAA
* Critérios comuns de engenharia do Windows
* Microsoft Fidedigno de Computação
* Como serviço Azure, os componentes que o Log Analytics utiliza aderem aos requisitos de conformidade do Azure. Pode ler mais no [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> Em algumas certificações/atestados, o Log Analytics está listado sob o seu nome anterior de *Insights Operacionais*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de computação em nuvem
O diagrama seguinte mostra uma arquitetura de segurança em nuvem como o fluxo de informação da sua empresa e como é segura como se move para o serviço Log Analytics, finalmente visto por si no portal Azure. Mais informações sobre cada passo seguem o diagrama.

![Imagem de recolha e segurança de dados do Log Analytics](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscreva-se no Log Analytics e recolha dados
Para que a sua organização envie dados para o Log Analytics, configura um agente Windows ou Linux a funcionar em máquinas virtuais Azure, ou em computadores virtuais ou físicos no seu ambiente ou noutro fornecedor de nuvem.  Se utilizar o Gestor de Operações, do grupo de gestão, configura o agente do Gestor de Operações. Os utilizadores (que podem ser você, outros utilizadores individuais ou um grupo de pessoas) criam um ou mais espaços de trabalho do Log Analytics e registam agentes utilizando uma das seguintes contas:

* [ID organizacional](../../active-directory/fundamentals/sign-up-organization.md)
* [Conta Microsoft - Outlook, Office Live, MSN](https://account.microsoft.com/account)

Um espaço de trabalho de Log Analytics é onde os dados são recolhidos, agregados, analisados e apresentados. Um espaço de trabalho é usado principalmente como um meio para dados de partição, e cada espaço de trabalho é único. Por exemplo, é melhor que os seus dados de produção sejam geridos com um espaço de trabalho e os seus dados de teste geridos com outro espaço de trabalho. Os espaços de trabalho também ajudam um administrador a controlar o acesso do utilizador aos dados. Cada espaço de trabalho pode ter várias contas de utilizador associadas a ele, e cada conta de utilizador pode aceder a vários espaços de trabalho do Log Analytics. Cria espaços de trabalho baseados na região do datacenter.

Para o Gestor de Operações, o grupo de gestão do Gestor de Operações estabelece uma ligação com o serviço Log Analytics. Em seguida, configura quais os sistemas geridos pelo agente no grupo de gestão que podem recolher e enviar dados para o serviço. Dependendo da solução que permitiu, os dados destas soluções são enviados diretamente de um servidor de gestão do Gestor de Operações para o serviço Log Analytics, ou devido ao volume de dados recolhidos pelo sistema gerido pelo agente, são enviados diretamente a partir de um servidor de gestão de Gestão de Operações para o serviço Log Analytics, ou devido ao volume de dados recolhidos pelo sistema gerido pelo agente, são enviados diretamente a partir de um sistema gerido pelo agente, são enviados diretamente a partir do sistema gerido pelo agente, ou são enviados diretamente a partir do sistema gerido pelo agente, são enviados diretamente a partir do sistema gerido pelo agente, são enviados diretamente a partir do sistema gerido pelo agente, são enviados direta o agente para o serviço. Para sistemas não monitorizados pelo Gestor de Operações, cada um liga-se de forma segura ao serviço Log Analytics diretamente.

Toda a comunicação entre sistemas conectados e o serviço Log Analytics está encriptada. O protocolo TLS (HTTPS) é utilizado para encriptação.  O processo SDL da Microsoft é seguido para garantir que o Log Analytics está atualizado com os avanços mais recentes em protocolos criptográficos.

Cada tipo de agente recolhe dados para o Log Analytics. O tipo de dados recolhidos depende dos tipos de soluções utilizados. Pode ver um resumo da recolha de dados nas [soluções Add Log Analytics da Galeria Solutions](../../azure-monitor/insights/solutions.md). Além disso, informações de recolha mais detalhadas estão disponíveis para a maioria das soluções. Uma solução é um conjunto de pontos de vista predefinidos, consultas de pesquisa de registo, regras de recolha de dados e lógica de processamento. Apenas os administradores podem usar o Log Analytics para importar uma solução. Depois de importada a solução, é transferida para os servidores de gestão do Gestor de Operações (se utilizados), e depois para quaisquer agentes que tenha escolhido. Depois, os agentes recolhem os dados.

## <a name="2-send-data-from-agents"></a>2. Enviar dados de agentes
Regista todos os tipos de agentes com uma chave de inscrição e estabelece-se uma ligação segura entre o agente e o serviço Log Analytics utilizando a autenticação baseada em certificados e o TLS com a porta 443. O Log Analytics utiliza uma loja secreta para gerar e manter as chaves. As chaves privadas são rodadas a cada 90 dias e são armazenadas em Azure e são geridas pelas operações azure que seguem práticas regulamentares e de conformidade rigorosas.

Com o Gestor de Operações, o grupo de gestão registado com um espaço de trabalho Log Analytics estabelece uma ligação HTTPS segura com um servidor de gestão do Gestor de Operações.

Para os agentes Windows ou Linux que executam máquinas virtuais Azure, uma chave de armazenamento apenas para leitura é usada para ler eventos de diagnóstico em tabelas Azure.  

Com qualquer agente a reportar a um grupo de gestão do Gestor de Operações que esteja integrado com o Log Analytics, se o servidor de gestão não conseguir comunicar com o serviço por qualquer motivo, os dados recolhidos são armazenados localmente numa cache temporária sobre a gestão servidor.   Tentam reenviar os dados a cada 8 minutos durante duas horas.  Para dados que contorna o servidor de gestão e é enviado diretamente para o Log Analytics, o comportamento é consistente com o agente windows.  

Os dados cached do Windows ou do agente de servidor de gestão estão protegidos pela loja de credenciais do sistema operativo. Se o serviço não puder processar os dados após duas horas, os agentes irão fazer fila dos dados. Se a fila ficar cheia, o agente começa a deixar cair os tipos de dados, começando pelos dados de desempenho. O limite de fila do agente é uma chave de registo para que possa modificá-la, se necessário. Os dados recolhidos são comprimidos e enviados para o serviço, contornando as bases de dados do grupo de gestão do Gestor de Operações, pelo que não lhes adiciona qualquer carga. Após o envio dos dados recolhidos, este é removido da cache.

Como descrito acima, os dados do servidor de gestão ou agentes ligados diretamente são enviados por TLS para centros de dados do Microsoft Azure. Opcionalmente, pode utilizar o ExpressRoute para fornecer segurança adicional aos dados. O ExpressRoute é uma forma de ligar diretamente ao Azure a partir da sua rede WAN existente, como uma VPN de comutação de etiquetas multi-protocolos (MPLS), fornecida por um fornecedor de serviços de rede. Para mais informações, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. O serviço Log Analytics recebe e processa dados
O serviço Log Analytics garante que os dados de entrada são de uma fonte fidedigna, validando certificados e a integridade dos dados com a autenticação Azure. Os dados brutos não processados são então armazenados num Azure Event Hub na região, os dados serão eventualmente armazenados em repouso. O tipo de dados armazenados depende dos tipos de soluções que foram importadas e utilizadas para recolher dados. Em seguida, o serviço Log Analytics processa os dados brutos e ingere-os na base de dados.

O período de retenção dos dados recolhidos armazenados na base de dados depende do plano de preços selecionado. Para *o* free tier, os dados recolhidos estão disponíveis por sete dias. Para o nível *Pago,* os dados recolhidos estão disponíveis por 31 dias por padrão, mas podem ser estendidos para 730 dias. Os dados são armazenados encriptados em repouso no armazenamento do Azure, para garantir a confidencialidade dos dados, e os dados são replicados na região local utilizando armazenamento localmente redundante (LRS). As últimas duas semanas de dados também são armazenadas em cache baseado em SSD e esta cache é encriptada.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utilize o Log Analytics para aceder aos dados
Para aceder ao seu espaço de trabalho log Analytics, inscrevo no portal Azure utilizando a conta organizacional ou a conta Microsoft que configura anteriormente. Todo o tráfego entre o portal e o serviço Log Analytics é enviado através de um canal HTTPS seguro. Ao utilizar o portal, é gerado um ID de sessão no cliente utilizador (navegador web) e os dados são armazenados numa cache local até que a sessão seja terminada. Quando terminada, a cache é eliminada. Os cookies do lado do cliente, que não contêm informações pessoalmente identificáveis, não são automaticamente removidos. Os cookies de sessão estão marcados HTTPOnly e estão seguros. Após um período de inatividade pré-determinado, a sessão do portal Azure é terminada.

## <a name="next-steps"></a>Passos seguintes
* Saiba como recolher dados com log Analytics para os seus VMs Azure após o [quickstart Azure VM](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Se procura recolher dados de computadores Físicos ou Virtuais Windows ou Linux no seu ambiente, consulte o [Quickstart para computadores Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) ou [Quickstart para computadores Windows](../../azure-monitor/learn/quick-collect-windows-computer.md)

