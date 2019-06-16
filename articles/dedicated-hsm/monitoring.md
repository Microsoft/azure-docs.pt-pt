---
title: Opções - HSM do Azure dedicado de monitorização | Documentos da Microsoft
description: Descrição geral do Azure dedicado HSM opções de monitorização e as responsabilidades de monitorização
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 2bcf1d1e007398cf80839f5e1d0bac78fd80db07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912234"
---
# <a name="azure-dedicated-hsm-monitoring"></a>A monitorização do Azure de HSM dedicados

O serviço do Azure dedicado HSM fornece um dispositivo físico para utilização exclusiva de cliente completa administrativa responsabilidade de controle e gerenciamento. Se o dispositivo disponibilizado um [modelos de HSM de 7 Gemalto SafeNet Luna A790](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).  A Microsoft terá sem acesso administrativo aprovisionado uma vez por um cliente, além de anexo de porta serial física, como uma função de monitorização. Como resultado, os clientes são responsáveis por atividades operacionais típicas incluindo monitoramento abrangente e análise de registo.
Os clientes são totalmente responsáveis por aplicativos que usam os HSMs e deverá trabalhar com Gemalto para suporte ou de assistência de consultoria. Devido à extensão da propriedade de cliente de higiene de troca operacional, não é possível que a Microsoft, ofereceram qualquer tipo de garantia de disponibilidade elevada para este serviço. É responsabilidade do cliente para garantir que seus aplicativos estão corretamente configuradas para assegurar elevada disponibilidade. A Microsoft irá monitorizar e manter a conetividade de rede e estado de funcionamento do dispositivo.

## <a name="microsoft-monitoring"></a>Monitorização da Microsoft

O dispositivo da Gemalto SafeNet em utilização tem por padrão SNMP e porta serial como opções para a monitorização do dispositivo. A Microsoft já usou a ligação de porta serial, como física significa para ligar ao dispositivo para obter telemetria básica sobre o estado de funcionamento do dispositivo. Isso inclui itens como estado de temperatura e componente, como fontes de alimentação e os nossos fãs.
Para conseguir isso, a Microsoft utiliza uma função de "monitorizar" não-administrativos configurada no dispositivo a Gemalto. Esta função fornece a capacidade de obter a telemetria, mas não dá qualquer acesso ao dispositivo em termos de tarefa administrativa ou de qualquer forma de visualização de informações de criptografia. Os nossos clientes podem ter a garantia do que dispositivo é verdadeiramente a sua própria conta para gerenciar, administrar e usar para armazenamento de chaves criptográfico confidencial. No caso de todos os clientes não estiverem satisfeito com este acesso mínimo para a monitorização de estado de funcionamento básico, eles têm a opção para desativar a conta de monitorização. A conseqüência óbvia, isso é que a Microsoft não terá nenhuma informação e, por conseguinte, não emite a nenhuma capacidade para fornecer qualquer notificação proativa do Estado de funcionamento do dispositivo. Nesta situação, o cliente é responsável pelo Estado de funcionamento do dispositivo.
A função de monitor em si é configurada para consultar o dispositivo a cada 10 minutos para obter dados de estado de funcionamento. Devido à natureza propenso a erro de comunicação serial, apenas depois de vários indicadores de estado de funcionamento negativo num período de uma hora seria um alerta gerado. Este alerta, por fim, poderia levar a uma comunicação de cliente proativa notificar o problema.
Dependendo da natureza do problema, o curso de ação apropriado poderia ser utilizado para reduzir o impacto e certifique-se de remediação de risco baixo. Por exemplo, uma falha de fonte de alimentação de energia é um procedimento de substituição em funcionamento com nenhuma resultante adulteração eventos para que podem ser executadas com baixo impacto e risco mínimo para a operação. Outros procedimentos podem exigir um dispositivo para ser zeroized e desaprovisionado para minimizar quaisquer riscos de segurança para o cliente. Nesta situação, um cliente teria aprovisionar um dispositivo alternativo, voltar a participar de uma combinação de elevada disponibilidade, portanto, acionar a sincronização do dispositivo. Operação normal seria retomado no mínimo de tempo, com a mínima interrupção e menor risco de segurança.  

## <a name="customer-monitoring"></a>Monitorização de cliente

Uma proposta de valor do serviço dedicado HSM é o controle que o cliente recebe do dispositivo, especialmente Considerando é uma cloud entregar o dispositivo. Uma conseqüência desse controle é de responsabilidade para monitorizar e gerir o estado de funcionamento do dispositivo. O dispositivo da Gemalto SafeNet inclui orientações para a implementação de SNMP e Syslog. Os clientes do serviço de HSM dedicados são recomendados para utilizá-lo, mesmo quando a conta de monitorização Microsoft permanece ativa e deve considerá-lo obrigatório se eles desativam a conta de monitorização da Microsoft.
De qualquer técnica disponível permitiria que um cliente identificar problemas e chamar o suporte da Microsoft para iniciar o trabalho de remediação adequadas.

## <a name="next-steps"></a>Passos Seguintes

Recomenda-se que todos os principais conceitos do serviço, como elevada disponibilidade e segurança, por exemplo, são bem compreendidos antes de qualquer aprovisionamento de dispositivos e design do aplicativo ou implementação. Mais tópicos de nível de conceito:

* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
