---
title: Tutorial - Desenvolver um ponto final SCIM para o fornecimento de utilizadores a apps a partir do Azure AD
description: O sistema de gestão de identidade de domínio cruzado (SCIM) normaliza o fornecimento automático do utilizador. Neste tutorial, aprende-se a desenvolver um ponto final SCIM, a integrar a sua API SCIM com o Azure Ative Directory e a automatizar utilizadores e grupos de a provisionamento nas suas aplicações na nuvem.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44e14bed3158fac1f8bed15f913dc8b7eddbbb96
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985959"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Tutorial: Desenvolver e planear o provisionamento para um ponto final do SCIM

Como desenvolvedor de aplicações, pode utilizar a API de gestão de identidade de controlo de identidade de domínio cruzado (SCIM) para permitir o fornecimento automático de utilizadores e grupos entre a sua aplicação e a Azure AD. Este artigo descreve como construir um ponto final SCIM e integrar-se com o serviço de fornecimento AZURE AD. A especificação SCIM fornece um esquema comum de utilização para o provisionamento. Quando usado em conjunto com padrões da federação como SAML ou OpenID Connect, o SCIM dá aos administradores uma solução baseada em padrões para a gestão de acessos.

SCIM é uma definição padronizada de dois pontos finais: um `/Users` ponto final e um ponto `/Groups` final. Utiliza verbos rest comuns para criar, atualizar e apagar objetos, e um esquema pré-definido para atributos comuns como nome de grupo, nome de utilizador, nome de primeiro nome, apelido e e-mail. As aplicações que oferecem uma API SCIM 2.0 REST podem reduzir ou eliminar a dor de trabalhar com uma API de gestão de utilizadores proprietário. Por exemplo, qualquer cliente SCIM em conformidade sabe como fazer um HTTP POST de um objeto JSON para o `/Users` ponto final para criar uma nova entrada no utilizador. Em vez de precisar de uma API ligeiramente diferente para as mesmas ações básicas, as aplicações que estão em conformidade com a norma SCIM podem instantaneamente tirar partido dos clientes, ferramentas e código pré-existentes. 

![Provisionamento de Azure AD para uma app com SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

O esquema padrão de objetos de utilizador e apis de repouso para gestão definido em SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) permitem que os fornecedores de identidade e apps se integrem mais facilmente uns com os outros. Os desenvolvedores de aplicações que construam um ponto final SCIM podem integrar-se com qualquer cliente compatível com SCIM sem ter que fazer trabalho personalizado.

Automatizar o fornecimento a uma aplicação requer a construção e integração de um ponto final SCIM com o cliente Azure AD SCIM. Execute os seguintes passos para começar a agru estar a a provisionar utilizadores e grupos na sua aplicação. 
    
  * **[Passo 1: Desenhe o seu esquema de utilizador e grupo.](#step-1-design-your-user-and-group-schema)** Identifique os objetos e atribui as necessidades da sua aplicação e determine como mapeiam para o utilizador e esquema de grupo suportado pela implementação do Azure AD SCIM.

  * **[Passo 2: Compreender a implementação do Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Compreenda como o cliente Azure AD SCIM é implementado e modele o seu protocolo SCIM de tratamento e respostas.

  * **[Passo 3: Construir um ponto final SCIM.](#step-3-build-a-scim-endpoint)** Um ponto final deve ser compatível com o SCIM 2.0 para integrar-se no serviço de fornecimento de Ad Azure. Como opção, pode utilizar bibliotecas e amostras de código da Microsoft Common Language Infrastructure (CLI) para construir o seu ponto final. Estas amostras destinam-se apenas a referência e a ensaios; recomendamos contra a codificação da sua app de produção para ter uma dependência deles.

  * **[Passo 4: Integre o seu ponto final SCIM com o cliente Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se a sua organização estiver a utilizar uma aplicação de terceiros que implemente o perfil do SCIM 2.0 que o Azure AD suporta, pode começar a automatizar o fornecimento e a desprovisionamento de utilizadores e grupos imediatamente.

  * **[Passo 5: Publique a sua candidatura na galeria de aplicações AZure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Facilite aos clientes a descoberta da sua aplicação e a configuração fácil do provisionamento. 

![Passos para a integração de um ponto final scim com Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passo 1: Desenhe o seu utilizador e esquema de grupo

Cada aplicação requer diferentes atributos para criar um utilizador ou grupo. Inicie a sua integração identificando os objetos (utilizadores, grupos) e atributos (nome, gestor, título de emprego, etc.) que a sua aplicação requer. A norma SCIM define um esquema para gerir utilizadores e grupos. O esquema principal do utilizador requer apenas três atributos: **id** (identificador definido pelo prestador de serviços), **externalId** (identificador definido pelo cliente) e **metadados** (metadados apenas de leitura mantidos pelo prestador de serviços). Todos os outros atributos são opcionais. Além do esquema principal do utilizador, a norma SCIM define uma extensão do utilizador da empresa e um modelo para alargar o esquema do utilizador para atender às necessidades da sua aplicação. Se, por exemplo, a sua aplicação necessitar de um gestor de utilizador, pode utilizar o esquema do utilizador da empresa para recolher o gestor do utilizador e o esquema principal para recolher o e-mail do utilizador. Para desenhar o seu esquema, siga os passos abaixo:
  1. Enuse os atributos que a sua aplicação requer. Pode ser útil decompor os seus requisitos nos atributos necessários para a autenticação (por exemplo, loginName e e-mail), atributos necessários para gerir o ciclo de vida do utilizador (por exemplo, estado/ativo), e outros atributos necessários para a sua aplicação particular para trabalhar (por exemplo, gestor, tag).
  2. Verifique se esses atributos já estão definidos no esquema principal do utilizador ou no esquema do utilizador da empresa. Se algum atributo que precise e não estiver coberto nos esquemas do núcleo ou do utilizador da empresa, terá de definir uma extensão ao esquema do utilizador que cubra os atributos de que necessita. No exemplo abaixo, adicionámos uma extensão ao utilizador para permitir o fornecimento de uma "tag" a um utilizador. É melhor começar apenas com os esquemas de utilizador core e empresa e expandir para esquemas personalizados adicionais mais tarde.  
  3. Mapear os atributos SCIM aos atributos do utilizador em Azure AD. Se um dos atributos que definiu no seu ponto final SCIM não tiver uma contrapartida clara no esquema do utilizador Azure AD, existe uma boa hipótese de os dados não serem armazenados no objeto do utilizador na maioria dos inquilinos. Considere se este atributo pode ser opcional para a criação de um utilizador. Se o atributo for fundamental para que a sua aplicação funcione, guie o administrador do arrendatário para estender o seu esquema ou use um atributo de extensão como mostrado abaixo para a propriedade "tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Quadro 1: Delinear os atributos de que precisa 
| Passo 1: Determinar atributos que a sua aplicação necessita| Passo 2: Requisitos de aplicação de mapa para o padrão SCIM| Passo 3: Mapa SCIM atribui aos atributos AD Azure|
|--|--|--|
|nome de login|userName|userPrincipalName|
|nomePróprio|name.givenName|nomeDado|
|apelido|nome.lastName|apelido|
|workMail|e-mails[tipo eq "work"].value|Correio|
|gestor|gestor|gestor|
|etiqueta|urn:ietf:params:scim:schemas:extensão:2.0:CustomExtension:tag|extensãoAttribute1|
|status|active|isSoftDeleted (valor calculado não armazenado no utilizador)|

O esquema acima definido seria representado utilizando a carga útil JSON abaixo. Note que além dos atributos necessários para a aplicação, a representação JSON inclui os `id` `externalId` necessários, e `meta` atributos.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
}   
```

### <a name="table-2-default-user-attribute-mapping"></a>Quadro 2: Mapeamento de atributos de utilizador predefinido
Em seguida, pode utilizar a tabela abaixo para entender como os atributos que a sua aplicação requer podem mapear para um atributo em AD AD e no SCIM RFC. Pode [personalizar](customize-application-attributes.md) como os atributos são mapeados entre a Azure AD e o seu ponto final SCIM. Note que não precisa de suportar tanto os utilizadores como os grupos ou todos os atributos apresentados abaixo. São uma referência para a forma como os atributos em AD Azure são frequentemente mapeados para propriedades no protocolo SCIM. 

| Utilizador do Azure Ative Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
|departamento|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|
| Facsimile-TelephoneNumber |números de telefone[tipo eq "fax"].valor |
| nomeDado |name.givenName |
| jobTitle |título |
| correio |emails[type eq "work"].value |
| mailSemame |externalId |
| gestor |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager |
| dispositivo móvel |phoneNumbers[type eq "mobile"].value |
| postalCode |endereços[tipo eq "work"].postalCode |
| proxy-Addresss |e-mails[tipo eq "outros"]. Valor |
| nome físico-entrega-escritório |endereços[tipo eq "outros"]. Formatado |
| streetAddress |endereços[tipo eq "work"].streetAddress |
| surname |name.familyName |
| número de telefone |phoneNumbers[type eq "work"].value |
| nome principal do utilizador |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Quadro 3: Mapeamento de atributos de grupo predefinido

| Grupo Azure Ative Directory | urn:ietf:params:scim:schemas:core:2.0:Grupo |
| --- | --- |
| displayName |displayName |
| correio |emails[type eq "work"].value |
| mailSemame |displayName |
| membros |membros |
| objectId |externalId |
| proxyAddresses |e-mails[tipo eq "outros"]. Valor |

Existem vários pontos finais definidos no SCIM RFC. Pode começar com o ponto final /Utilizador e depois expandir a partir daí. O ponto final /Schemas é útil quando se utiliza atributos personalizados ou se o seu esquema muda frequentemente. Permite que um cliente recupere automaticamente o esquema mais atualizado. O ponto final /Granel é especialmente útil quando grupos de apoio. O quadro abaixo descreve os vários pontos finais definidos na norma SCIM.
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Quadro 4: Determinar os pontos finais que gostaria de desenvolver
|PONTO FINAL|DESCRIÇÃO|
|--|--|
|/Utilizador|Executar operações CRUD num objeto do utilizador.|
|/Grupo|Execute operações CRUD num objeto de grupo.|
|/ServiceProviderConfig|Fornece detalhes sobre as características da norma SCIM que são suportadas, por exemplo os recursos que são suportados e o método de autenticação.|
|/RecursosTypes|Especifica metadados sobre cada recurso|
|/Schemas|O conjunto de atributos suportados por cada cliente e prestador de serviços pode variar. Um prestador de serviços pode incluir `name` `title` , `emails` e, enquanto outro prestador de serviços usa `name` , e `title` `phoneNumbers` . O ponto final dos esquemas permite a descoberta dos atributos suportados.|
|/Granel|As operações a granel permitem realizar operações numa grande coleção de objetos de recursos numa única operação (por exemplo, atualizações para um grande grupo).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passo 2: Compreender a implementação do Azure AD SCIM
> [!IMPORTANT]
> O comportamento da implementação do Azure AD SCIM foi atualizado pela última vez em 18 de dezembro de 2018. Para obter informações sobre o que mudou, consulte [o protocolo SCIM 2.0 da conformidade do serviço de Fornecimento de Utilizadores Azure AD](application-provisioning-config-problem-scim-compatibility.md).

Se estiver a construir uma aplicação que suporte uma API de gestão de utilizadores SCIM 2.0, esta secção descreve em detalhe como o cliente Azure AD SCIM é implementado. Também mostra como modelar o seu protocolo SCIM solicita tratamento e respostas. Uma vez implementado o seu ponto final SCIM, pode testá-lo seguindo o procedimento descrito na secção anterior.

Dentro da especificação do [protocolo SCIM 2.0,](http://www.simplecloud.info/#Specification)a sua aplicação deve satisfazer estes requisitos:

* Suporta a criação de utilizadores, e opcionalmente também grupos, de acordo com a secção [3.3 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Suporta modificar utilizadores ou grupos com pedidos PATCH, de acordo com [a secção 3.5.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2). O apoio garante que os grupos e utilizadores são a provisionados de forma performante. 
* Suporta a recuperação de um recurso conhecido para um utilizador ou grupo criado anteriormente, de acordo com [a secção 3.4.1 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Suporta consulta de utilizadores ou grupos, de acordo com a secção [3.4.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por padrão, os utilizadores são recuperados pelos seus `id` `username` `externalId` e, e os grupos são questionados por `displayName` .  
* Suporta consulta do utilizador por ID e por gestor, de acordo com a secção 3.4.2 do protocolo SCIM.  
* Suporta grupos de consulta por ID e por membro, de acordo com a secção 3.4.2 do protocolo SCIM.  
* Suporta o filtro [excluídoAttributes=membros](#get-group) ao consultar o recurso de grupo, de acordo com a secção 3.4.2.5 do protocolo SCIM.
* Aceita um único sinal de portador para autenticação e autorização da Azure AD para a sua aplicação.
* Suporta a eliminação suave de um utilizador `active=false` e a restauração do utilizador `active=true` (o objeto do utilizador deve ser devolvido num pedido, quer o utilizador esteja ou não ativo). A única altura em que o utilizador não deve ser devolvido é quando é duramente eliminado da aplicação. 

Siga estas orientações gerais ao implementar um ponto final SCIM para garantir a compatibilidade com a Azure AD:

* `id` é uma propriedade necessária para todos os recursos. Cada resposta que devolva um recurso deve garantir que cada recurso tem esta propriedade, exceto `ListResponse` com zero membros.
* A resposta a um pedido de consulta/filtro deve ser sempre um `ListResponse` .
* Os grupos são opcionais, mas só são apoiados se a implementação do SCIM apoiar os pedidos do PATCH.
* Não é necessário incluir todo o recurso na resposta PATCH.
* O Microsoft Azure AD utiliza apenas os seguintes operadores:  
    - `eq`
    - `and`
* Não exija uma correspondência sensível a elementos estruturais no SCIM, em particular `op` os valores de operação PATCH, tal como definidos em https://tools.ietf.org/html/rfc7644#section-3.5.2 . A AZure AD emite os valores de 'op' como `Add` `Replace` , e `Remove` .
* O Microsoft Azure AD faz pedidos para obter um utilizador e grupo aleatórios para garantir que o ponto final e as credenciais são válidos. Também é feito como parte do fluxo de **ligação** de teste no [portal Azure](https://portal.azure.com). 
* O atributo em que os recursos podem ser consultados deve ser definido como um atributo correspondente à aplicação no [portal Azure](https://portal.azure.com). Para mais informações, consulte [Personalizar a provisionamento do utilizador atribuindo mapeamentos de atributos](customize-application-attributes.md)
* Suporte HTTPS no seu ponto final SCIM

### <a name="user-provisioning-and-deprovisioning"></a>Fornecimento e desprovisionamento dos utilizadores

A seguinte ilustração mostra as mensagens que o Azure Ative Directory envia para um serviço SCIM para gerir o ciclo de vida de um utilizador na loja de identidade da sua aplicação.  

![Mostra a sequência de provisionamento e de desprovisionamento do utilizador](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sequência de provisão e desprovisionamento do utilizador*

### <a name="group-provisioning-and-deprovisioning"></a>Provisão e desprovisionamento em grupo

O provisionamento e a desprovisionamento em grupo são opcionais. Quando implementada e ativada, a seguinte ilustração mostra as mensagens que a Azure AD envia a um serviço SCIM para gerir o ciclo de vida de um grupo na loja de identidade da sua aplicação.  Estas mensagens diferem das mensagens sobre os utilizadores de duas formas:

* Os pedidos de recuperação de grupos especificam que os membros atribuídos devem ser excluídos de qualquer recurso fornecido em resposta ao pedido.  
* Os pedidos para determinar se um atributo de referência tem um determinado valor são pedidos sobre os membros atribuídos.  

![Mostra a sequência de provisão e desprovisionamento em grupo](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequência de provisionamento e desprovisionamento em grupo*

### <a name="scim-protocol-requests-and-responses"></a>Pedidos e respostas do protocolo SCIM
Esta secção fornece o exemplo dos pedidos do SCIM emitidos pelo cliente Azure AD SCIM e exemplo de respostas esperadas. Para obter os melhores resultados, deverá codificar a sua app para lidar com estes pedidos neste formato e emitir as respostas esperadas.

> [!IMPORTANT]
> Para compreender como e quando o serviço de fornecimento de utilizadores Azure AD emite as operações descritas abaixo, consulte a secção [Ciclos de Provisionamento: Inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em [Como funciona o provisionamento](how-provisioning-works.md).

[Operações de Utilizador](#user-operations)
  - [Criar Utilizador](#create-user) (Resposta[de Pedido)](#request)  /  [](#response)
  - [Obter Utilizador](#get-user) (Resposta[de Pedido)](#request-1)  /  [](#response-1)
  - [Obter Utilizador por consulta](#get-user-by-query) (Resposta[de Pedido)](#request-2)  /  [](#response-2)
  - [Obtenha o Utilizador por consulta - Resultados zero](#get-user-by-query---zero-results) (Resposta[de Pedido)](#request-3)  /  [](#response-3)
  - [Atualizar o Utilizador [propriedades multi-valorizadas]](#update-user-multi-valued-properties) [](#request-4)  /  [(Resposta de Pedido)](#response-4)
  - [Atualizar o Utilizador [propriedades de valor único]](#update-user-single-valued-properties) (Resposta[de](#request-5)  /  [Pedido)](#response-5) 
  - [Utilizador desativação](#disable-user) (Resposta[de](#request-14)  /  [](#response-14)Pedido)
  - [Excluir utilizador](#delete-user) (Resposta[de Pedido)](#request-6)  /  [](#response-6)


[Operações de Grupo](#group-operations)
  - [Criar Grupo](#create-group) (Resposta[de Pedido)](#request-7)  /  [](#response-7)
  - [Obter Grupo](#get-group) (Resposta[de Pedido)](#request-8)  /  [](#response-8)
  - [Obter Grupo por displayName](#get-group-by-displayname) [](#request-9)  /  [(Resposta de Pedido)](#response-9)
  - [Grupo de Atualização [Atributos não membros]](#update-group-non-member-attributes) [](#request-10)  /  [(Resposta de](#response-10)Pedido)
  - [Grupo de Atualização [Add Members]](#update-group-add-members) [](#request-11)  /  [(Resposta do](#response-11)Pedido)
  - [Grupo de atualização [Remover Membros]](#update-group-remove-members) [](#request-12)  /  [(Resposta do](#response-12)Pedido)
  - [Excluir Grupo](#delete-group) (Resposta[de Pedido)](#request-13)  /  [](#response-13)

### <a name="user-operations"></a>Operações de Utilizador

* Os utilizadores podem ser questionados por `userName` ou `email[type eq "work"]` atributos.  

#### <a name="create-user"></a>Criar Utilizador

###### <a name="request"></a>Pedir

*POST /Utilizadores*
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

*HTTP/1.1 201 Criado*
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

#### <a name="get-user"></a>Obter Utilizador

###### <a name="request"></a><a name="request-1"></a>Pedir
*GET /Utilizadores/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Resposta (Utilizador encontrado)
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

###### <a name="request"></a>Pedir
*GET /Utilizadores/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Resposta (Utilizador não encontrado. Note que o detalhe não é necessário, apenas o estado.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Obtenha o utilizador por consulta

##### <a name="request"></a><a name="request-2"></a>Pedir

*GET /Utilizadores?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Resposta

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

#### <a name="get-user-by-query---zero-results"></a>Obtenha o Utilizador por consulta - Resultados zero

##### <a name="request"></a><a name="request-3"></a>Pedir

*GET /Utilizadores?filter=userName eq "utilizador inexistente"*

##### <a name="response"></a><a name="response-3"></a>Resposta

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

#### <a name="update-user-multi-valued-properties"></a>Atualizar o Utilizador [propriedades multi-valorizadas]

##### <a name="request"></a><a name="request-4"></a>Pedir

*PATCH /Utilizadores/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Resposta

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

#### <a name="update-user-single-valued-properties"></a>Atualizar o Utilizador [propriedades de valor único]

##### <a name="request"></a><a name="request-5"></a>Pedir

*PATCH /Utilizadores/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Resposta

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

### <a name="disable-user"></a>Desativar Utilizador

##### <a name="request"></a><a name="request-14"></a>Pedir

*PATCH /Utilizadores/5171a35d82074e068ce2 HTTP/1.1*
```json
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

##### <a name="response"></a><a name="response-14"></a>Resposta

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Eliminar Utilizador

##### <a name="request"></a><a name="request-6"></a>Pedir

*DELETE /Utilizadores/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="group-operations"></a>Operações de Grupo

* Os grupos devem ser sempre criados com uma lista de membros vazios.
* Os grupos podem ser questionados pelo `displayName` atributo.
* A atualização do pedido patch do grupo deve produzir um *HTTP 204 No Content* na resposta. Devolver um corpo com uma lista de todos os membros não é aconselhável.
* Não é necessário apoiar a devolução de todos os membros do grupo.

#### <a name="create-group"></a>Criar Grupo

##### <a name="request"></a><a name="request-7"></a>Pedir

*POST /Grupos HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Resposta

*HTTP/1.1 201 Criado*
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

#### <a name="get-group"></a>Obter Grupo

##### <a name="request"></a><a name="request-8"></a>Pedir

*GET /Groups/40734ae655284ad3abcc?excluídosAttributes=membros HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Resposta
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

#### <a name="get-group-by-displayname"></a>Obtenha grupo por displayName

##### <a name="request"></a><a name="request-9"></a>Pedir
*GET /Groups?excluiuAttributes=membros&filtro=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Resposta

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

#### <a name="update-group-non-member-attributes"></a>Grupo de Atualização [Atributos não membros]

##### <a name="request"></a><a name="request-10"></a>Pedir

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="update-group-add-members"></a>Grupo de Atualização [Adicionar Membros]

##### <a name="request"></a><a name="request-11"></a>Pedir

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="update-group-remove-members"></a>Grupo de atualização [Remover Membros]

##### <a name="request"></a><a name="request-12"></a>Pedir

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="delete-group"></a>Eliminar Grupo

##### <a name="request"></a><a name="request-13"></a>Pedir

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="security-requirements"></a>Requisitos de segurança
**Versões de protocolo TLS**

As únicas versões aceitáveis do protocolo TLS são tls 1.2 e TLS 1.3. Não são permitidas outras versões de TLS. Não é permitida nenhuma versão do SSL. 
- As chaves RSA devem ser de pelo menos 2.048 bits.
- As chaves ECC devem ser de pelo menos 256 bits, geradas com uma curva elíptica aprovada


**Comprimentos-chave**

Todos os serviços devem utilizar certificados X.509 gerados com teclas criptográficas de comprimento suficiente, o que significa:

**Suítes Cipher**

Todos os serviços devem ser configurados para utilizar as seguintes suítes cifra, na ordem exata especificada abaixo. Note que se tiver apenas um certificado RSA, as suítes de cifra ECDSA não têm qualquer efeito. </br>

Barra mínima TLS 1.2 Cipher Suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>Gamas IP
O serviço de prestação de AD Azure opera atualmente ao abrigo dos Intervalos IP para AzureActiveDirectory, conforme [listado aqui.](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all) Pode adicionar as gamas IP listadas na tag AzureActiveDirectory para permitir o tráfego do serviço de fornecimento AZure AD na sua aplicação. Note que terá de rever cuidadosamente a lista de gama IP para endereços computados. Um endereço como '40.126.25.32' poderia estar representado na lista de intervalos de IP como '40.126.0.0/18'. Também pode recuperar programáticamente a lista de gama IP utilizando a seguinte [API.](/rest/api/virtualnetwork/servicetags/list)

## <a name="step-3-build-a-scim-endpoint"></a>Passo 3: Construir um ponto final SCIM

Agora que concebeu o seu esquema e compreendeu a implementação do Azure AD SCIM, pode começar a desenvolver o seu ponto final SCIM. Em vez de começar do zero e construir a implementação completamente por conta própria, você pode confiar em uma série de bibliotecas SCIM de código aberto publicadas pela comunidade SCIM.

Para obter orientações sobre como construir um ponto final SCIM, incluindo exemplos, consulte [Desenvolver uma amostra do ponto final SCIM](use-scim-to-build-users-and-groups-endpoints.md).

O exemplo de [código de referência](https://aka.ms/SCIMReferenceCode) de código de azure é um desses recursos que podem saltar para iniciar o seu desenvolvimento. Depois de ter construído o seu ponto final SCIM, vai querer testá-lo. Pode utilizar a recolha de testes de [carteiro](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) fornecidos como parte do código de referência ou passar pelos pedidos/respostas da amostra fornecidos [acima.](#user-operations)  

   > [!Note]
   > O código de referência destina-se a ajudá-lo a começar a construir o seu ponto final SCIM e é fornecido "AS IS". As contribuições da comunidade são bem-vindas para ajudar a construir e manter o código.

A solução é composta por dois projetos, _Microsoft.SCIM_ e _Microsoft.SCIM.WebHostSample_.

O projeto _Microsoft.SCIM_ é a biblioteca que define os componentes do serviço web que está em conformidade com a especificação SCIM. Declara a interface _Microsoft.SCIM.IProvider,_ os pedidos são traduzidos em chamadas para os métodos do fornecedor, que seriam programados para operar numa loja de identidade.

![Repartição: Um pedido traduzido em chamadas para os métodos do fornecedor](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

O projeto _Microsoft.SCIM.WebHostSample_ é um ASP.NET Core Web Application, baseado no modelo _Empty._ Isto permite que o código de amostra seja implantado como autónomo, alojado em contentores ou dentro dos Serviços de Informação da Internet. Também implementa a interface _Microsoft.SCIM.IProvider_ mantendo as aulas na memória como uma loja de identidade de amostra.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Construção de um ponto final personalizado do SCIM

O serviço SCIM deve ter um certificado de autenticação de endereço HTTP e servidor, do qual a autoridade de certificação raiz é um dos seguintes nomes:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Vai papai
* VeriSign
* Rio WoSign
* Raiz DST CA X3

O .NET Core SDK inclui um certificado de desenvolvimento HTTPS que pode ser utilizado durante o desenvolvimento, o certificado é instalado como parte da experiência de primeira execução. Dependendo da forma como executar a aplicação core web ASP.NET, ouvirá uma porta diferente:

* Microsoft.SCIM.WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Para mais informações sobre HTTPS em ASP.NET Utilização do núcleo o seguinte link: [Impor HTTPS em ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Tratamento da autenticação do ponto final

Os pedidos do Azure Ative Directory incluem um token portador de OAuth 2.0. Qualquer serviço que receba o pedido deve autenticar o emitente como sendo a Azure Ative Directory para o inquilino esperado do Azure Ative Directory.

No token, o emitente é identificado por uma reivindicação do ISS, como `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . Neste exemplo, o endereço base do valor da reclamação, `https://sts.windows.net` identifica o Azure Ative Directory como o emitente, enquanto o segmento de endereço relativo, _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, é um identificador único do inquilino do Diretório Ativo Azure para o qual o token foi emitido.

O público para o token será o ID do modelo de candidatura para a aplicação na galeria, cada uma das aplicações registadas num único inquilino pode receber a mesma `iss` reclamação com pedidos de SCIM. O ID do modelo de aplicação para todas as aplicações personalizadas é _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. O símbolo gerado pelo serviço de fornecimento Azure AD só deve ser utilizado para testes. Não deve ser utilizado em ambientes de produção.

No código de amostra, os pedidos são autenticados utilizando o pacote Microsoft.AspNetCore.Authentication.JwtBearer. O seguinte código aplica que os pedidos a qualquer um dos pontos finais do serviço são autenticados usando o token ao portador emitido pela Azure Ative Directory para um inquilino especificado:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Um sinal de portador também é necessário para usar os testes de [carteiro](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) fornecidos e realizar depuragem local usando o local. O código de amostra utiliza ambientes ASP.NET Core para alterar as opções de autenticação durante a fase de desenvolvimento e permitir a utilização de um token auto-assinado.

Para obter mais informações sobre vários ambientes em ASP.NET Utilização do núcleo o seguinte link: [Use vários ambientes em ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

O seguinte código aplica que os pedidos a qualquer um dos pontos finais do serviço são autenticados usando um token ao portador assinado com uma chave personalizada:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Envie um pedido GET ao controlador Token para obter um token ao portador válido, o método _GenerateJSONWebToken_ é responsável por criar um símbolo correspondente aos parâmetros configurados para o desenvolvimento:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Tratamento do fornecimento e desprovisionamento dos utilizadores

***Exemplo 1. Consultar o serviço para um utilizador correspondente** _

O Azure Ative Directory consulta o serviço para um utilizador com um `externalId` valor de atributo correspondente ao valor de atributo mailSname de um utilizador em Azure AD. A consulta é expressa como um pedido de Hipertext Transfer Protocol (HTTP) como este exemplo, em que jyoung é uma amostra de um nome de correioNickname de um utilizador em Azure Ative Directory.

>[!NOTE]
> Este é apenas um exemplo. Nem todos os utilizadores terão um atributo de mailNickname, e o valor que um utilizador tem pode não ser único no diretório. Além disso, o atributo utilizado para a correspondência (que neste caso `externalId` é) é configurável nos [mapeamentos de atributos AD Azure](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

No código de amostra, o pedido é traduzido numa chamada para o método QueryAsync do prestador do serviço. Aqui está a assinatura deste método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

Na consulta da amostra, para um utilizador com um dado valor para o `externalId` atributo, os valores dos argumentos transmitidos ao método QueryAsync são:

_ parâmetros. Filtros Alternativos.Contagem: 1
* parâmetros. AlternateFiltros.ElementAt(0). AtributoPath: "externalId"
* parâmetros. AlternateFiltros.ElementAt(0). ComparaçãoOperador: CompareOperador.Equals
* parâmetros. AlternateFilter.ElementAt(0). ComparaçãoValue: "jyoung"

***Exemplo 2. Provision a um utilizador** _

Se a resposta a uma consulta ao serviço web para um utilizador com um `externalId` valor de atributo que corresponda ao valor de atributo mailSname de um utilizador não devolver nenhum utilizador, então o Azure Ative Directory solicita que a prestação de serviço seja um utilizador correspondente ao do Diretório Ativo Azure.  Aqui está um exemplo de tal pedido: 

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

No código de amostra, o pedido é traduzido numa chamada para o método CreateAsync do prestador do serviço. Aqui está a assinatura deste método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

Num pedido de fornecimento de um utilizador, o valor do argumento de recurso é uma instância da classe Microsoft.SCIM.Core2EnterpriseUser, definida na biblioteca Microsoft.SCIM.Schemas.  Se o pedido de provisionamento do utilizador for bem sucedido, espera-se que a implementação do método devolva uma instância da classe Microsoft.SCIM.Core2EnterpriseUser, com o valor da propriedade Identifier definida para o identificador único do utilizador recém-atado.  

_*_Exemplo 3. Consulta do estado atual de um utilizador_*_ 

Para atualizar um utilizador conhecido por existir numa loja de identidade frontalizada por um SCIM, o Azure Ative Directory procede solicitando ao serviço o estado atual desse utilizador com um pedido como: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

No código de amostra, o pedido é traduzido numa chamada para o método RetrieveAsync do prestador do serviço. Aqui está a assinatura deste método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

No exemplo de um pedido de recuperação do estado atual de um utilizador, os valores das propriedades do objeto fornecidos como o valor do argumento dos parâmetros são os seguintes: 
  
_ Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Exemplo 4. Consulta o valor de um atributo de referência a atualizar** _ 

Se um atributo de referência for atualizado, então o Azure Ative Directory consulta o serviço para determinar se o valor atual do atributo de referência na loja de identidade frontalmente pelo serviço já corresponde ao valor desse atributo no Azure Ative Directory. Para os utilizadores, o único atributo do qual o valor atual é consultado desta forma é o atributo do gestor. Aqui está um exemplo de um pedido para determinar se o atributo do gestor de um objeto de utilizador tem atualmente um determinado valor: No código de amostra o pedido é traduzido numa chamada para o método QueryAsync do prestador do serviço. O valor das propriedades do objeto fornecido como o valor do argumento dos parâmetros são os seguintes: 
  
_ parâmetros. Filtros Alternativos.Contagem: 2
* parâmetros. AlternateFiltros.ElementAt(x). AtributoPata: "ID"
* parâmetros. AlternateFiltros.ElementAt(x). ComparaçãoOperador: CompareOperador.Equals
* parâmetros. AlternateFilter.ElementAt(x). ComparaçãoValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* parâmetros. AlternateFilters.ElementAt(y). AtributoPata: "manager"
* parâmetros. AlternateFilters.ElementAt(y). ComparaçãoOperador: CompareOperador.Equals
* parâmetros. AlternateFilter.ElementAt(y). ComparaçãoValue: "2819c223-7f76-453a-919d-413861904646"
* parâmetros. SolicitadoAttributePaths.ElementAt(0): "ID"
* parâmetros. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Aqui, o valor do índice x pode ser 0 e o valor do índice y pode ser 1, ou o valor de x pode ser 1 e o valor de y pode ser 0, dependendo da ordem das expressões do parâmetro de consulta do filtro.   

***Exemplo 5. Pedido da Azure AD a um serviço SCIM para atualizar um utilizador** _ 

Aqui está um exemplo de um pedido do Azure Ative Directory a um serviço SCIM para atualizar um utilizador: 

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

No código de amostra, o pedido é traduzido numa chamada para o método UpdateAsync do prestador do serviço. Aqui está a assinatura deste método: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

No exemplo de um pedido de atualização de um utilizador, o objeto fornecido como o valor do argumento do patch tem estes valores de propriedade: 
  
_ ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
* (PatchRequest como PatchRequest2). Operações.Contagem: 1
* (PatchRequest como PatchRequest2). Operações.ElementAt(0). OperaçãoName: OperaçãoName.Add
* (PatchRequest como PatchRequest2). Operações.ElementAt(0). Path.AttributePath: "manager"
* (PatchRequest como PatchRequest2). Operações.ElementAt(0). Valor.Contagem: 1
* (PatchRequest como PatchRequest2). Operações.ElementAt(0). Valor.ElementAt(0). Referência: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest como PatchRequest2). Operações.ElementAt(0). Valor.ElementAt(0). Valor: 2819c223-7f76-453a-919d-413861904646

***Exemplo 6. Desprovisione um utilizador** _

Para desprovisionar um utilizador de uma loja de identidade frontalizada por um serviço SCIM, a Azure AD envia um pedido como:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

No código de amostra, o pedido é traduzido numa chamada para o método DeleteAsync do prestador do serviço. Aqui está a assinatura deste método: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

O objeto fornecido como o valor do argumento do gestor de recursos tem estes valores de propriedade no exemplo de um pedido de desprovisionamento de um utilizador: 

_ ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passo 4: Integre o seu ponto final SCIM com o cliente Azure AD SCIM

O Azure AD pode ser configurado para fornecer automaticamente utilizadores e grupos atribuídos a aplicações que implementem um perfil específico do [protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644). As especificidades do perfil estão documentadas no [Passo 2: Compreender a implementação do Azure AD SCIM](#step-2-understand-the-azure-ad-scim-implementation).

Consulte o seu fornecedor de aplicações ou a documentação do seu fornecedor de aplicações para obter declarações de compatibilidade com estes requisitos.

> [!IMPORTANT]
> A implementação do Azure AD SCIM é construída em cima do serviço de fornecimento de utilizadores Azure AD, que é projetado para manter os utilizadores constantemente sincronizados entre a Azure AD e a aplicação-alvo, e implementa um conjunto muito específico de operações padrão. É importante entender estes comportamentos para entender o comportamento do cliente Azure AD SCIM. Para obter mais informações, consulte a secção [Ciclos de Provisionamento: Inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em [Como funciona o provisionamento](how-provisioning-works.md).

### <a name="getting-started"></a>Introdução

As aplicações que suportam o perfil SCIM descrito neste artigo podem ser ligadas ao Azure Ative Directory utilizando a funcionalidade "non-gallery application" na galeria de aplicações AZure AD. Uma vez conectado, o Azure AD executa um processo de sincronização a cada 40 minutos onde consulta o ponto final SCIM da aplicação para utilizadores e grupos designados, e cria ou modifica de acordo com os detalhes da atribuição.

**Para ligar uma aplicação que suporta o SCIM:**

1. Inscreva-se no [portal Azure Ative Directory](https://aad.portal.azure.com). Note que pode ter acesso gratuito a um teste gratuito para O Diretório Ativo Azure com licenças P2 ao inscrever-se no [programa de desenvolvedores](https://developer.microsoft.com/office/dev-program)
2. Selecione **aplicações** Enterprise a partir do painel esquerdo. É mostrada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas da galeria.
3. Selecione **+ Nova aplicação**  >  **+ Crie a sua própria aplicação.**
4. Introduza um nome para a sua aplicação, escolha a opção "*integrar qualquer outra aplicação que não encontre na galeria*" e selecione **Adicionar** para criar um objeto de aplicação. A nova aplicação é adicionada à lista de aplicações empresariais e abre-se ao seu ecrã de gestão de aplicações.

   ![Screenshot mostra a galeria de aplicações Azure AD ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *galeria de aplicações Azure AD*

   > [!NOTE]
   > Se estiver a utilizar a experiência antiga da galeria de aplicações, siga o guia de ecrã abaixo.
   
   ![Screenshot mostra a antiga galeria de aplicações Azure AD experiência ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *Azure AD antiga galeria de aplicações*

5. No ecrã de gestão de aplicações, selecione **Provisioning** no painel esquerdo.
6. No menu **Modo de Provisionamento,** selecione **Automatic**.

   ![Exemplo: Página de Provisionamento de uma aplicação no portal Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configuração do provisionamento no portal Azure*

7. No campo URL do **inquilino,** insira o URL do ponto final SCIM da aplicação. Exemplo: `https://api.contoso.com/scim/`
8. Se o ponto final do SCIM necessitar de um token portador de OAuth de um emitente diferente do Azure AD, em seguida, copie o símbolo do portador de OAuth necessário no campo **token secreto** opcional. Se este campo ficar em branco, o Azure AD inclui um token portador de OAuth emitido a partir de Azure AD a cada pedido. As aplicações que usam o Azure AD como fornecedor de identidade podem validar este token emitido pela AZure AD. 
   > [!NOTE]
   > Não *_é_* recomendado deixar este campo em branco e confiar num símbolo gerado pela Azure AD. Esta opção está disponível principalmente para efeitos de teste.
9. Selecione _ *Ligação de teste** para que o Azure Ative Directory tente ligar-se ao ponto final DO SCIM. Se a tentativa falhar, é apresentada informação de erro.  

    > [!NOTE]
    > **A Ligação** de Teste consulta o ponto final DO SCIM para um utilizador que não existe, utilizando um GUID aleatório como a propriedade correspondente selecionada na configuração AD Azure. A resposta correta esperada é HTTP 200 OK com uma mensagem scim ListResponse vazia.

10. Se as tentativas de ligação à aplicação forem bem sucedidas, então **selecione Guardar** para guardar as credenciais de administração.
11. Na secção **mapeamentos,** existem dois conjuntos selecionáveis de [mapeamentos de atributos:](customize-application-attributes.md)um para objetos do utilizador e outro para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados do Azure Ative Directory para a sua aplicação. Os atributos selecionados como propriedades **de matching** são usados para combinar com os utilizadores e grupos na sua aplicação para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    > [!NOTE]
    > Pode desativar opcionalmente a sincronização de objetos de grupo desativando o mapeamento de "grupos".

12. Em **Definições**, o campo **Scope** define quais os utilizadores e grupos que estão sincronizados. Selecione **Sync apenas utilizadores e grupos atribuídos** (recomendado) apenas para sincronizar utilizadores e grupos atribuídos no **separador Utilizadores e grupos.**
13. Uma vez concluída a sua configuração, desa um conjunto do **Estado de Provisionamento** para **On**.
14. **Selecione Guardar** para iniciar o serviço de fornecimento de Ad Azure.
15. Se sincronizar apenas utilizadores e grupos designados (recomendado), certifique-se de selecionar o **separador Utilizadores e grupos** e atribuir os utilizadores ou grupos que pretende sincronizar.

Uma vez iniciado o ciclo inicial, pode **selecionar registos de provisionamento** no painel esquerdo para monitorizar o progresso, o que mostra todas as ações feitas pelo serviço de fornecimento na sua aplicação. Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](check-status-user-account-provisioning.md).

> [!NOTE]
> O ciclo inicial demora mais tempo a ser efetua do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passo 5: Publique a sua candidatura na galeria de aplicações AZure AD

Se estiver a construir uma aplicação que será usada por mais de um inquilino, pode disponibilizá-la na galeria de candidaturas Azure AD. Isto facilitará às organizações a descoberta da aplicação e a configuração do provisionamento. Publicar a sua aplicação na galeria Azure AD e disponibilizar o provisionamento a outros é fácil. Confira os passos [aqui.](../develop/v2-howto-app-gallery-listing.md) A Microsoft trabalhará consigo para integrar a sua aplicação na nossa galeria, testar o seu ponto final e lançar [documentação](../saas-apps/tutorial-list.md) de bordo para os clientes utilizarem.

### <a name="gallery-onboarding-checklist"></a>Galeria de verificação de bordo
Siga a lista de verificação abaixo para garantir que a sua aplicação está a bordo rapidamente e os clientes têm uma experiência de implementação suave. A informação será recolhida de si quando embarcar na galeria. 
> [!div class="checklist"]
> * Suporte um utilizador [SCIM 2.0](#step-2-understand-the-azure-ad-scim-implementation) e ponto final de grupo (Apenas um é necessário, mas ambos são recomendados)
> * Apoiar pelo menos 25 pedidos por segundo por inquilino para garantir que os utilizadores e grupos sejam aprovisionados e desprovisionados sem demora (Obrigatório)
> * Estabelecer contactos de engenharia e apoio para orientar os clientes post gallery onboarding (Obrigatório)
> * 3 Credenciais de teste não expiradas para a sua aplicação (Requerida)
> * Apoiar a concessão do código de autorização OAuth ou um símbolo de longa duração, conforme descrito abaixo (Obrigatório)
> * Estabeleça um ponto de contacto de engenharia e apoio para apoiar os clientes post gallery onboarding (Obrigatório)
> * Apoiar a atualização de várias associações de grupo com um único PATCH (Recomendado) 
> * Documente publicamente o seu ponto final SCIM (Recomendado) 
> * [Deteção de esquemas de](https://tools.ietf.org/html/rfc7643#section-6) suporte (Recomendado)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorização de provisionamento de conectores na galeria de aplicações
A especificação SCIM não define um regime específico scim para a autenticação e autorização. Baseia-se na utilização das normas industriais existentes. O cliente azure AD que fornece apoio dois métodos de autorização para pedidos na galeria. 

|Método de autorização|Vantagens|Desvantagens|Suporte|
|--|--|--|--|
|Nome de utilizador e palavra-passe (não recomendado ou suportado pelo Azure AD)|Fácil de implementar|Inseguro - [O seu pa$$word não importa](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Suportado caso a caso para aplicações de galeria. Não suportado para aplicações não-galeria.|
|Ficha de portador de longa data|Fichas de longa duração não requerem a presença de um utilizador. São fáceis de utilizar para os administradores na configuração do provisionamento.|Fichas de longa duração podem ser difíceis de partilhar com um administrador sem usar métodos inseguros como e-mail. |Suportado para apps de galeria e não galeria. |
|Concessão de código de autorização OAu|Os tokens de acesso são muito mais curtos do que as palavras-passe, e têm um mecanismo automatizado de atualização que os tokens portadores de longa duração não têm.  Um utilizador real deve estar presente durante a autorização inicial, adicionando um nível de prestação de contas. |Requer que um utilizador esteja presente. Se o utilizador sair da organização, o token é inválido e a autorização terá de ser concluída novamente.|Suportado para apps de galeria, mas não para aplicações não-galeria. No entanto, você pode fornecer um token de acesso na UI como o símbolo secreto para fins de teste de curto prazo. O suporte para a concessão de código OAuth em não-galeria está no nosso atraso, além do suporte para URLs de auth/token configuráveis na aplicação da galeria.|
|Concessão de credenciais de cliente oauth|Os tokens de acesso são muito mais curtos do que as palavras-passe, e têm um mecanismo automatizado de atualização que os tokens portadores de longa duração não têm. Tanto a concessão do código de autorização como as credenciais do cliente concedem criam o mesmo tipo de sinal de acesso, pelo que a deslocação entre estes métodos é transparente para a API.  O provisionamento pode ser completamente automatizado, e novos tokens podem ser solicitados silenciosamente sem a interação do utilizador. ||Não suportado para apps de galeria e não-galeria. O apoio está nos nossos atrasos.|

> [!NOTE]
> Não é aconselhável deixar o campo simbólico em branco na aplicação personalizada de configuração AD AD Azure. O token gerado está principalmente disponível para fins de teste.

**Fluxo de concessão de código de autorização OAuth:** O serviço de prestação suporta a concessão do [código de autorização.](https://tools.ietf.org/html/rfc6749#page-24) Depois de submeter o seu pedido de publicação da sua app na galeria, a nossa equipa trabalhará consigo para recolher as seguintes informações:
*  URL de autorização: UM URL do cliente para obter autorização do proprietário do recurso através da reorientação do agente de utilizador. O utilizador é redirecionado para este URL para autorizar o acesso. Note que este URL não é atualmente configurável por inquilino.
*  URL de troca de token: UM URL do cliente para trocar um subsídio de autorização para um token de acesso, tipicamente com autenticação do cliente. Note que este URL não é atualmente configurável por inquilino.
*  Identificação do cliente: O servidor de autorização emite ao cliente registado um identificador de cliente, que é uma cadeia única que representa as informações de registo fornecidas pelo cliente.  O identificador de clientes não é um segredo; está exposto ao titular do recurso e **não deve** ser utilizado sozinho para a autenticação do cliente.  
*  Segredo do cliente: O segredo do cliente é um segredo gerado pelo servidor de autorização. Deve ser um valor único conhecido apenas pelo servidor de autorização. 

Note que o OAuth v1 não é suportado devido à exposição do segredo do cliente. OAu v2 é suportado.  

Boas práticas (recomendadas, mas não necessárias):
* Suporte urls de redirecionamento múltiplos. Os administradores podem configurar o fornecimento de "portal.azure.com" e "aad.portal.azure.com". O suporte a múltiplos URLs de redirecionamento garantirá que os utilizadores podem autorizar o acesso a partir de qualquer portal.
* Apoie vários segredos para garantir uma renovação secreta suave, sem tempo de inatividade. 

Etapas no fluxo de concessão de código OAuth:
1. Os sinais do utilizador no portal Azure > aplicações enterprise > Select > Provisioning > click authoriz.
2. O portal Azure redireciona o utilizador para o URL de Autorização (sinal na página para a aplicação de terceiros).
3. A administração fornece credenciais para a aplicação de terceiros. 
4. App de terceiros redireciona o utilizador de volta para o portal Azure e fornece o código de subvenção 
5. Os serviços de fornecimento de AD Azure ligam para o URL simbólico e fornecem o código de concessão. A aplicação de terceiros responde com o token de acesso, token de atualização e data de validade
6. Quando o ciclo de provisionamento começa, o serviço verifica se o token de acesso atual é válido e troca-o por um novo token, se necessário. O token de acesso é fornecido em cada pedido feito à app e a validade do pedido é verificada antes de cada pedido.

> [!NOTE]
> Embora não seja possível configurar o OAuth na aplicação não-galeria hoje em dia, pode gerar manualmente um token de acesso a partir do seu servidor de autorização e inserir isso no campo simbólico secreto da aplicação não-galeria. Isto permite-lhe verificar a compatibilidade do seu servidor SCIM com o cliente Azure AD SCIM antes de embarcar na galeria de aplicações, que suporta a concessão de código OAuth.  

**Fichas portadoras de OAuth de longa duração:** Se a sua aplicação não suportar o fluxo de concessão de código de autorização OAuth, também pode gerar um token portador de OAuth de longa duração do que aquele que um administrador pode usar para configurar a integração do provisionamento. O símbolo deve ser perpétuo, ou então o trabalho de provisionamento será [colocado em quarentena](application-provisioning-quarantine-status.md) quando o símbolo expirar.

Para métodos adicionais de autenticação e autorização, informe-nos no [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Lista de verificação de lançamento de galeria ir ao mercado
Para ajudar a impulsionar a consciencialização e a procura da nossa integração conjunta, recomendamos que atualize a documentação existente e amplifique a integração nos seus canais de marketing.  Abaixo está um conjunto de atividades de lista de verificação que recomendamos que complete para apoiar o lançamento

* **Prontidão de vendas e apoio ao cliente.** Certifique-se de que as suas equipas de vendas e suporte estão conscientes e podem falar com as capacidades de integração. Informe a sua equipa de vendas e suporte, forneça-lhes perguntas frequentes e inclua a integração nos seus materiais de venda. 
* **Blog post e/ou comunicado de imprensa.** Faça um post de blog ou um comunicado de imprensa que descreva a integração conjunta, os benefícios e como começar. [Exemplo: Imprivata e Azure Ative Directory Press](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Redes sociais.** Aproveite as suas redes sociais como o Twitter, Facebook ou LinkedIn para promover a integração aos seus clientes. Certifique-se de incluir para @AzureAD que possamos retweetar o seu post. [Exemplo: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Site de marketing.** Crie ou atualize as suas páginas de marketing (por exemplo, página de integração, página de parceiros, página de preços, etc.) para incluir a disponibilidade da integração conjunta. [Exemplo: Página de integração de pingboard](https://pingboard.com/org-chart-for), [página de integração smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [Monday.com página de preços](https://monday.com/pricing/) 
* **Documentação técnica.** Crie um artigo do centro de ajuda ou documentação técnica sobre como os clientes podem começar. [Exemplo: Integração do Diretório Ativo Do Enviado + Microsoft Azure.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Comunicação do cliente.** Alerte os clientes para a nova integração através da comunicação do seu cliente (newsletters mensais, campanhas de email, notas de lançamento do produto). 

## <a name="related-articles"></a>Artigos relacionados

* [Desenvolver uma amostra do ponto final DO SCIM](use-scim-to-build-users-and-groups-endpoints.md)
* [Automatizar o fornecimento e desprovisionamento de utilizadores para aplicações SaaS](user-provisioning.md)
* [Personalize os mapeamentos de atributos para o fornecimento do utilizador](customize-application-attributes.md)
* [Expressãos de escrita para mapeamentos de atributos](functions-for-customizing-application-data.md)
* [Filtros de deteção para o fornecimento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notificações de provisão de conta](user-provisioning.md)
* [Lista de tutoriais sobre como integrar apps saaS](../saas-apps/tutorial-list.md)

