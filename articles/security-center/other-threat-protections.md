---
title: Proteção adicional de ameaças no Centro de Segurança Azure
description: Conheça a proteção de ameaças disponível no Azure Security Center para além do Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: d03416f390abb8656ac1aed81419864443122ce6
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122417"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Proteções adicionais de ameaças no Centro de Segurança Azure
Além das [proteções incorporadas do Azure Defender,](azure-defender.md)o Azure Security Center também oferece as seguintes capacidades de proteção contra ameaças.

> [!TIP]
> Para ativar as capacidades de proteção de ameaças do Security Center, deve ativar o Azure Defender na subscrição que contém as cargas de trabalho aplicáveis.
>
> Pode ativar a proteção contra ameaças para **a Base de Dados Azure para MariaDB/MySQL/PostgreSQL apenas** ao nível dos recursos.


## <a name="threat-protection-for-azure-network-layer"></a>Proteção contra ameaças para camada de rede Azure <a name="network-layer"></a>
A análise da camada de rede do Security Center baseia-se em [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)da amostra, que são cabeçalhos de pacote recolhidos pelos routers do núcleo Azure. Com base neste feed de dados, o Security Center utiliza modelos de machine learning para identificar e sinalizar atividades de tráfego malicioso. O Security Center também utiliza a base de dados da Microsoft Threat Intelligence para enriquecer endereços IP.

Algumas configurações de rede restringem o Centro de Segurança de gerar alertas sobre atividades suspeitas de rede. Para que o Centro de Segurança gere alertas de rede, certifique-se de que:
- A sua máquina virtual tem um endereço IP público (ou está num equilibrador de carga com um endereço IP público).
- O tráfego de saída de rede da sua máquina virtual não está bloqueado por uma solução de IDS externa.

Para obter uma lista dos alertas da camada de rede Azure, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Proteção contra ameaças para o Gestor de Recursos Azure (Pré-visualização)<a name ="management-layer"></a>
A camada de proteção do Centro de Segurança baseada no Azure Resource Manager está atualmente em pré-visualização.

O Security Center oferece uma camada adicional de proteção utilizando eventos Azure Resource Manager, que é considerado o avião de controlo para a Azure. Ao analisar os registos do Gestor de Recursos Azure, o Security Center deteta operações incomuns ou potencialmente nocivas no ambiente de subscrição do Azure.

Para obter uma lista dos alertas do Gestor de Recursos Azure (Pré-visualização), consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureresourceman).


>[!NOTE]
> Várias das análises anteriores são alimentadas pela Microsoft Cloud App Security. Para beneficiar destas análises, tem de ativar uma licença de Segurança cloud App. Se tiver uma licença de Segurança cloud App, então estes alertas são ativados por padrão. Para desativar os alertas:
>
> 1. A partir do menu do Centro de Segurança, **selecione definições de preços &**.
> 1. Selecione a subscrição que pretende alterar.
> 1. Selecione **deteção de ameaças**.
> 1. **Limpar Permitir que a Microsoft Cloud App Security aceda aos meus dados** e selecione **Save**.


>[!NOTE]
>O Security Center armazena dados de clientes relacionados com a segurança no mesmo geo que o seu recurso. Se a Microsoft ainda não implementou o Security Center no geo dos recursos, então armazena os dados nos Estados Unidos. Quando a Cloud App Security está ativada, estas informações são armazenadas de acordo com as regras de localização geoparbrada da Cloud App Security. Para obter mais informações, consulte [o armazenamento de dados para serviços não regionais.](https://azuredatacentermap.azurewebsites.net/)

1. Ajuste o espaço de trabalho no qual está a instalar o agente. Certifique-se de que o espaço de trabalho está na mesma subscrição que utiliza no Centro de Segurança e que tem permissões de leitura/escrita no espaço de trabalho.

1. Ativar **o Azure Defender** e selecione **Save**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Proteção contra ameaças para Azure Cosmos DB (Pré-visualização)<a name="cosmos-db"></a>

Os alertas DB do Azure Cosmos são gerados por tentativas incomuns e potencialmente nocivas de aceder ou explorar contas DB do Azure Cosmos.

Para obter mais informações, veja:

* [Proteção avançada de ameaças para Azure Cosmos DB (Preview)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [A lista de alertas de proteção contra ameaças para Azure Cosmos DB (Preview)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Proteção contra ameaças para outros serviços da Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Proteção contra ameaças para a Azure WAF <a name="azure-waf"></a>

O Gateway de Aplicação do Azure oferece uma firewall de aplicações Web (WAF) que fornece proteção centralizada para as suas aplicações Web contra exploits e vulnerabilidades comuns.

As aplicações web são cada vez mais direcionadas por ataques maliciosos que exploram vulnerabilidades vulgarmente conhecidas. O Gateway DE Aplicação WAF baseia-se no Conjunto de Regras Principais 3.0 ou 2.2.9 do Open Web Application Security Project. O WAF é atualizado automaticamente para proteger contra novas vulnerabilidades. 

Se tiver uma licença para a Azure WAF, os seus alertas WAF são transmitidos para o Centro de Segurança sem necessidade de configuração adicional. Para obter mais informações sobre os alertas gerados pela WAF, consulte [grupos e regras de regras de regras de crs de firewall de aplicação web](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Proteção contra ameaças para a proteção do DDoS Azure <a name="azure-ddos"></a>

Os ataques de negação de serviço distribuídos (DDoS) são conhecidos por serem fáceis de executar. Tornaram-se uma grande preocupação com a segurança, especialmente se estiveres a mover as tuas aplicações para a nuvem. 

Um ataque DDoS tenta esgotar os recursos de uma aplicação, tornando a aplicação indisponível para utilizadores legítimos. Os ataques DDoS podem visar qualquer ponto final que possa ser alcançado através da internet.

Para se defender dos ataques do DDoS, compre uma licença para a Azure DDoS Protection e certifique-se de que está a seguir as melhores práticas do design de aplicações. A DDoS Protection fornece diferentes níveis de serviço. Para mais informações, consulte [a visão geral da Proteção DDoS do Azure](../virtual-network/ddos-protection-overview.md).

Para obter uma lista dos alertas de Proteção DDoS Azure, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre os alertas de segurança destes recursos de proteção contra ameaças, consulte os seguintes artigos:

* [Tabela de referência para todos os alertas do Centro de Segurança Azure](alerts-reference.md)
* [Alertas de segurança no Centro de Segurança do Azure](security-center-alerts-overview.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Exportar continuamente dados do Centro de Segurança](continuous-export.md)