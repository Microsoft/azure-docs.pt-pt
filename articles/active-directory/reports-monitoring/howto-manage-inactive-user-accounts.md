---
title: Como gerir contas de utilizadores inativas em Azure AD | Microsoft Docs
description: Saiba como detetar e lidar com contas de utilizadores em Azure AD que se tornaram obsoletas
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535100"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Como: Gerir contas de utilizadores inativas em Azure AD

Em grandes ambientes, as contas de utilizador nem sempre são eliminadas quando os colaboradores deixam uma organização. Como administrador de TI, pretende detetar e lidar com estas contas de utilizador obsoletas porque representam um risco de segurança.

Este artigo explica um método para lidar com contas de utilizador obsoletas em Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>O que são contas de utilizador inativas?

As contas inativas são contas de utilizador que já não são exigidas pelos membros da sua organização para terem acesso aos seus recursos. Um dos principais identificadores para contas inativas é que não são *usados há algum tempo* para se inscreverem no seu ambiente. Como as contas inativas estão ligadas à atividade de inscrição, pode utilizar o tempotando do último sinal que foi bem sucedido para detetá-las. 

O desafio deste método é definir o que *durante algum tempo* significa no caso do seu ambiente. Por exemplo, os utilizadores podem não se inscrever num ambiente *durante algum tempo*, porque estão de férias. Ao definir qual é o seu delta para contas de utilizador inativas, precisa de ter em conta todas as razões legítimas para não iniciar sessão no seu ambiente. Em muitas organizações, o delta das contas de utilizadores inativas está entre 90 e 180 dias. 

O último sing-in bem sucedido fornece potenciais informações sobre a necessidade contínua de acesso de um utilizador aos recursos.  Pode ajudar a determinar se a adesão ao grupo ou o acesso a apps ainda são necessários ou podem ser removidos. Para a gestão externa do utilizador, pode entender se um utilizador externo ainda está ativo dentro do arrendatário ou deve ser limpo. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Como detetar contas de utilizadores inativas

Deteta contas inativas avaliando a **última propriedadeSignInDateTime** exposta pelo tipo de recurso **signInActivity** da **Microsoft Graph** API. Utilizando esta propriedade, pode implementar uma solução para os seguintes cenários:

- **Utilizadores pelo nome**: Neste cenário, procura um utilizador específico pelo nome, o que lhe permite avaliar o último SignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Utilizadores por data**: Neste cenário, solicita uma lista de utilizadores com um último SignInDateTime antes de uma data especificada: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Pode haver a necessidade de gerar um relatório do último sinal na data de todos os utilizadores, se assim puder utilizar o seguinte cenário.
> **Última s nota na data e hora para todos os utilizadores**: Neste cenário, solicita uma lista de todos os utilizadores e a última Última Hora do Sinal para cada respetivo utilizador: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>O que tem de saber

Esta secção lista o que precisa de saber sobre a última propriedadeSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Como posso aceder a esta propriedade?

A **última propriedadeSignInDateTime** é exposta pelo tipo de [recurso signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) do [Microsoft Graph REST API](/graph/overview#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>A última propriedade DoignInDateTime está disponível através do Get-AzureAdUser cmdlet?

N.º

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Que edição do Azure AD preciso para aceder à propriedade?

Você pode acessar esta propriedade em todas as edições do Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Que permissão preciso para ler a propriedade?

Para ler este imóvel, você precisa conceder os seguintes direitos: 

- AuditLogs.Read.All
- Organização.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Quando é que o Azure AD atualiza a propriedade?

Cada sindução interativa que foi bem sucedida resulta numa atualização da loja de dados subjacente. Normalmente, as entradas bem sucedidas aparecem no relatório de inscrição relacionado dentro de 10 minutos.
 

### <a name="what-does-a-blank-property-value-mean"></a>O que significa um valor de propriedade em branco?

Para gerar uma última marca de tempoSignInDateTime, precisa de um sinal de sposição bem-sucedido. Como a última propriedadeSignInDateTime é uma nova funcionalidade, o valor da última propriedadeSignInDateTime pode ficar em branco se:

- A última sdecisão bem sucedida de um utilizador ocorreu antes de abril de 2020.
- A conta de utilizador afetada nunca foi utilizada para uma sins insusição bem sucedida.

## <a name="next-steps"></a>Passos seguintes

* [Obter dados com a API de relatórios do Azure Active Directory com certificados](tutorial-access-api-with-certificates.md)
* [Referência de API de auditoria](/graph/api/resources/directoryaudit) 
* [Referência de API do relatório de atividade de inscrição](/graph/api/resources/signin)
