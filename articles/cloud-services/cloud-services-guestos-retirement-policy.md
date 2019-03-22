---
title: Guia de Suportabilidade e política de reforma para o SO convidado do Azure | Documentos da Microsoft
description: Fornece informações sobre o que Microsoft oferecerá suporte como regards ao SO de convidado do Azure utilizada pelos serviços Cloud.
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: ''
ms.assetid: 919dd781-4dc6-4e50-bda8-9632966c5458
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/20/2017
ms.author: raiye
ms.openlocfilehash: 6e4a83eb8b3488c4ce2816151ca31b4a594dd742
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58338637"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Política do Azure de Suportabilidade e extinção de SO convidado
As informações nesta página se relaciona com o sistema operativo convidado do Azure ([SO convidado](cloud-services-guestos-update-matrix.md)) para funções dos serviços Cloud web e de trabalho (PaaS). Não é aplicável a máquinas virtuais (IaaS).

A Microsoft tem um publicados [política de suporte para o SO convidado](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). A página que estiver lendo agora descreve como a política é implementada.

A política é

1. Microsoft suportará **, pelo menos, as duas famílias mais recente do SO convidado**. Quando uma família é extinto, os clientes têm de 12 meses a partir da data de retirada oficial para atualizar para uma família de SO convidado mais recente suportada.
2. Microsoft suportará **, pelo menos, as duas versões mais recentes das famílias de SO convidado suportadas**.
3. Microsoft suportará **, pelo menos, as duas versões mais recentes do SDK do Azure**. Quando uma versão do SDK é extinto, os clientes terão de 12 meses a partir da data de retirada oficial para atualizar para uma versão mais recente.

Às vezes, mais de duas famílias ou de versões podem ser suportado. As informações de suporte oficiais do SO convidado aparecerá no [versões do SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Quando uma versão de SO convidado é extinto
Novo SO convidado **versões** são introduzidas sobre todos os meses para incorporar as atualizações mais recentes do MSRC. Devido as atualizações mensais regulares, uma versão de SO convidado é, normalmente, desativada cerca de 60 dias após seu lançamento. Esta atividade mantém, pelo menos, duas versões de SO convidado para cada família disponível para utilização.

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante um extinção da família de SO convidado
Assim que é anunciada a descontinuação, os clientes têm um período de 12 meses "transição" antes da família mais antiga oficialmente é removida do serviço. Este período de transição pode ser estendido a critério do Microsoft. Atualizações serão publicadas na [versões do SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

Um processo de extinção gradual começará a seis (6) meses para o período de transição. Durante este período:

1. A Microsoft irá notificar os clientes a desativação.
2. A versão mais recente do SDK do Azure não suporta a família de SO convidado extinto.
3. Novas implementações e redeployments dos serviços Cloud não serão permitidas na família extinto

A Microsoft continuará a ter de introduzir a nova versão de SO convidado incorporar as atualizações mais recentes do MSRC até ao último dia do período de transição, conhecido como "data de expiração". Na data de expiração, serviços Cloud ainda em execução irá ser não suportados sob o SLA do Azure. A Microsoft tem o critério para forçar a atualização, elimine ou pare esses serviços após essa data.

### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante a desativação de uma versão do SO convidado
Se os clientes definir seu SO convidado para atualizar automaticamente, eles nunca precisam se preocupar sobre como lidar com as versões de SO convidado. Podem sempre utilizar a versão mais recente do SO convidado.

Versões de SO convidado são lançadas mensalmente. Devido a taxa de lançamentos regulares, cada versão tem um tempo de vida fixo.

Em 60 dias para o tempo de vida, é uma versão "*desativada*". "Desativado" significa que a versão é removida do portal. Já não pode ser definida a versão do arquivo de configuração de CSCFG. As implementações existentes são mantidas em execução. Mas novas Implantações e atualizações de código e a configuração para as implementações existentes não serão permitidas.

Algum tempo depois da tornar-se "desativado", a versão de SO convidado "expirar" e quaisquer instalações ainda em execução que a versão expirada estão expostas a segurança e problemas de vulnerabilidade. Em geral, a expiração é feita em lotes, para que o período de desabilitação para expiração pode variar.

Os clientes que configurar seus serviços para atualizar o SO convidado manualmente, deve certificar-se de que as respetivas implementações estão em execução num SO convidado suportados. Se um serviço estiver configurado para atualizar automaticamente o SO convidado, a plataforma subjacente será garantir a conformidade e irá atualizar para o SO convidado mais recente.

Estes períodos poderão ser estabelecidos mais tempo a critério da Microsoft para facilitar as transições de cliente. Quaisquer alterações serão comunicadas no [versões do SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Notificações durante a extinção
* **Extinção de família** <br>Microsoft irá utilizar mensagens de blogue e notificação do portal. Os clientes que ainda estão a utilizar uma família de SO convidado extinto serão notificados através da comunicação direta (e-mail, mensagens de portais, chamada telefónica) para os administradores de serviço atribuído. Todas as alterações serão publicadas para o [versões do SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).
* **Descontinuação da versão** <br>Todas as alterações e as datas que ocorrem serão publicadas para o [versões do SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md), incluindo o lançamento, desativado e expiração. Administradores de serviços irão receber e-mails se eles tiverem as implementações em execução numa versão de SO convidado desativado ou a família. A temporização dos mesmos pode variar. Em geral, eles são, pelo menos, um mês antes de desabilitação, embora neste momento não é um SLA oficial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Como posso reduzir o impacto da migração?**

Recomendamos que utilize a família de SO convidado mais recente para criar serviços Cloud.

1. Começar a planejar sua migração para uma família de mais recente no início.
2. Configure as implementações de teste temporário para testar o seu serviço Cloud em execução na nova família.
3. Definir a sua versão de SO convidado **automática** (osVersion = * no [. cscfg](cloud-services-model-and-package.md#cscfg) ficheiro), para a migração para novas versões de SO convidado ocorre automaticamente.

**E se meu aplicativo web requer uma integração mais profunda com o sistema operacional?**

Se a sua arquitetura de aplicativos web depende de recursos subjacentes do sistema operativo, utilize as capacidades de plataforma suportada, como [tarefas de arranque](cloud-services-startup-tasks.md) ou de outros mecanismos de extensibilidade. Em alternativa, também pode utilizar [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infraestrutura como serviço), onde é responsável por manter o sistema operacional subjacente.

## <a name="next-steps"></a>Passos Seguintes
Reveja a versão mais recente [versões de SO convidado](cloud-services-guestos-update-matrix.md).
