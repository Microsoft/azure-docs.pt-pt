---
title: Utilize o SCIM, o Microsoft Graph e o AD Azure para forrê-lo e enriquecer aplicações com dados
description: Utilizando o SCIM e o Microsoft Graph em conjunto para forrar os utilizadores e enriquecer a sua aplicação com os dados de que necessita.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: reference
ms.date: 04/26/2020
ms.author: kenwith
ms.reviewer: arvinh, celested
ms.openlocfilehash: b69e2c9b12b2db34f3eb70e54d2c6aede6b54784
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88235507"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Utilizando o SCIM e o Microsoft Graph em conjunto para obter utilizadores e enriquecer a sua aplicação com os dados de que necessita

**Público-alvo:** Este artigo destina-se a desenvolvedores que couem aplicações a integrar com o Azure Ative Directory (Azure AD). Se procura utilizar aplicações já integradas com Azure AD, como Zoom, ServiceNow e DropBox, pode ignorar este artigo e rever os [tutoriais específicos](../saas-apps/tutorial-list.md) da aplicação ou rever [como funciona o serviço de fornecimento.](./how-provisioning-works.md)

**Cenários comuns**

A Azure AD fornece um serviço fora da caixa para fornecimento e uma plataforma extensível para construir as suas aplicações. A árvore de decisão descreve como um desenvolvedor usaria [o SCIM](https://aka.ms/scimoverview) e o [Microsoft Graph](/graph/overview) para automatizar o provisionamento. 

> [!div class="checklist"]
> * Criar automaticamente utilizadores na minha aplicação
> * Remova automaticamente os utilizadores da minha aplicação quando não deveriam ter mais acesso
> * Integre a minha aplicação com vários fornecedores de identidade para provisionamento
> * Enriqueça a minha aplicação com dados de serviços da Microsoft como Teams, Outlook e Office.
> * Criar, atualizar e apagar automaticamente utilizadores e grupos em AD E Ative Directory

![Árvore de decisão do SCIM Graph](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Cenário 1: Criar automaticamente utilizadores na minha aplicação
Hoje em dia, a TI administra os utilizadores através da criação manual de contas de utilizador ou do envio periódico de ficheiros CSV para a minha aplicação. O processo é moroso para os clientes e atrasa a adoção da minha aplicação. Tudo o que preciso é de informações básicas do utilizador, como nome, e-mail e userPrincipalName para criar um utilizador. 

**Recomendação:** 
* Se os seus clientes utilizarem vários IDPs e não pretender manter um motor de sincronização para integrar com cada um deles, suporte um ponto final compatível com SCIM [/Utilizadores.](https://aka.ms/scimreferencecode) Os seus clientes poderão utilizar facilmente este ponto final para se integrarem no serviço de fornecimento de Azure AD e criarem automaticamente contas de utilizador quando precisarem de acesso. Pode construir o ponto final uma vez e será compatível com todos os IDPs. Confira o pedido de exemplo abaixo sobre como um utilizador seria criado usando o SCIM.
* Se necessitar de dados do utilizador encontrados no objeto do utilizador em AD Azure e outros dados de toda a Microsoft, considere a construção de um ponto final SCIM para o fornecimento de utilizadores e chamada para o Microsoft Graph para obter o resto dos dados. 

```json
POST /Users
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "BillG",
    "active": true,
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "Bill Gates",
        "familyName": "Gates",
        "givenName": "Bill"
    },
    "roles": []
}
```
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Cenário 2: Remover automaticamente os utilizadores da minha aplicação
Os clientes que usam a minha aplicação estão focados na segurança e têm requisitos de governação para remover contas quando os funcionários já não precisam deles. Como posso automatizar a desprovisionamento da minha candidatura?

**Recomendação:** Suporte um ponto final compatível com SCIM /Utilizadores. O serviço de fornecimento AZure AD enviará pedidos para desativar e apagar quando o utilizador não deve ter mais acesso. Recomendamos apoiar os utilizadores incapacitantes e apagados. Veja os exemplos abaixo para saber como é um pedido de desativação e apagar. 

Desativar o utilizador
```json
PATCH /Users/5171a35d82074e068ce2 HTTP/1.1
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```
Eliminar utilizador
```json
DELETE /Users/5171a35d82074e068ce2 HTTP/1.1
```

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Cenário 3: Automatizar a gestão de membros do grupo na minha app
A minha aplicação baseia-se em grupos de acesso a vários recursos, e os clientes querem reutilizar os grupos que têm no Azure AD. Como posso importar grupos da Azure AD e mantê-los atualizados à medida que os membros mudam?  

**Recomendação:** Suporte um [ponto final](https://aka.ms/scimreferencecode)compatível com SCIM /Grupos . O serviço de fornecimento de Ad Azure cuidará da criação de grupos e da gestão de atualizações de membros na sua aplicação. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Cenário 4: Enriqueça a minha app com dados de serviços da Microsoft como Teams, Outlook e OneDrive
A minha aplicação está integrada nas Equipas da Microsoft e baseia-se em dados de mensagens. Além disso, armazenamos ficheiros para utilizadores no OneDrive. Como posso enriquecer a minha aplicação com os dados destes serviços e em toda a Microsoft?

**Recomendação:** O [Microsoft Graph](/graph/) é o seu ponto de entrada para aceder aos dados da Microsoft. Cada carga de trabalho expõe APIs com os dados de que precisa. O gráfico da Microsoft pode ser utilizado juntamente com [o fornecimento scim](./use-scim-to-provision-users-and-groups.md) para os cenários acima. Pode utilizar o SCIM para obter atributos básicos do utilizador na sua aplicação enquanto liga para o gráfico para obter quaisquer outros dados de que necessite. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Cenário 5: Acompanhar as mudanças nos serviços da Microsoft, tais como Equipas, Outlook e AZure AD
Preciso de ser capaz de rastrear as alterações nas mensagens das equipas e do Outlook e reagir a elas em tempo real. Como posso fazer com que estas alterações se empurrem para a minha candidatura?

**Recomendação:** O Microsoft Graph fornece [notificações de alteração](/graph/webhooks) e [altera o rastreio](/graph/delta-query-overview) de vários recursos. Note as seguintes limitações das notificações de alteração:
- Se um recetor de eventos reconhecer um evento, mas não agir por qualquer motivo, o evento pode perder-se.
- A ordem em que as alterações são recebidas não é garantidamente cronológica.
- As notificações de alteração nem sempre contêm os [dados](/graph/webhooks-with-resource-data) dos recursos Pelas razões acima referidas, os desenvolvedores usam frequentemente notificações de alterações, juntamente com o rastreio de alterações para cenários de sincronização. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Cenário 6: Utilizadores e grupos de provisionamento em Azure AD
A minha aplicação cria informações sobre um utilizador de que os clientes precisam no Azure AD. Esta poderia ser uma aplicação DE RH do que gerir a contratação, uma app de comunicações que cria números de telefone para os utilizadores, ou qualquer outra app que gere dados que seriam valiosos no AD AZure. Como povoar o registo do utilizador em Azure AD com esses dados? 

**Recomendação** O gráfico da Microsoft expõe /Utilizadores e /Grupos pontos finais com os quais pode integrar-se hoje em dia para a provisionar os utilizadores em AD AZure. Por favor, note que o Azure Ative Directory não suporta escrever esses utilizadores de volta para o Ative Directory. 

> [!NOTE]
> A Microsoft tem um serviço de fornecimento que recossa dados de aplicações de RH como Workday e SuccessFactors. Estas integrações são construídas e geridas pela Microsoft. Para embarcar uma nova aplicação de RH para o nosso serviço, pode solicitá-la no [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Artigos relacionados

- [Reveja a sincronização da documentação do Microsoft Graph](/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrando uma app PERSONALIZADA SCIM com Azure AD](use-scim-to-provision-users-and-groups.md)