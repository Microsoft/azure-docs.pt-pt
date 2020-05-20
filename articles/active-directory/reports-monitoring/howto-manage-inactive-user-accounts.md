---
title: Como gerir contas de utilizadores inativos em Azure AD [ Microsoft Docs
description: Saiba como detetar e lidar com contas de utilizadores em AD Azure que se tornaram obsoletos
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/07/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f025a434c6db134237325a2f949b2a7c772672
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83679161"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Como: Gerir contas de utilizadores inativos em Azure AD

Em grandes ambientes, as contas dos utilizadores nem sempre são eliminadas quando os funcionários deixam uma organização. Como administrador de TI, pretende detetar e lidar com estas contas de utilizador obsoletas porque representam um risco de segurança.

Este artigo explica um método para lidar com contas de utilizadores obsoletas em Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>O que são contas de utilizadores inativos?

Contas inativas são contas de utilizador que já não são necessárias pelos membros da sua organização para ter acesso aos seus recursos. Um dos principais identificadores para contas inativas é que não são usados *há algum tempo* para iniciar sessão no seu ambiente. Como as contas inativas estão ligadas à atividade de inscrição, pode usar o carimbo de tempo do último sinal que foi bem sucedido em detetá-los. 

O desafio deste método é definir o que *significa por um tempo* no caso do seu ambiente. Por exemplo, os utilizadores podem não se inscrever num ambiente *por um tempo*, porque estão de férias. Ao definir qual é o seu delta para contas de utilizadores inativos, você precisa ter em conta todas as razões legítimas para não se inscrever no seu ambiente. Em muitas organizações, o delta para contas de utilizadores inativos é entre 90 e 180 dias. 

O último insessão bem sucedido fornece potenciais informações sobre a necessidade contínua de acesso aos recursos por parte de um utilizador.  Pode ajudar a determinar se a adesão ao grupo ou o acesso à aplicação ainda é necessário ou pode ser removido. Para a gestão externa do utilizador, pode compreender se um utilizador externo ainda está ativo dentro do inquilino ou se deve ser limpo. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Como detetar contas de utilizadores inativos

Deteta contas inativas avaliando a última propriedade **SignInDateTime** exposta pelo tipo de recurso **signInActivity** da Microsoft **Graph** API. Utilizando esta propriedade, pode implementar uma solução para os seguintes cenários:

- **Utilizadores por nome**: Neste cenário, procura um utilizador específico pelo nome, o que lhe permite avaliar o último SignInDateTime:`https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Utilizadores por data**: Neste cenário, solicita uma lista de utilizadores com um último SignInDateTime antes de uma data especificada:`https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`






## <a name="what-you-need-to-know"></a>O que tem de saber

Esta secção lista o que precisa de saber sobre a última propriedade SignInDateTime.

### <a name="how-can-i-access-this-property"></a>Como posso aceder a esta propriedade?

A última propriedade **SignInDateTime** é exposta pelo tipo de [recurso signInActivity](https://docs.microsoft.com/graph/api/resources/signinactivity?view=graph-rest-beta) do [Microsoft Graph REST API](https://docs.microsoft.com/graph/overview?view=graph-rest-beta#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>A última propriedade SignInDateTime está disponível através do cmdlet Get-AzureAdUser?

Não.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Que edição do Azure AD preciso para aceder à propriedade?

Você pode aceder a esta propriedade em todas as edições do Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Que permissão preciso para ler a propriedade?

Para ler este imóvel, você precisa conceder os seguintes direitos: 

- AuditLogs.Read.All
- Organização.Leia.Todos  


### <a name="when-does-azure-ad-update-the-property"></a>Quando é que o Azure AD atualiza a propriedade?

Cada sinal interativo que foi bem sucedido resulta numa atualização da loja de dados subjacente. Normalmente, os inscrições bem sucedidos aparecem no relatório de inscrição em 10 minutos.
 

### <a name="what-does-a-blank-property-value-mean"></a>O que significa um valor de propriedade em branco?

Para gerar uma última marca de tempoSignInDateTime, precisa de um sessão de acesso bem-sucedido. Como a última propriedade SignInDateTime é uma nova funcionalidade, o valor da última propriedade SignInDateTime pode ficar em branco se:

- O último início de inscrição bem-sucedido de um utilizador ocorreu antes de esta funcionalidade ser lançada (1 de dezembro de 2019).
- A conta de utilizador afetada nunca foi utilizada para um acesso bem sucedido.

## <a name="next-steps"></a>Passos seguintes

* [Obter dados com a API de relatórios do Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência da Auditoria API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Relatório de atividade de inatividade referência API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
