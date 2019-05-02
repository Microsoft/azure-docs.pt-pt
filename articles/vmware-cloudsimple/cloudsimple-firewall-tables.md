---
title: Firewall tabelas - solução de VMware ao CloudSimple - Azure
description: Saiba mais sobre tabelas de firewall de nuvem privada CloudSimple e regras de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577349"
---
# <a name="firewall-tables-overview"></a>Descrição geral de tabelas de firewall

Uma tabela de firewall apresenta uma lista de regras para filtrar o tráfego de rede de e para recursos de nuvem privada. Pode aplicá-las para uma sub-rede ou VLAN. As regras, em seguida, controlam o tráfego de rede entre uma rede de origem ou o endereço IP e uma rede de destino ou o endereço IP.

## <a name="firewall-rules"></a>Regras da firewall

A tabela seguinte descreve os parâmetros numa regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra de firewall e sua finalidade. |
| **Prioridade** | Um número entre 100 e 4096, com 100 à prioridade máxima. As regras são processadas na ordem de prioridade. Quando o tráfego chega numa correspondência de regra, o processamento da regra pára. Como resultado, qualquer regra que exista com prioridades mais baixas que têm os mesmos atributos como regras com prioridades superiores não é processada.  Tenha cuidado para evitar regras em conflito. |
| **Controlo de estado** | Controlo pode ser sem estado (nuvem privada, Internet ou VPN) ou com estado (IP público).  |
| **Protocolo** | As opções incluem qualquer, TCP ou UDP. Se precisar deste, use qualquer. |
| **Direção** | Indica se a regra se aplica a tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar para o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, blocos classless entre domínios de encaminhamento (CIDR) (10.0.0.0/24, por exemplo) ou qualquer um.  Especificar um intervalo, uma etiqueta de serviço ou grupo de segurança de aplicações permite-lhe criar menos regras de segurança. |
| **Porta de origem** | Porta de rede à qual se origina o tráfego.  Pode especificar uma porta individual ou intervalo de portas, por exemplo, 443 ou 8000 8080. Especificar intervalos permite-lhe criar menos regras de segurança. |
| **Destino** | Um endereço IP, blocos classless entre domínios de encaminhamento (CIDR) (10.0.0.0/24, por exemplo) ou qualquer um.  Especificar um intervalo, uma etiqueta de serviço ou grupo de segurança de aplicações permite-lhe criar menos regras de segurança.  |
| **Porta de destino** | Porta à qual flui o tráfego de rede.  Pode especificar uma porta individual ou intervalo de portas, por exemplo, 443 ou 8000 8080. Especificar intervalos permite-lhe criar menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra sem monitoração de estado em conta somente pacotes individuais e filtros-los com base na regra.  
Regras adicionais podem ser necessárias para o fluxo de tráfego na direção inversa.  Utilize regras sem monitoração de estado para o tráfego entre os seguintes pontos:

* Sub-redes de nuvens privadas
* Sub-rede e uma sub-rede de nuvem privada no local
* Tráfego de Internet das nuvens privadas

### <a name="stateful"></a>Com monitorização de estado

 Uma regra com monitorização de estado está ciente das conexões que passam por ela. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo.  Utilize este tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras predefinidas

Regras predefinidas seguintes são criadas em cada tabela de firewall.

|Prioridade|Name|Controlo de estado|Direction|Tipo de tráfego|Protocolo|Origem|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-all-para-internet|Com monitorização de estado|Saída|Tráfego IP ou de internet público|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65001|deny-all-from-internet|Com monitorização de estado|Entrada|Tráfego IP ou de internet público|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir-all-para-intranet|Sem estado|Saída|Nuvem privada interna ou tráfego VPN|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65003|allow-all-from-intranet|Sem estado|Entrada|Nuvem privada interna ou tráfego VPN|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|

## <a name="next-steps"></a>Passos Seguintes

* [Configurar tabelas de firewall e regras](https://docs.azure.cloudsimple.com/firewall/)