---
title: Automatizar o aprovisionamento de aplicações com SCIM no Azure Active Directory | Documentos da Microsoft
description: O Azure Active Directory podem aprovisionar automaticamente os utilizadores e grupos para qualquer arquivo de identidade ou de aplicação é apoiado por um serviço web com a interface definida na especificação de protocolo SCIM
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7e819551e7d85ccd039e23298b852302bba2d92
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807584"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Sistema de gestão de identidade entre domínios (SCIM) a utilizar para aprovisionar automaticamente os utilizadores e grupos do Azure Active Directory a aplicações

SCIM é padronizado de protocolo e o esquema que visa consistência maior de unidade em como as identidades são geridas em todos os sistemas. Quando um aplicativo oferece suporte a um ponto de extremidade SCIM para gestão de utilizadores, o serviço de aprovisionamento de utilizador do Azure AD pode enviar pedidos para criar, modificar ou eliminar os utilizadores atribuídos e grupos para este ponto final.

Muitos dos aplicativos para que suporte do Azure AD [pré-integradas aprovisionamento automático de utilizadores](../saas-apps/tutorial-list.md) implementar SCIM, conforme os meios para o utilizador de receber notificações de alteração.  Além destas, os clientes podem ligar-se aplicações que suportam um perfil específico do [SCIM 2.0 a especificação de protocolo](https://tools.ietf.org/html/rfc7644) utilizando a opção de integração de "externas à Galeria" genérico no portal do Azure.

O foco principal deste artigo é o perfil do SCIM 2.0, que implementa o Azure AD como parte do seu conector SCIM genérico para as aplicações externas à galeria. No entanto, com êxito de testes de um aplicativo que suporta SCIM com o Azure AD genérico o conector é um passo para um aplicativo listado na galeria do Azure AD como suportar o aprovisionamento de utilizadores. Para obter mais informações sobre como obter a sua aplicação na Galeria de aplicações do Azure AD, consulte [como: Inclua a sua aplicação na Galeria de aplicações do Azure AD](../develop/howto-app-gallery-listing.md).

> [!IMPORTANT]
> O comportamento da implementação do Azure AD SCIM foi atualizado pela última vez 18 de Dezembro de 2018. Para obter informações sobre o que foi alterado, consulte [SCIM 2.0 compatibilidade de protocolo do serviço aprovisionamento de utilizador do Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![Mostra o aprovisionamento do Azure AD para uma loja de aplicações ou de identidade][0]<br/>
*Figura 1: Aprovisionamento do Azure Active Directory a um arquivo de identidade ou de aplicativo que implementa SCIM*

Este artigo é dividido em quatro seções:

* **[Aprovisionamento de utilizadores e grupos para aplicações de terceiros que suportam SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  - se a sua organização está a utilizar uma aplicação de terceiros que implementa o perfil do SCIM 2.0 que o Azure AD suporta, pode começar a automatizar a ambos provisionamento e desprovisionamento de utilizadores e grupos hoje mesmo.
* **[Compreender a implementação do Azure AD SCIM](#understanding-the-azure-ad-scim-implementation)**  -se estiver a criar uma aplicação que suporte uma API de gerenciamento utilizador SCIM 2.0, esta secção descreve em detalhes como o cliente do Azure AD SCIM é implementado e como deve modelar seu protocolo SCIM solicitar manipulação e respostas.
* **[Criação de um ponto de extremidade SCIM usando bibliotecas Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  -bibliotecas do Common Language Infrastructure (CLI), juntamente com exemplos de código mostram-lhe como desenvolver um ponto de extremidade SCIM e traduzir mensagens SCIM.  
* **[Referência de esquema de utilizador e grupo](#user-and-group-schema-reference)**  -descreve o esquema de utilizador e grupo suportado pela implementação do Azure AD SCIM para as aplicações externas à galeria.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamento de utilizadores e grupos para aplicações que suportam SCIM

O Azure AD pode ser configurado para automaticamente os utilizadores de aprovisionamento atribuído e grupos de aplicações que implementar um perfil específico dos [protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644). Os detalhes do perfil de estão documentados em [Noções básicas sobre a implementação do Azure AD SCIM](#understanding-the-azure-ad-scim-implementation).

Contacte o seu fornecedor do aplicativo, ou na documentação do seu fornecedor de aplicações para declarações de estado de compatibilidade com esses requisitos.

> [!IMPORTANT]
> A implementação do Azure AD SCIM é criada sobre o serviço, que foi criado para manter os utilizadores sincronizados entre o Azure AD constantemente de aprovisionamento de utilizador do Azure AD e o aplicativo de destino e implementa um conjunto muito específico de operações padrão. É importante entender esses comportamentos para compreender o comportamento do cliente do Azure AD SCIM. Para obter mais informações, consulte [o que acontece durante o aprovisionamento do utilizador?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Introdução

Aplicações que suportam o perfil SCIM descrito neste artigo podem ser ligadas ao Azure Active Directory usando o recurso de "aplicativo externas à Galeria" na Galeria de aplicações do Azure AD. Assim que estiver ligado, do Azure AD é executado um processo de sincronização de todos os 40 minutos em que ele consulta o ponto final SCIM do aplicativo para os utilizadores atribuídos e grupos e cria ou modifica-los, de acordo com os detalhes de atribuição.

**Para ligar uma aplicação que suporte SCIM:**

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com). 
1. Selecione **aplicações empresariais** no painel à esquerda. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.
1. Selecione **+ nova aplicação** > **todos** > **aplicação da Galeria não**.
1. Introduza um nome para a sua aplicação e selecione **adicionar** para criar um objeto de aplicação. A nova aplicação é adicionada à lista de aplicações empresariais e abre-se para o ecrã de gestão de aplicação.

   ![Captura de ecrã mostra a Galeria de aplicações do Azure AD][1]<br/>
   *Figura 2: Galeria de aplicações do Azure AD*

1. No ecrã de gestão de aplicações, selecione **aprovisionamento** no painel esquerdo.
1. Na **modo de aprovisionamento** menu, selecione **automática**.

   ![Exemplo: Página de aprovisionamento de uma aplicação no portal do Azure][2]<br/>
   *Figura 3: Configurar o aprovisionamento no portal do Azure*

1. Na **URL de inquilino** campo, introduza o URL do ponto final SCIM do aplicativo. Exemplo: https://api.contoso.com/scim/v2/
1. Se o ponto de extremidade SCIM exige um token de portador do OAuth a partir de um emissor diferente do Azure AD, em seguida, copie o token de portador do OAuth necessário para o opcional **segredo de Token** campo.
1. Selecione **Testar ligação** ter o Azure Active Directory tentar estabelecer ligação ao ponto final do SCIM. Se a tentativa falhar, são apresentadas informações de erro.  

    > [!NOTE]
    > **Testar ligação** consulta o ponto de extremidade SCIM para um utilizador que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK, com uma mensagem de SCIM ListResponse vazia.

1. Se as tentativas de estabelecer ligação com êxito a aplicação, em seguida, selecione **guardar** para guardar as credenciais de administrador.
1. Na **mapeamentos** secção, existem dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de utilizador e uma para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os utilizadores e grupos na sua aplicação para operações de atualização. Selecione **guardar** para consolidar as alterações.

    > [!NOTE]
    > Opcionalmente, pode desativar a sincronização de objetos de grupo, desativando os mapeamento de "grupos".

1. Sob **configurações**, o **âmbito** campo define quais usuários e grupos são sincronizados. Selecione **sincronização atribuído apenas utilizadores e grupos** (recomendado) para sincronizar apenas os utilizadores e grupos atribuídos na **utilizadores e grupos** separador.
1. Depois da configuração estiver concluída, definir o **estado de aprovisionamento** ao **no**.
1. Selecione **guardar** para iniciar o serviço de aprovisionamento do AD do Azure.
1. Se a sincronização atribuído apenas a utilizadores e grupos (recomendados), certifique-se de que selecione os **utilizadores e grupos** separador e atribua os utilizadores ou grupos que pretende sincronizar.

Assim que tiver iniciado a sincronização inicial, pode selecionar **registos de auditoria** no painel à esquerda para monitorizar o progresso, que mostra todas as ações feitas pelo serviço de aprovisionamento na sua aplicação. Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](check-status-user-account-provisioning.md).

> [!NOTE]
> A sincronização inicial demora mais tempo a serem executados do que as sincronizações posteriores, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço está em execução.

## <a name="understanding-the-azure-ad-scim-implementation"></a>Compreender a implementação do Azure AD SCIM

Se estiver a criar uma aplicação que suporte uma API de gerenciamento utilizador SCIM 2.0, esta secção descreve em detalhes como o cliente do Azure AD SCIM é implementado e como deve modelar o seu protocolo SCIM e respostas do processamento do pedido. Assim que implementou o ponto final SCIM, poderá testá-la ao seguir o procedimento descrito na secção anterior.

Dentro de [SCIM 2.0 a especificação de protocolo](http://www.simplecloud.info/#Specification), a aplicação tem de cumprir estes requisitos:

* Suporta a criação de utilizadores e, opcionalmente, também grupos, de acordo com a secção [3.3 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Suporta a modificação de utilizadores ou grupos com os pedidos PATCH, como por [secção 3.5.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Suporta a recuperação de um recurso conhecido para um utilizador ou grupo que criou anteriormente, como por [secção 3.4.1 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Suporta a consulta de utilizadores ou grupos, de acordo com a secção [3.4.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por predefinição, os utilizadores são obtidos pelo respetivo `id` e consultados por seus `username` e `externalid`, e grupos são consultados por `displayName`.  
* Suporta a consulta de utilizador por ID e pelo gestor, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Suporta a consulta de grupos por ID e por membro, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Aceita um token de portador único para autenticação e autorização do Azure AD à sua aplicação.

Ao implementar um ponto de extremidade SCIM para garantir a compatibilidade com o Azure AD, siga estas Diretrizes gerais:

* `id` é uma propriedade necessária para todos os recursos. Cada resposta que retorna um recurso deve certificar-se cada recurso tem esta propriedade, exceto para `ListResponse` com zero membros.
* Resposta a um pedido de consulta/filtro deve ser sempre um `ListResponse`.
* Os grupos são opcionais, mas só suportada se a implementação de SCIM suporta pedidos de PATCH.
* Não é necessário incluir o recurso de todo na resposta de PATCH.
* Microsoft Azure AD apenas utiliza os seguintes operadores:  
     - `eq`
     - `and`
* Não necessitam de uma correspondência de maiúsculas e minúsculas nos elementos estruturais SCIM, em particular PATCH `op` valores de operação, conforme definido na https://tools.ietf.org/html/rfc7644#section-3.5.2. O Azure AD emite os valores de "op" como `Add`, `Replace`, e `Remove`.
* Microsoft Azure AD torna os pedidos para obter um utilizador aleatório e o grupo para se certificar de que o ponto final e as credenciais são válidas. Também é feito como parte da **Testar ligação** flow na [portal do Azure](https://portal.azure.com). 
* O atributo que os recursos podem ser consultados no deve ser definido como um atributo correspondente na aplicação no [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [personalizando utilizador aprovisionamento mapeamentos de atributos](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Utilizador provisionamento e desprovisionamento

A ilustração seguinte mostra as mensagens que o Azure Active Directory envia para um serviço SCIM para gerir o ciclo de vida de um utilizador no repositório de identidades da sua aplicação.  

![Mostra o utilizador de provisionamento e desprovisionamento sequência][4]<br/>
*Figura 4: Utilizador provisionamento e desprovisionamento sequência*

### <a name="group-provisioning-and-de-provisioning"></a>Grupo de provisionamento e desprovisionamento

Grupo de provisionamento e desprovisionamento é opcional. Quando implementada e ativada, a ilustração seguinte mostra as mensagens de benefícios do Azure AD envia para um serviço SCIM para gerir o ciclo de vida de um grupo no repositório de identidades da sua aplicação.  Essas mensagens são diferentes de mensagens sobre os utilizadores de duas formas:

* Pedidos para obter os grupos de especificar que o atributo de membros é a excluir de qualquer recurso fornecido em resposta ao pedido.  
* Os pedidos para determinar se um atributo de referência tem um determinado valor são pedidos sobre o atributo de membros.  

![Mostra o grupo de provisionamento e desprovisionamento sequência][5]<br/>
*Figura 5: Grupo de provisionamento e desprovisionamento sequência*

### <a name="scim-protocol-requests-and-responses"></a>SCIM protocolo solicitações e respostas
Esta seção fornece exemplo SCIM pedidos emitidos pelo cliente do Azure AD SCIM e exemplo respostas esperadas. Para obter melhores resultados, deve codificar sua aplicação para processar estes pedidos no seguinte formato e emitir as respostas esperadas.

> [!IMPORTANT]
> Para compreender como e quando o serviço de aprovisionamento de utilizador do Azure AD emite as operações descritas abaixo, veja [o que acontece durante o aprovisionamento do utilizador?](user-provisioning.md#what-happens-during-provisioning).

- [Operações de utilizador](#user-operations)
  - [Criar Utilizador](#create-user)
    - [Pedido](#request)
    - [Resposta](#response)
  - [Obter Utilizador](#get-user)
    - [Pedido](#request-1)
    - [Resposta](#response-1)
  - [Obter utilizador por consulta](#get-user-by-query)
    - [Pedido](#request-2)
    - [Resposta](#response-2)
  - [Obter utilizador por consulta - Zero resultados](#get-user-by-query---zero-results)
    - [Pedido](#request-3)
    - [Resposta](#response-3)
  - [Atualizar o utilizador [Propriedades com múltiplos valores]](#update-user-multi-valued-properties)
    - [Pedido](#request-4)
    - [Resposta](#response-4)
  - [Atualizar o utilizador [Propriedades de valor único]](#update-user-single-valued-properties)
    - [Pedido](#request-5)
    - [Resposta](#response-5)
  - [Eliminar Utilizador](#delete-user)
    - [Pedido](#request-6)
    - [Resposta](#response-6)
- [Operações de grupo](#group-operations)
  - [Criar Grupo](#create-group)
    - [Pedido](#request-7)
    - [Resposta](#response-7)
  - [Obter Grupo](#get-group)
    - [Pedido](#request-8)
    - [Resposta](#response-8)
  - [Obter grupo por displayName](#get-group-by-displayname)
    - [Pedido](#request-9)
    - [Resposta](#response-9)
  - [Grupo de atualização [atributos de não-membro]](#update-group-non-member-attributes)
    - [Pedido](#request-10)
    - [Resposta](#response-10)
  - [Grupo de atualização [Adicionar membros]](#update-group-add-members)
    - [Pedido](#request-11)
    - [Resposta](#response-11)
  - [Grupo de atualização [remover membros]](#update-group-remove-members)
    - [Pedido](#request-12)
    - [Resposta](#response-12)
  - [Eliminar Grupo](#delete-group)
    - [Pedido](#request-13)
    - [Resposta](#response-13)

### <a name="user-operations"></a>Operações de utilizador

* Os utilizadores podem ser consultados ao `userName` ou `email[type eq "work"]` atributos.  

#### <a name="create-user"></a>Criar utilizador

###### <a name="request"></a>Pedir

*POST /utilizadores*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Resposta

*HTTP/1.1 201 criado*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Obter utilizador

###### <a name="request-1"></a>Pedido
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Resposta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user-by-query"></a>Obter utilizador por consulta

##### <a name="request-2"></a>Pedido

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Obter utilizador por consulta - Zero resultados

##### <a name="request-3"></a>Pedido

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Atualizar o utilizador [Propriedades com múltiplos valores]

##### <a name="request-4"></a>Pedido

*Aplicar o PATCH/utilizadores/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Atualizar o utilizador [Propriedades de valor único]

##### <a name="request-5"></a>Pedido

*Aplicar o PATCH/utilizadores/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Eliminar utilizador

##### <a name="request-6"></a>Pedido

*Eliminar /Users/5171a35d82074e068ce2 HTTP/1.1.*

##### <a name="response-6"></a>Resposta

*HTTP/1.1 204 nenhum conteúdo*

### <a name="group-operations"></a>Operações de grupo

* Grupos sempre deverá ser criados com uma lista de membros vazio.
* Grupos podem ser consultados ao `displayName` atributo.
* Atualização para o pedido de PATCH de grupo deve resultar numa *HTTP 204 sem conteúdo* na resposta. Retornar um corpo de uma lista de todos os membros não é aconselhável.
* Não é necessário suportar a retornar todos os membros do grupo.

#### <a name="create-group"></a>Create Group

##### <a name="request-7"></a>Pedido

*/Groups de POSTAGEM HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Resposta

*HTTP/1.1 201 criado*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obter grupo

##### <a name="request-8"></a>Pedido

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>Resposta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obter grupo por displayName

##### <a name="request-9"></a>Pedido
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>Resposta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Grupo de atualização [atributos de não-membro]

##### <a name="request-10"></a>Pedido

*Aplicar o PATCH/grupos/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Resposta

*HTTP/1.1 204 nenhum conteúdo*

### <a name="update-group-add-members"></a>Grupo de atualização [Adicionar membros]

##### <a name="request-11"></a>Pedido

*Aplicar o PATCH/grupos/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Resposta

*HTTP/1.1 204 nenhum conteúdo*

#### <a name="update-group-remove-members"></a>Grupo de atualização [remover membros]

##### <a name="request-12"></a>Pedido

*Aplicar o PATCH/grupos/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Resposta

*HTTP/1.1 204 nenhum conteúdo*

#### <a name="delete-group"></a>Eliminar grupo

##### <a name="request-13"></a>Pedido

*Eliminar /Groups/cdb1ce18f65944079d37 HTTP/1.1.*

##### <a name="response-13"></a>Resposta

*HTTP/1.1 204 nenhum conteúdo*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Criação de um ponto de extremidade SCIM usando bibliotecas Microsoft CLI

Ao criar um serviço da web SCIM que faz interface com o Azure Active Directory, pode ativar o aprovisionamento automático de utilizadores para praticamente qualquer arquivo de identidade ou aplicação.

Eis como funciona:

1. O Azure AD fornece uma biblioteca de infraestrutura (CLI) de linguagem comum com o nome Microsoft.SystemForCrossDomainIdentityManagement, incluído no código exemplos descrevem abaixo. Integradores de sistemas e os programadores podem utilizar esta biblioteca para criar e implementar um ponto de extremidade de serviço web baseado em SCIM que pode ligar-se do Azure AD para o armazenamento de identidade de qualquer aplicativo.
2. Mapeamentos são implementados no serviço web para mapear o esquema de usuário padronizado para o esquema de utilizador e o protocolo necessário para a aplicação. 
3. O URL de ponto final está registado no Azure AD como parte de uma aplicação personalizada na Galeria de aplicações.
4. Utilizadores e grupos são atribuídos a esta aplicação no Azure AD. Após a atribuição, estão colocados numa fila sejam sincronizados com o aplicativo de destino. O processo de sincronização lidar com a fila é executado em 40 minutos.

### <a name="code-samples"></a>Exemplos de código

Para facilitar este processo, [exemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) são fornecidas, que criar um SCIM ponto final de serviço da web e demonstrar o aprovisionamento automático. O exemplo é de um provedor que mantém um ficheiro com linhas de valores separados por vírgulas que representam os utilizadores e grupos.

**Pré-requisitos**

* Visual Studio 2013 ou posterior
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) (Azure SDK para .NET)
* Windows máquina que suporta a estrutura do ASP.NET 4.5 para ser utilizado como o ponto de extremidade SCIM. Esta máquina tem de ser acessível a partir da cloud.
* [Uma subscrição do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Introdução

É a forma mais fácil de implementar um ponto de extremidade SCIM que pode aceitar pedidos de aprovisionamento do Azure AD criar e implementar o exemplo de código que produz os utilizadores aprovisionados para um ficheiro de valores separados por vírgulas (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Criar um ponto de final do exemplo SCIM

1. Transferir o pacote de exemplo de código em [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Deszipe o pacote e coloque-o no seu computador Windows num local, como C:\AzureAD-BYOA-Provisioning-Samples\.
1. Nesta pasta, inicie o projeto de FileProvisioning\Host\FileProvisioningService.csproj no Visual Studio.
1. Selecione **ferramentas** > **Gestor de pacotes NuGet** > **Package Manager Console**e execute os seguintes comandos para o Projeto de FileProvisioningService para resolver as referências de solução:

   ```powershell
    Update-Package -Reinstall
   ```

1. Crie o projeto de FileProvisioningService.
1. Iniciar o aplicativo de linha de comandos no Windows (como administrador) e utilizar o **cd** comandos para alterar o diretório para sua **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**pasta.
1. Execute o seguinte comando, substituindo `<ip-address>` com o nome de domínio ou endereço IP da máquina Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. No Windows sob **definições do Windows** > **rede & definições da Internet**, selecione o **Windows Firewall**  >   **Definições avançadas**e crie uma **regra de entrada** que permite o acesso de entrada para porta 9000.
1. Se a máquina do Windows estiver atrás de um roteador, o roteador tem de ser configurado para executar a tradução do acesso de rede entre a sua porta 9000 que é exposto à internet e a porta 9000 na máquina do Windows. Esta configuração é necessária para o Azure AD aceder a este ponto final na cloud.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Registar o ponto de final SCIM de exemplo no Azure AD

1. Inicie sessão para o [portal do Azure Active Directory](https://aad.portal.azure.com). 
1. Selecione **aplicações empresariais** no painel à esquerda. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.
1. Selecione **+ nova aplicação** > **todos** > **aplicação da Galeria não**.
1. Introduza um nome para a sua aplicação e selecione **adicionar** para criar um objeto de aplicação. O objeto de aplicativo criado destina-se para representar a aplicação de destino que seria o aprovisionamento e implementação de início de sessão único para e não apenas o ponto final SCIM.
1. No ecrã de gestão de aplicações, selecione **aprovisionamento** no painel esquerdo.
1. Na **modo de aprovisionamento** menu, selecione **automática**.    
1. Na **URL de inquilino** , insira o URL e a porta do ponto final do SCIM exposta de internet. A entrada é algo como http://testmachine.contoso.com:9000 ou http://\< ip-address >: 9000 /, onde \< ip-address > é a internet expostos IP endereço. 
1. Se o ponto de extremidade SCIM exige um token de portador do OAuth a partir de um emissor diferente do Azure AD, em seguida, copie o token de portador do OAuth necessário para o opcional **segredo de Token** campo. 
1. Selecione **Testar ligação** ter o Azure Active Directory tentar estabelecer ligação ao ponto final do SCIM. Se a tentativa falhar, são apresentadas informações de erro.  

    > [!NOTE]
    > **Testar ligação** consulta o ponto de extremidade SCIM para um utilizador que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK, com uma mensagem de SCIM ListResponse vazia

1. Se as tentativas de estabelecer ligação com êxito a aplicação, em seguida, selecione **guardar** para guardar as credenciais de administrador.
1. Na **mapeamentos** secção, existem dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de utilizador e uma para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os utilizadores e grupos na sua aplicação para operações de atualização. Selecione **guardar** para consolidar as alterações.
1. Sob **configurações**, o **âmbito** campo define quais os utilizadores e ou grupos são sincronizados. Selecione **"sincronização atribuído apenas utilizadores e grupos** (recomendado) para sincronizar apenas os utilizadores e grupos atribuídos na **utilizadores e grupos** separador.
1. Depois da configuração estiver concluída, definir o **estado de aprovisionamento** ao **no**.
1. Selecione **guardar** para iniciar o serviço de aprovisionamento do AD do Azure.
1. Se a sincronização atribuído apenas a utilizadores e grupos (recomendados), certifique-se de que selecione os **utilizadores e grupos** separador e atribua os utilizadores ou grupos que pretende sincronizar.

Assim que tiver iniciado a sincronização inicial, pode selecionar **registos de auditoria** no painel à esquerda para monitorizar o progresso, que mostra todas as ações feitas pelo serviço de aprovisionamento na sua aplicação. Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](check-status-user-account-provisioning.md).

A verificar o exemplo a etapa final é abrir o ficheiro de TargetFile.csv na pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador Windows. Assim que o processo de aprovisionamento é executado, este ficheiro mostra os detalhes de todos os atribuído e aprovisionamento de utilizadores e grupos.

### <a name="development-libraries"></a>Bibliotecas de programação

Para desenvolver seu próprio serviço da web que está em conformidade com a especificação de SCIM, primeiro familiarize-se com as seguintes bibliotecas fornecidas pela Microsoft para ajudar a acelerar o processo de desenvolvimento:

* Bibliotecas do Common Language Infrastructure (CLI) são oferecidas para utilização com linguagens com base em que a infraestrutura, como c#. Uma dessas bibliotecas, Microsoft.SystemForCrossDomainIdentityManagement.Service, declara uma interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, mostrada na ilustração seguinte. Um desenvolvedor que usa as bibliotecas seria implementar a interface com uma classe que pode ser direcionada para, mais genericamente, como um fornecedor. As bibliotecas permitem que o desenvolvedor implementar um serviço web que está em conformidade com a especificação de SCIM. O serviço web pode ser hospedado em serviços de informação Internet ou qualquer assembly executável do CLI. Pedido é convertido em chamadas para métodos do provedor, que poderiam ser programados pelo desenvolvedor para operar num armazenamento de identidade.
  
   ![Análise detalhada: Um pedido traduzido em chamadas para métodos do fornecedor][3]
  
* [Express manipuladores de rotas](https://expressjs.com/guide/routing.html) são feitas disponíveis para análise de objetos de solicitação de node. js que representam chamadas (conforme definido pela especificação SCIM), a um serviço web do node. js.

### <a name="building-a-custom-scim-endpoint"></a>Criação de um ponto de extremidade personalizado SCIM

Os desenvolvedores que usam as bibliotecas CLI podem alojar os seus serviços em qualquer assembly do executável da CLI ou dentro de serviços de informação Internet. Eis o código de exemplo para hospedar um serviço num assembly executável, no endereço http://localhost:9000: 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

Este serviço tem de ter um HTTP endereço e o servidor de certificado de autenticação dos quais a autoridade de certificação de raiz é um dos seguintes nomes: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Um certificado de autenticação de servidor pode ser associado a uma porta num anfitrião Windows usando o utilitário de shell de rede: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Aqui, o valor fornecido para o argumento de certhash é o thumbprint do certificado, enquanto o valor fornecido para o argumento de appid é um identificador exclusivo global arbitrário.  

Para hospedar o serviço dentro de serviços de informação Internet, um desenvolvedor criaria um assembly de biblioteca de código da CLI com uma classe chamada Startup no espaço de nomes padrão do assembly.  Eis um exemplo dessa classe: 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>Processar a autenticação de ponto final

Pedidos do Azure Active Directory incluem um token de portador do OAuth 2.0.   Qualquer serviço que o pedido a receber deve autenticar o emissor como sendo o Azure Active Directory para o inquilino do Azure Active Directory esperado, para acesso ao serviço da web do Azure Active Directory Graph.  No token, o emissor é identificado por uma afirmação de iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ".  Neste exemplo, o endereço base do valor de afirmação, https://sts.windows.net , identifica o Azure Active Directory como o emissor, enquanto o caminho relativo de segmento, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422 endereços, que é um identificador exclusivo do inquilino do Azure Active Directory para qual o token foi emitido.  Se o token foi emitido para acessar o serviço web do Azure Active Directory Graph, em seguida, o identificador do serviço, 00000002-0000-0000-c000-000000000000, deve estar no valor de afirmação de aud o token.  Cada um dos aplicativos que estão registados num único inquilino poderá receber o mesmo `iss` a afirmação com SCIM pedidos.

Os desenvolvedores que usam as bibliotecas CLI fornecidas pela Microsoft para a criação de um serviço SCIM podem autenticar os pedidos do Azure Active Directory utilizando o pacote de Microsoft.Owin.Security.ActiveDirectory seguindo estes passos: 

1. Num provedor, implemente a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior fazendo com que ele retorne um método a ser chamado sempre que o serviço é iniciado: 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

1. Adicione o seguinte código para esse método para que qualquer pedido para qualquer um dos pontos de extremidade do serviço autenticados como tendo um token emitido pelo Azure Active Directory para um inquilino especificado, para acesso ao serviço da web do Azure AD Graph: 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Manipulação de aprovisionamento e desaprovisionamento de utilizadores

1. O Azure Active Directory consulta o serviço para um utilizador com um valor de atributo externalId correspondência o valor do atributo mailNickname de um utilizador no Azure AD. A consulta é expressa como um pedido de protocolo HTTP (Hypertext Transfer), como neste exemplo, na qual jyoung é um exemplo de um mailNickname de um utilizador no Azure Active Directory.

    >[!NOTE]
    > Este é apenas um exemplo. Nem todos os utilizadores terão um atributo mailNickname, e o valor que tem de um utilizador pode não ser exclusivo no diretório. Além disso, o atributo utilizado para efetuar a correspondência (que neste caso é externalId) pode ser configurado no [mapeamentos de atributos do Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Se o serviço foi criado com as bibliotecas CLI fornecidas pela Microsoft para implementar os serviços SCIM, em seguida, o pedido é convertido numa chamada para o método de consulta do fornecedor do serviço.  Esta é a assinatura desse método: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Esta é a definição da Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Se o serviço foi criado com as bibliotecas de infra-estrutura de linguagem comum fornecidas pela Microsoft para implementar os serviços SCIM, em seguida, o pedido é convertido numa chamada para o método de consulta do fornecedor do serviço.  Esta é a assinatura desse método: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Esta é a definição da Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   No seguinte exemplo de uma consulta para um utilizador com um valor especificado para o atributo externalId, valores de argumentos transmitidos para o método de consulta são: 
   * parameters.AlternateFilters.Count: 1
   * parâmetros. AlternateFilters.ElementAt(0). AttributePath: externalId""
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parâmetros. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. Se a resposta a uma consulta para o serviço web para um utilizador com um valor de atributo externalId que corresponde ao valor de atributo mailNickname de um utilizador não retorna todos os utilizadores, em seguida, Azure Active Directory solicita que o serviço aprovisionamento de um utilizador correspondente ao no Azure Active Directory.  Eis um exemplo de pedido: 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   As bibliotecas CLI fornecidas pela Microsoft para implementar os serviços SCIM converte esse pedido numa chamada para o método de criação do fornecedor do serviço.  O método Create tem esta assinatura: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   Num pedido de aprovisionamento de um utilizador, o valor do argumento de recursos é uma instância do Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, definida na biblioteca do Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se o pedido para aprovisionar o utilizador for bem-sucedida, a implementação do método é esperada para retornar uma instância do Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, com o valor da propriedade Identificador definido como o identificador exclusivo do utilizador recentemente aprovisionado.  

1. Para atualizar um utilizador sabe existirem num arquivo de identidade apoiado por um SCIM, o Azure Active Directory continua ao pedir o estado atual do que o utilizador do serviço com um pedido, tais como: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   Num serviço criado com as bibliotecas CLI fornecidas pela Microsoft para implementar os serviços SCIM, o pedido é convertido numa chamada para o método de recuperação do fornecedor do serviço.  Esta é a assinatura do método obter: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   No exemplo de um pedido para obter o estado atual de um utilizador, os valores das propriedades do objeto fornecido como o valor do argumento parâmetros são os seguintes: 
  
   * Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. Se um atributo de referência está a ser atualizado, em seguida, Azure Active Directory consulta o serviço para determinar que se o valor atual do atributo de referência no arquivo de identidade apoiado pelo serviço já corresponde ao valor desse atributo no Active Directory do Azure Diretório. Para os utilizadores, o único atributo que o valor atual é consultado dessa maneira é o atributo de gestor. Eis um exemplo de um pedido para determinar se o atributo de Gestor de um objeto de utilizador em particular tem atualmente um determinado valor: 

   Se o serviço foi criado com as bibliotecas CLI fornecidas pela Microsoft para implementar os serviços SCIM, em seguida, o pedido é convertido numa chamada para o método de consulta do fornecedor do serviço. O valor das propriedades do objeto fornecido como o valor do argumento parâmetros são os seguintes: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parâmetros. AlternateFilters.ElementAt(y). AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Aqui, o valor do índice x pode ser 0 e o valor de y o índice pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões de parâmetro de consulta de filtro.   

1. Eis um exemplo de um pedido do Azure Active Directory a um serviço SCIM para atualizar um utilizador: 
   ```
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```
   As bibliotecas de Microsoft CLI para implementar os serviços SCIM converte o pedido numa chamada para o método de atualização do fornecedor do serviço. Esta é a assinatura do método de atualização: 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   Se o serviço foi criado com as bibliotecas de infra-estrutura de linguagem comum fornecidas pela Microsoft para implementar os serviços SCIM, em seguida, o pedido é convertido numa chamada para o método de consulta do fornecedor do serviço. O valor das propriedades do objeto fornecido como o valor do argumento parâmetros são os seguintes: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parâmetros. AlternateFilters.ElementAt(y). AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Aqui, o valor do índice x pode ser 0 e o valor de y o índice pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões de parâmetro de consulta de filtro.   

1. Eis um exemplo de um pedido do Azure Active Directory a um serviço SCIM para atualizar um utilizador: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   As bibliotecas de infra-estrutura de linguagem comum do Microsoft para implementar os serviços SCIM converte o pedido numa chamada para o método de atualização do fornecedor do serviço. Esta é a assinatura do método de atualização: 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    No exemplo de um pedido para atualizar um utilizador, o objeto fornecido como o valor do argumento de patch tem estes valores de propriedade: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest como PatchRequest2). Operations.Count: 1
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.Count: 1
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referência: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valor: 2819c223-7f76-453a-919d-413861904646

1. Desaprovisionar um utilizador de um arquivo de identidade apoiado por um serviço SCIM, do Azure AD envia um pedido, tais como: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Se o serviço foi criado com as bibliotecas de infra-estrutura de linguagem comum fornecidas pela Microsoft para implementar os serviços SCIM, em seguida, o pedido é convertido numa chamada para o método Delete do fornecedor do serviço.   Esse método tem esta assinatura: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   O objeto fornecido como o valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de um pedido para desaprovisionar um utilizador: 

1. Desaprovisionar um utilizador de um arquivo de identidade apoiado por um serviço SCIM, do Azure AD envia um pedido, tais como: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Se o serviço foi criado com as bibliotecas CLI fornecidas pela Microsoft para implementar os serviços SCIM, em seguida, o pedido é convertido numa chamada para o método Delete do fornecedor do serviço.   Esse método tem esta assinatura: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   O objeto fornecido como o valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de um pedido para desaprovisionar um utilizador: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Referência de esquema de utilizador e grupo

O Azure Active Directory pode aprovisionar dois tipos de recursos para serviços da web SCIM.  Esses tipos de recursos são utilizadores e grupos.  

Recursos de utilizador são identificados pelo identificador de esquema, `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, que está incluído nessa especificação de protocolo: https://tools.ietf.org/html/rfc7643.  O mapeamento predefinido de atributos de utilizadores no Azure Active Directory para os atributos de recursos de utilizador é fornecido na tabela 1.  

Grupo de recursos é identificados pelo identificador de esquema, `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabela 2 mostra o mapeamento predefinido de atributos de grupos no Azure Active Directory para os atributos do grupo de recursos.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Mapeamento de atributo de utilizador padrão

| Utilizador do Active Directory do Azure | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |ativo |
| displayName |displayName |
| TelephoneNumber de fax |. Value de phoneNumbers [tipo eq "fax"] |
| givenName |name.givenName |
| jobTitle |título |
| mail |. Value de e-mails [tipo eq "trabalho"] |
| mailNickname |externalId |
| gestor |gestor |
| móvel |. Value de phoneNumbers [tipo eq "móvel"] |
| objectId |ID |
| postalCode |.postalCode endereços [tipo eq "trabalho"] |
| Endereços de proxy |e-mails de [tipo é igual a "other"]. Valor |
| physical-Delivery-OfficeName |endereços [tipo é igual a "other"]. Formatado |
| streetAddress |.streetAddress endereços [tipo eq "trabalho"] |
| Apelido |name.familyName |
| Número de telefone |. Value de phoneNumbers [tipo eq "trabalho"] |
| utilizador PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapeamento de atributo de grupo predefinido

| Grupo do Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |. Value de e-mails [tipo eq "trabalho"] |
| mailNickname |displayName |
| membros |membros |
| objectId |ID |
| proxyAddresses |e-mails de [tipo é igual a "other"]. Valor |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Permitir que o IP endereços utilizados pelo Azure AD do serviço de aprovisionamento para fazer pedidos SCIM

Determinadas aplicações permitem tráfego de entrada para a respetiva aplicação. Por ordem para o serviço de aprovisionamento do AD Azure a funcionar conforme esperado, os endereços IP utilizados têm de ser permitidos. Para obter uma lista de endereços IP para cada etiqueta de serviço/região, consulte o ficheiro JSON - [intervalos de IP do Azure e as etiquetas de serviço – Cloud pública](https://www.microsoft.com/download/details.aspx?id=56519). Pode transferir e programadas estes IPs para seu firewall, conforme necessário. Os intervalos IP reservados para o aprovisionamento do Azure AD podem ser encontrados em "AzureActiveDirectoryDomainServices."

## <a name="related-articles"></a>Artigos relacionados

* [Automatizar o utilizador aprovisionamento/desaprovisionamento às aplicações SaaS](user-provisioning.md)
* [Personalizar mapeamentos de atributos para o aprovisionamento do utilizador](customize-application-attributes.md)
* [Escrever expressões para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Filtros de âmbito para o aprovisionamento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notificações do aprovisionamento de contas](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
