---
title: Solução Azure VMware by CloudSimple - Tabelas de firewall
description: Saiba mais sobre as tabelas de firewall e firewall privadas CloudSimple, incluindo regras padrão que são criadas em todas as tabelas de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140875"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego da rede de e para os recursos private Cloud. Pode aplicar tabelas de firewall numa VLAN/sub-rede. As regras controlam o tráfego de rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras da firewall

A tabela seguinte descreve os parâmetros numa regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra da firewall e o seu propósito. |
| **Priority** | Um número entre 100 e 4096, com 100 sendo a maior prioridade. As regras são processadas por ordem prioritária. Quando o tráfego encontra uma correspondência de regras, o processamento de regras para. Como resultado, regras com prioridades mais baixas que têm os mesmos atributos que as regras com prioridades mais elevadas não são processadas.  Cuidado para evitar regras contraditórias. |
| **Rastreio do Estado** | O rastreio pode ser apátrida (Private Cloud, Internet ou VPN) ou stateful (IP público).  |
| **Protocolo** | As opções incluem Any, TCP ou UDP. Se necessitar de ICMP, utilize Qualquer. |
| **Direção** | Indica se a regra se aplica a tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, um bloco de encaminhamento inter-domínio sem classe (CIDR) (10.0.0.0/24, por exemplo), ou Qualquer.  Especificar um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. |
| **Porto fonte** | Porto de onde é originário o tráfego da rede.  Pode especificar uma porta ou uma gama individual de portas, tais como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança. |
| **Destino** | Um endereço IP, um bloco de encaminhamento inter-domínio sem classe (CIDR) (10.0.0.0/24, por exemplo), ou Qualquer.  Especificar um intervalo, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança.  |
| **Porto de Destino** | Porto para onde o tráfego da rede flui.  Pode especificar uma porta ou uma gama individual de portas, tais como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra apátrida olha apenas para pacotes individuais e filtra-os com base na regra.  
Podem ser necessárias regras adicionais para o fluxo de tráfego no sentido inverso.  Utilize regras apátridas para o tráfego entre os seguintes pontos:

* Sub-redes de Nuvens Privadas
* Sub-rede no local e uma sub-rede Private Cloud
* Tráfego de internet das Nuvens Privadas

### <a name="stateful"></a>Com monitorização de estado

 Uma regra imponente está ciente das ligações que a atravessam. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo.  Utilize este tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras predefinidas

As seguintes regras predefinidos são criadas em todas as tabelas de firewall.

|Prioridade|Name|Rastreio do Estado|Direção|Tipo de tráfego|Protocolo|Origem|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir tudo-para-internet|Com monitorização de estado|Saída|IP público ou tráfego de internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65001|negar-tudo-da-internet|Com monitorização de estado|Entrada|IP público ou tráfego de internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir tudo-para-intranet|Sem estado|Saída|Tráfego interno ou VPN de Nuvem Privada|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65003|permitir tudo-a partir de intranet|Sem estado|Entrada|Tráfego interno ou VPN de Nuvem Privada|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|

## <a name="next-steps"></a>Passos seguintes

* [Configurar tabelas e regras de firewall](firewall.md)
