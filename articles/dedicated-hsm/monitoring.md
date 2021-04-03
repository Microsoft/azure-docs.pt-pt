---
title: Opções de monitorização - Azure Dedicado HSM | Microsoft Docs
description: Visão geral das opções de monitorização e monitorização do HSM dedicados à Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c3d062d615208177fcb9c7df511f598613bf6a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97092708"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Monitorização dedicada ao HSM da Azure

O Azure Dedicad HSM Service fornece um dispositivo físico para uso exclusivo do cliente com total responsabilidade administrativa e de gestão. O dispositivo disponibilizado é um [modelo Thales Luna 7 HSM A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).  A Microsoft não terá acesso administrativo uma vez que seja adesertado por um cliente, para além do acessório de porta em série física como função de monitorização. Como resultado, os clientes são responsáveis por atividades operacionais típicas, incluindo monitorização abrangente e análise de registos.
Os clientes são totalmente responsáveis pelas aplicações que utilizam os HSMs e devem trabalhar com o Thales para apoio ou assistência de consultoria. Devido à extensão da propriedade do cliente de higiene operacional, não é possível para a Microsoft oferecer qualquer tipo de garantia de alta disponibilidade para este serviço. É da responsabilidade do cliente garantir que as suas aplicações estão corretamente configuradas para alcançar uma elevada disponibilidade. A Microsoft monitorizará e manterá a saúde do dispositivo e a conectividade da rede.

## <a name="microsoft-monitoring"></a>Monitorização da Microsoft

O dispositivo Thales Luna 7 HSM em uso tem por defeito SNMP e porta de série como opções para monitorizar o dispositivo. A Microsoft utilizou a ligação da porta em série como meio físico para se ligar ao dispositivo para recuperar a telemetria básica na saúde do dispositivo. Isto inclui itens como temperatura e estado do componente, tais como fontes de alimentação e ventiladores.
Para tal, a Microsoft utiliza uma função de "monitor" não administrativa criada no dispositivo Thales. Esta função dá a capacidade de recuperar a telemetria, mas não dá qualquer acesso ao dispositivo em termos de tarefa administrativa ou de qualquer forma visualizando informações criptográficas. Os nossos clientes podem ter a certeza de que o seu dispositivo é realmente seu próprio para gerir, administrar e usar para armazenamento de chaves criptográficas sensíveis. Caso algum cliente não esteja satisfeito com este acesso mínimo para monitorização básica da saúde, tem a opção de desativar a conta de monitorização. A consequência óbvia disto é que a Microsoft não terá nenhuma informação e, portanto, nenhuma capacidade de fornecer qualquer notificação proativa de problemas de saúde do dispositivo. Nesta situação, o cliente é responsável pela saúde do dispositivo.
A função do monitor em si é configurada para sondar o dispositivo a cada 10 minutos para obter dados de saúde. Devido à natureza propensa a erros das comunicações em série, só depois de vários indicadores de saúde negativos durante um período de uma hora seria levantado um alerta. Este alerta acabaria por levar a uma comunicação proativa do cliente a notificar o problema.
Dependendo da natureza da questão, serão tomadas as medidas adequadas para reduzir o impacto e garantir uma remediação de baixo risco. Por exemplo, uma falha de alimentação é um procedimento de troca de calor sem qualquer evento de adulteração resultante, pelo que pode ser realizado com baixo impacto e risco mínimo de funcionamento. Outros procedimentos podem exigir que um dispositivo seja zeroizado e desprovisionado para minimizar qualquer risco de segurança para o cliente. Nesta situação, um cliente providenciaria um dispositivo alternativo, reunindo um emparelhamento de alta disponibilidade, desencadeando assim a sincronização do dispositivo. O funcionamento normal seria retomado no mínimo tempo, com uma interrupção mínima e menor risco de segurança.  

## <a name="customer-monitoring"></a>Monitorização do cliente

Uma proposta de valor do serviço Dedicado HSM é o controlo que o cliente obtém do dispositivo, especialmente tendo em conta que se trata de um dispositivo entregue em nuvem. Uma consequência deste controlo é a responsabilidade de monitorizar e gerir a saúde do dispositivo. O dispositivo Thales Luna 7 HSM vem com orientação para a implementação do SNMP e do Syslog. Recomenda-se aos clientes do serviço Dedicado HSM que utilizem este serviço mesmo quando a conta do monitor da Microsoft se mantenha ativa e deverão considerá-la obrigatória se desativarem a conta monitor da Microsoft.
Qualquer uma das técnicas disponíveis permitiria a um cliente identificar problemas e ligar para o suporte da Microsoft para iniciar um trabalho de remediação apropriado.

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que todos os conceitos-chave do serviço, como a alta disponibilidade e segurança, por exemplo, sejam bem compreendidos antes de qualquer fornecimento de dispositivos e design de aplicações ou implementação. Outros tópicos de nível de conceito:

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Rede](networking.md)
* [Suportabilidade](supportability.md)
