---
title: Registar | de segurança de dados do Registo Microsoft Docs
description: Saiba como o Log Analytics protege a sua privacidade e protege os seus dados.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: f8fa0344a6959025856f002e1de4eb8700c32da5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311316"
---
# <a name="log-analytics-data-security"></a>Log Analytics segurança de dados
Este documento destina-se a fornecer informações específicas ao Log Analytics, que é uma característica do Azure Monitor, para complementar a informação no [Azure Trust Center](https://www.microsoft.com/en-us/trust-center?rtc=1).  

Este artigo explica a forma como os dados são recolhidos, processados e protegidos pelo Log Analytics. Pode utilizar agentes para se conectar ao serviço web, utilizar o Gestor de Operações do Centro de Sistema para recolher dados operacionais ou recolher dados dos diagnósticos do Azure para utilização pelo Log Analytics. 

O serviço Log Analytics gere os seus dados baseados na nuvem de forma segura, utilizando os seguintes métodos:

* Segregação de dados
* Retenção de dados
* Segurança física
* Gestão de Incidentes
* Conformidade
* Certificações de normas de segurança

Também pode utilizar funcionalidades de segurança adicionais incorporadas no Azure Monitor e no Log Analytics. Estas funcionalidades requerem mais gestão de administradores. 
* Chaves geridas pelo cliente (segurança)
* Armazenamento Privado Azure
* Rede do Private Link 
* Limites de acesso de suporte Azure definidos por Azure Lockbox

Contacte-nos com quaisquer questões, sugestões ou questões sobre qualquer uma das seguintes informações, incluindo as nossas políticas de segurança nas [opções de suporte da Azure.](https://azure.microsoft.com/support/options/)

## <a name="sending-data-securely-using-tls-12"></a>Envio de dados de forma segura usando TLS 1.2 

Para garantir a segurança dos dados em trânsito para o Log Analytics, encorajamo-lo a configurar o agente para utilizar pelo menos a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da Camada de Tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**, e a indústria está rapidamente a mover-se para abandonar o suporte a estes protocolos mais antigos. 

O Conselho de Normas de Segurança do [PCI](https://www.pcisecuritystandards.org/) fixou um [prazo de 30 de junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar versões mais antigas do TLS/SSL e atualizar para protocolos mais seguros. Uma vez que o Azure deixe cair o suporte ao legado, se os seus agentes não conseguirem comunicar pelo menos o TLS 1.2, não poderá enviar dados para o Log Analytics. 

Não recomendamos que o seu agente utilize apenas o TLS 1.2 a menos que seja absolutamente necessário, pois pode quebrar funcionalidades de segurança de nível da plataforma que lhe permitam detetar e tirar partido de protocolos mais recentes e mais seguros à medida que estes ficam disponíveis, como o TLS 1.3. 

### <a name="platform-specific-guidance"></a>Orientação específica da plataforma

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições linux tendem a contar com [o suporte OpenSSL](https://www.openssl.org) para suporte TLS 1.2.  | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows 8.0 - 10 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinições](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 - 2016 | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidos](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por defeito. | Consulte a página [de definições de registo de segurança da camada de transporte (TLS)](/windows-server/security/tls/tls-registry-settings) para obter mais detalhes sobre como ativar.  |

## <a name="data-segregation"></a>Segregação de dados
Após os seus dados são ingeridos pelo serviço Log Analytics, os dados são mantidos logicamente separados em cada componente durante todo o serviço. Todos os dados são marcados por espaço de trabalho. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. Os seus dados são armazenados numa base de dados dedicada no cluster de armazenamento da região que selecionou.

## <a name="data-retention"></a>Retenção de dados
Os dados de pesquisa de registo indexados são armazenados e mantidos de acordo com o seu plano de preços. Para obter mais informações, consulte [o Registo de Preços.](https://azure.microsoft.com/pricing/details/log-analytics/)

Como parte do seu contrato de [subscrição,](https://azure.microsoft.com/support/legal/subscription-agreement/)a Microsoft irá reter os seus dados de acordo com os termos do acordo.  Quando os dados do cliente são removidos, não são destruídos unidades físicas.  

A tabela que se segue lista algumas das soluções disponíveis e fornece exemplos do tipo de dados que recolhem.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Capacidade e Desempenho |Dados de desempenho e metadados |
| Gestão de Atualizações |Metadados e dados estatais |
| Gestão de Registos |Registos de eventos definidos pelo utilizador, registos de eventos do Windows e/ou registos IIS |
| Monitorização de Alterações |Inventário de software, serviço Windows e metadados linux daemon, e metadados de ficheiros Windows/Linux |
| SQL e Avaliação do Diretório Ativo |Dados do WMI, dados de registo, dados de desempenho e resultados de visão dinâmica do SQL Server |

A tabela a seguir mostra exemplos de tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta |Nome de alerta, Descrição de Alerta, BaseManagedEntityId, Problem ID, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModifiedBy, LastModifiedExceptRepetCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionLastModified, |
| Configuração |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota:** Quando escreve eventos com campos personalizados no registo de eventos do Windows, o Log Analytics recolhe-os. |
| Metadados |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, FísicosProcessadores, Nome de Rede, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromenWich |
| Desempenho |Nome de objeto, contraname, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física
O serviço Log Analytics é gerido pelo pessoal da Microsoft e todas as atividades são registadas e podem ser auditadas. O Log Analytics é operado como um Serviço Azure e cumpre todos os requisitos de Conformidade e Segurança Azure. Pode ver detalhes sobre a segurança física dos ativos da Azure na página 18 da Visão Geral de Segurança do [Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Os direitos de acesso físico a áreas seguras são alterados no prazo de um dia útil para quem já não tem responsabilidade pelo serviço Log Analytics, incluindo transferência e rescisão. Pode ler sobre a infraestrutura física global que utilizamos no [Microsoft Datacenters.](https://azure.microsoft.com/global-infrastructure/)

## <a name="incident-management"></a>Gestão de Incidentes
O Log Analytics tem um processo de gestão de incidentes a que todos os serviços da Microsoft aderem. Resumindo, nós:

* Utilize um modelo de responsabilidade partilhada onde uma parte da responsabilidade de segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerir incidentes de segurança da Azure:
  * Inicie uma investigação após a deteção de um incidente
  * Avalie o impacto e a gravidade de um incidente por um membro da equipa de resposta a incidentes de serviço. Com base em evidências, a avaliação pode ou não resultar em uma nova escalada para a equipa de resposta de segurança.
  * Diagnosticar um incidente por peritos em resposta de segurança para conduzir a investigação técnica ou forense, identificar estratégias de contenção, mitigação e solução. Se a equipa de segurança acredita que os dados do cliente podem ter ficado expostos a um indivíduo ilegal ou não autorizado, a execução paralela do processo de Notificação de Incidentes com o Cliente começa em paralelo.  
  * Estabilizar e recuperar do incidente. A equipa de resposta a incidentes cria um plano de recuperação para mitigar o problema. As medidas de contenção de crise, tais como a quarentena de sistemas com impacto, podem ocorrer imediatamente e em paralelo com o diagnóstico. Podem ser previstas atenuações a longo prazo que ocorram após a ultrapassou o risco imediato.  
  * Feche o incidente e conduza uma autópsia. A equipa de resposta a incidentes cria uma autópsia que descreve os detalhes do incidente, com a intenção de rever políticas, procedimentos e processos para evitar uma recorrência do evento.
* Notificar os clientes de incidentes de segurança:
  * Determinar o âmbito de aplicação dos clientes afetados e fornecer a qualquer pessoa que seja impactada o mais detalhada possível
  * Crie um aviso para fornecer aos clientes informações detalhadas o suficiente para que possam realizar uma investigação sobre o seu fim e cumprir quaisquer compromissos que tenham assumido com os seus utilizadores finais, sem atrasar indevidamente o processo de notificação.
  * Confirme e declare o incidente, se necessário.
  * Notifique os clientes com uma notificação de incidente sem atrasos irrazoáveis e de acordo com qualquer compromisso legal ou contratual. As notificações de incidentes de segurança são entregues a um ou mais administradores de um cliente por qualquer meio que a Microsoft selecione, incluindo via e-mail.
* Realizar prontidão e formação da equipa:
  * O pessoal da Microsoft é obrigado a completar a formação de segurança e sensibilização, o que os ajuda a identificar e reportar suspeitas de problemas de segurança.  
  * Os operadores que trabalham no serviço Microsoft Azure têm obrigações de formação de adição em torno do seu acesso a sistemas sensíveis que hospedam dados dos clientes.
  * Pessoal de resposta à segurança da Microsoft recebe formação especializada para as suas funções

Embora muito rara, a Microsoft notificará cada cliente dentro de um dia se ocorrer perda significativa de quaisquer dados do cliente. 

Para obter mais informações sobre como a Microsoft responde a incidentes de segurança, consulte [a Microsoft Azure Security Response na Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Conformidade
O programa de segurança e governação de software Log Analytics suporta os seus requisitos de negócio e adere às leis e regulamentos descritos no [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e no Microsoft Trust Center [Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Como o Log Analytics estabelece requisitos de segurança, identifica controlos de segurança, gere e monitoriza os riscos também são descritos lá. Anualmente, revemos polícias, normas, procedimentos e diretrizes.

Cada membro da equipa de desenvolvimento recebe formação formal de segurança de aplicações. Internamente, usamos um sistema de controlo de versão para o desenvolvimento de software. Cada projeto de software está protegido pelo sistema de controlo de versão.

A Microsoft tem uma equipa de segurança e conformidade que supervisiona e avalia todos os serviços na Microsoft. Os oficiais de segurança da informação compõem a equipa e não estão associados às equipas de engenharia que desenvolvem o Log Analytics. Os agentes de segurança têm a sua própria cadeia de gestão e realizam avaliações independentes de produtos e serviços para garantir a segurança e o cumprimento.

O conselho de administração da Microsoft é notificado por um relatório anual sobre todos os programas de segurança da informação na Microsoft.

A equipa de desenvolvimento e serviço de software Log Analytics está a trabalhar ativamente com as equipas da Microsoft Legal e Compliance e outros parceiros do setor para adquirir várias certificações.

## <a name="certifications-and-attestations"></a>Certificações e atestados
A Azure Log Analytics cumpre os seguintes requisitos:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Indústria de Cartões de Pagamento (PCI Compliant) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) pelo Conselho de Normas de Segurança do PCI.
* [Controlos da Organização de Serviços (SOC) 1 Tipo 1 e SOC 2 Tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) em conformidade
* [HIPAA e HITECH](/compliance/regulatory/offering-hipaa-hitech) para empresas que têm um Acordo de Associado de Negócios HIPAA
* Critérios de Engenharia Comuns do Windows
* Microsoft Computação confiável
* Como serviço Azure, os componentes que o Log Analytics utiliza aderem aos requisitos de conformidade da Azure. Pode ler mais no [Microsoft Trust Center Compliance](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> Em algumas certificações/atestados, o Log Analytics está listado sob o seu nome anterior de *Insights Operacionais.*
>
>

## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de computação em nuvem
O diagrama que se segue mostra uma arquitetura de segurança na nuvem como o fluxo de informação da sua empresa e como é protegido como se move para o serviço Log Analytics, finalmente visto por si no portal Azure. Mais informações sobre cada passo seguem o diagrama.

![Imagem de recolha e segurança de dados do Log Analytics](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscreva-se no Log Analytics e recolha dados
Para que a sua organização envie dados para o Log Analytics, configura um agente Windows ou Linux em execução em máquinas virtuais Azure, ou em computadores virtuais ou físicos no seu ambiente ou noutro fornecedor de nuvem.  Se utilizar o Gestor de Operações, do grupo de gestão, configura o agente Gestor de Operações. Os utilizadores (que podem ser vocês, outros utilizadores individuais ou um grupo de pessoas) criam um ou mais espaços de trabalho do Log Analytics e registam agentes utilizando uma das seguintes contas:

* [ID organizacional](../../active-directory/fundamentals/sign-up-organization.md)
* [Conta Microsoft - Outlook, Office Live, MSN](https://account.microsoft.com/account)

Um espaço de trabalho Log Analytics é onde os dados são recolhidos, agregados, analisados e apresentados. Um espaço de trabalho é usado principalmente como um meio para os dados de partição, e cada espaço de trabalho é único. Por exemplo, é possível que os seus dados de produção tenham sido geridos com um espaço de trabalho e os seus dados de teste geridos com outro espaço de trabalho. Os espaços de trabalho também ajudam um administrador a controlar o acesso do utilizador aos dados. Cada espaço de trabalho pode ter várias contas de utilizador associadas a ele, e cada conta de utilizador pode aceder a vários espaços de trabalho do Log Analytics. Cria espaços de trabalho baseados na região do datacenter.

Para o Gestor de Operações, o grupo de gestão de Gestores de Operações estabelece uma ligação com o serviço Log Analytics. Em seguida, configura quais os sistemas geridos pelo agente no grupo de gestão que podem recolher e enviar dados para o serviço. Dependendo da solução ativada, os dados destas soluções são enviados diretamente de um servidor de gestão de Gestor de Operações para o serviço Log Analytics, ou devido ao volume de dados recolhidos pelo sistema gerido pelo agente, são enviados diretamente do agente para o serviço. Para sistemas não monitorizados pelo Gestor de Operações, cada um liga-se de forma segura ao serviço Log Analytics diretamente.

Toda a comunicação entre os sistemas conectados e o serviço Log Analytics está encriptada. O protocolo TLS (HTTPS) é utilizado para encriptação.  O processo Microsoft SDL é seguido para garantir que o Log Analytics está atualizado com os mais recentes avanços nos protocolos criptográficos.

Cada tipo de agente recolhe dados para o Log Analytics. O tipo de dados recolhidos depende dos tipos de soluções utilizadas. Pode ver um resumo da recolha de dados nas [soluções Add Log Analytics da Galeria solutions.](../insights/solutions.md) Além disso, informações de recolha mais detalhadas estão disponíveis para a maioria das soluções. Uma solução é um pacote de vistas predefinidas, consultas de pesquisa de registo, regras de recolha de dados e lógica de processamento. Apenas os administradores podem usar o Log Analytics para importar uma solução. Após a importação da solução, é transferida para os servidores de gestão do Gestor de Operações (se utilizado), e depois para quaisquer agentes que tenha escolhido. Depois, os agentes recolhem os dados.

## <a name="2-send-data-from-agents"></a>2. Enviar dados de agentes
Regista todos os tipos de agente com uma chave de inscrição e é estabelecida uma ligação segura entre o agente e o serviço Log Analytics utilizando a autenticação baseada em certificados e TLS com a porta 443. O Log Analytics utiliza uma loja secreta para gerar e manter as chaves. As chaves privadas são giradas a cada 90 dias e são armazenadas em Azure e são geridas pelas operações da Azure que seguem práticas regulamentares e de conformidade rigorosas.

Com o Gestor de Operações, o grupo de gestão registado num espaço de trabalho Log Analytics estabelece uma ligação HTTPS segura com um servidor de gestão de Gestor de Operações.

Para agentes Windows ou Linux que executam em máquinas virtuais Azure, uma chave de armazenamento apenas de leitura é usada para ler eventos de diagnóstico em tabelas Azure.  

Com qualquer agente a reportar a um grupo de gestão de Gestores de Operações que esteja integrado com o Log Analytics, se o servidor de gestão não conseguir comunicar com o serviço por qualquer motivo, os dados recolhidos são armazenados localmente numa cache temporária no servidor de gestão.   Tentam reencamê-lo a cada oito minutos durante duas horas.  Para dados que contornam o servidor de gestão e são enviados diretamente para o Log Analytics, o comportamento é consistente com o agente Windows.  

Os dados em cache do Windows ou do agente de gestão estão protegidos pela loja de credenciais do sistema operativo. Se o serviço não conseguir processar os dados após duas horas, os agentes farão fila dos dados. Se a fila ficar cheia, o agente começa a deixar cair tipos de dados, começando pelos dados de desempenho. O limite de fila do agente é uma chave de registo para que possa modificá-la, se necessário. Os dados recolhidos são comprimidos e enviados para o serviço, contornando as bases de dados do grupo de gestão de operações, pelo que não lhes adiciona qualquer carga. Após o envio dos dados recolhidos, é removido da cache.

Como descrito acima, os dados do servidor de gestão ou agentes ligados diretamente são enviados sobre TLS para centros de dados microsoft Azure. Opcionalmente, pode utilizar o ExpressRoute para fornecer segurança adicional aos dados. O ExpressRoute é uma forma de ligar diretamente ao Azure a partir da sua rede WAN existente, como uma VPN de comutação de etiquetas multi-protocolo (MPLS), fornecida por um prestador de serviços de rede. Para mais informações, consulte [o ExpressRoute.](https://azure.microsoft.com/services/expressroute/)

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. O serviço Log Analytics recebe e processa dados
O serviço Log Analytics garante que os dados recebidos são de uma fonte fidedigna, validando certificados e a integridade dos dados com a autenticação Azure. Os dados brutos não processados são então armazenados num Hub de Eventos Azure na região, os dados serão eventualmente armazenados em repouso. O tipo de dados armazenados depende dos tipos de soluções que foram importadas e utilizadas para recolher dados. Em seguida, o serviço Log Analytics processa os dados brutos e ingere-os na base de dados.

O período de retenção dos dados recolhidos armazenados na base de dados depende do plano de preços selecionado. Para o nível *Livre,* os dados recolhidos estão disponíveis durante sete dias. Para o nível *Pago,* os dados recolhidos estão disponíveis por 31 dias por defeito, mas podem ser alargados para 730 dias. Os dados são armazenados encriptados em repouso no armazenamento do Azure, para garantir a confidencialidade dos dados, e os dados são replicados na região local usando armazenamento localmente redundante (LRS). As duas últimas semanas de dados também são armazenadas em cache baseado em SSD e esta cache é encriptada.

Os dados no armazenamento da base de dados não podem ser alterados uma vez ingeridos, mas podem ser eliminados através do caminho [ *da expurgação* da API](personal-data-mgmt.md#delete). Embora os dados não possam ser alterados, algumas certificações exigem que os dados sejam mantidos imutáveis e não possam ser alterados ou eliminados no armazenamento. A imutabilidade de dados pode ser alcançada usando a exportação de [dados](logs-data-export.md) para uma conta de armazenamento configurada como [armazenamento imutável](../../storage/blobs/storage-blob-immutability-policies-manage.md).

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utilize o Log Analytics para aceder aos dados
Para aceder ao seu espaço de trabalho Log Analytics, inscreva-se no portal Azure utilizando a conta organizacional ou a conta microsoft que criou anteriormente. Todo o tráfego entre o portal e o serviço Log Analytics é enviado através de um canal HTTPS seguro. Ao utilizar o portal, é gerado um ID de sessão no cliente utilizador (navegador web) e os dados são armazenados numa cache local até que a sessão seja terminada. Quando terminada, a cache é apagada. Os cookies do lado do cliente, que não contêm informações pessoalmente identificáveis, não são removidos automaticamente. Os cookies de sessão estão marcados HTTPOnly e estão seguros. Após um período de inatividade pré-determinado, a sessão do portal Azure é encerrada.


## <a name="additional-security-features"></a>Recursos adicionais de segurança
Pode utilizar estas funcionalidades de segurança adicionais para proteger ainda mais o ambiente Azure Monitor/Log Analytics. Estas funcionalidades requerem mais gestão de administradores. 
- [Teclas (segurança) geridas pelo cliente](../logs/customer-managed-keys.md) - Pode utilizar as chaves geridas pelo cliente para encriptar os dados enviados para os seus espaços de trabalho do Log Analytics. Requer o uso do Cofre da Chave Azure. 
- [Armazenamento privado / gerido pelo cliente](./private-storage.md) - Gerencie a sua conta de armazenamento encriptada pessoalmente e diga ao Log Analytics para a utilizar para armazenar dados de monitorização 
- [Private Link networking](./private-link-security.md) - Azure Private Link permite-lhe ligar de forma segura os serviços Azure PaaS (incluindo O Azure Monitor) à sua rede virtual utilizando pontos finais privados. 
- [Lockbox do cliente Azure](../../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-preview) - O Bloqueio do Cliente para o Microsoft Azure fornece uma interface para os clientes reverem e aprovarem ou rejeitarem os pedidos de acesso aos dados dos clientes. É utilizado nos casos em que um engenheiro da Microsoft precisa de aceder aos dados do cliente durante um pedido de suporte.


## <a name="next-steps"></a>Passos seguintes
* Saiba como recolher dados com o Log Analytics para os seus VMs Azure seguindo o [quickstart Azure VM](../vm/quick-collect-azurevm.md).  

*  Se procura recolher dados de computadores Físicos ou virtuais do Windows ou Linux no seu ambiente, consulte o [Quickstart para computadores Linux](../vm/quick-collect-linux-computer.md) ou [Quickstart para computadores Windows](../vm/quick-collect-windows-computer.md)
