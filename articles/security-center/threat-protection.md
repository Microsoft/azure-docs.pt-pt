---
title: Proteção contra ameaças no Centro de Segurança do Azure
description: Este tópico descreve os recursos protegidos pelas funcionalidades de proteção contra ameaças do Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 2e563cd0f9a8a25e57312494f1313f895c3b4628
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267159"
---
# <a name="threat-protection-in-azure-security-center"></a>Proteção contra ameaças no Centro de Segurança do Azure

Quando o Centro de Segurança deteta uma ameaça em qualquer área do seu ambiente, gera um alerta. Estes alertas descrevem detalhes dos recursos afetados, sugeriram medidas de reparação e, em alguns casos, uma opção para desencadear uma aplicação lógica em resposta.

A proteção contra ameaças do Azure Security Center fornece defesas abrangentes para o seu ambiente:

* **Proteção contra ameaças para recursos de computação Azure**: máquinas Windows, máquinas Linux, Serviço de Aplicações Azure e contentores Azure

* **Proteção contra ameaças para recursos de dados Azure**: SQL Database e SQL Data Warehouse, Azure Storage e Azure Cosmos DB

* **Proteção contra ameaças para camadas de serviço Azure**: Camada de rede Azure, camada de gestão Azure (Gestor de Recursos Azure) (Preview) e Cofre de Chaves Azure (Pré-visualização)

Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center a partir de um produto de segurança diferente, você pode exportá-lo. Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md). 

> [!NOTE]
> Alertas de diferentes fontes podem levar diferentes quantidades de tempo a aparecer. Por exemplo, alertas que requerem a análise do tráfego da rede podem demorar mais tempo a aparecer do que alertas relacionados com processos suspeitos em funcionamento em máquinas virtuais.

> [!TIP]
> Para ativar as capacidades de proteção de ameaças do Security Center, deve aplicar o nível de preços padrão à subscrição que contém as cargas de trabalho aplicáveis.
>
> Pode permitir a proteção contra ameaças das **contas de Armazenamento Azure** ao nível da subscrição ou ao nível de recursos.
> Pode ativar a proteção contra ameaças para **servidores SQL base de dados SQL Azure,** quer ao nível da subscrição quer ao nível dos recursos.
> Pode ativar a proteção contra ameaças para **a Base de Dados Azure para MariaDB/ MySQL/ PostgreSQL apenas** ao nível dos recursos.



## <a name="threat-protection-for-windows-machines"></a>Proteção contra ameaças para máquinas Windows<a name="windows-machines"></a>

O Azure Security Center integra-se com os serviços Azure para monitorizar e proteger as suas máquinas baseadas no Windows. O Centro de Segurança apresenta as sugestões de alerta e remediação de todos estes serviços num formato fácil de usar.

* **Microsoft Defender Proteção Avançada de Ameaças (ATP)** <a name="windows-atp"></a> - O Security Center alarga as suas plataformas de proteção da carga de trabalho em nuvem, integrando-se com o Microsoft Defender Advanced Threat Protection (ATP). Em conjunto, fornecem capacidades abrangentes de deteção e resposta de pontos finais (EDR).

    > [!IMPORTANT]
    > O sensor ATP do Microsoft Defender está automaticamente ativado nos servidores do Windows que utilizam o Security Center.

    Quando o Microsoft Defender ATP deteta uma ameaça, desencadeia um alerta. O alerta é mostrado no painel do Centro de Segurança. A partir do painel de instrumentos, podes orientar-te para a consola ATP do Microsoft Defender e realizar uma investigação detalhada para descobrir o alcance do ataque. Para obter mais informações sobre o Microsoft Defender ATP, consulte [os servidores onboard para o serviço ATP Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Deteção de ataque sem ficheiros** <a name="windows-fileless"></a> - Os ataques sem ficheiros dirigidos aos seus pontos finais são comuns. Para evitar a deteção, os ataques sem ficheiros injetam cargas maliciosas na memória. As cargas do intruso persistem na memória de processos comprometidos e realizam uma vasta gama de atividades maliciosas.

    Com a deteção de ataques sem ficheiros, as técnicas forenses de memória automatizada identificam kits de ferramentas de ataque sem ficheiros, técnicas e comportamentos. Esta solução analisa periodicamente a sua máquina em tempo de execução e extrai insights diretamente da memória de processos críticos de segurança.

    Encontra provas de exploração, injeção de código e execução de cargas maliciosas. A deteção de ataques sem ficheiros gera alertas de segurança detalhados para acelerar a triagem de alerta, a correlação e o tempo de resposta a jusante. Esta abordagem complementa as soluções EDR baseadas em eventos, proporcionando uma maior cobertura de deteção.

    Para obter mais detalhes sobre os alertas de deteção de ataques sem ficheiro, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-windows).

> [!TIP]
> Pode simular alertas windows descarregando [o Playbook do Azure Security Center: Security Alerts](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Proteção contra ameaças para máquinas Linux<a name="linux-machines"></a>

O Security Center recolhe registos de auditoria de máquinas Linux utilizando **auditados,** um dos quadros de auditoria linux mais comuns. vidas auditadas no núcleo principal. 

* **Linux auditou alertas e integração** <a name="linux-auditd"></a> de agente do Log Analytics - O sistema auditado é constituído por um subsistema de nível kernel, responsável pelas chamadas do sistema de monitorização. Filtra-os por um conjunto de regras especificado e escreve mensagens para eles numa tomada. O Security Center integra funcionalidades a partir do pacote auditado dentro do agente Log Analytics. Esta integração permite a recolha de eventos auditados em todas as distribuições apoiadas do Linux, sem quaisquer requisitos.

    os registos auditados são recolhidos, enriquecidos e agregados em eventos utilizando o agente Log Analytics para o agente Linux. O Security Center adiciona continuamente novas análises que usam sinais Linux para detetar comportamentos maliciosos em máquinas Linux em nuvem e no local. Semelhantes às capacidades do Windows, estas análises estendem-se por processos suspeitos, tentativas duvidosas de iniciar súm, carregamento de módulos de kernel e outras atividades. Estas atividades podem indicar que uma máquina está a ser atacada ou foi violada.  

    Para obter uma lista dos alertas linux, consulte a [tabela de alertas de referência.](alerts-reference.md#alerts-linux)

> [!TIP]
> Pode simular alertas Linux descarregando [o Playbook do Azure Security Center: Linux Detections](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Proteção contra ameaças para o Serviço de Aplicações Azure<a name="app-services"></a>

> [!NOTE]
> Este serviço não está atualmente disponível no governo de Azure e nas regiões de nuvem soberana.

O Security Center utiliza a escala da nuvem para identificar ataques direcionados a aplicações que passam por cima do Serviço de Aplicações. Os atacantes sondam aplicações web para encontrar e explorar fraquezas. Antes de serem encaminhados para ambientes específicos, os pedidos para aplicações em execução em Azure passam por vários gateways, onde são inspecionados e registados. Estes dados são então usados para identificar explorações e atacantes, e para aprender novos padrões que serão usados mais tarde.

Ao usar a visibilidade que o Azure tem como fornecedor de nuvem, o Security Center analisa os registos internos do Serviço de Aplicações para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui a digitalização generalizada e os ataques distribuídos. Este tipo de ataque normalmente vem de um pequeno subconjunto de IPs, e mostra padrões de rastejar para pontos finais semelhantes em vários anfitriões. Os ataques estão à procura de uma página ou plugin vulneráveis, e não podem ser identificados do ponto de vista de um único hospedeiro.

Se estiver a executar um plano de Serviço de Aplicações baseado no Windows, o Security Center também tem acesso às caixas de areia e VMs subjacentes. Juntamente com os dados de registo acima mencionados, a infraestrutura pode contar a história, desde um novo ataque que circula na natureza até compromissos nas máquinas de clientes. Portanto, mesmo que o Security Center seja implementado depois de uma aplicação web ter sido explorada, pode ser capaz de detetar ataques em curso.

Para obter uma lista dos alertas do Serviço de Aplicações Azure, consulte a [tabela de alertas de referência.](alerts-reference.md#alerts-azureappserv)

Para obter mais informações sobre os planos do Serviço de Aplicações, consulte [os planos do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)





## <a name="threat-protection-for-azure-containers"></a>Proteção contra ameaças para contentores Azure<a name="azure-containers"></a>

> [!NOTE]
> Este serviço não está atualmente disponível no governo de Azure e nas regiões de nuvem soberana.

O Security Center fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

O Centro de Segurança fornece proteção contra ameaças a diferentes níveis: 

* **Nível de anfitrião** - O agente do Security Center (disponível no nível Standard, ver [preços](security-center-pricing.md) para detalhes) monitoriza o Linux para atividades suspeitas. O agente aciona alertas para atividades suspeitas originárias do nó ou de um contentor que nele corre. Exemplos de tais atividades incluem deteção de conchas web e conexão com endereços IP suspeitos conhecidos.

    Para uma visão mais profunda da segurança do seu ambiente contentorizado, o agente monitoriza análises específicas do contentor. Irá desencadear alertas para eventos como a criação de contentores privilegiados, acesso suspeito aos servidores da API e servidores Secure Shell (SSH) que estão dentro de um contentor do Docker.

    >[!IMPORTANT]
    > Se optar por não instalar os agentes nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.

    Para obter uma lista dos alertas de nível de anfitrião, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-containerhost).


* Ao **nível do cluster AKS,** a proteção contra ameaças baseia-se na análise dos registos de auditoria da Kubernetes. Para ativar esta monitorização **sem agente,** adicione a opção Kubernetes à sua subscrição a partir da página **de definições & de preços** (ver [preços).](security-center-pricing.md) Para gerar alertas a este nível, o Security Center monitoriza os seus serviços geridos pela AKS utilizando os registos recuperados pela AKS. Exemplos de eventos a este nível incluem dashboards kubernetes expostos, criação de papéis privilegiados elevados, e a criação de suportes sensíveis.

    >[!NOTE]
    > O Security Center gera alertas de segurança para as ações e implementações do Serviço Azure Kubernetes que ocorrem após a opção Kubernetes estar ativada nas definições de subscrição. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-akscluster).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o cenário de ameaças. Acrescentam alertas e vulnerabilidades específicos aos contentores à medida que são descobertos.

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Proteção contra ameaças para base de dados SQL e Armazém de Dados SQL<a name="data-sql"></a>

A Advanced Threat Protection for Azure SQL Database deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

Você verá alertas quando há atividades de base de dados suspeitas, potenciais vulnerabilidades, ou ataques de injeção DE SQL, e padrões anómalos de acesso e consulta de bases de dados.

A Advanced Threat Protection for Azure SQL Database and SQL faz parte do pacote unificado de Segurança avançada de [Dados (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para capacidades avançadas de segurança SQL, abrangendo bases de dados Azure SQL, Azure SQL Database geridos instâncias, bases de dados Azure SQL Data Warehouse e servidores SQL em Azure Virtual Machines.

Para obter mais informações, consulte:

* [Como permitir a Proteção Avançada de Ameaças para Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como permitir a proteção avançada de ameaças para servidores SQL em Máquinas Virtuais Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção de ameaças para a Base de Dados SQL e o SqL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)



## <a name="threat-protection-for-azure-storage"></a>Proteção contra ameaças para armazenamento Azure<a name="azure-storage"></a>

A proteção avançada de ameaças para o Azure Storage deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Esta camada de proteção permite-lhe lidar com ameaças sem exigir que seja um perito em segurança e ajuda-o a gerir os seus sistemas de monitorização de segurança. 

Os alertas de segurança são desencadeados quando há atividades suspeitas na sua conta de armazenamento ou é detetado um comportamento anómalo. Atividades suspeitas podem incluir o upload de uma bolha suspeita de conter malware. Alertas de comportamento anómalo incluem alterações no padrão de acesso a uma conta de armazenamento.

Os alertas incluem detalhes do incidente que os desencadeou, bem como recomendações sobre como investigar e remediar ameaças.

Atualmente, a proteção contra ameaças para o armazenamento Azure está disponível apenas para [o armazenamento de blobs](https://azure.microsoft.com/services/storage/blobs/). 

Este serviço está disponível em todas as nuvens públicas e nuvens do governo dos EUA, mas nenhuma outra região de nuvens soberanas ou do Governo Azure. 

Para obter detalhes sobre os preços, incluindo um teste gratuito de 30 dias, consulte a página de preços do [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Para obter mais informações, consulte:

* [Como permitir a proteção avançada de ameaças para armazenamento de Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o Armazenamento Azure](alerts-reference.md#alerts-azurestorage)

> [!TIP]
> Pode simular alertas de Armazenamento Azure seguindo as instruções [neste post de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Proteção contra ameaças para Azure Cosmos DB<a name="cosmos-db"></a>

Os alertas DB do Azure Cosmos são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar contas DB do Azure Cosmos.

Para obter mais informações, consulte:

* [Proteção avançada de ameaças para Azure Cosmos DB (Pré-visualização)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para camada de rede Azure<a name="network-layer"></a>

A análise da camada de rede do Security Center baseia-se em [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)da amostra, que são cabeçalhos de pacote recolhidos pelos routers do núcleo Azure. Com base neste feed de dados, o Security Center utiliza modelos de machine learning para identificar e sinalizar atividades de tráfego malicioso. O Security Center também utiliza a base de dados da Microsoft Threat Intelligence para enriquecer endereços IP.

Algumas configurações de rede podem restringir o Centro de Segurança de gerar alertas sobre atividades suspeitas de rede. Para que o Centro de Segurança gere alertas de rede, certifique-se de que:

- A sua máquina virtual tem um endereço IP público (ou está num equilibrador de carga com um endereço IP público).

- O tráfego de saída de rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

- A sua máquina virtual foi atribuída ao mesmo endereço IP durante toda a hora durante a qual ocorreu a comunicação suspeita. Isto também se aplica aos VMs criados como parte de um serviço gerido (por exemplo, AKS, Databricks).

Para obter uma lista dos alertas da camada de rede Azure, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azurenetlayer).

Para obter detalhes sobre como o Security Center pode utilizar sinais relacionados com a rede para aplicar a proteção contra ameaças, consulte [deteções de DNS heurísticos no Centro de Segurança](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Proteção contra ameaças para camada de gestão Azure (Gestor de Recursos Azure) (Preview)<a name ="management-layer"></a>

A camada de proteção do Centro de Segurança baseada no Azure Resource Manager está atualmente em pré-visualização.

O Security Center oferece uma camada adicional de proteção utilizando eventos Azure Resource Manager, que é considerado o avião de controlo para a Azure. Ao analisar os registos do Gestor de Recursos Azure, o Security Center deteta operações incomuns ou potencialmente nocivas no ambiente de subscrição do Azure.

Para obter uma lista dos alertas do Gestor de Recursos Azure (Pré-visualização), consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas pela Microsoft Cloud App Security. Para beneficiar destas análises, tem de ativar uma licença de Segurança cloud App. Se tiver uma licença de Segurança cloud App, então estes alertas são ativados por padrão. Para desativar os alertas:
>
> 1. Na lâmina do **Centro de Segurança,** selecione **a política de segurança.** Para a subscrição que pretende alterar, **selecione configurações de Editar**.
> 2. Selecione **deteção de ameaças**.
> 3. No **Âmbito de ativações**, **limpe o Microsoft Cloud App Security para aceder aos meus dados**e selecione **Save**.

>[!NOTE]
>O Security Center armazena dados de clientes relacionados com a segurança no mesmo geo que o seu recurso. Se a Microsoft ainda não implementou o Security Center no geo dos recursos, então armazena os dados nos Estados Unidos. Quando a Cloud App Security está ativada, estas informações são armazenadas de acordo com as regras de localização geoparbrada da Cloud App Security. Para obter mais informações, consulte [o armazenamento de dados para serviços não regionais.](https://azuredatacentermap.azurewebsites.net/)








## <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para cofre de chave Azure (pré-visualização)<a name="azure-keyvault"></a>

> [!NOTE]
> Este serviço não está atualmente disponível no governo de Azure e nas regiões de nuvem soberana.

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. 

O Azure Security Center inclui proteção de ameaças avançadas para a Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. O Centro de Segurança deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas key vault. Esta camada de proteção permite-lhe lidar com ameaças sem ser um perito em segurança, e sem a necessidade de gerir sistemas de monitorização de segurança de terceiros.  

Quando ocorrem atividades anómalas, o Centro de Segurança mostra alertas e envia-os opcionalmente por e-mail aos administradores de subscrição. Estes alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e remediar ameaças. 

Para obter uma lista dos alertas do Cofre da Chave Azure, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra ameaças para a Azure WAF<a name="azure-waf"></a>

O Gateway de Aplicação do Azure oferece uma firewall de aplicações Web (WAF) que fornece proteção centralizada para as suas aplicações Web contra exploits e vulnerabilidades comuns.

As aplicações web são cada vez mais direcionadas por ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. O Gateway DE Aplicação WAF baseia-se no Conjunto de Regras Principais 3.0 ou 2.2.9 do Open Web Application Security Project. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades. 

Se tiver uma licença para a Azure WAF, os seus alertas WAF são transmitidos para o Centro de Segurança sem necessidade de configuração adicional. Para obter mais informações sobre os alertas gerados pela WAF, consulte [grupos e regras de regras de regras de crs de firewall de aplicação web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra ameaças para a proteção do DDoS Azure<a name="azure-ddos"></a>

Os ataques de negação de serviço distribuídos (DDoS) são conhecidos por serem fáceis de executar. Tornaram-se uma grande preocupação com a segurança, especialmente se estiveres a mover as tuas aplicações para a nuvem. 

Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques DDoS podem visar qualquer ponto final que possa ser alcançado através da internet.

Para se defender dos ataques do DDoS, compre uma licença para a Azure DDoS Protection e certifique-se de que está a seguir as melhores práticas do design de aplicações. A DDoS Protection fornece diferentes níveis de serviço. Para mais informações, consulte [a visão geral da Proteção DDoS do Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Para obter uma lista dos alertas de Proteção DDoS Azure, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os alertas de segurança destes recursos de proteção contra ameaças, consulte os seguintes artigos:

* [Tabela de referência para todos os alertas do Centro de Segurança Azure](alerts-reference.md)
* [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas e recomendações de segurança das exportações (Pré-visualização)](continuous-export.md)