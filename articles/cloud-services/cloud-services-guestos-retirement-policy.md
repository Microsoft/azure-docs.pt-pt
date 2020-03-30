---
title: Guia de política de apoio e reforma para o Os de Hóspedes Azure [ Microsoft Docs
description: Fornece informações sobre o que a Microsoft irá suportar no que diz respeito ao Sistema operativo Azure Guest utilizado pela Cloud Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68945455"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Política de apoio e reforma do Os OS do Azure Guest
As informações desta página dizem respeito ao sistema operativo Azure Guest[(Guest OS)](cloud-services-guestos-update-matrix.md)para trabalhador de Serviços na Nuvem e funções web (PaaS). Não se aplica a Máquinas Virtuais (IaaS).

A Microsoft tem uma política de suporte publicada [para o Os](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq)convidado . A página que está a ler descreve agora como a política é implementada.

A política é

1. A Microsoft apoiará **pelo menos as duas últimas famílias do Os**Convidado. Quando uma família está reformada, os clientes têm 12 meses a contar da data oficial da reforma para atualizar para uma nova família de Os Convidados apoiada.
2. A Microsoft suportará **pelo menos as duas versões mais recentes das famílias de Os OS de hóspedes suportadas.**
3. A Microsoft suportará **pelo menos as duas versões mais recentes do Azure SDK.** Quando uma versão do SDK for reformada, os clientes terão 12 meses a contar da data oficial de reforma para atualizar para uma versão mais recente.

Por vezes, mais de duas famílias ou libertações podem ser apoiadas. As informações oficiais de apoio ao OS do Guest os aparecerão nos lançamentos do Os do Azure Guest e na Matriz de [Compatibilidade SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Quando uma versão do Os convidado é aposentada
Novas **versões** de Os Convidados são introduzidas todos os meses para incorporar as mais recentes atualizações do MSRC. Devido às atualizações mensais regulares, uma versão Guest OS é normalmente desativada cerca de 60 dias após o seu lançamento. Esta atividade mantém pelo menos duas versões de Os convidados para cada família disponíveis para uso.

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante uma aposentadoria familiar guest os
Uma vez anunciada a reforma, os clientes têm um período de "transição" de 12 meses antes de a família mais velha ser oficialmente retirada do serviço. Este tempo de transição pode ser alargado ao critério da Microsoft. As atualizações serão publicadas nos lançamentos do Os do Azure Guest e na Matriz de [Compatibilidade SDK](cloud-services-guestos-update-matrix.md).

Iniciar-se-á um processo de reforma gradual de seis (6) meses para o período de transição. Durante este tempo:

1. A Microsoft notificará os clientes da reforma.
2. A versão mais recente do Azure SDK não vai apoiar a família de Hóspedes os soques aposentados.
3. Novas implementações e recolocações de Serviços cloud não serão permitidas na família aposentada

A Microsoft continuará a introduzir a nova versão do Guest OS, incorporando as mais recentes atualizações do MSRC até ao último dia do período de transição, conhecida como "data de validade". Na data de validade, os Serviços cloud ainda em funcionamento não serão suportados no âmbito do Azure SLA. A Microsoft tem a discrição de forçar a atualização, eliminar ou parar esses serviços após essa data.

### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante uma reforma da versão osso convidado
Se os clientes configurarem o seu Os de Hóspedes para atualizar automaticamente, nunca terão de se preocupar em lidar com as versões do Guest OS. Estarão sempre a usar a mais recente versão do Guest OS.

As versões osso são lançadas todos os meses. Devido à taxa de lançamentos regulares, cada versão tem uma vida útil fixa.

Aos 60 dias de vida, uma versão é "*desativada*". "Desativado" significa que a versão é removida do portal. A versão já não pode ser definida a partir do ficheiro de configuração CSCFG. As missões existentes são deixadas em funcionamento. Mas novas implementações e atualizações de código e configuração para as implementações existentes não serão permitidas.

Algum tempo depois de se ter tornado "desativado", a versão oss "expira" e quaisquer instalações ainda em funcionamento dessa versão expirada estão expostas a problemas de segurança e vulnerabilidade. Geralmente, a expiração é feita em lotes, pelo que o período de desativação para expiração pode variar.

Os clientes que configuram os seus serviços para atualizar manualmente o Os do Hóspede, devem garantir que os seus serviços estão a funcionar num Sistema operativo de hóspedes suportado. Se um serviço estiver configurado para atualizar automaticamente o Os do Hóspede, a plataforma subjacente garantirá a conformidade e irá atualizar para o mais recente Os convidado.

Estes períodos podem ser prolongados a critério da Microsoft para facilitar as transições dos clientes. Quaisquer alterações serão comunicadas nos lançamentos do [Os do IDK E](cloud-services-guestos-update-matrix.md)na Matriz de Compatibilidade sdK .

### <a name="notifications-during-retirement"></a>Notificações durante a reforma
* **Aposentadoria familiar** <br>A Microsoft utilizará posts de blog e notificação do portal. Os clientes que ainda estiverem a usar uma família de Os OS convidados reformados serão notificados através de comunicação direta (e-mail, mensagens de portal, chamadas telefónicas) para administradores de serviços atribuídos. Todas as alterações serão publicadas nos lançamentos do [Os de Hóspedes Azure e](cloud-services-guestos-update-matrix.md)na Matriz de Compatibilidade SDK .
* **Versão Aposentadoria** <br>Todas as alterações e as datas em que ocorrem serão publicadas nos Lançamentos do Os e Nato de [Compatibilidade do SDK,](cloud-services-guestos-update-matrix.md)incluindo libertação, desativação e expiração. Os administradores de serviços receberão e-mails se tiverem implementações em execução numa versão ou família de SI ou família de hóspedes com deficiência. O tempo destes e-mails pode variar. Geralmente, estão pelo menos um mês antes da desativação, embora este timing não seja um SLA oficial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Como posso mitigar os impactos da migração?**

Recomendamos que utilize a mais recente família de Os convidados para conceber os seus Serviços cloud.

1. Comece a planear a sua migração para uma família mais nova mais cedo.
2. Instale implementações temporárias de teste para testar o seu Serviço cloud em funcionamento na nova família.
3. Desloque a sua versão OS do Hóspede para **Automática** (osVersion=* no ficheiro [.cscfg)](cloud-services-model-and-package.md#cscfg) para que a migração para novas versões de Os do Hóspede ocorra automaticamente.

**E se a minha aplicação web exigir uma integração mais profunda com o SO?**

Se a sua arquitetura de aplicação web depender das funcionalidades subjacentes do sistema operativo, utilize capacidades suportadas pela plataforma, tais como [tarefas](cloud-services-startup-tasks.md) de arranque ou outros mecanismos de extebilidade. Em alternativa, também pode utilizar [máquinas virtuais Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infraestrutura como serviço), onde é responsável pela manutenção do sistema operativo subjacente.

## <a name="next-steps"></a>Passos seguintes
Reveja os mais recentes [lançamentos do Guest OS](cloud-services-guestos-update-matrix.md).
