---
title: Guia de política de apoio e aposentadoria para o Azure Guest OS | Microsoft Docs
description: Fornece informações sobre o que a Microsoft irá suportar no que diz respeito ao Azure Guest OS utilizado pela Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: fbe9b3379799fe3cf0a56d921ab257bc87606ca9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "68945455"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Azure Guest OS apoia a política de apoio e reforma
As informações desta página dizem respeito ao sistema operativo Azure Guest[(Guest OS](cloud-services-guestos-update-matrix.md)) para os serviços de cloud e funções web (PaaS). Não se aplica a Máquinas Virtuais (IaaS).

A Microsoft tem uma política de suporte publicada [para o Guest OS](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). A página que está a ler descreve agora como a política é implementada.

A política é

1. A Microsoft irá suportar **pelo menos as duas últimas famílias do Sistema operativo Convidado**. Quando uma família está reformada, os clientes têm 12 meses a contar da data oficial de reforma para atualizar para uma nova família de Guest OS apoiada.
2. A Microsoft irá suportar **pelo menos as duas versões mais recentes das famílias de Guest OS suportadas.**
3. A Microsoft irá suportar **pelo menos as duas versões mais recentes do Azure SDK**. Quando uma versão do SDK for reformada, os clientes terão 12 meses a partir da data oficial de reforma para atualizar para uma versão mais recente.

Por vezes, mais de duas famílias ou libertações podem ser apoiadas. As informações oficiais de suporte do Guest OS aparecerão nos lançamentos do [Azure Guest OS e na Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Quando uma versão guest OS é reformada
Novas **versões** de Guest OS são introduzidas todos os meses para incorporar as mais recentes atualizações do MSRC. Devido às atualizações mensais regulares, uma versão Guest OS é normalmente desativada cerca de 60 dias após o seu lançamento. Esta atividade mantém pelo menos duas versões de Guest OS para cada família disponíveis para uso.

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante uma aposentadoria da família Guest OS
Uma vez anunciada a reforma, os clientes têm um período de "transição" de 12 meses antes de a família mais velha ser oficialmente retirada do serviço. Este tempo de transição pode ser prolongado à discrição da Microsoft. As atualizações serão publicadas nos lançamentos do [Azure Guest OS e na Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).

Um processo de reforma gradual iniciar-se-á seis (6) meses após o período de transição. Durante este tempo:

1. A Microsoft notificará os clientes da reforma.
2. A versão mais recente do Azure SDK não vai apoiar a família de hóspedes aposentados.
3. Não serão permitidas novas implementações e reafectações de Serviços Cloud na família reformada

A Microsoft continuará a introduzir a nova versão guest OS incorporando as mais recentes atualizações do MSRC até ao último dia do período de transição, conhecido como "data de validade". Na data de validade, os Serviços cloud ainda em execução não serão suportados no âmbito do Azure SLA. A Microsoft tem a discrição de forçar a atualização, eliminar ou parar esses serviços após essa data.

### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante uma reforma da versão do Guest OS
Se os clientes definirem o seu Sistema De hóspedes para atualizar automaticamente, nunca terão de se preocupar em lidar com as versões do Guest OS. Estarão sempre a utilizar a mais recente versão do Guest OS.

As versões os convidados são lançadas todos os meses. Devido à taxa de lançamentos regulares, cada versão tem uma vida útil fixa.

A 60 dias do tempo de vida, uma versão é "*desativada*". "Desativado" significa que a versão é removida do portal. A versão já não pode ser definida a partir do ficheiro de configuração CSCFG. As implementações existentes são deixadas em funcionamento. Mas não serão permitidas novas implementações e atualizações de código e configuração para as implementações existentes.

Algum tempo depois de ter ficado "desativada", a versão Guest OS "expira" e quaisquer instalações ainda em execução que a versão expirada estão expostas a problemas de segurança e vulnerabilidade. Geralmente, a expiração é feita em lotes, pelo que o período de desativação para a expiração pode variar.

Os clientes que configuram os seus serviços para atualizar manualmente o Guest OS, devem garantir que os seus serviços estão a funcionar num Sistema operativo convidado suportado. Se um serviço estiver configurado para atualizar automaticamente o So convidado, a plataforma subjacente garantirá a conformidade e irá atualizar para o mais recente Sistema operativo convidado.

Estes períodos podem ser dificultados, a critério da Microsoft, para facilitar as transições dos clientes. Quaisquer alterações serão comunicadas nos lançamentos do [Azure Guest OS e na Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Notificações durante a reforma
* **Reforma familiar** <br>A Microsoft utilizará posts de blog e notificação do portal. Os clientes que ainda utilizam uma família de Os Hóspedes reformados serão notificados através de comunicação direta (e-mail, mensagens de portal, chamada telefónica) aos administradores de serviço atribuídos. Todas as alterações serão publicadas nos lançamentos do [Azure Guest OS e na Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).
* **Aposentadoria versão** <br>Todas as alterações e datas em que ocorrem serão publicadas nos [Lançamentos do Os do Azure Guest e na Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md), incluindo lançamento, desativação e expiração. Os administradores de serviços receberão e-mails se tiverem implementações em execução numa versão ou família de guestos desativados. O tempo destes e-mails pode variar. Geralmente, estão pelo menos um mês antes do desactivamento, embora este momento não seja um SLA oficial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Como posso mitigar os impactos da migração?**

Recomendamos que utilize a mais recente família guest OS para desenhar os seus Serviços Cloud.

1. Comece a planear a sua migração para uma família mais nova mais cedo.
2. Configurar implementações temporárias de teste para testar o seu Serviço cloud em execução na nova família.
3. Desajei a sua versão Guest OS a **Automática** (osVersion=* no ficheiro [.cscfg)](cloud-services-model-and-package.md#cscfg) para que a migração para as novas versões do Guest OS ocorra automaticamente.

**E se a minha aplicação web requer uma integração mais profunda com o SO?**

Se a arquitetura da sua aplicação web depender das funcionalidades subjacentes ao sistema operativo, utilize capacidades suportadas pela plataforma, tais como [tarefas de arranque](cloud-services-startup-tasks.md) ou outros mecanismos de extensibilidade. Em alternativa, também pode utilizar [máquinas virtuais Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infraestrutura como Serviço), onde é responsável pela manutenção do sistema operativo subjacente.

## <a name="next-steps"></a>Passos seguintes
Reveja as [últimas versões do Guest OS](cloud-services-guestos-update-matrix.md).
