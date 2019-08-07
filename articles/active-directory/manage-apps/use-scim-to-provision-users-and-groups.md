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
ms.date: 07/31/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48eb36151948dc6e39edd4ae0fd863c63e83a52b
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741354"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Sistema de gestão de identidade entre domínios (SCIM) a utilizar para aprovisionar automaticamente os utilizadores e grupos do Azure Active Directory a aplicações

O SCIM é um protocolo e esquema padronizados que visam aumentar a consistência na forma como as identidades são gerenciadas entre os sistemas. Quando um aplicativo dá suporte a um ponto de extremidade SCIM para gerenciamento de usuário, o serviço de provisionamento de usuário do Azure AD pode enviar solicitações para criar, modificar ou excluir usuários e grupos atribuídos a esse ponto de extremidade.

Muitos dos aplicativos para os quais o Azure AD dá suporte ao [provisionamento de usuário automático autônomo](../saas-apps/tutorial-list.md) implementam scim como o meio de receber notificações de alteração do usuário.  Além disso, os clientes podem conectar aplicativos que dão suporte a um perfil específico da [especificação do protocolo SCIM 2,0](https://tools.ietf.org/html/rfc7644) usando a opção de integração genérica "não Galeria" no portal do Azure.

O foco principal deste artigo é o perfil do SCIM 2,0 que o Azure AD implementa como parte de seu conector SCIM genérico para aplicativos que não são da galeria. No entanto, o teste bem-sucedido de um aplicativo que dá suporte a SCIM com o conector do Azure AD genérico é uma etapa para obter um aplicativo listado na galeria do Azure AD como suporte ao provisionamento de usuário. Para obter mais informações sobre como colocar seu aplicativo listado na Galeria de aplicativos do Azure [AD, consulte Como: Liste seu aplicativo na Galeria](../develop/howto-app-gallery-listing.md)de aplicativos do Azure AD.

> [!IMPORTANT]
> O comportamento da implementação de SCIM do Azure AD foi atualizado pela última vez em 18 de dezembro de 2018. Para obter informações sobre o que mudou, consulte [conformidade do protocolo SCIM 2,0 do serviço de provisionamento de usuário do Azure ad](application-provisioning-config-problem-scim-compatibility.md).

![Mostra o provisionamento do Azure AD para um aplicativo ou repositório de identidades][0]<br/>
*Figura 1: Provisionamento de Azure Active Directory para um aplicativo ou repositório de identidade que implementa SCIM*

Este artigo é dividido em quatro seções:

* **[Provisionando usuários e grupos para aplicativos de terceiros que dão suporte ao SCIM 2,0](#provisioning-users-and-groups-to-applications-that-support-scim)** – se sua organização estiver usando um aplicativo de terceiros que implemente o perfil do scim 2,0 ao qual o Azure ad dá suporte, você poderá começar a automatizar o provisionamento e desprovisionamento de usuários e grupos hoje.
* **[Compreendendo a implementação de SCIM do Azure ad](#understanding-the-azure-ad-scim-implementation)** – se você estiver criando um aplicativo que dá suporte a uma API de gerenciamento de usuário do scim 2,0, esta seção descreve detalhadamente como o cliente scim do Azure AD é implementado e como você deve modelar o protocolo scim tratamento de solicitação e respostas.
* **[Criar um ponto de extremidade scim usando](#building-a-scim-endpoint-using-microsoft-cli-libraries)** bibliotecas da CLI (biblioteca da Microsoft) Common Language Infrastructure bibliotecas (CLI), juntamente com exemplos de código, mostram como desenvolver um ponto de extremidade scim e converter mensagens SCIM.  
* **[Referência de esquema de usuário e grupo](#user-and-group-schema-reference)** -descreve o esquema de usuário e grupo com suporte da implementação scim do Azure ad para aplicativos que não são da galeria.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamento de utilizadores e grupos para aplicações que suportam SCIM

O Azure AD pode ser configurado para provisionar automaticamente usuários e grupos atribuídos a aplicativos que implementam um perfil específico do [protocolo SCIM 2,0](https://tools.ietf.org/html/rfc7644). As especificidades do perfil são documentadas em [noções básicas sobre a implementação do scim do Azure ad](#understanding-the-azure-ad-scim-implementation).

Contacte o seu fornecedor do aplicativo, ou na documentação do seu fornecedor de aplicações para declarações de estado de compatibilidade com esses requisitos.

> [!IMPORTANT]
> A implementação do Azure AD SCIM é criada sobre o serviço de provisionamento de usuários do Azure AD, que é projetado para manter constantemente os usuários sincronizados entre o Azure AD e o aplicativo de destino e implementa um conjunto muito específico de operações padrão. É importante entender esses comportamentos para entender o comportamento do cliente SCIM do Azure AD. Para obter mais informações, consulte [o que acontece durante o provisionamento do usuário?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Introdução

Aplicações que suportam o perfil SCIM descrito neste artigo podem ser ligadas ao Azure Active Directory usando o recurso de "aplicativo externas à Galeria" na Galeria de aplicações do Azure AD. Assim que estiver ligado, do Azure AD é executado um processo de sincronização de todos os 40 minutos em que ele consulta o ponto final SCIM do aplicativo para os utilizadores atribuídos e grupos e cria ou modifica-los, de acordo com os detalhes de atribuição.

**Para ligar uma aplicação que suporte SCIM:**

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com). 
1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo os aplicativos que foram adicionados da galeria.
1. Selecione **+ novo aplicativo** > **todos os** > **aplicativos que não são da Galeria**.
1. Insira um nome para seu aplicativo e selecione **Adicionar** para criar um objeto de aplicativo. O novo aplicativo é adicionado à lista de aplicativos empresariais e é aberto em sua tela de gerenciamento de aplicativo.

   ![Captura de tela mostra a Galeria de aplicativos do Azure AD][1]<br/>
   *Figura 2: Galeria de aplicativos do Azure AD*

1. Na tela gerenciamento de aplicativos, selecione **provisionamento** no painel esquerdo.
1. Na **modo de aprovisionamento** menu, selecione **automática**.

   ![Exemplo: A página de provisionamento de um aplicativo no portal do Azure][2]<br/>
   *Figura 3: Configurando o provisionamento no portal do Azure*

1. Na **URL de inquilino** campo, introduza o URL do ponto final SCIM do aplicativo. Exemplo: https://api.contoso.com/scim/
1. Se o ponto de extremidade SCIM exige um token de portador do OAuth a partir de um emissor diferente do Azure AD, em seguida, copie o token de portador do OAuth necessário para o opcional **segredo de Token** campo. Se esse campo for deixado em branco, o Azure AD incluirá um token de portador OAuth emitido pelo Azure AD com cada solicitação. Os aplicativos que usam o Azure AD como um provedor de identidade podem validar esse token emitido pelo Azure AD.
1. Selecione **testar conexão** para que Azure Active Directory tente se conectar ao ponto de extremidade SCIM. Se a tentativa falhar, as informações de erro serão exibidas.  

    > [!NOTE]
    > O **Test Connection** consulta o ponto de extremidade scim para um usuário que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK com uma mensagem SCIM ListResponse vazia.

1. Se as tentativas de conexão com o aplicativo forem bem-sucedidos, selecione **salvar** para salvar as credenciais de administrador.
1. Na **mapeamentos** secção, existem dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de utilizador e uma para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os utilizadores e grupos na sua aplicação para operações de atualização. Selecione **salvar** para confirmar as alterações.

    > [!NOTE]
    > Opcionalmente, pode desativar a sincronização de objetos de grupo, desativando os mapeamento de "grupos".

1. Em **configurações**, o campo **escopo** define quais usuários e grupos são sincronizados. Selecione **sincronizar somente usuários e grupos atribuídos** (recomendado) para sincronizar somente usuários e grupos atribuídos na guia **usuários e grupos** .
1. Quando a configuração for concluída, defina o **status de provisionamento** como **ativado**.
1. Selecione **salvar** para iniciar o serviço de provisionamento do Azure AD.
1. Se estiver sincronizando apenas usuários e grupos atribuídos (recomendado), selecione a guia **usuários e grupos** e atribua os usuários ou grupos que você deseja sincronizar.

Depois que a sincronização inicial for iniciada, você poderá selecionar **logs de auditoria** no painel esquerdo para monitorar o progresso, que mostra todas as ações realizadas pelo serviço de provisionamento em seu aplicativo. Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](check-status-user-account-provisioning.md).

> [!NOTE]
> A sincronização inicial demora mais para ser executada do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução.

## <a name="understanding-the-azure-ad-scim-implementation"></a>Noções básicas sobre a implementação de SCIM do Azure AD

Se você estiver criando um aplicativo que dá suporte a uma API de gerenciamento de usuário do SCIM 2,0, esta seção descreve detalhadamente como o cliente SCIM do Azure AD é implementado e como você deve modelar suas respostas e tratamento de solicitação de protocolo SCIM. Depois de implementar o ponto de extremidade do SCIM, você pode testá-lo seguindo o procedimento descrito na seção anterior.

Na [especificação do protocolo SCIM 2,0](http://www.simplecloud.info/#Specification), seu aplicativo deve atender a esses requisitos:

* Dá suporte à criação de usuários e, opcionalmente, também a grupos, de acordo com [a seção 3,3 do protocolo scim](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Dá suporte à modificação de usuários ou grupos com solicitações de PATCH, de acordo com [a seção 3.5.2 do protocolo scim](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Dá suporte à recuperação de um recurso conhecido para um usuário ou grupo criado anteriormente, de acordo com [a seção 3.4.1 do protocolo scim](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Dá suporte à consulta de usuários ou grupos, de acordo com [a seção 3.4.2 do protocolo scim](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por padrão, os usuários são recuperados `id` por seus e consultados `username` por `externalid`seus e, e os grupos são `displayName`consultados pelo.  
* Dá suporte à consulta de usuário por ID e pelo gerente, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Dá suporte à consulta de grupos por ID e por membro, de acordo com a seção 3.4.2 do protocolo SCIM.  
* Aceita um único token de portador para autenticação e autorização do Azure AD para seu aplicativo.

Siga estas diretrizes gerais ao implementar um ponto de extremidade SCIM para garantir a compatibilidade com o Azure AD:

* `id`é uma propriedade obrigatória para todos os recursos. Cada resposta que retorna um recurso deve garantir que cada recurso tenha essa propriedade, exceto `ListResponse` por zero Members.
* A resposta a uma solicitação de consulta/filtro deve ser `ListResponse`sempre uma.
* Os grupos são opcionais, mas só têm suporte se a implementação do SCIM der suporte a solicitações de PATCH.
* Não é necessário incluir o recurso inteiro na resposta do PATCH.
* Microsoft Azure AD usa apenas os seguintes operadores:  
     - `eq`
     - `and`
* Não exija uma correspondência que diferencia maiúsculas de minúsculas em elementos estruturais em scim `op` , em determinados valores de operação https://tools.ietf.org/html/rfc7644#section-3.5.2 de patch, conforme definido em. O Azure ad emite os valores de ' op ' como `Add`, `Replace`e `Remove`.
* Microsoft Azure AD faz com que as solicitações busquem um usuário aleatório e um grupo para garantir que o ponto de extremidade e as credenciais sejam válidos. Isso também é feito como parte do fluxo de **conexão de teste** no [portal do Azure](https://portal.azure.com). 
* O atributo no qual os recursos podem ser consultados deve ser definido como um atributo correspondente no aplicativo na [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte Personalizando mapeamentos de [atributo de provisionamento de usuário](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Utilizador provisionamento e desprovisionamento

A ilustração a seguir mostra as mensagens que Azure Active Directory envia para um serviço SCIM para gerenciar o ciclo de vida de um usuário no repositório de identidades do aplicativo.  

![Mostra a sequência de provisionamento e desprovisionamento de usuário][4]<br/>
*Figura 4: Sequência de provisionamento e desprovisionamento de usuário*

### <a name="group-provisioning-and-de-provisioning"></a>Grupo de provisionamento e desprovisionamento

O provisionamento e o desprovisionamento de grupo são opcionais. Quando implementado e habilitado, a ilustração a seguir mostra as mensagens que o Azure AD envia a um serviço SCIM para gerenciar o ciclo de vida de um grupo no repositório de identidades do aplicativo.  Essas mensagens diferem das mensagens sobre os usuários de duas maneiras:

* Solicitações para recuperar grupos especificam que o atributo Members deve ser excluído de qualquer recurso fornecido em resposta à solicitação.  
* Os pedidos para determinar se um atributo de referência tem um determinado valor são pedidos sobre o atributo de membros.  

![Mostra a sequência de provisionamento e desprovisionamento de grupo][5]<br/>
*Figura 5: Sequência de provisionamento e desprovisionamento de grupo*

### <a name="scim-protocol-requests-and-responses"></a>Solicitações e respostas do protocolo SCIM
Esta seção fornece exemplos de solicitações SCIM emitidas pelo cliente SCIM do Azure AD e exemplos de respostas esperadas. Para obter melhores resultados, você deve codificar seu aplicativo para lidar com essas solicitações nesse formato e emitir as respostas esperadas.

> [!IMPORTANT]
> Para entender como e quando o serviço de provisionamento de usuário do Azure AD emite as operações descritas abaixo, consulte [o que acontece durante o provisionamento do usuário?](user-provisioning.md#what-happens-during-provisioning).

- [Operações do usuário](#user-operations)
  - [Criar Utilizador](#create-user)
    - [Pedido](#request)
    - [Resposta](#response)
  - [Obter Utilizador](#get-user)
    - [Pedido](#request-1)
    - [Resposta](#response-1)
  - [Obter usuário por consulta](#get-user-by-query)
    - [Pedido](#request-2)
    - [Resposta](#response-2)
  - [Obter o usuário por consulta-zero resultados](#get-user-by-query---zero-results)
    - [Pedido](#request-3)
    - [Resposta](#response-3)
  - [Atualizar usuário [Propriedades com vários valores]](#update-user-multi-valued-properties)
    - [Pedido](#request-4)
    - [Resposta](#response-4)
  - [Atualizar usuário [Propriedades de valor único]](#update-user-single-valued-properties)
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
  - [Atualizar grupo [atributos de não-membro]](#update-group-non-member-attributes)
    - [Pedido](#request-10)
    - [Resposta](#response-10)
  - [Atualizar grupo [adicionar membros]](#update-group-add-members)
    - [Pedido](#request-11)
    - [Resposta](#response-11)
  - [Atualizar grupo [remover membros]](#update-group-remove-members)
    - [Pedido](#request-12)
    - [Resposta](#response-12)
  - [Eliminar Grupo](#delete-group)
    - [Pedido](#request-13)
    - [Resposta](#response-13)

### <a name="user-operations"></a>Operações do usuário

* Os usuários podem ser consultados `userName` por `email[type eq "work"]` atributos ou.  

#### <a name="create-user"></a>Criar utilizador

###### <a name="request"></a>Pedir

*PÓS/Users*
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

###### <a name="request-1"></a>Quest
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Responde
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

#### <a name="get-user-by-query"></a>Obter usuário por consulta

##### <a name="request-2"></a>Quest

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Responde

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

#### <a name="get-user-by-query---zero-results"></a>Obter o usuário por consulta-zero resultados

##### <a name="request-3"></a>Quest

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>Responde

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

#### <a name="update-user-multi-valued-properties"></a>Atualizar usuário [Propriedades com vários valores]

##### <a name="request-4"></a>Quest

*PATCH/Users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response-4"></a>Responde

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

#### <a name="update-user-single-valued-properties"></a>Atualizar usuário [Propriedades de valor único]

##### <a name="request-5"></a>Quest

*PATCH/Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response-5"></a>Responde

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

##### <a name="request-6"></a>Quest

*EXCLUIR/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Responde

*HTTP/1.1 204 sem conteúdo*

### <a name="group-operations"></a>Operações de grupo

* Os grupos sempre devem ser criados com uma lista de Membros vazios.
* Os `displayName` grupos podem ser consultados pelo atributo.
* A atualização para a solicitação de PATCH de grupo deve gerar um *HTTP 204 sem conteúdo* na resposta. O retorno de um corpo com uma lista de todos os membros não é aconselhável.
* Não é necessário dar suporte ao retorno de todos os membros do grupo.

#### <a name="create-group"></a>Create Group

##### <a name="request-7"></a>Quest

*POST/groups HTTP/1.1*
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

##### <a name="response-7"></a>Responde

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

##### <a name="request-8"></a>Quest

*OBTER/groups/40734ae655284ad3abcc? excludeble = Members HTTP/1.1*

##### <a name="response-8"></a>Responde
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

##### <a name="request-9"></a>Quest
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>Responde

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

#### <a name="update-group-non-member-attributes"></a>Atualizar grupo [atributos de não-membro]

##### <a name="request-10"></a>Quest

*PATCH/groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response-10"></a>Responde

*HTTP/1.1 204 sem conteúdo*

### <a name="update-group-add-members"></a>Atualizar grupo [adicionar membros]

##### <a name="request-11"></a>Quest

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-11"></a>Responde

*HTTP/1.1 204 sem conteúdo*

#### <a name="update-group-remove-members"></a>Atualizar grupo [remover membros]

##### <a name="request-12"></a>Quest

*PATCH/groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response-12"></a>Responde

*HTTP/1.1 204 sem conteúdo*

#### <a name="delete-group"></a>Eliminar grupo

##### <a name="request-13"></a>Quest

*EXCLUIR/groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Responde

*HTTP/1.1 204 sem conteúdo*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Criando um ponto de extremidade SCIM usando bibliotecas da CLI da Microsoft

Ao criar um serviço Web SCIM que interage com Azure Active Directory, você pode habilitar o provisionamento automático de usuário para praticamente qualquer repositório de identidade ou aplicativo.

Eis como funciona:

1. O Azure AD fornece uma biblioteca de CLI (infraestrutura de linguagem comum) chamada Microsoft. SystemForCrossDomainIdentityManagement, incluída com os exemplos de código descritos abaixo. Integradores de sistema e desenvolvedores podem usar essa biblioteca para criar e implantar um ponto de extremidade de serviço Web baseado em SCIM que pode conectar o Azure AD ao repositório de identidades de qualquer aplicativo.
2. Mapeamentos são implementados no serviço web para mapear o esquema de usuário padronizado para o esquema de utilizador e o protocolo necessário para a aplicação. 
3. O URL de ponto final está registado no Azure AD como parte de uma aplicação personalizada na Galeria de aplicações.
4. Utilizadores e grupos são atribuídos a esta aplicação no Azure AD. Após a atribuição, eles são colocados em uma fila para serem sincronizados com o aplicativo de destino. O processo de sincronização lidar com a fila é executado em 40 minutos.

### <a name="code-samples"></a>Exemplos de código

Para facilitar esse processo, são fornecidos [exemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , que criam um ponto de extremidade de serviço Web scim e demonstram o provisionamento automático. O exemplo é de um provedor que mantém um arquivo com linhas de valores separados por vírgulas que representam usuários e grupos.

**Pré-requisitos**

* Visual Studio 2013 ou posterior
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) (Azure SDK para .NET)
* Windows máquina que suporta a estrutura do ASP.NET 4.5 para ser utilizado como o ponto de extremidade SCIM. Esse computador deve estar acessível na nuvem.
* [Uma subscrição do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Introdução

É a forma mais fácil de implementar um ponto de extremidade SCIM que pode aceitar pedidos de aprovisionamento do Azure AD criar e implementar o exemplo de código que produz os utilizadores aprovisionados para um ficheiro de valores separados por vírgulas (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Para criar um ponto de extremidade SCIM de exemplo

1. Transferir o pacote de exemplo de código em [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Deszipe o pacote e coloque-o no seu computador Windows num local, como C:\AzureAD-BYOA-Provisioning-Samples\.
1. Nesta pasta, inicie o projeto de FileProvisioning\Host\FileProvisioningService.csproj no Visual Studio.
1. Selecione **ferramentas** > **Gerenciador**de pacotes NuGet console do Gerenciador de pacotes e execute os seguintes comandos para o projeto FileProvisioningService para resolver as referências da solução: > 

   ```powershell
    Update-Package -Reinstall
   ```

1. Crie o projeto de FileProvisioningService.
1. Iniciar o aplicativo de linha de comandos no Windows (como administrador) e utilizar o **cd** comandos para alterar o diretório para sua **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**pasta.
1. Execute o seguinte comando, substituindo `<ip-address>` pelo endereço IP ou nome de domínio do computador Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. No Windows em **configurações** > do Windows**rede & configurações da Internet**, selecione as**Configurações avançadas**do **Firewall** > do Windows e crie uma **regra de entrada** que permita o acesso de entrada à porta 9000.
1. Se o computador Windows estiver atrás de um roteador, o roteador precisará ser configurado para executar a conversão de acesso à rede entre sua porta 9000 exposta à Internet e a porta 9000 no computador com Windows. Essa configuração é necessária para que o Azure AD acesse esse ponto de extremidade na nuvem.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Para registrar o ponto de extremidade SCIM de exemplo no Azure AD

1. Entre no portal de [Azure Active Directory](https://aad.portal.azure.com). 
1. Selecione **aplicativos empresariais** no painel esquerdo. Uma lista de todos os aplicativos configurados é mostrada, incluindo os aplicativos que foram adicionados da galeria.
1. Selecione **+ novo aplicativo** > **todos os** > **aplicativos que não são da Galeria**.
1. Insira um nome para seu aplicativo e selecione **Adicionar** para criar um objeto de aplicativo. O objeto de aplicativo criado destina-se para representar a aplicação de destino que seria o aprovisionamento e implementação de início de sessão único para e não apenas o ponto final SCIM.
1. Na tela gerenciamento de aplicativos, selecione **provisionamento** no painel esquerdo.
1. Na **modo de aprovisionamento** menu, selecione **automática**.    
1. Na **URL de inquilino** campo, introduza o URL do ponto final SCIM do aplicativo. Exemplo: https://api.contoso.com/scim/

1. Se o ponto de extremidade SCIM exige um token de portador do OAuth a partir de um emissor diferente do Azure AD, em seguida, copie o token de portador do OAuth necessário para o opcional **segredo de Token** campo. Se esse campo for deixado em branco, o Azure AD incluirá um token de portador OAuth emitido pelo Azure AD com cada solicitação. Os aplicativos que usam o Azure AD como um provedor de identidade podem validar esse token emitido pelo Azure AD.
1. Selecione **testar conexão** para que Azure Active Directory tente se conectar ao ponto de extremidade SCIM. Se a tentativa falhar, as informações de erro serão exibidas.  

    > [!NOTE]
    > O **Test Connection** consulta o ponto de extremidade scim para um usuário que não existe, usando um GUID aleatório como a propriedade correspondente selecionada na configuração do Azure AD. A resposta correta esperada é HTTP 200 OK com uma mensagem SCIM ListResponse vazia

1. Se as tentativas de conexão com o aplicativo forem bem-sucedidos, selecione **salvar** para salvar as credenciais de administrador.
1. Na **mapeamentos** secção, existem dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de utilizador e uma para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os utilizadores e grupos na sua aplicação para operações de atualização. Selecione **salvar** para confirmar as alterações.
1. Sob **configurações**, o **âmbito** campo define quais os utilizadores e ou grupos são sincronizados. Selecione **"sincronizar somente usuários e grupos atribuídos** (recomendado) para sincronizar somente usuários e grupos atribuídos na guia **usuários e grupos** .
1. Quando a configuração for concluída, defina o **status de provisionamento** como **ativado**.
1. Selecione **salvar** para iniciar o serviço de provisionamento do Azure AD.
1. Se estiver sincronizando apenas usuários e grupos atribuídos (recomendado), selecione a guia **usuários e grupos** e atribua os usuários ou grupos que você deseja sincronizar.

Depois que a sincronização inicial for iniciada, você poderá selecionar **logs de auditoria** no painel esquerdo para monitorar o progresso, que mostra todas as ações realizadas pelo serviço de provisionamento em seu aplicativo. Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](check-status-user-account-provisioning.md).

A verificar o exemplo a etapa final é abrir o ficheiro de TargetFile.csv na pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador Windows. Assim que o processo de aprovisionamento é executado, este ficheiro mostra os detalhes de todos os atribuído e aprovisionamento de utilizadores e grupos.

### <a name="development-libraries"></a>Bibliotecas de programação

Para desenvolver seu próprio serviço da web que está em conformidade com a especificação de SCIM, primeiro familiarize-se com as seguintes bibliotecas fornecidas pela Microsoft para ajudar a acelerar o processo de desenvolvimento:

* Bibliotecas do Common Language Infrastructure (CLI) são oferecidas para utilização com linguagens com base em que a infraestrutura, como c#. Uma dessas bibliotecas, Microsoft. SystemForCrossDomainIdentityManagement. Service, declara uma interface, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, mostrada na ilustração a seguir. Um desenvolvedor que usa as bibliotecas implementaria essa interface com uma classe que pode ser referenciada, genericamente, como um provedor. As bibliotecas permitem que o desenvolvedor implante um serviço Web que esteja em conformidade com a especificação SCIM. O serviço Web pode ser hospedado dentro de Serviços de Informações da Internet ou qualquer assembly de CLI executável. Pedido é convertido em chamadas para métodos do provedor, que poderiam ser programados pelo desenvolvedor para operar num armazenamento de identidade.
  
   ![Visões Uma solicitação convertida em chamadas para os métodos do provedor][3]
  
* [Express manipuladores de rotas](https://expressjs.com/guide/routing.html) são feitas disponíveis para análise de objetos de solicitação de node. js que representam chamadas (conforme definido pela especificação SCIM), a um serviço web do node. js.

### <a name="building-a-custom-scim-endpoint"></a>Criando um ponto de extremidade SCIM personalizado

Os desenvolvedores que usam as bibliotecas da CLI podem hospedar seus serviços em qualquer assembly da CLI executável ou em Serviços de Informações da Internet. Eis o código de exemplo para hospedar um serviço num assembly executável, no endereço http://localhost:9000: 

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

Para hospedar o serviço no Serviços de Informações da Internet, um desenvolvedor criaria um assembly da biblioteca de códigos da CLI com uma classe chamada Startup no namespace padrão do assembly.  Eis um exemplo dessa classe: 

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

Pedidos do Azure Active Directory incluem um token de portador do OAuth 2.0.   Qualquer serviço que recebe a solicitação deve autenticar o emissor como sendo Azure Active Directory para o locatário de Azure Active Directory esperado, para acessar o serviço Web de Azure Active Directory Graph.  No token, o emissor é identificado por uma declaração ISS, como "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ".  Neste exemplo, o endereço base do valor da declaração, https://sts.windows.net , identifica Azure Active Directory como o emissor, enquanto o segmento de endereço relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do locatário Azure Active Directory para que o token foi emitido. O público-alvo do token será a ID do modelo de aplicativo para o aplicativo na galeria. A ID do modelo de aplicativo para todos os aplicativos personalizados é 8adf8e6e-67b2-4cf2-a259-e3dc5476c621. A ID do modelo de aplicativo para cada aplicativo na Galeria varia. Entre em ProvisioningFeedback@microsoft.com contato com perguntas sobre a ID do modelo de aplicativo para um aplicativo da galeria. Cada um dos aplicativos registrados em um único locatário pode receber a mesma `iss` declaração com solicitações SCIM.

Os desenvolvedores que usam as bibliotecas de CLI fornecidas pela Microsoft para criar um serviço SCIM podem autenticar solicitações de Azure Active Directory usando o pacote Microsoft. Owin. Security. ActiveDirectory seguindo estas etapas: 

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

1. Adicione o seguinte código a esse método para que qualquer solicitação a qualquer um dos pontos de extremidade do serviço seja autenticada como um token emitido por Azure Active Directory para um locatário especificado, para acesso ao serviço Web do Azure AD Graph: 

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
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Manipulando o provisionamento e o desprovisionamento de usuários

1. O Azure Active Directory consulta o serviço para um utilizador com um valor de atributo externalId correspondência o valor do atributo mailNickname de um utilizador no Azure AD. A consulta é expressa como uma solicitação HTTP (Hypertext Transfer Protocol), como este exemplo, em que Jyoung é um exemplo de um mailNickname de um usuário no Azure Active Directory.

    >[!NOTE]
    > Este é apenas um exemplo. Nem todos os usuários terão um atributo mailNickname, e o valor que um usuário pode não ser exclusivo no diretório. Além disso, o atributo usado para correspondência (que nesse caso é externalId) é configurável nos mapeamentos de [atributo do Azure ad](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Se o serviço foi criado usando as bibliotecas de CLI fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada para o método de consulta do provedor do serviço.  Esta é a assinatura desse método: 
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
   * parâmetro. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator.Equals
   * parâmetros. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. Se a resposta a uma consulta para o serviço Web para um usuário com um valor de atributo externalId que corresponda ao valor do atributo mailNickname de um usuário não retornar usuários, o Azure Active Directory solicitará que o serviço provisione um usuário correspondente ao único em Azure Active Directory.  Eis um exemplo de pedido: 

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
   As bibliotecas de CLI fornecidas pela Microsoft para implementar serviços SCIMs converterão essa solicitação em uma chamada para o método Create do provedor do serviço.  O método Create tem esta assinatura: 
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
   Em um serviço criado usando as bibliotecas de CLI fornecidas pela Microsoft para implementar serviços SCIMs, a solicitação é convertida em uma chamada para o método de recuperação do provedor do serviço.  Esta é a assinatura do método obter: 
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
  
   * ID "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. Se um atributo de referência for para ser atualizado, Azure Active Directory consultará o serviço para determinar se o valor atual do atributo de referência no repositório de identidades administrado pelo serviço já corresponde ao valor desse atributo no Azure active Active. Para os utilizadores, o único atributo que o valor atual é consultado dessa maneira é o atributo de gestor. Eis um exemplo de um pedido para determinar se o atributo de Gestor de um objeto de utilizador em particular tem atualmente um determinado valor: 

   Se o serviço foi criado usando as bibliotecas de CLI fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada para o método de consulta do provedor do serviço. O valor das propriedades do objeto fornecido como o valor do argumento parâmetros são os seguintes: 
  
   * parameters.AlternateFilters.Count: 2
   * parâmetro. AlternateFilters. ElementAt (x). AttributePath: "ID"
   * parâmetro. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parâmetros. AlternateFilters.ElementAt(y). AttributePath: "manager"
   * parâmetro. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator.Equals
   * parâmetro. AlternateFilter. ElementAt (y). ComparisonValue "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro de consulta de filtro.   

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
   As bibliotecas da CLI da Microsoft para implementar serviços SCIMs converterão a solicitação em uma chamada para o método Update do provedor do serviço. Esta é a assinatura do método de atualização: 
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
* parâmetro. AlternateFilters. ElementAt (x). AttributePath: "ID"
* parâmetro. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parâmetros. AlternateFilters.ElementAt(y). AttributePath: "manager"
* parâmetro. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator.Equals
* parâmetro. AlternateFilter. ElementAt (y). ComparisonValue  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro de consulta de filtro.   

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
   * (PatchRequest como PatchRequest2). Operações. contagem: 1
   * (PatchRequest como PatchRequest2). Operations. ElementAt (0). OperationName OperationName.Add
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "manager"
   * (PatchRequest como PatchRequest2). Operations. ElementAt (0). Valor. contagem: 1
   * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referência: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest como PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Valor 2819c223-7f76-453a-919d-413861904646

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
   Se o serviço foi criado usando as bibliotecas de CLI fornecidas pela Microsoft para implementação de serviços SCIM, a solicitação é convertida em uma chamada para o método Delete do provedor do serviço.   Esse método tem esta assinatura: 
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

## <a name="user-and-group-schema-reference"></a>Referência de esquema de usuário e grupo

O Azure Active Directory pode aprovisionar dois tipos de recursos para serviços da web SCIM.  Esses tipos de recursos são utilizadores e grupos.  

Os recursos do usuário são identificados pelo identificador de `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`esquema,, que está incluído nesta especificação de https://tools.ietf.org/html/rfc7643 protocolo:.  O mapeamento padrão dos atributos de usuários no Azure Active Directory aos atributos dos recursos do usuário é fornecido na tabela 1.  

Grupo de recursos é identificados pelo identificador de esquema, `urn:ietf:params:scim:schemas:core:2.0:Group`. A tabela 2 mostra o mapeamento padrão dos atributos de grupos em Azure Active Directory aos atributos dos recursos de grupo.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabela 1: Mapeamento de atributo de usuário padrão

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

### <a name="table-2-default-group-attribute-mapping"></a>Tabela 2: Mapeamento de atributo de grupo padrão

| Grupo do Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| mail |. Value de e-mails [tipo eq "trabalho"] |
| mailNickname |displayName |
| membros |membros |
| objectId |ID |
| proxyAddresses |e-mails de [tipo é igual a "other"]. Valor |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Permitir que os endereços IP usados pelo serviço de provisionamento do Azure AD façam solicitações SCIM

Determinados aplicativos permitem o tráfego de entrada para seu aplicativo. Para que o serviço de provisionamento do Azure AD funcione conforme o esperado, os endereços IP usados devem ser permitidos. Para obter uma lista de endereços IP para cada marca de serviço/região, consulte o arquivo JSON – [intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). Você pode baixar e programar esses IPs em seu firewall, conforme necessário. Os intervalos de IP reservados para o provisionamento do Azure AD podem ser encontrados em "AzureActiveDirectoryDomainServices".

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
