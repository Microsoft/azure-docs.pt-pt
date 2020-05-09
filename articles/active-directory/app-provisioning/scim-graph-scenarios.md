---
title: Utilize o SCIM, o Microsoft Graph e o Azure AD para fornecer aos utilizadores e enriquecer aplicações com dados
description: Utilizando o SCIM e o Microsoft Graph em conjunto para fornecer aos utilizadores e enriquecer a sua aplicação com os dados de que necessita.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: mimart
ms.reviewer: arvinh, celested
ms.openlocfilehash: 0b2c8e6bb71e00ccb6eda33ecb1b087d09ce5de7
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626195"
---
# <a name="using-scim-and-microsoft-graph-together-to-provision-users-and-enrich-your-application-with-the-data-it-needs"></a>Utilizando o SCIM e o Microsoft Graph em conjunto para fornecer aos utilizadores e enriquecer a sua aplicação com os dados de que necessita

**Público-alvo:** Este artigo destina-se a que os desenvolvedores construam aplicações a integrar com o Azure Ative Directory (Azure AD). Se procura utilizar aplicações já integradas com a Azure AD, como zoom, ServiceNow e DropBox, pode ignorar este artigo e rever os [tutoriais específicos](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) da aplicação ou rever como funciona o serviço de [provisionamento.](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

**Cenários comuns**

A Azure AD fornece um serviço fora da caixa para o provisionamento e uma plataforma extensível para construir as suas aplicações. A árvore de decisão descreve como um desenvolvedor usaria [o SCIM](https://aka.ms/scimoverview) e o [Microsoft Graph](https://docs.microsoft.com/graph/overview) para automatizar o fornecimento. 

> [!div class="checklist"]
> * Criar automaticamente utilizadores na minha aplicação
> * Remova automaticamente os utilizadores da minha aplicação quando já não deveriam ter acesso
> * Integrar a minha aplicação com vários fornecedores de identidade para o provisionamento
> * Enriqueça a minha aplicação com dados de serviços da Microsoft, tais como Teams, Outlook e Office.
> * Criar, atualizar e eliminar automaticamente utilizadores e grupos em Azure AD e Ative Diretório

![Árvore de decisão do gráfico sCIM](./media/user-provisioning/scim-graph.png)

## <a name="scenario-1-automatically-create-users-in-my-app"></a>Cenário 1: Criar automaticamente os utilizadores na minha app
Hoje em dia, o IT administra aos utilizadores através da criação manual de contas de utilizador ou do upload periódico de ficheiros CSV para a minha aplicação. O processo é demorado para os clientes e atrasa a adoção da minha aplicação. Tudo o que preciso é de informações básicas do utilizador, como nome, e-mail e userPrincipalName para criar um utilizador. 

**Recomendação:** 
* Se os seus clientes utilizarem vários IdPs e não pretender manter um motor de sincronização para integrar cada um, suporte um ponto final compatível com o SCIM [/Utilizadores.](https://aka.ms/scimreferencecode) Os seus clientes poderão utilizar facilmente este ponto final para se integrarem com o serviço de provisionamento de AD Azure e criarem automaticamente contas de utilizador quando precisarem de acesso. Pode construir o ponto final uma vez e será compatível com todos os IDPs. Confira o pedido de exemplo abaixo sobre como um utilizador seria criado usando o SCIM.
* Se necessitar de dados de utilizador encontrados no objeto de utilizador em AD Azure e outros dados de toda a Microsoft, considere a construção de um ponto final do SCIM para o fornecimento de utilizadores e chamada para o Microsoft Graph para obter o resto dos dados. 

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
    
## <a name="scenario-2-automatically-remove-users-from-my-app"></a>Cenário 2: Remova automaticamente os utilizadores da minha app
Os clientes que usam a minha aplicação estão focados na segurança e têm requisitos de governação para remover contas quando os funcionários já não precisam delas. Como posso automatizar a desprovisionamento da minha candidatura?

**Recomendação:** Suporte um ponto final compatível com o SCIM /Utilizadores. O serviço de provisionamento de AD Azure enviará pedidos para desativar e eliminar quando o utilizador não deveria ter mais acesso. Recomendamos apoiar tanto os utilizadores incapacitantes como a apagar. Veja os exemplos abaixo para saber como é um pedido de desativação e exclusão. 

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

## <a name="scenario-3-automate-managing-group-memberships-in-my-app"></a>Cenário 3: Automatizar membros do grupo na minha app
A minha aplicação baseia-se em grupos de acesso a vários recursos, e os clientes querem reutilizar os grupos que têm em Azure AD. Como posso importar grupos da AD Azure e mantê-los atualizados à medida que os membros mudam?  

**Recomendação:** Apoie um [ponto final](https://aka.ms/scimreferencecode)compatível com o SCIM /Grupos . O serviço de provisionamento de AD Azure cuidará da criação de grupos e da gestão de atualizações de adesão na sua aplicação. 

## <a name="scenario-4-enrich-my-app-with-data-from-microsoft-services-such-as-teams-outlook-and-onedrive"></a>Cenário 4: Enriqueça a minha aplicação com dados de serviços da Microsoft como Teams, Outlook e OneDrive
A minha aplicação está integrada nas Equipas microsoft e baseia-se em dados de mensagens. Além disso, armazenamos ficheiros para utilizadores no OneDrive. Como posso enriquecer a minha aplicação com os dados destes serviços e através da Microsoft?

**Recomendação:** O [Microsoft Graph](https://docs.microsoft.com/graph/) é o seu ponto de entrada para aceder aos dados da Microsoft. Cada carga de trabalho expõe APIs com os dados de que necessita. O gráfico da Microsoft pode ser utilizado juntamente com o [fornecimento de SCIM](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups) para os cenários acima indicados. Pode utilizar o SCIM para fornecer atributos básicos de utilizador na sua aplicação, enquanto chama o gráfico para obter quaisquer outros dados que necessite. 

## <a name="scenario-5-track-changes-in-microsoft-services-such-as-teams-outlook-and-azure-ad"></a>Cenário 5: Acompanhe as mudanças nos serviços da Microsoft, tais como Teams, Outlook e Azure AD
Preciso de ser capaz de rastrear as alterações nas mensagens Teams e Outlook e reagir a elas em tempo real. Como posso conseguir que estas alterações me empurrem para a minha candidatura?

**Recomendação:** O Microsoft Graph fornece [notificações](https://docs.microsoft.com/graph/webhooks) de alteração e [alterações](https://docs.microsoft.com/graph/delta-query-overview) no rastreio de vários recursos. Note as seguintes limitações das notificações de alteração:
- Se um recetor de eventos reconhecer um evento, mas não agir nele por qualquer motivo, o evento pode ser perdido.
- A ordem em que são recebidas alterações não é garantida como cronológica.
- As notificações de alteração nem sempre contêm os dados de [recursos](https://docs.microsoft.com/graph/webhooks-with-resource-data) Pelas razões acima referidas, os desenvolvedores usam frequentemente notificações de alteração juntamente com o rastreio de alterações para cenários de sincronização. 

## <a name="scenario-6-provision-users-and-groups-in-azure-ad"></a>Cenário 6: Fornecer utilizadores e grupos em Azure AD
A minha aplicação cria informações sobre um utilizador de que os clientes precisam em Anúncio saqueado do Azure. Esta pode ser uma aplicação de RH do que gerir a contratação, uma aplicação de comunicações que cria números de telefone para os utilizadores, ou qualquer outra aplicação que gere dados que seriam valiosos em Azure AD. Como povoo o registo de utilizadores em Azure AD com esses dados? 

**Recomendação** O gráfico da Microsoft expõe /Utilizadores e /Grupos pontos finais com os quais pode integrar hoje em dia para fornecer utilizadores em AD Azure. Por favor, note que o Azure Ative Directory não suporta escrever esses utilizadores de volta ao Ative Directory. 

> [!NOTE]
> A Microsoft tem um serviço de provisionamento que retira dados de aplicações de RH como Workday e SuccessFactors. Estas integrações são construídas e geridas pela Microsoft. Para embarcar numa nova aplicação de RH para o nosso serviço, pode solicitá-la no [UserVoice](https://feedback.azure.com/forums/374982-azure-active-directory-application-requests). 

## <a name="related-articles"></a>Artigos relacionados

- [Reveja a documentação de sincronização do Microsoft Graph](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Integrando uma aplicação SCIM personalizada com AD Azure](use-scim-to-provision-users-and-groups.md)
