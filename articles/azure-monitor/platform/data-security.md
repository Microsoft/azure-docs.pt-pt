---
title: Segurança de dados de análise de registo | Documentos da Microsoft
description: Saiba mais sobre como o Log Analytics protege a sua privacidade e protege os seus dados.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: magoedte
ms.openlocfilehash: dd4efcd2f1d4cbf497ad1fde6936088513cb5fd0
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312856"
---
# <a name="log-analytics-data-security"></a>Segurança de dados de análise de registo
Este documento destina-se para fornecer informações específicas para o Log Analytics, que é um recurso do Azure Monitor, para complementar as informações sobre [Centro de fidedignidade do Azure](../../security/security-microsoft-trust-center.md).  

Este artigo explica como os dados são recolhidos, processados e protegidos pelo Log Analytics. Pode utilizar agentes para ligar ao serviço web, utilize o System Center Operations Manager para recolher dados operacionais ou obter dados de diagnóstico do Azure para utilização pelo Log Analytics. 

O serviço Log Analytics gere os seus dados com base na cloud em segurança, utilize os seguintes métodos:

* segregação de dados
* Retenção de dados
* Segurança física
* gestão de incidentes
* Conformidade
* certificações de normas de segurança

Contacte-nos com quaisquer dúvidas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo as nossas políticas de segurança em [opções de suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Enviar dados de forma segura através de TLS 1.2 

Para garantir a segurança dos dados em trânsito para o Log Analytics, recomendamos que configure o agente para utilizar, pelo menos, Transport Layer Security (TLS) 1.2. As versões mais antigas do TLS/Secure Sockets Layer (SSL) foram encontradas vulneráveis e enquanto trabalham ainda atualmente para permitir a compatibilidade com versões anteriores, estão **não recomendada**, e o setor que está a mudar rapidamente para abandonar o suporte para esses protocolos mais antigos. 

O [PCI Security Standards Council](https://www.pcisecuritystandards.org/) definiu um [dentro do prazo de 30 de Junho de 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) para desativar as versões mais antigas do TLS/SSL e a atualização para proteger mais os protocolos. Assim que o Azure ignora o suporte legacy, se os agentes não é possível comunicar através de, pelo menos, TLS 1.2 não seria capaz de enviar dados para o Log Analytics. 

Não é recomendável definindo explicitamente seu agente para utilizar apenas o TLS 1.2, a menos que absolutamente necessário, como ele pode quebrar a recursos de segurança de nível de plataforma que permitem-lhe detetar e tirar partido das mais recentes protocolos mais seguros à medida que ficam disponíveis, como automaticamente como TLS 1.3. 

### <a name="platform-specific-guidance"></a>Orientações específicas de plataforma

|Plataforma/linguagem | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | Distribuições do Linux tendem a depender [OpenSSL](https://www.openssl.org) para suporte de TLS 1.2.  | Verifique os [registo de alterações de OpenSSL](https://www.openssl.org/news/changelog.html) para confirmar a sua versão do OpenSSL que é suportado.|
| Windows 8.0 10 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar o [predefinições](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2016 de 2012 | Suportado e ativado por predefinição. | Para confirmar que ainda está a utilizar o [predefinições](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) |
| Windows 7 SP1 e Windows Server 2008 R2 SP1 | Suportado, mas não ativado por predefinição. | Consulte a [definições de registo de Transport Layer Security (TLS)](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) página para obter detalhes sobre como ativar.  |

## <a name="data-segregation"></a>segregação de dados
Depois dos dados são ingeridos pelo serviço Log Analytics, os dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por área de trabalho. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. Os dados são armazenados numa base de dados dedicado do cluster de armazenamento na região que selecionou.

## <a name="data-retention"></a>Retenção de dados
Dados de pesquisa indexada do registo são armazenados e mantidos, de acordo com o plano de preços. Para obter mais informações, consulte [preços do Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

Como parte da sua [contrato de subscrição](https://azure.microsoft.com/support/legal/subscription-agreement/), a Microsoft retém os dados nos termos do contrato.  Quando os dados do cliente for removidos, não existem unidades físicas são destruídas.  

A tabela seguinte lista algumas das soluções disponíveis e fornece exemplos dos tipos de dados que recolher.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Capacidade e desempenho |Dados de desempenho e metadados |
| Gestão de Atualizações |Dados de metadados e o Estado |
| Gestão de Registos |Definido pelo utilizador registos de eventos, registos de eventos do Windows e/ou registos do IIS |
| Monitorização de Alterações |Inventário de software, serviço do Windows e Linux daemon metadados e metadados de ficheiros do Windows/Linux |
| SQL e de avaliação do Active Directory |Dados do WMI, dados de registo, dados de desempenho e gerenciamento dinâmico do SQL Server ver resultados |

A tabela seguinte mostra exemplos dos tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta |Alerta de nome, descrição do alerta, Timemodified, ID do problema, IsMonitorAlert, RuleId, ResolutionState, prioridade, gravidade, categoria, proprietário, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, RepeatCount TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, |
| Configuração |CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota:** Quando escreve eventos com campos personalizados no registo de eventos do Windows, o Log Analytics recolhe-los. |
| Metadados |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Segurança física
O serviço Log Analytics é gerido pela equipa da Microsoft e todas as atividades são registadas e podem ser auditadas. O log Analytics é operado como serviço do Azure e cumpre todos os requisitos de segurança e de conformidade do Azure. Pode ver os detalhes sobre a segurança física dos recursos do Azure na página 18 do [descrição geral de segurança do Microsoft Azure](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Direitos de acesso físico a áreas de proteger são alterados num dia útil para qualquer pessoa que já não tem a responsabilidade para o serviço do Log Analytics, incluindo a transferência e cessação. Pode ler sobre a infraestrutura física global que usamos em [Datacenters da Microsoft](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>gestão de incidentes
Log Analytics tem um processo de gestão de incidentes que cumprem todos os serviços Microsoft. Para resumir, podemos:

* Utilizar um modelo de responsabilidade partilhada em que uma parte da responsabilidade de segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerir incidentes de segurança do Azure:
  * Iniciar uma investigação após a deteção de um incidente
  * Avalie o impacto e a gravidade de um incidente por um membro da equipa de resposta a incidentes na chamada. Com base na evidência, a avaliação pode ou não pode resultar em mais de escalonamento para a equipa de resposta de segurança.
  * Diagnosticar um incidente por especialistas de resposta de segurança para conduzir a investigação forense ou técnica, identifique as estratégias de contenção, atenuação e solução. Se a equipe de segurança acredita que os dados dos clientes podem ficar expostos a uma pessoa não autorizada ou ilícita, começa a execução paralela do processo de notificação de incidente do cliente em paralelo.  
  * Estabilizar e recuperar do incidente. A equipe de resposta a incidentes cria um plano de recuperação para mitigar o problema. Passos de contenção de crise, tais como os colocar em quarentena sistemas afetados podem ocorrer em paralelo com o diagnóstico e imediatamente. Podem ser planeadas atenuações de termo mais tempo do que ocorrem após ter passado o risco de imediato.  
  * Fechar o incidente e realizar um post-mortem. A equipe de resposta a incidentes cria um post-mortem que descreve os detalhes do incidente, com a intenção revisar diretivas, procedimentos e processos para impedir que uma periodicidade do evento.
* Notifique os clientes de incidentes de segurança:
  * Determinar o escopo dos clientes afetados e para fornecer qualquer pessoa que é afetado como detalhadas um aviso de possível
  * Crie um aviso para fornecer aos clientes detalhadas informações suficientes para que eles possam realizar uma investigação no respetivo fim e cumprir qualquer compromissos que tenha introduzido aos usuários finais enquanto não preocupavam indevidamente atrasando o processo de notificação.
  * Confirme e declarar o incidente, conforme necessário.
  * Notifique os clientes com uma notificação de incidente sem atraso razoável e em conformidade com qualquer compromisso legal ou contratual. Notificações de incidentes de segurança são entregues a um ou mais dos administradores de um cliente por qualquer meio que seleciona da Microsoft, incluindo através de e-mail.
* Preparação da equipe de conduta e treinamento:
  * As equipas da Microsoft são necessárias para concluir a segurança e treinamento de reconhecimento, que ajuda a identificar e comunicar problemas de segurança suspeitos.  
  * Operadores de trabalhar no serviço do Microsoft Azure tem obrigações de treinamento de adição que envolvem o acesso a sistemas confidenciais que aloja os dados de cliente.
  * Equipe de resposta de segurança do Microsoft recebe treinamento especializado para as respetivas funções

Se ocorrer a perda de todos os dados dos clientes, iremos notificá-cada customer dentro de um dia. No entanto, perda de dados do cliente nunca ocorreu com o serviço. 

Para obter mais informações sobre como a Microsoft responde a incidentes de segurança, consulte [resposta de segurança do Microsoft Azure na Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf).

## <a name="compliance"></a>Conformidade
Programa de segurança e governação de informações do Log Analytics software serviço e de desenvolvimento da Equipe oferece suporte a seus requisitos de negócio e em conformidade com as leis e regulamentos, conforme descrito em [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e [ Conformidade com o Centro de fidedignidade do Microsoft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Como Log Analytics estabelece requisitos de segurança, identifica os controlos de segurança, gerencia e monitora os riscos também são descritas aqui. Anualmente, vamos rever políticas, normas, procedimentos e diretrizes.

Cada membro da equipe de desenvolvimento recebe treinamento de segurança de aplicativo formal. Internamente, usamos um sistema de controle de versão para o desenvolvimento de software. Cada projeto de software está protegido pelo sistema de controle de versão.

A Microsoft tem uma equipa de segurança e conformidade que supervisiona e avalia todos os serviços Microsoft. Responsáveis pela segurança de informações compõem a equipe e não estão associadas com as equipas de engenharia que desenvolve o Log Analytics. Os responsáveis pela segurança tem sua própria cadeia de gestão e realizar avaliações independentes dos produtos e serviços para garantir a segurança e conformidade.

Conselho administrativo da Microsoft é notificado através de um relatório anual sobre todos os programas de segurança de informações da Microsoft.

O desenvolvimento de software do Log Analytics e a equipa do serviço estão a trabalhar ativamente com as equipes da Microsoft Legal e conformidade e outros parceiros do setor para adquirir várias certificações.

## <a name="certifications-and-attestations"></a>Certificações e atestados
O Azure Log Analytics cumpre os seguintes requisitos:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Padrão de segurança de dados de setor (em conformidade com PCI) de cartão do pagamento (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) pela PCI Security Standards Council.
* [Tipo de controles e organização do serviço (SOC) 1 1 e SOC 2 tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) em conformidade
* [A HIPAA e da HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) para as empresas com um contrato de associar do HIPAA Business
* Critérios comuns de engenharia do Windows
* Informática Fidedigna da Microsoft
* Como um serviço do Azure, os componentes que utiliza o Log Analytics cumprem os requisitos de conformidade do Azure. Pode ler mais em [conformidade da Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> Em alguns certificações/atestados, do Log Analytics está listado em seu nome antigo do *das informações operacionais*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança de informática na cloud
O diagrama seguinte mostra uma arquitetura de segurança na cloud como o fluxo de informações da sua empresa e como são protegido conforme é movido para o serviço do Log Analytics, em última análise, visto por si no portal do Azure. Mais informações sobre cada passo segue o diagrama.

![Imagem de recolha de dados do Log Analytics e segurança](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscreva-se para o Log Analytics e recolher dados
Para a sua organização enviar dados para o Log Analytics, configurar um agente do Windows ou Linux em execução em máquinas virtuais do Azure ou em computadores físicos ou virtuais no seu ambiente ou de outro fornecedor de cloud.  Se utilizar o Operations Manager, do grupo de gestão é configurar o agente do Operations Manager. Os utilizadores (que podem ser, outros utilizadores individuais ou um grupo de pessoas) criar uma ou mais áreas de trabalho do Log Analytics e registem agentes com uma das seguintes contas:

* [ID de organização](../../active-directory/fundamentals/sign-up-organization.md)
* [Conta Microsoft - Outlook, Office Live, MSN](https://account.microsoft.com/account)

Uma área de trabalho do Log Analytics é onde dados são recolhidos, agregados, analisados e apresentados. Uma área de trabalho é usada principalmente como um meio de dados de partição, e cada área de trabalho é exclusiva. Por exemplo, pode querer ter seus dados de produção geridos com uma área de trabalho e os seus dados de teste geridos com outra área de trabalho. Áreas de trabalho também ajudam a um administrador controlar utilizador o acesso aos dados. Cada área de trabalho pode ter várias contas de utilizador associadas e cada conta de utilizador pode aceder a várias áreas de trabalho do Log Analytics. Criar áreas de trabalho com base na região do datacenter.

Para o Operations Manager, o grupo de gestão do Operations Manager estabelece uma conexão com o serviço Log Analytics. Em seguida, configure os sistemas geridos por agente no grupo de gestão têm permissão para recolher e enviar dados para o serviço. Consoante a solução que tiver ativado, os dados a partir dessas soluções são seja enviado diretamente a partir de um servidor de gestão do Operations Manager para o serviço Log Analytics ou devido ao volume de dados recolhidos pelo sistema gerido por agente, são enviadas diretamente a partir de o agente para o serviço. Para sistemas não monitorizados pelo Operations Manager, os ligam em segurança para o serviço Log Analytics diretamente.

Todas as comunicações entre sistemas conectados e o serviço Log Analytics são encriptadas. O protocolo TLS (HTTPS) é utilizado para encriptação.  O processo Microsoft SDL é seguido para garantir que o Log Analytics é atualizado com os mais recentes avanços em protocolos de criptografia.

Cada tipo de agente recolhe dados para o Log Analytics. O tipo de dados que são recolhidos é dependerá dos tipos de soluções utilizadas. Pode ver um resumo de recolha de dados no [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md). Além disso, as informações mais detalhadas de recolha estão disponíveis para a maioria das soluções. Uma solução é um pacote de vistas predefinidas, consultas de pesquisa de registo, as regras de recolha de dados e lógica de processamento. Apenas os administradores podem utilizar o Log Analytics para importar uma solução. Após a importação a solução, é movido para os servidores de gestão do Operations Manager (se utilizado) e, em seguida, para quaisquer agentes que escolheu. Depois disso, os agentes de recolher os dados.

## <a name="2-send-data-from-agents"></a>2. Enviar dados a partir de agentes
Registar todos os tipos de agente com uma chave de registo e uma ligação segura é estabelecida entre o agente e o serviço do Log Analytics a utilizar autenticação baseada em certificados e o SSL com a porta 443. O log Analytics utiliza um arquivo de segredos para gerar e manter as chaves. As chaves privadas são revezadas de todos os 90 dias e são armazenadas no Azure e são geridas pelas operações do Azure que sigam práticas rigorosas de regulamentação e conformidade.

Com o Operations Manager, o grupo de gestão registado com uma área de trabalho do Log Analytics estabelece uma ligação HTTPS segura com um servidor de gestão do Operations Manager.

Para agentes do Windows ou Linux em execução em máquinas virtuais do Azure, uma chave de armazenamento só de leitura é utilizada para ler eventos de diagnóstico em tabelas do Azure.  

Com qualquer agente que reporta a um grupo de gestão do Operations Manager que está integrado com o Log Analytics, se o servidor de gestão não consegue comunicar com o serviço por qualquer motivo, os dados recolhidos é armazenado localmente numa cache temporária do gerenciamento servidor.   Tente reenviar os dados a cada oito minutos durante duas horas.  Para dados que ignora o servidor de gestão e são enviados diretamente para o Log Analytics, o comportamento é consistente com o agente do Windows.  

O Windows ou dados de agente em cache do servidor de gestão estão protegidos pelo arquivo de credenciais do sistema operacional. Se o serviço não é possível processar os dados depois de duas horas, os agentes serão colocados em fila os dados. Se a fila ficar cheio, o agente inicia a remover tipos de dados, começando com dados de desempenho. O limite de fila do agente é uma chave de registo para que pode modificá-la, se necessário. Os dados recolhidos são comprimidos e enviados para o serviço, ignorando os Operations Manager management agrupar bases de dados, pelo que não adiciona qualquer carga a eles. Depois dos dados recolhidos são enviados, esta é removida da cache.

Conforme descrito acima, são enviados dados do servidor de gestão ou agentes direto ligado através de SSL para os datacenters do Microsoft Azure. Opcionalmente, pode utilizar o ExpressRoute para fornecer segurança adicional para os dados. O ExpressRoute é uma forma de ligar diretamente ao Azure da sua rede WAN existente, como um protocolo múltiplo multiprotocolo (MPLS) VPN, fornecida por um fornecedor de serviço de rede. Para obter mais informações, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. O serviço Log Analytics recebe e processa dados
O serviço Log Analytics garante que os dados recebidos de uma origem fidedigna ao validar os certificados e a integridade dos dados com a autenticação do Azure. Os dados não processados não processados, em seguida, são armazenados num Hub de eventos do Azure na região, eventualmente, serão armazenados os dados em repouso. O tipo de dados armazenados depende dos tipos de soluções que foram importados e utilizadas para recolher dados. Em seguida, o Log Analytics service processos os dados não processados e ingere-lo no banco de dados.

O período de retenção de dados recolhidos, armazenados na base de dados depende do plano de preços selecionado. Para o *gratuito* camada, os dados recolhidos estão disponíveis durante sete dias. Para o *pago* camada, os dados recolhidos está disponível para 31 dias por predefinição, mas pode ser estendida e 730 dias. Os dados são armazenados encriptados em inatividade no armazenamento do Azure, para garantir a confidencialidade de dados, e os dados são replicados dentro da região local, utilizar o armazenamento localmente redundante (LRS). As duas últimas semanas de dados também são armazenadas na cache baseada em SSD e esta cache é encriptado.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utilizar o Log Analytics para acessar os dados
Para acessar sua área de trabalho do Log Analytics, iniciar sessão no portal do Azure com a conta organizacional ou a conta Microsoft que configurou anteriormente. Todo o tráfego entre o portal e serviço do Log Analytics é enviado através de um canal seguro de HTTPS. Quando utilizar o portal, um ID de sessão é gerado no cliente de utilizador (navegador da web) e os dados são armazenados num cache local até que a sessão é terminada. Quando terminado, a cache é eliminada. Cookies de lado do cliente, que não contêm informações de identificação pessoal, não são automaticamente removidos. Cookies de sessão são marcados HTTPOnly e estão protegidos. Após um período de inatividade pré-determinado, foi terminada a sessão do portal do Azure.

## <a name="next-steps"></a>Passos Seguintes
* Saiba como recolher dados com o Log Analytics para o seguinte de VMs do Azure a [início rápido de VM do Azure](../../azure-monitor/learn/quick-collect-azurevm.md).  

*  Se estiver à procura para recolher dados de computadores físicos ou virtuais Windows ou Linux no seu ambiente, consulte a [início rápido para computadores Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) ou [computadores de início rápido para Windows](../../azure-monitor/learn/quick-collect-windows-computer.md)

