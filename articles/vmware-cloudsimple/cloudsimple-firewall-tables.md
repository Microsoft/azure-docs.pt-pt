---
title: Solução Azure VMware by CloudSimple - Tabelas firewall
description: Saiba mais sobre as tabelas privadas de firewall da CloudSimple e as regras de firewall.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 89bef6cef48f2b972aa3f931008b0db84431b832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025049"
---
# <a name="firewall-tables-overview"></a>Visão geral das tabelas de firewall

Uma tabela de firewall lista regras para filtrar o tráfego da rede de e para os recursos privados da Cloud. Pode aplicar tabelas de firewall a uma VLAN/subnet. As regras controlam o tráfego da rede entre uma rede de origem ou endereço IP e uma rede de destino ou endereço IP.

## <a name="firewall-rules"></a>Regras da firewall

A tabela seguinte descreve os parâmetros numa regra de firewall.

| Propriedade | Detalhes |
| ---------| --------|
| **Nome** | Um nome que identifica exclusivamente a regra da firewall e o seu propósito. |
| **Prioridade** | Um número entre 100 e 4096, sendo 100 a maior prioridade. As regras são processadas por ordem prioritária. Quando o trânsito encontra uma regra, o processamento de regras para. Como resultado, regras com prioridades mais baixas que têm os mesmos atributos que regras com prioridades mais altas não são processadas.  Tome cuidado para evitar regras contraditórias. |
| **Rastreio do Estado** | O rastreio pode ser apátrida (Private Cloud, Internet ou VPN) ou stateful (IP público).  |
| **Protocolo** | As opções incluem Qualquer, TCP ou UDP. Se precisar do ICMP, use qualquer. |
| **Direção** | Indica se a regra se aplica a tráfego de entrada ou de saída. |
| **Ação** | Permitir ou negar o tipo de tráfego definido na regra. |
| **Origem** | Um endereço IP, bloco de encaminhamento inter-domínio sem classe (CIDR) (10.0.0.0.0/24, por exemplo), ou Qualquer.  Especificar uma gama, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança. |
| **Porto fonte** | Porto de onde provém o tráfego da rede.  Pode especificar uma porta ou uma gama individual de portas, tais como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança. |
| **Destino** | Um endereço IP, bloco de encaminhamento inter-domínio sem classe (CIDR) (10.0.0.0.0/24, por exemplo), ou Qualquer.  Especificar uma gama, uma etiqueta de serviço ou um grupo de segurança de aplicações permite-lhe criar menos regras de segurança.  |
| **Porto de Destino** | Porto para o qual o tráfego da rede flui.  Pode especificar uma porta ou uma gama individual de portas, tais como 443 ou 8000-8080. Especificar intervalos permite-lhe criar menos regras de segurança.|

### <a name="stateless"></a>Sem estado

Uma regra apátrida olha apenas para pacotes individuais e filtra-os com base na regra.  
Podem ser exigidas regras adicionais para o fluxo de tráfego na direção inversa.  Utilize regras apátridas para o tráfego entre os seguintes pontos:

* Subredes de nuvens privadas
* Sub-rede no local e uma subnet private cloud
* Tráfego de internet das Nuvens Privadas

### <a name="stateful"></a>Com monitorização de estado

 Uma regra imponente está ciente das ligações que passam por ela. É criado um registo de fluxo para as ligações existentes. A comunicação é permitida ou negada com base no estado da ligação do registo do fluxo.  Utilize este tipo de regra para endereços IP públicos para filtrar o tráfego da Internet.

### <a name="default-rules"></a>Regras predefinidas

As seguintes regras padrão são criadas em todas as tabelas de firewall.

|Prioridade|Nome|Rastreio do Estado|Direção|Tipo de tráfego|Protocolo|Origem|Porta de origem|Destino|Porta de destino|Ação|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|permitir-tudo-para-internet|Com monitorização de estado|Saída|IP público ou tráfego de internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65001|negar-tudo-a-internet|Com monitorização de estado|Entrada|IP público ou tráfego de internet|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Negar|
|65002|permitir-tudo-a-intranet|Sem estado|Saída|Tráfego interno ou VPN da Nuvem Privada|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|
|65003|permitir-tudo-de-intranet|Sem estado|Entrada|Tráfego interno ou VPN da Nuvem Privada|Todos|Qualquer|Qualquer|Qualquer|Qualquer|Permitir|

## <a name="next-steps"></a>Passos seguintes

* [Configurar tabelas e regras de firewall](firewall.md)
