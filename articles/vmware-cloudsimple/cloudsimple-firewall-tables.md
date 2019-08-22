---
title: Solução do Azure VMware por tabelas CloudSimple-firewall
description: Saiba mais sobre as tabelas de firewall e as regras de firewall da nuvem privada do CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877720"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego de rede de e para recursos de nuvem privada. Você pode aplicar tabelas de firewall a uma VLAN/sub-rede. As regras controlam o tráfego de rede entre uma rede de origem ou um endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras de firewall

A tabela a seguir descreve os parâmetros em uma regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Name** | Um nome que identifica exclusivamente a regra de firewall e sua finalidade. |
| **prioridade** | Um número entre 100 e 4096, com 100 sendo a prioridade mais alta. As regras são processadas em ordem de prioridade. Quando o tráfego encontra uma correspondência de regra, o processamento da regra é interrompido. Como resultado, as regras com prioridades mais baixas que têm os mesmos atributos que as regras com prioridades mais altas não são processadas.  Tome cuidado para evitar regras conflitantes. |
| **Rastreamento de estado** | O rastreamento pode ser sem estado (nuvem privada, Internet ou VPN) ou com estado (IP público).  |
| **Protocolo** | As opções incluem any, TCP ou UDP. Se você precisar de ICMP, use any. |
| **Direção** | Indica se a regra se aplica a tráfego de entrada ou de saída. |
| **ação** | Permitir ou negar para o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, bloco CIDR (roteamento entre domínios sem classificação) (10.0.0.0/24, por exemplo) ou qualquer um.  Especificar um intervalo, uma marca de serviço ou um grupo de segurança de aplicativo permite que você crie menos regras de segurança. |
| **Porta de origem** | Porta da qual o tráfego de rede é originado.  Você pode especificar uma porta individual ou um intervalo de portas, como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança. |
| **Destino** | Um endereço IP, bloco CIDR (roteamento entre domínios sem classificação) (10.0.0.0/24, por exemplo) ou qualquer um.  Especificar um intervalo, uma marca de serviço ou um grupo de segurança de aplicativo permite que você crie menos regras de segurança.  |
| **Porta de destino** | Porta para a qual o tráfego de rede flui.  Você pode especificar uma porta individual ou um intervalo de portas, como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra sem estado só é semelhante a pacotes individuais e filtra-as com base na regra.  
Regras adicionais podem ser necessárias para o fluxo de tráfego na direção inversa.  Use regras sem estado para o tráfego entre os seguintes pontos:

* Sub-redes de nuvens privadas
* Sub-rede local e uma sub-rede de nuvem privada
* Tráfego de Internet das nuvens privadas

### <a name="stateful"></a>Dinâmico

 Uma regra com estado está ciente das conexões que passam por ela. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo.  Use esse tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras predefinidas

As regras padrão a seguir são criadas em cada tabela de firewall.

|Priority|Name|Rastreamento de estado|Direction|Tipo de tráfego|Protocol|Source|Porta de Origem|Destino|Porta de Destino|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-tudo para a Internet|Dinâmico|Saída|IP público ou tráfego de Internet|Todos|Any|Any|Any|Any|Allow|
|65001|negar-tudo-da-Internet|Dinâmico|Entrada|IP público ou tráfego de Internet|Todos|Any|Any|Any|Any|Negar|
|65002|permitir-tudo para a intranet|Sem estado|Saída|Tráfego interno ou VPN da nuvem privada|Todos|Any|Any|Any|Any|Allow|
|65003|allow-all-from-intranet|Sem estado|Entrada|Tráfego interno ou VPN da nuvem privada|Todos|Any|Any|Any|Any|Allow|

## <a name="next-steps"></a>Passos Seguintes

* [Configurar regras e tabelas de firewall](firewall.md)
