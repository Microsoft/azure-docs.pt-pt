---
title: Opções de monitorização - Azure Dedicado HSM [ Microsoft Docs
description: Visão geral das opções de monitorização e monitorização de HSM dedicadas do Azure e responsabilidades de monitorização
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: 3fde577a6b0efb7584e1c9efd57c95583ebe4ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881418"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitorização HSM dedicada azure

O Serviço HSM Dedicado Azure fornece um dispositivo físico para uso exclusivo do cliente com total responsabilidade de controlo administrativo e gestão. O dispositivo disponibilizado é um [Modelo Gemalto SafeNet Luna 7 HSM A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  A Microsoft não terá acesso administrativo uma vez aprovisionado por um cliente, para além do acessório físico por porta em série como uma função de monitorização. Como resultado, os clientes são responsáveis por atividades operacionais típicas, incluindo monitorização abrangente e análise de registos.
Os clientes são totalmente responsáveis por aplicações que utilizam os HSMs e devem trabalhar com a Gemalto para assistência de apoio ou consultoria. Devido à extensão da propriedade do cliente da higiene operacional, não é possível que a Microsoft ofereça qualquer tipo de garantia de alta disponibilidade para este serviço. É da responsabilidade do cliente garantir que as suas aplicações estão corretamente configuradas para alcançar uma elevada disponibilidade. A Microsoft monitorizará e manterá a conectividade da rede e saúde do dispositivo.

## <a name="microsoft-monitoring"></a>Monitorização da Microsoft

O dispositivo Gemalto SafeNet em uso tem por padrão SNMP e porta de série como opções para monitorizar o dispositivo. A Microsoft utilizou a ligação da porta em série como meio físico para se ligar ao dispositivo para recuperar a telemetria básica sobre a saúde do dispositivo. Isto inclui itens como temperatura e estado do componente, tais como fontes de alimentação e ventiladores.
Para tal, a Microsoft utiliza uma função de "monitor" não administrativo criada no dispositivo Gemalto. Esta função dá a capacidade de recuperar a telemetria, mas não dá qualquer acesso ao dispositivo em termos de tarefa administrativa ou de qualquer forma visualização de informações criptográficas. Os nossos clientes podem ter a certeza de que o seu dispositivo é verdadeiramente seu próprio para gerir, administrar e usar para armazenamento de chaves criptográficas sensíveis. Caso algum cliente não esteja satisfeito com este acesso mínimo para monitorização básica da saúde, eles têm a opção de desativar a conta de monitorização. A consequência óbvia disto é que a Microsoft não terá informações e, portanto, não terá capacidade de fornecer qualquer notificação proativa de problemas de saúde do dispositivo. Nesta situação, o cliente é responsável pela saúde do dispositivo.
A função de monitor em si é configurada para fazer uma sondagem ao dispositivo a cada 10 minutos para obter dados de saúde. Devido ao erro de natureza propensa a comunicações em série, só depois de múltiplos indicadores negativos de saúde durante um período de uma hora seria levantado um alerta. Este alerta acabaria por levar a uma comunicação proactiva do cliente notificando o problema.
Dependendo da natureza da questão, seria tomada a medida adequada para reduzir o impacto e garantir uma reparação de baixo risco. Por exemplo, uma falha de alimentação é um procedimento de troca de calor sem nenhum evento de adulteração resultante, pelo que pode ser realizado com baixo impacto e risco mínimo de funcionamento. Outros procedimentos podem exigir que um dispositivo seja zeroizado e desprovisionado para minimizar qualquer risco de segurança para o cliente. Nesta situação, um cliente forneceria um dispositivo alternativo, juntando-se a um emparelhamento de alta disponibilidade, desencadeando assim a sincronização do dispositivo. O funcionamento normal seria retomado em tempo mínimo, com o mínimo de perturbação e menor risco de segurança.  

## <a name="customer-monitoring"></a>Monitorização do cliente

Uma proposta de valor do serviço HSM dedicado é o controlo que o cliente obtém do dispositivo, especialmente tendo em conta que se trata de um dispositivo entregue em nuvem. Uma consequência deste controlo é a responsabilidade de monitorizar e gerir a saúde do dispositivo. O dispositivo Gemalto SafeNet vem com orientação para a implementação de SNMP e Syslog. Recomenda-se aos clientes do serviço HSM dedicado que utilizem isto mesmo quando a conta de monitor da Microsoft permanece ativa e devem considerá-la obrigatória se desativar a conta de monitor da Microsoft.
Qualquer uma das técnicas disponíveis permitiria que um cliente identificasse problemas e ligasse para o suporte da Microsoft para iniciar um trabalho de reparação apropriado.

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que todos os conceitos-chave do serviço, como a elevada disponibilidade e segurança, por exemplo, sejam bem compreendidos antes de qualquer fornecimento de dispositivos e design de aplicações ou implementação. Outros tópicos de nível de conceito:

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
