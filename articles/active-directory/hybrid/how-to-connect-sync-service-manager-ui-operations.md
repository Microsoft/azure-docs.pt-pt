---
title: Operações de Gestor de Serviços de Sincronização da Azure AD Connect Microsoft Docs
description: Compreenda o separador Operações no Gestor de Serviços de Sincronização para o Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474000d1d4d7e1358682d1421125d482e3782049
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "60381428"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Utilizando o separador de operações do Gestor de Serviçosincronizado

![Gestor de Serviços de Sincronização](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

O separador de operações mostra os resultados das operações mais recentes. Este separador é fundamental para compreender e resolver problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Compreender a informação visível no separador de operações
A metade superior mostra todas as corridas por ordem cronológica. Por predefinição, o registo de operações mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [programador](how-to-connect-sync-feature-scheduler.md). Queres procurar qualquer corrida que não mostre um estatuto de sucesso. Pode alterar a classificação clicando nos cabeçalhos.

A coluna **Status** é a informação mais importante e mostra o problema mais grave para uma corrida. Aqui está um resumo rápido dos estatutos mais comuns por ordem prioritária para investigar (onde * indicar várias possíveis cadeias de erro).

| Estado | Comentário |
| --- | --- |
| parou.\* |A corrida não podia completar. Por exemplo, se o sistema remoto estiver avariado e não puder ser contactado. |
| parou-erro-limite |Há mais de 5.000 erros. A corrida foi automaticamente interrompida devido ao grande número de erros. |
| concluídos-\*-erros |A execução concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| concluídas-\*-avisos |A execução concluída, mas alguns dados não estão no estado esperado. Se tiver erros, então esta mensagem é geralmente apenas um sintoma. Até que tenha resolvido erros, não deve investigar avisos. |
| exito |Sem problemas. |

Quando selecionar uma linha, as atualizações inferiores para mostrar os detalhes dessa execução. Para a esquerda do fundo, pode ter uma lista a dizer **Passo #**. Esta lista só aparece se tiver vários domínios na sua floresta onde cada domínio é representado por um passo. O nome de domínio pode ser encontrado na rubrica **Partição**. De acordo com as Estatísticas de **Sincronização,** pode encontrar mais informações sobre o número de alterações que foram processadas. Pode clicar nos links para obter uma lista dos objetos alterados. Se tiver objetos com erros, esses erros aparecem em erros de **sincronização**.

Para mais informações, consulte [problemas sobre um objeto que não está sincronizado](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração de [sincronização azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
