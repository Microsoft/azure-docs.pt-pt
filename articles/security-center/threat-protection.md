---
title: Proteção contra ameaças no Centro de Segurança Azure
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
ms.openlocfilehash: 395f4b5481fcf2028d6bfe736e58c3174a0c80b2
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435353"
---
# <a name="threat-protection-in-azure-security-center"></a>Proteção contra ameaças no Centro de Segurança Azure

Quando o Centro de Segurança deteta uma ameaça em qualquer área do seu ambiente, gera um alerta. Estes alertas descrevem detalhes dos recursos afetados, sugeriram medidas de reparação e, em alguns casos, uma opção para desencadear uma aplicação lógica em resposta.

A proteção contra ameaças do Azure Security Center fornece defesas abrangentes para o seu ambiente:

* **Proteção contra ameaças para os recursos da computação Azure**: Máquinas Windows, máquinas Linux, Serviço de Aplicações Azure e contentores Azure

* **Proteção contra ameaças para os recursos de dados do Azure**: Base de Dados SQL e Armazém de Dados SQL, Armazenamento Azure e Azure Cosmos DB

* **Proteção contra ameaças para camadas**de serviço Azure : Camada de rede Azure, camada de gestão Azure (Gestor de Recursos Azure) (Pré-visualização) e Cofre chave Azure (Pré-visualização)

Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center de um produto de segurança diferente, pode exportá-lo. Para exportar os seus alertas para o Azure Sentinel (ou um SIEM de terceiros) ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md). 




## <a name="threat-protection-for-windows-machines"></a>Proteção contra ameaças para máquinas Windows<a name="windows-machines"></a>

O Azure Security Center integra-se com os serviços Azure para monitorizar e proteger as suas máquinas baseadas no Windows. O Centro de Segurança apresenta os alertas e sugestões de reparação de todos estes serviços num formato fácil de usar.

* **Microsoft Defender ATP** <a name="windows-atp"></a> - Security Center alarga as suas plataformas de proteção de carga de trabalho em nuvem, integrando-se com a Microsoft Defender Advanced Threat Protection (ATP). Juntos, fornecem capacidades abrangentes de deteção e resposta de pontofinal (EDR).

    > [!IMPORTANT]
    > O sensor ATP do Microsoft Defender é ativado automaticamente nos servidores do Windows que utilizam o Security Center.

    Quando o Microsoft Defender ATP deteta uma ameaça, desencadeia um alerta. O alerta está no painel do Centro de Segurança. A partir do painel de instrumentos, pode sondar para a consola ATP do Microsoft Defender e realizar uma investigação detalhada para descobrir o alcance do ataque. Para obter mais informações sobre o Microsoft Defender ATP, consulte [os servidores do Bordo para o serviço ATP do Microsoft Defender](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* Análise de despejo de **colisão** <a name="windows-dump"></a> - Quando o software falha, um depósito de lixo captura uma parte da memória no momento do acidente.

    Um acidente pode ter sido causado por malware ou conter malware. Para evitar ser detetado por produtos de segurança, várias formas de malware utilizam um ataque sem ficheiros, o que evita escrever em disco ou encriptar componentes de software escritos em disco. Este tipo de ataque é difícil de detetar usando abordagens tradicionais baseadas em disco.

    No entanto, ao utilizar a análise da memória, pode detetar este tipo de ataque. Ao analisar a memória no depósito de colisão, o Centro de Segurança pode detetar as técnicas que o ataque está a usar. Por exemplo, o ataque pode estar a tentar explorar vulnerabilidades no software, aceder a dados confidenciais e persistir subrepticiamente dentro de uma máquina comprometida. O Centro de Segurança faz este trabalho com o mínimo impacto de desempenho para os anfitriões.

    Para obter detalhes sobre os alertas de análise de despejo de colisão, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-windows).

* **Deteção de** <a name="windows-fileless"></a> ataque sem ficheiros - Ataques sem ficheiro sem ficheiro sem registo que visam os seus pontos finais são comuns. Para evitar a deteção, ataques sem ficheiro injetam cargas maliciosas na memória. As cargas dos atacantes persistem na memória de processos comprometidos e realizam uma vasta gama de atividades maliciosas.

    Com a deteção de ataques sem ficheiros, técnicas forenses de memória automatizadas identificam kits de ferramentas de ataque sem ficheiros, técnicas e comportamentos. Esta solução digitaliza periodicamente a sua máquina no tempo de execução, e extrai insights diretamente da memória de processos críticos de segurança.

    Encontra provas de exploração, injeção de código e execução de cargas maliciosas. A deteção de ataques sem ficheiros gera alertas de segurança detalhados para acelerar a triagem de alerta, a correlação e o tempo de resposta a jusante. Esta abordagem complementa as soluções EDR baseadas em eventos, proporcionando uma maior cobertura de deteção.

    Para obter detalhes sobre os alertas de deteção de ataques sem arquivo, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-windows).

> [!TIP]
> Pode simular alertas do Windows descarregando o Manual do Centro de [Segurança Do Azure: Alertas de Segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).






## <a name="threat-protection-for-linux-machines"></a>Proteção contra ameaças para máquinas Linux<a name="linux-machines"></a>

O Security Center recolhe registos de auditoria de máquinas Linux utilizando **auditorias,** um dos quadros de auditoria mais comuns do Linux. vida auditada no núcleo principal. 

* **Alertas auditados linux e integração** <a name="linux-auditd"></a> de agentes log analytics - O sistema auditado consiste num subsistema de nível kernel, que é responsável pela monitorização das chamadas do sistema. Filtra-os por um conjunto de regras especificado e escreve mensagens para eles a uma tomada. O Security Center integra funcionalidades do pacote auditado dentro do agente Log Analytics. Esta integração permite a recolha de eventos auditados em todas as distribuições suportadas do Linux, sem quaisquer pré-requisitos.

    os registos auditados são recolhidos, enriquecidos e agregados em eventos utilizando o agente Log Analytics para o agente Linux. O Security Center adiciona continuamente novas análises que utilizam sinais Linux para detetar comportamentos maliciosos na nuvem e nas máquinas Linux no local. À semelhança das capacidades do Windows, estas análises estendem-se por processos suspeitos, sinal duvidoso em tentativas, carregamento de módulos kernel e outras atividades. Estas atividades podem indicar que uma máquina está a ser atacada ou foi violada.  

    Para obter uma lista dos alertas Linux, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-linux).

> [!TIP]
> Pode simular alertas Linux baixando o Manual do Centro de [Segurança Azure: Deteções de Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).





## <a name="threat-protection-for-azure-app-service"></a>Proteção contra ameaças para o Serviço de Aplicações Azure<a name="app-services"></a>

> [!NOTE]
> Este serviço não está atualmente disponível nas regiões do governo de Azure e nas nuvens soberanas.

O Security Center usa a escala da nuvem para identificar ataques direcionados para aplicações que passam pelo Serviço de Aplicações. Os atacantes sondam aplicações web para encontrar e explorar fraquezas. Antes de serem encaminhados para ambientes específicos, os pedidos de aplicações em funcionamento em Azure passam por vários gateways, onde são inspecionados e registados. Estes dados são então utilizados para identificar explorações e atacantes, e para aprender novos padrões que serão usados mais tarde.

Ao usar a visibilidade que o Azure tem como fornecedor de nuvem, o Security Center analisa os registos internos do App Service para identificar a metodologia de ataque em vários alvos. Por exemplo, a metodologia inclui a digitalização generalizada e ataques distribuídos. Este tipo de ataque normalmente provém de um pequeno subconjunto de IPs, e mostra padrões de rastejar para pontos finais semelhantes em vários hospedeiros. Os ataques estão à procura de uma página ou plugin vulnerável, e não podem ser identificados do ponto de vista de um único hospedeiro.

Se estiver a executar um plano de serviço de aplicações baseado no Windows, o Security Center também tem acesso às caixas de areia e VMs subjacentes. Juntamente com os dados de registo acima mencionados, a infraestrutura pode contar a história, desde um novo ataque que circula na natureza até compromissos nas máquinas de clientes. Portanto, mesmo que o Security Center seja implementado depois de uma aplicação web ter sido explorada, pode ser capaz de detetar ataques em curso.

Para obter uma lista dos alertas do Serviço de Aplicações Azure, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-azureappserv).

Para obter mais informações sobre os planos do Serviço de Aplicações, consulte os planos do Serviço de [Aplicações.](https://azure.microsoft.com/pricing/details/app-service/plans/)





## <a name="threat-protection-for-azure-containers"></a>Proteção contra ameaças para contentores Azure<a name="azure-containers"></a>

> [!NOTE]
> Este serviço não está atualmente disponível nas regiões do governo de Azure e nas nuvens soberanas.

O Security Center fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

O Centro de Segurança fornece proteção contra ameaças a diferentes níveis: 

* **Nível** de anfitrião - O agente do Centro de Segurança (disponível no nível Standard, consulte [os preços](security-center-pricing.md) para detalhes) monitoriza o Linux para atividades suspeitas. O agente aciona alertas para atividades suspeitas originárias do nó ou de um contentor que o correnter. Exemplos de tais atividades incluem deteção de conchas web e conexão com endereços IP suspeitos conhecidos.

    Para uma visão mais aprofundada da segurança do seu ambiente contentorizado, o agente monitoriza a análise específica do contentor. Irá desencadear alertas para eventos como criação de contentores privilegiados, acesso suspeito a servidores API e servidores Secure Shell (SSH) dentro de um contentor Docker.

    >[!IMPORTANT]
    > Se optar por não instalar os agentes nos seus anfitriões, só receberá um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análise de rede e comunicações com servidores maliciosos.

    Para obter uma lista dos alertas de nível de acolhimento, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-containerhost).


* Ao **nível do cluster AKS,** a proteção contra ameaças baseia-se na análise dos registos de auditoria da Kubernetes. Para ativar esta monitorização **sem agente,** adicione a opção Kubernetes à sua subscrição a partir da página de **definições de Preços &** (ver [preços).](security-center-pricing.md) Para gerar alertas a este nível, o Security Center monitoriza os seus serviços geridos pela AKS utilizando os registos recuperados pela AKS. Exemplos de eventos a este nível incluem dashboards Kubernetes expostos, criação de altos papéis privilegiados, e a criação de suportes sensíveis.

    >[!NOTE]
    > O Security Center gera alertas de segurança para as ações e implementações do Serviço Azure Kubernetes que ocorrem após a opção Kubernetes estar ativada nas definições de subscrição. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-akscluster).

Além disso, a nossa equipa global de investigadores de segurança monitoriza constantemente o panorama da ameaça. Acrescentam alertas e vulnerabilidades específicos do contentor à medida que são descobertos.

> [!TIP]
> Pode simular alertas de contentores seguindo as instruções [neste post](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)de blog .








## <a name="threat-protection-for-sql-database-and-sql-data-warehouse"></a>Proteção contra ameaças para base de dados SQL e Armazém de Dados SQL<a name="data-sql"></a>

A Advanced Threat Protection for Azure SQL Database deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

Você verá alertas quando há atividades suspeitas de base de dados, potenciais vulnerabilidades, ou ataques de injeção SQL, e padrões anómalos de acesso à base de dados e padrões de consulta.

Advanced Threat Protection for Azure SQL Database and SQL faz parte do pacote unificado de Segurança de [Dados Avançados (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) para capacidades avançadas de segurança SQL, abrangendo bases de dados Azure SQL, Base de Dados Azure SQL geridas, bases de dados azure SQL Data Warehouse e servidores SQL em Máquinas Virtuais Azure.

Para obter mais informações, consulte:

* [Como ativar a Proteção Avançada de Ameaças para a Base de Dados SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)
* [Como ativar a Proteção Avançada de Ameaças para servidores SQL em Máquinas Virtuais Azure](security-center-iaas-advanced-data.md)
* [A lista de alertas de proteção contra ameaças para a Base de Dados SQL e o Armazém de Dados SQL](alerts-reference.md#alerts-sql-db-and-warehouse)




## <a name="threat-protection-for-azure-storage"></a>Proteção contra ameaças para armazenamento azure<a name="azure-storage"></a>

> [!NOTE]
> Este serviço está disponível nas nuvens do governo dos EUA, mas nenhuma outra região soberana ou azure nuvem de nuvem.

A Advanced Threat Protection for Storage (atualmente disponível apenas para armazenamento blob) deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Esta camada de proteção permite-lhe lidar com ameaças sem exigir que seja um perito em segurança, e ajuda-o a gerir os seus sistemas de monitorização de segurança.

Para obter mais informações, consulte:

* [Como permitir a Proteção Avançada de Ameaças para armazenamento de Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection)
* [A lista de alertas de proteção contra ameaças para o Armazenamento De Azure](alerts-reference.md#alerts-azurestorage)


> [!TIP]
> Pode simular alertas de armazenamento azure seguindo as instruções [nesta publicação de blog](https://techcommunity.microsoft.com/t5/azure-security-center/validating-atp-for-azure-storage-detections-in-azure-security/ba-p/1068131).




## <a name="threat-protection-for-azure-cosmos-db"></a>Proteção contra ameaças para Azure Cosmos DB<a name="cosmos-db"></a>

Os alertas Azure Cosmos DB são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar contas Azure Cosmos DB.

Para obter mais informações, consulte:

* [Proteção avançada de ameaças para O Cosmos Db (Pré-visualização)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para O Azure Cosmos DB (Pré-visualização)](alerts-reference.md#alerts-azurecosmos)




## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para camada de rede Azure<a name="network-layer"></a>

A análise da camada de rede do Security Center baseia-se em [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)da amostra, que são cabeçalhos de pacote sacados pelos routers centrais do Azure. Com base neste feed de dados, o Security Center utiliza modelos de machine learning para identificar e sinalizar atividades de tráfego maliciosas. O Security Center também utiliza a base de dados da Microsoft Threat Intelligence para enriquecer endereços IP.

Algumas configurações de rede podem restringir o Centro de Segurança de gerar alertas sobre atividades suspeitas da rede. Para que o Centro de Segurança gere alertas de rede, certifique-se de que:

- A sua máquina virtual tem um endereço IP público (ou está num equilibrista de carga com um endereço IP público).

- O tráfego de rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

- A sua máquina virtual foi atribuída ao mesmo endereço IP durante toda a hora durante a qual ocorreu a comunicação suspeita. Isto também se aplica aos VMs criados como parte de um serviço gerido (por exemplo, AKS, Databricks).

Para obter uma lista dos alertas de camada de rede Azure, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-azurenetlayer).

Para mais detalhes sobre como o Security Center pode usar sinais relacionados com a rede para aplicar a proteção contra ameaças, consulte [deteções heurísticas de DNS no Centro](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/)de Segurança .



## <a name="threat-protection-for-azure-management-layer-azure-resource-manager-preview"></a>Proteção contra ameaças para camada de gestão Azure (Gestor de Recursos Azure) (Pré-visualização)<a name ="management-layer"></a>

A camada de proteção do Security Center baseada no Gestor de Recursos Azure está atualmente em pré-visualização.

O Security Center oferece uma camada adicional de proteção utilizando eventos do Gestor de Recursos Azure, que é considerado o plano de controlo para o Azure. Ao analisar os registos do Gestor de Recursos Do Azure, o Centro de Segurança deteta operações incomuns ou potencialmente nocivas no ambiente de subscrição do Azure.

Para obter uma lista dos alertas do Gestor de Recursos Azure (Pré-visualização), consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-azureresourceman).



>[!NOTE]
> Várias das análises anteriores são alimentadas pela Microsoft Cloud App Security. Para beneficiar destas análises, tem de ativar uma licença cloud App Security. Se tiver uma licença cloud App Security, estes alertas são ativados por defeito. Para desativar os alertas:
>
> 1. Na lâmina do Centro de **Segurança,** selecione **a política**de segurança . Para a subscrição que pretende alterar, selecione **definições de Editar**.
> 2. Selecione **deteção de ameaças**.
> 3. Em **integrações Enable**, claro permitir que a **Segurança da Aplicação Microsoft Cloud aceda aos meus dados**e selecione **Save**.

>[!NOTE]
>O Security Center armazena dados de clientes relacionados com a segurança no mesmo geo que o seu recurso. Se a Microsoft ainda não implementou o Security Center no geo do recurso, então armazena os dados nos Estados Unidos. Quando a Cloud App Security está ativada, esta informação é armazenada de acordo com as regras de localização geo-localização da Cloud App Security. Para mais informações, consulte o armazenamento de [dados para serviços não regionais.](https://azuredatacentermap.azurewebsites.net/)








## <a name="threat-protection-for-azure-key-vault-preview"></a>Proteção contra ameaças para cofre de chaves Azure (pré-visualização)<a name="azure-keyvault"></a>

> [!NOTE]
> Este serviço não está atualmente disponível nas regiões do governo de Azure e nas nuvens soberanas.

Azure Key Vault é um serviço na nuvem que salvaguarda chaves de encriptação e segredos como certificados, cordas de ligação e senhas. 

O Azure Security Center inclui a proteção de ameaças avançada e nativa de Azure para o Cofre de Chaves Azure, fornecendo uma camada adicional de inteligência de segurança. O Security Center deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas key vault. Esta camada de proteção permite-lhe lidar com ameaças sem ser um perito em segurança, e sem a necessidade de gerir sistemas de monitorização de segurança de terceiros.  

Quando ocorrem atividades anómalas, o Security Center mostra alertas e envia-os opcionalmente por e-mail para administradores de subscrição. Estes alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e remediar ameaças. 

Para obter uma lista dos alertas do Cofre chave Azure, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-azurekv).





## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft<a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra ameaças para o Azure WAF<a name="azure-waf"></a>

O Gateway de Aplicação do Azure oferece uma firewall de aplicações Web (WAF) que fornece proteção centralizada para as suas aplicações Web contra exploits e vulnerabilidades comuns.

As aplicações web são cada vez mais alvo de ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. O Gateway WAF da aplicação baseia-se no Conjunto de Regras 3.0 ou 2.2.9 do Projeto de Segurança de Aplicações Abertas web. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades. 

Se tiver uma licença para o Azure WAF, os seus alertas WAF são transmitidos para o Security Center sem necessidade de configuração adicional. Para obter mais informações sobre os alertas gerados pela WAF, consulte os [grupos e regras](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)de regras CRS da firewall de aplicação web .


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra ameaças para proteção de DDoS Azure<a name="azure-ddos"></a>

Os ataques de negação de serviço distribuídos (DDoS) são conhecidos por serem fáceis de executar. Tornaram-se uma grande preocupação de segurança, especialmente se estiveres a mover as tuas aplicações para a nuvem. 

Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques dDoS podem visar qualquer ponto final que possa ser alcançado através da internet.

Para se defender de ataques DDoS, compre uma licença para a Proteção DDoS Azure e certifique-se de que está a seguir as melhores práticas de design de aplicações. A DDoS Protection fornece diferentes níveis de serviço. Para mais informações, consulte a visão geral da [Proteção DDoS azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Para obter uma lista dos alertas de Proteção DDoS Azure, consulte a [tabela de alertas de Referência](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os alertas de segurança destas funcionalidades de proteção contra ameaças, consulte os seguintes artigos:

* [Tabela de referência para todos os alertas do Azure Security Center](alerts-reference.md)
* [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Alertas e recomendações de segurança para exportação (Pré-visualização)](continuous-export.md)