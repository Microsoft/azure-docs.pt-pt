---
title: Soluções Azure VMware (AVS) - Tabelas de firewall
description: Saiba mais sobre as tabelas privadas de firewall de nuvem AVS e as regras de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 631ccb33e14586edce71752826f134c1e50819c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025049"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego da rede de e para os recursos da AVS Private Cloud. Pode aplicar tabelas de firewall a uma VLAN/subnet. As regras controlam o tráfego da rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras da firewall

A tabela seguinte descreve os parâmetros numa regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra da firewall e o seu propósito. |
| **Prioridade** | Um número entre 100 e 4096, sendo 100 a maior prioridade. As regras são processadas em ordem de prioridade. Quando o trânsito encontra uma regra, o processamento de regras para. Como resultado, regras com prioridades mais baixas que têm os mesmos atributos que regras com prioridades mais altas não são processadas. Tome cuidado para evitar regras contraditórias. |
| **Rastreio do Estado** | O rastreio pode ser apátrida (AVS Private Cloud, Internet ou VPN) ou stateful (IP público).  |
| **Protocolo** | As opções incluem Qualquer, TCP ou UDP. Se precisar do ICMP, use qualquer. |
| **Direção** | Indica se a regra se aplica a tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, um bloco de encaminhamento inter-domínio sem classe (CIDR) (10.0.0.0.0/24, por exemplo), ou qualquer endereço IP.  Especificar uma gama, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. |
| **Porto fonte** | Porto de onde provém o tráfego da rede.  Pode especificar uma porta ou uma gama individual de portas, tais como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança. |
| **Destino** | Um endereço IP, um bloco de encaminhamento inter-domínio sem classe (CIDR) (10.0.0.0.0/24, por exemplo), ou qualquer endereço IP.  Especificar uma gama, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança.  |
| **Porto de Destino** | Porto para o qual o tráfego da rede flui.  Pode especificar uma porta ou uma gama individual de portas, tais como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra apátrida olha apenas para pacotes individuais e filtra-os com base na regra. Podem ser exigidas regras adicionais para o fluxo de tráfego na direção inversa. Utilize regras apátridas para o tráfego entre os seguintes pontos:

* Subredes de Nuvens Privadas AVS
* Sub-rede no local e uma subnet AVS Private Cloud
* Tráfego de internet das Nuvens Privadas da AVS

### <a name="stateful"></a>Audais

 Uma regra imponente está ciente das ligações que passam por ela. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo. Utilize este tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras predefinidas

As seguintes regras padrão são criadas em todas as tabelas de firewall.

|Prioridade|Nome|Rastreio do Estado|Direção|Tipo de tráfego|Protocolo|Origem|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-tudo-para-internet|Audais|Saída|IP público ou tráfego de internet|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65001|negar-tudo-a-internet|Audais|Entrada|IP público ou tráfego de internet|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir-tudo-a-intranet|Sem estado|Saída|Tráfego interno ou VPN da Nuvem Privada AVS|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65003|allow-all-from-intranet|Sem estado|Entrada|Tráfego interno ou VPN da Nuvem Privada AVS|Tudo|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|

## <a name="next-steps"></a>Passos seguintes

* [Configurar tabelas e regras de firewall](firewall.md)
