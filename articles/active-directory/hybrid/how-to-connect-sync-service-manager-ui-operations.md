---
title: Azure AD Connect Synchronization Service Manager Operations / Microsoft Docs
description: Compreenda o separador Operações no Gestor de Serviços de Sincronização do Azure AD Connect.
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
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85357108"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Utilização do separador De Operações do Gestor de Serviços de Sincronização

![Gestor de Serviço sincronizado](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

O separador de operações mostra os resultados das operações mais recentes. Este separador é fundamental para compreender e resolver problemas.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>Compreender a informação visível no separador de operações
A metade superior mostra tudo corre por ordem cronológica. Por predefinição, o registo de operações mantém informações sobre os últimos sete dias, mas esta definição pode ser alterada com o [programador](how-to-connect-sync-feature-scheduler.md). Quer procurar qualquer corrida que não mostre um estatuto de sucesso. Pode alterar a triagem clicando nos cabeçalhos.

A coluna **Status** é a informação mais importante e mostra o problema mais grave para uma corrida. Aqui está um resumo rápido dos estatutos mais comuns por ordem de prioridade para investigar (onde * indicar várias possíveis cordas de erro).

| Estado | Comentário |
| --- | --- |
| parado...\* |A corrida não pôde ser completada. Por exemplo, se o sistema remoto estiver em avaria e não puder ser contactado. |
| limite de erro parada |Há mais de 5.000 erros. A execução foi interrompida automaticamente devido ao grande número de erros. |
| \*concluídos- erros |A corrida foi concluída, mas há erros (menos de 5.000) que devem ser investigados. |
| concluído- \* avisos |A execução foi concluída, mas alguns dados não estão no estado esperado. Se tiver erros, esta mensagem geralmente é apenas um sintoma. Até que tenha abordado os erros, não deve investigar avisos. |
| exito |Sem problemas. |

Quando seleciona uma linha, as atualizações inferiores para mostrar os detalhes dessa execução. Para a extrema esquerda do fundo, pode ter uma lista a dizer **Step #**. Esta lista só aparece se tiver vários domínios na sua floresta onde cada domínio é representado por um passo. O nome de domínio pode ser encontrado sob o título **Partição**. Nas **Estatísticas de Sincronização,** pode encontrar mais informações sobre o número de alterações que foram processadas. Pode clicar nos links para obter uma lista dos objetos alterados. Se tiver objetos com erros, esses erros aparecem por **erros de sincronização**.

Para mais informações, consulte [resolução de problemas de um objeto que não está a sincronizar](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a configuração da [sincronização Azure AD Connect.](how-to-connect-sync-whatis.md)

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md).
