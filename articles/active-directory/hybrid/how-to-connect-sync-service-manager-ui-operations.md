---
title: Operações de Gestor do serviço de sincronização do Azure AD Connect | Documentos da Microsoft
description: Compreenda o guia de operações no serviço Gerenciador de sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/13/2017
ms.date: 11/12/2018
ms.component: hybrid
ms.author: v-junlch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381428"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Usando a guia de operações de Gestor do serviço de sincronização

![Gestor do serviço de sincronização](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

A guia de operações mostra os resultados das operações mais recentes. Este separador é fundamental para compreender e resolver problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Compreender as informações visíveis na guia operações
A metade superior mostra todas as execuções por ordem cronológica. Por predefinição, as operações de registo mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [agendador](how-to-connect-sync-feature-scheduler.md). Deve procurar qualquer execução que não mostra um Estado de êxito. Pode alterar a ordenação ao clicar nos cabeçalhos.

O **estado** coluna são as informações mais importantes e mostra o problema mais grave para uma execução. Aqui está um resumo rápido dos Estados mais comuns na ordem de prioridade para investigar (onde * indicar várias cadeias de caracteres de erro possíveis).

| Estado | Comentário |
| --- | --- |
| stopped-\* |Não foi possível concluir a execução. Por exemplo, se o sistema remoto está inativo e não pode ser contactado. |
| stopped-error-limit |Há mais de 5.000 erros. A execução automaticamente foi parada devido ao grande número de erros. |
| concluída -\*-erros |A execução foi concluída, mas existem erros (menos de 5.000), que devem ser investigados. |
| concluída -\*-avisos |A execução foi concluída, mas alguns dados não está no estado esperado. Se tiver erros, em seguida, esta mensagem é, normalmente, apenas um sintoma. Até que a corrigir esses erros, avisos não deve investigar. |
| exito |Não existem problemas. |

Quando seleciona uma linha, atualiza a parte inferior para mostrar os detalhes de que são executados. Mais à esquerda da parte inferior, terá de indicar uma lista **passo n. º**. Esta lista aparece apenas se tiver vários domínios na sua floresta em que cada domínio é representado por um passo. O nome de domínio pode ser encontrado no cabeçalho **partição**. Sob **estatísticas de sincronização**, pode encontrar mais informações sobre o número de alterações que foram processados. Pode clicar nas hiperligações para obter uma lista dos objetos alterados. Se tiver objetos com erros, esses erros apresentados no **erros de sincronização**.

Para obter mais informações, consulte [resolver problemas de um objeto que não está a sincronizar](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [do Azure AD Connect](how-to-connect-sync-whatis.md) configuração.

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).

