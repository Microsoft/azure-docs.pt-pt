---
title: Guia de política de suporte e desativação para o SO convidado do Azure | Microsoft Docs
description: Fornece informações sobre o que a Microsoft dará suporte em relação ao sistema operacional convidado do Azure usado pelos serviços de nuvem.
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
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945455"
---
# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Política de suporte e desativação do SO convidado do Azure
As informações nesta página estão relacionadas ao sistema operacional convidado do Azure ([SO convidado](cloud-services-guestos-update-matrix.md)) para o trabalho de serviços de nuvem e as funções Web (PaaS). Ele não se aplica a máquinas virtuais (IaaS).

A Microsoft tem uma [política de suporte publicada para o SO convidado](https://support.microsoft.com/gp/azure-cloud-lifecycle-faq). A página que você está lendo agora descreve como a política é implementada.

A política é

1. A Microsoft dará suporte **a pelo menos as duas famílias mais recentes do sistema operacional convidado**. Quando uma família é desativada, os clientes têm 12 meses a partir da data de aposentadoria oficial para atualizar para uma família de sistemas operacionais convidados com suporte mais recente.
2. A Microsoft dará suporte **a pelo menos as duas versões mais recentes das famílias de sistemas operacionais convidados com suporte**.
3. A Microsoft dará suporte **a pelo menos as duas versões mais recentes do SDK do Azure**. Quando uma versão do SDK for desativada, os clientes terão 12 meses a partir da data de aposentadoria oficial para atualizar para uma versão mais recente.

Às vezes, podem ser oferecidas mais de duas famílias ou versões. As informações oficiais de suporte do sistema operacional convidado serão exibidas nas [versões do SO convidado do Azure e na matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).

## <a name="when-a-guest-os-version-is-retired"></a>Quando uma versão do sistema operacional convidado é desativada
Novas **versões** do sistema operacional convidado são introduzidas a cada mês para incorporar as atualizações mais recentes do MSRC. Devido às atualizações regulares mensais, uma versão do sistema operacional convidado normalmente é desabilitada cerca de 60 dias após seu lançamento. Essa atividade mantém pelo menos duas versões do sistema operacional convidado para cada família disponível para uso.

### <a name="process-during-a-guest-os-family-retirement"></a>Processo durante uma desativação da família do SO convidado
Depois que a aposentadoria é anunciada, os clientes têm um período de "transição" de 12 meses antes que a família mais antiga seja oficialmente removida do serviço. Esse tempo de transição pode ser estendido a critério da Microsoft. As atualizações serão publicadas nas [versões do SO convidado do Azure e na matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).

Um processo de aposentadoria gradual começará seis (6) meses no período de transição. Durante esse tempo:

1. A Microsoft notificará os clientes sobre a aposentadoria.
2. A versão mais recente do SDK do Azure não dará suporte à família de sistemas operacionais convidados desativada.
3. Novas implantações e renovações de serviços de nuvem não serão permitidas na família desativada

A Microsoft continuará apresentando a nova versão do sistema operacional convidado incorporando as atualizações mais recentes do MSRC até o último dia do período de transição, conhecido como "data de expiração". Na data de expiração, os serviços de nuvem ainda em execução não terão suporte no SLA do Azure. A Microsoft tem o critério de forçar a atualização, excluir ou interromper esses serviços após essa data.

### <a name="process-during-a-guest-os-version-retirement"></a>Processo durante uma desativação da versão do SO convidado
Se os clientes definirem seu sistema operacional convidado para atualização automática, eles nunca precisarão se preocupar em lidar com as versões do sistema operacional convidado. Eles sempre estarão usando a versão mais recente do SO convidado.

As versões do sistema operacional convidado são lançadas todos os meses. Devido à taxa de lançamentos regulares, cada versão tem um ciclo de vida fixo.

Às 60 dias no tempo de vida, uma versão é"desabilitada". "Disabled" significa que a versão é removida do Portal. A versão não pode mais ser definida a partir do arquivo de configuração CSCFG. As implantações existentes são deixadas em execução. Mas novas implantações e atualizações de código e configuração para implantações existentes não serão permitidas.

Algum tempo depois de se tornar "desabilitado", a versão do sistema operacional convidado "expira" e todas as instalações que ainda estiverem executando essa versão expirada serão expostas a problemas de segurança e vulnerabilidade. Geralmente, a expiração é feita em lotes e, portanto, o período de desativação para expiração pode variar.

Os clientes que configuram seus serviços para atualizar o SO convidado manualmente devem garantir que seus serviços estejam em execução em um SO convidado com suporte. Se um serviço estiver configurado para atualizar o SO convidado automaticamente, a plataforma subjacente garantirá a conformidade e será atualizada para o SO convidado mais recente.

Esses períodos podem ser mais longos a critério da Microsoft de facilitar as transições do cliente. Todas as alterações serão comunicadas nas [versões do SO convidado do Azure e na matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).

### <a name="notifications-during-retirement"></a>Notificações durante a aposentadoria
* **Aposentadoria da família** <br>A Microsoft usará Postagens de blog e notificação do Portal. Os clientes que ainda estiverem usando uma família de sistemas operacionais convidados desativados serão notificados por meio de comunicação direta (email, mensagens do portal, telefonema) para administradores de serviço atribuídos. Todas as alterações serão postadas nas [versões do SO convidado do Azure e na matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).
* **Desativação da versão** <br>Todas as alterações e as datas que elas ocorrem serão postadas nas [versões do SO convidado do Azure e na matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md), incluindo versão, desabilitação e expiração. OS administradores de serviços receberão emails se tiverem implantações em execução em uma versão ou família desabilitada do sistema operacional convidado. O tempo desses emails pode variar. Geralmente, eles são pelo menos um mês antes da desabilitação, embora esse tempo não seja um SLA oficial.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
**Como posso reduzir os impactos da migração?**

Recomendamos que você use a família de sistemas operacionais convidados mais recente para projetar seus serviços de nuvem.

1. Comece a planejar sua migração para uma família mais recente no início.
2. Configure implantações de teste temporárias para testar seu serviço de nuvem em execução na nova família.
3. Defina a versão do sistema operacional convidado como **automática** (OSVersion = * no arquivo [. cscfg](cloud-services-model-and-package.md#cscfg) ) para que a migração para novas versões do sistema operacional convidado ocorra automaticamente.

**E se meu aplicativo Web exigir uma integração mais profunda com o sistema operacional?**

Se a arquitetura do aplicativo Web depender de recursos subjacentes do sistema operacional, use recursos com suporte de plataforma, como [tarefas de inicialização](cloud-services-startup-tasks.md) ou outros mecanismos de extensibilidade. Como alternativa, você também pode usar as [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infraestrutura como serviço), em que você é responsável por manter o sistema operacional subjacente.

## <a name="next-steps"></a>Passos seguintes
Examine as versões mais recentes do [sistema operacional convidado](cloud-services-guestos-update-matrix.md).
