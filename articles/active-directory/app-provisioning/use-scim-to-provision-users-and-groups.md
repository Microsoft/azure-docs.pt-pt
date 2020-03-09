---
title: Desenvolver um ponto final do SCIM para o fornecimento de utilizadores a apps da Azure AD
description: O sistema de gestão de identidade de domínio transversal (SCIM) normaliza o fornecimento automático de utilizadores. Aprenda a desenvolver um ponto final do SCIM, integre o seu SCIM API com o Diretório Ativo Azure e comece a automatizar utilizadores e grupos de aprovisionamento nas suas aplicações na nuvem.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42fc10c1e7e88e36e4d2174671702e043fb96538
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2020
ms.locfileid: "78926849"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Construir um ponto final SCIM e configurar o fornecimento de utilizadores com o Azure Ative Directory (Azure AD)

Como desenvolvedor de aplicações, pode utilizar o Sistema de Gestão de Identidade de Domínio Transversal (SCIM) para permitir o fornecimento automático de utilizadores e grupos entre a sua aplicação e a Azure AD. Este artigo descreve como construir um ponto final do SCIM e integrar-se com o serviço de provisionamento de AD Azure. A especificação SCIM fornece um esquema comum de utilização para o fornecimento. Quando utilizado em conjunto com padrões da federação como o SAML ou o OpenID Connect, o SCIM oferece aos administradores uma solução de ponta a ponta, baseada em padrões para a gestão de acesso.

O SCIM é uma definição padronizada de dois pontos finais: um ponto final /Utilizadores e um ponto final /Grupos. Utiliza verbos REST comuns para criar, atualizar e eliminar objetos, e um esquema pré-definido para atributos comuns como nome de grupo, nome de utilizador, primeiro nome, apelido e e-mail. As aplicações que oferecem um SCIM 2.0 REST API podem reduzir ou eliminar a dor de trabalhar com uma API de gestão de utilizadores proprietária. Por exemplo, qualquer cliente SCIM conforme sabe como fazer uma PUBLICAÇÃO HTTP de um objeto JSON ao ponto final /Utilizadores para criar uma nova entrada de utilizador. Em vez de necessitar de uma API ligeiramente diferente para as mesmas ações básicas, as aplicações que se conformam com a norma SCIM podem instantaneamente tirar partido de clientes, ferramentas e código pré-existentes. 

![Provisionamento da Azure AD para uma app com SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

O esquema de objeto de utilizador padrão e as APIs de repouso para gestão definidas no SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) permitem que os fornecedores de identidade e apps se integrem mais facilmente entre si. Os desenvolvedores de aplicações que constroem um ponto final do SCIM podem integrar-se com qualquer cliente compatível com o SCIM sem ter de fazer trabalho personalizado.

Automatizar o provisionamento a uma aplicação requer a construção e integração de um ponto final sCIM com o cliente Azure AD SCIM. Execute as seguintes etapas para começar a fornecer utilizadores e grupos na sua aplicação. 
    
  * **[Passo 1: Desenhe o seu esquema de utilizador e grupo.](#step-1-design-your-user-and-group-schema)** Identifique os objetos e atributos que a sua aplicação necessita, e determine como mapeiam para o utilizador e grupo esquema suportado pela implementação do Azure AD SCIM.

  * **[Passo 2: Compreender a implementação do Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Compreenda como o cliente Azure AD SCIM é implementado e modele o seu tratamento e respostas de pedidos de protocolo SCIM.

  * **[Passo 3: Construir um ponto final sCIM.](#step-3-build-a-scim-endpoint)** Um ponto final deve ser compatível com o SCIM 2.0 para integrar com o serviço de provisionamento AD Azure. Como opção, pode utilizar bibliotecas e amostras de código da Microsoft Common Language Infrastructure (CLI) para construir o seu ponto final. Estas amostras destinam-se apenas a referência e a testes; recomendamos que não codificar a sua aplicação de produção para tomar uma dependência deles.

  * **[Passo 4: Integre o seu ponto final sCIM com o cliente Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Se a sua organização estiver a utilizar uma aplicação de terceiros que implemente o perfil do SCIM 2.0 que o Azure AD suporta, pode começar a automatizar o fornecimento e o desprovisionamento de utilizadores e grupos imediatamente.

  * **[Passo 5: Publique a sua candidatura na galeria de aplicações da AD Azure.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Facilite aos clientes a descoberta da sua aplicação e configurar facilmente o fornecimento. 

![Passos para integrar um ponto final do SCIM com a AD Azure](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Passo 1: Desenhe o seu utilizador e o seu esquema de grupo

Cada aplicação requer diferentes atributos para criar um utilizador ou grupo. Inicie a sua integração identificando os objetos (utilizadores, grupos) e atributos (nome, gestor, título de trabalho, etc.) que a sua aplicação necessita. A norma SCIM define um esquema para gerir utilizadores e grupos. O esquema principal do utilizador requer apenas três atributos: **id** (identificador definido pelo prestador de serviços), **id externo** (identificador definido pelo cliente) e **meta** (metaapenas de leitura mantido pelo prestador de serviços). Todos os outros atributos são opcionais. Além do esquema principal do utilizador, a norma SCIM define uma extensão de utilizador empresarial e um modelo para alargar o esquema do utilizador para satisfazer as necessidades da sua aplicação. Se, por exemplo, a sua aplicação necessitar do gestor de um utilizador, pode utilizar o esquema do utilizador da empresa para recolher o gestor do utilizador e o esquema principal para recolher o e-mail do utilizador. Para desenhar o seu esquema, siga os passos abaixo:
  1. Enumera rindo os atributos que a sua aplicação requer. Pode ser útil decompor os seus requisitos nos atributos necessários para a autenticação (por exemplo, loginName e e-mail), atributos necessários para gerir o ciclo de vida do utilizador (por exemplo, estado/ativo), e outros atributos necessários para que a sua aplicação específica funcione (por exemplo, gestor, etiqueta).
  2. Verifique se esses atributos já estão definidos no esquema principal do utilizador ou no esquema do utilizador da empresa. Se precisar de quaisquer atributos de que necessita e não estiver coberto no núcleo ou no utilizador da empresa, terá de definir uma extensão ao esquema do utilizador que cubra os atributos de que necessita. No exemplo abaixo, adicionámos uma extensão ao utilizador para permitir o fornecimento de uma "etiqueta" num utilizador. É melhor começar com apenas o núcleo e os schemas de utilizador da empresa e expandir-se para schemas personalizados adicionais mais tarde.  
  3. Mapeie os atributos SCIM aos atributos do utilizador em Azure AD. Se um dos atributos que definiu no seu ponto final do SCIM não tiver uma contrapartida clara no esquema de utilizador da AD Azure, há uma boa hipótese de os dados não serem armazenados no objeto do utilizador na maioria dos inquilinos. Considere se este atributo pode ser opcional para a criação de um utilizador. Se o atributo for fundamental para a sua candidatura funcionar, guie o administrador do inquilino para estender o seu esquema ou utilize um atributo de extensão, como mostrado abaixo para a propriedade "tags".

### <a name="table-1-outline-the-attributes-that-you-need"></a>Quadro 1: Delineie os atributos de que precisa 
| Passo 1: Determinar atribui a sua aplicação| Passo 2: Requisitos de aplicações de mapas para padrão SCIM| Passo 3: Mapa SCIM atribui aos atributos Da AD Azure|
|--|--|--|
|loginName|userName|userPrincipalName|
|firstName|name.givenName|givenName|
|lastName|nome.lastName|lastName|
|workMail|E-mails[tipo eq "work"].value|Correio|
|Manager|Manager|Manager|
|tag|urn:ietf:params:scim:schemas:extensão:2.0:CustomExtension:tag|extensãoAtribuir1|
|status|ativo|isSoftDeleted (valor computorizado não armazenado no utilizador)|

O esquema acima definido seria representado utilizando a carga útil Json abaixo. Note que além dos atributos necessários para a aplicação, a representação jSON inclui os atributos "id", "externalId" e "meta" necessários.

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
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Quadro 2: Mapeamento predefinido do utilizador
Em seguida, pode utilizar a tabela abaixo para entender como os atributos que a sua aplicação requer podem mapear para um atributo em Azure AD e no SCIM RFC. Pode [personalizar](customize-application-attributes.md) como os atributos são mapeados entre o Azure AD e o seu ponto final sCIM. Note que não precisa de suportar tanto utilizadores como grupos ou todos os atributos abaixo mostrados. São uma referência para a forma como os atributos em Azure AD são frequentemente mapeados para propriedades no protocolo SCIM. 

| Utilizador do Active Directory do Azure | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |ativo |
|Departamento|urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:department|
| displayName |displayName |
|employeeId|urn:ietf:params:scim:schemas:extens:enterprise:2.0:Utilizador:empregadoNúmero|
| TelephoneNumber de fax |. Value de phoneNumbers [tipo eq "fax"] |
| givenName |name.givenName |
| jobTitle |Título |
| mail |. Value de e-mails [tipo eq "trabalho"] |
| mailNickname |externalId |
| Manager |urn:ietf:params:scim:schemas:extens:enterprise:2.0:User:manager |
| móvel |. Value de phoneNumbers [tipo eq "móvel"] |
| postalCode |.postalCode endereços [tipo eq "trabalho"] |
| Endereços de proxy |e-mails de [tipo é igual a "other"]. Valor |
| physical-Delivery-OfficeName |endereços [tipo é igual a "other"]. Formatado |
| streetAddress |.streetAddress endereços [tipo eq "trabalho"] |
| Apelido |name.familyName |
| Número de telefone |. Value de phoneNumbers [tipo eq "trabalho"] |
| utilizador PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Quadro 3: Mapeamento de atributo de grupo padrão

| Grupo do Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| mail |. Value de e-mails [tipo eq "trabalho"] |
| mailNickname |displayName |
| membros |membros |
| objectId |externalId |
| proxyAddresses |e-mails de [tipo é igual a "other"]. Valor |

Existem vários pontos finais definidos no RFC SCIM. Pode começar com o ponto final /Utilizador e depois expandir a partir daí. O ponto final /Schemas é útil quando se utilizam atributos personalizados ou se o seu esquema muda frequentemente. Permite que um cliente recupere automaticamente o esquema mais atualizado. O ponto final /Granel é especialmente útil quando se apoiam grupos. O quadro abaixo descreve os vários pontos finais definidos na norma SCIM. O ponto final /Schemas é útil quando se utilizam atributos personalizados ou se o seu esquema muda frequentemente. Permite que um cliente recupere automaticamente o esquema mais atualizado. O ponto final /Granel é especialmente útil quando se apoiam grupos. O quadro abaixo descreve os vários pontos finais definidos na norma SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Quadro 4: Determine os pontos finais que gostaria de desenvolver
|PONTO FINAL|DESCRIÇÃO|
|--|--|
|/Utilizador|Execute operações CRUD num objeto de utilizador.|
|/Group|Execute operações crud num objeto de grupo.|
|/ServiceProviderConfig|Fornece detalhes sobre as características da norma SCIM que são suportadas, por exemplo, os recursos que são suportados e o método de autenticação.|
|/Tipos de Recursos|Especifica metadados sobre cada recurso|
|/Schemas|O conjunto de atributos suportados por cada cliente e prestador de serviços pode variar. Enquanto um prestador de serviços pode incluir "nome", "título" e "e-mails", enquanto outro prestador de serviços usa "nome", "título" e "números de telefone". O ponto final dos schemas permite a descoberta dos atributos suportados.|
|/Granel|As operações a granel permitem-lhe efetuar operações numa grande recolha de objetos de recursos numa única operação (por exemplo, atualizar as adesões para um grande grupo).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Passo 2: Compreender a implementação do Azure AD SCIM
> [!IMPORTANT]
> O comportamento da implementação do Azure AD SCIM foi atualizado pela última vez em 18 de dezembro de 2018. Para obter informações sobre o que mudou, consulte o [protocolo SCIM 2.0 de conformidade do serviço de fornecimento de utilizadores DaAzure AD](application-provisioning-config-problem-scim-compatibility.md).

Se está a construir uma aplicação que suporta uma API de gestão de utilizadores SCIM 2.0, esta secção descreve em detalhe como o cliente Azure AD SCIM é implementado. Também mostra como modelar o seu tratamento de pedidos de protocolo SCIM e respostas. Uma vez implementado o seu ponto final SCIM, pode testá-lo seguindo o procedimento descrito na secção anterior.

No âmbito da especificação do [protocolo SCIM 2.0,](http://www.simplecloud.info/#Specification)a sua aplicação deve satisfazer estes requisitos:

* Suporta a criação de utilizadores e, opcionalmente, também grupos, de acordo com a secção [3.3 do protocolo SCIM.](https://tools.ietf.org/html/rfc7644#section-3.3)  
* Suporta modificar utilizadores ou grupos com pedidos patch, de acordo com a [secção 3.5.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Suporta a recuperação de um recurso conhecido para um utilizador ou grupo criado anteriormente, de acordo com a [secção 3.4.1 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Suporta consulta de utilizadores ou grupos, de acordo com a secção [3.4.2 do protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Por padrão, os utilizadores são recuperados pelos seus `id` e questionados pelos seus `username` e `externalid`, e os grupos são questionados por `displayName`.  
* Suporta consulta do utilizador por ID e por gestor, de acordo com a secção 3.4.2 do protocolo SCIM.  
* Apoia os grupos de consulta por ID e por membro, de acordo com a secção 3.4.2 do protocolo SCIM.  
* Aceita um único símbolo do portador para autenticação e autorização da Azure AD para o seu pedido.

Siga estas orientações gerais ao implementar um ponto final do SCIM para garantir a compatibilidade com a AD Azure:

* `id` é uma propriedade necessária para todos os recursos. Cada resposta que derreta um recurso deve garantir que cada recurso tem esta propriedade, exceto para `ListResponse` com zero membros.
* A resposta a um pedido de consulta/filtro deve ser sempre uma `ListResponse`.
* Os grupos são opcionais, mas apenas apoiados se a implementação do SCIM apoiar os pedidos do PATCH.
* Não é necessário incluir todo o recurso na resposta patch.
* O Microsoft Azure AD utiliza apenas os seguintes operadores:  
    - `eq`
    - `and`
* Não exija uma correspondência sensível a casos em elementos estruturais no SCIM, em particular os valores de funcionamento do PATCH `op`, tal como definidos em https://tools.ietf.org/html/rfc7644#section-3.5.2. A AD Azure emite os valores de 'op' como `Add`, `Replace`e `Remove`.
* O Microsoft Azure AD solicita que um utilizador e um grupo aleatórios garantam que o ponto final e as credenciais são válidos. Também é feito como parte do fluxo de **Conexão de Teste** no [portal Azure.](https://portal.azure.com) 
* O atributo em que os recursos podem ser consultados deve ser definido como um atributo correspondente na aplicação no [portal Azure](https://portal.azure.com). Para mais informações, consulte [Personalizar os mapeamentos de atributos](customize-application-attributes.md) de fornecimento de utilizadores

### <a name="user-provisioning-and-deprovisioning"></a>Fornecimento e desprovisionamento de utilizadores

A seguinte ilustração mostra as mensagens que o Azure Ative Directory envia para um serviço SCIM para gerir o ciclo de vida de um utilizador na loja de identidade da sua aplicação.  

![mostra a sequência de fornecimento e dedeprovisionamento do utilizador](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Sequência de fornecimento e deprovisionamento de utilizadores*

### <a name="group-provisioning-and-deprovisioning"></a>Prestação e desprovisionamento de grupos

O fornecimento e a desprovisionamento do grupo são facultativos. Quando implementada e ativada, a seguinte ilustração mostra as mensagens que a Azure AD envia para um serviço SCIM para gerir o ciclo de vida de um grupo na loja de identidade da sua aplicação.  Estas mensagens diferem das mensagens sobre os utilizadores de duas formas:

* Os pedidos de recuperação de grupos especificam que os membros atributos devem ser excluídos de qualquer recurso fornecido em resposta ao pedido.  
* Os pedidos para determinar se um atributo de referência tem um determinado valor são pedidos sobre o atributo de membros.  

![mostra a sequência de fornecimento e dedeprovisionamento do grupo](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Sequência de provisionamento e deprovisionamento de grupos*

### <a name="scim-protocol-requests-and-responses"></a>Pedidos e respostas protocolares do Protocolo SCIM
Esta secção fornece os pedidos de SCIM emitidos pelo cliente Azure AD SCIM e respostas esperadas exemplo. Para obter os melhores resultados, deve codificar a sua aplicação para lidar com estes pedidos neste formato e emitir as respostas esperadas.

> [!IMPORTANT]
> Para compreender como e quando o serviço de fornecimento de utilizadores da AD Azure emite as operações descritas abaixo, consulte os ciclos de [provisionamento da secção: Inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em Como funciona o [provisionamento](how-provisioning-works.md).

[Operações de Utilizador](#user-operations)
  - [Criar utilizador](#create-user) [(Solicitar](#request) [resposta](#response) / )
  - [Obter utilizador](#get-user) [(Pedido](#request-1) / [Resposta)](#response-1)
  - [Obtenha o Utilizador por consulta](#get-user-by-query) [(Pedido](#request-2) / [Resposta)](#response-2)
  - [Obtenha o Utilizador por consulta - Resultados zero](#get-user-by-query---zero-results) [(Pedido](#request-3)
/ [Resposta)](#response-3)
  - [Utilizador atualizado [propriedades multi-valorizadas]](#update-user-multi-valued-properties) ([Pedido](#request-4) /  [Resposta)](#response-4)
  - [Utilizador atualizado [propriedades de valor único]](#update-user-single-valued-properties) ([Pedido](#request-5)
/ [Resposta)](#response-5) 
  - [Utilizador desativado](#disable-user) [(Pedido](#request-14) / 
[Resposta)](#response-14)
  - [Excluir utilizador](#delete-user) [(Pedido](#request-6) / 
[Resposta)](#response-6)


[Operações de Grupo](#group-operations)
  - [Criar grupo](#create-group) [(Pedido](#request-7) / [Resposta)](#response-7)
  - [Obter Grupo](#get-group) ( [Pedido](#request-8) / [Resposta)](#response-8)
  - [Obtenha o Grupo por displayName](#get-group-by-displayname) [(Pedido](#request-9) / [Resposta)](#response-9)
  - [Grupo de Atualização [Atributos não membros]](#update-group-non-member-attributes) ([Pedido](#request-10) /
 [Resposta)](#response-10)
  - [Grupo de Atualização [Adicionar Membros]](#update-group-add-members) ( [Pedido](#request-11) /
[Resposta)](#response-11)
  - [Grupo de Atualização [Remover Membros]](#update-group-remove-members) ( [Pedido](#request-12) /
[Resposta)](#response-12)
  - [Eliminar grupo](#delete-group) [(Pedido](#request-13) /
[Resposta)](#response-13)

### <a name="user-operations"></a>Operações de Utilizador

* Os utilizadores podem ser consultados por `userName` ou `email[type eq "work"]` atributos.  

#### <a name="create-user"></a>Criar utilizador

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

#### <a name="get-user"></a>Obter utilizador

###### <a name="request-1"></a>Pedido
*GET /Utilizadores/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Resposta (Utilizador encontrado)
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

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Resposta (Utilizador não encontrado. Note que o detalhe não é necessário, apenas estado.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Obtenha o Utilizador por consulta

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

#### <a name="get-user-by-query---zero-results"></a>Obtenha o Utilizador por consulta - Resultados zero

##### <a name="request-3"></a>Pedido

*GET /Users?filter=userName eq "utilizador inexistente"*

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

#### <a name="update-user-multi-valued-properties"></a>Utilizador atualizado [Propriedades multi-valorizadas]

##### <a name="request-4"></a>Pedido

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

#### <a name="update-user-single-valued-properties"></a>Utilizador atualizado [Propriedades de valor único]

##### <a name="request-5"></a>Pedido

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

### <a name="disable-user"></a>Desativar utilizador

##### <a name="request-14"></a>Pedido

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

##### <a name="response-14"></a>Resposta

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

##### <a name="request-6"></a>Pedido

*DELETE /Utilizadores/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="group-operations"></a>Operações de Grupo

* Os grupos serão sempre criados com uma lista de membros vazios.
* Os grupos podem ser consultados pelo atributo `displayName`.
* A atualização ao pedido do patch do grupo deve produzir um *HTTP 204 No Content* na resposta. Devolver um corpo com uma lista de todos os membros não é aconselhável.
* Não é necessário apoiar a devolução de todos os membros do grupo.

#### <a name="create-group"></a>Criar Grupo

##### <a name="request-7"></a>Pedido

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

##### <a name="response-7"></a>Resposta

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

#### <a name="get-group"></a>Obter grupo

##### <a name="request-8"></a>Pedido

*GET /Grupos/40734ae655284ad3abcc?excluídoS=membros HTTP/1.1*

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

#### <a name="get-group-by-displayname"></a>Obter Grupo por displayName

##### <a name="request-9"></a>Pedido
*GET /Groups?excluiuOsAtributos=membros e filtrante=displayName eq "displayName" HTTP/1.1*

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

#### <a name="update-group-non-member-attributes"></a>Grupo de Atualização [Atributos não membros]

##### <a name="request-10"></a>Pedido

*PATCH /Grupos/fa2ce26709934589afc5 HTTP/1.1*
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

*HTTP/1.1 204 Sem Conteúdo*

### <a name="update-group-add-members"></a>Grupo de Atualização [Adicionar Membros]

##### <a name="request-11"></a>Pedido

*PATCH /Grupos/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="update-group-remove-members"></a>Grupo de Atualização [Remover Membros]

##### <a name="request-12"></a>Pedido

*PATCH /Grupos/a99962b9f99d4c4fac67 HTTP/1.1*
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

*HTTP/1.1 204 Sem Conteúdo*

#### <a name="delete-group"></a>Eliminar Grupo

##### <a name="request-13"></a>Pedido

*DELETE /Grupos/cdb1ce18f6594079d37 HTTP/1.1*

##### <a name="response-13"></a>Resposta

*HTTP/1.1 204 Sem Conteúdo*

### <a name="security-requirements"></a>Requisitos de segurança
**Versões de Protocolo TLS**

As únicas versões aceitáveis do protocolo TLS são TLS 1.2 e TLS 1.3. Não são permitidas outras versões de TLS. Nenhuma versão do SSL é permitida. 
- As teclas RSA devem ter pelo menos 2.048 bits.
- As teclas ECC devem ser pelo menos 256 bits, geradas com uma curva elíptica aprovada


**Comprimentos-chave**

Todos os serviços devem utilizar certificados X.509 gerados com teclas criptográficas de comprimento suficiente, o que significa:

**Suítes Cipher**

Todos os serviços devem ser configurados para utilizar as seguintes suítes cifra, na ordem exata especificada abaixo. Note que se tiver apenas um certificado RSA, as suites de cifra ECDSA não têm qualquer efeito. </br>

TLS 1.2 Cifra Suites barra mínima:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Passo 3: Construir um ponto final do SCIM

Agora que desenhou o seu esquema e compreendeu a implementação do Azure AD SCIM, pode começar a desenvolver o seu ponto final sCIM. Em vez de começar do zero e construir a implementação completamente por conta própria, pode contar com uma série de bibliotecas SCIM de código aberto publicadas pela commuinty SCIM.  
O código de [referência](https://aka.ms/SCIMReferenceCode) de código de referência .NET Core de código de referência publicado pela equipa de provisionamento da AD Azure é um desses recursos que podem iniciar o seu desenvolvimento. Assim que construíres o teu ponto final do SCIM, vais querer testá-lo. Pode utilizar a recolha de [testes carteiros](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) fornecidos como parte do código de referência ou executar através dos pedidos/respostas da amostra [fornecidos acima .](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations)  

Nota: O código de referência destina-se a ajudá-lo a começar a construir o seu ponto final SCIM e é fornecido "AS IS". As contribuições da comunidade são bem-vindas para ajudar a construir e manter o código. 

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Passo 4: Integre o seu ponto final do SCIM com o cliente Azure AD SCIM

A Azure AD pode ser configurada para fornecer automaticamente utilizadores e grupos atribuídos a aplicações que implementem um perfil específico do [protocolo SCIM 2.0.](https://tools.ietf.org/html/rfc7644) As especificidades do perfil estão documentadas no [passo 2: Compreender a implementação do Azure AD SCIM](#step-2-understand-the-azure-ad-scim-implementation).

Contacte o seu fornecedor do aplicativo, ou na documentação do seu fornecedor de aplicações para declarações de estado de compatibilidade com esses requisitos.

> [!IMPORTANT]
> A implementação do Azure AD SCIM baseia-se em cima do serviço de fornecimento de utilizadores Da Azure AD, que é projetado para manter constantemente os utilizadores sincronizados entre a AD Azure e a aplicação-alvo, e implementa um conjunto muito específico de operações padrão. É importante compreender estes comportamentos para entender o comportamento do cliente Azure AD SCIM. Para mais informações, consulte a secção [Ciclos de provisionamento: Inicial e incremental](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) em Como funciona o [provisionamento](how-provisioning-works.md).

### <a name="getting-started"></a>Introdução

Aplicações que suportam o perfil SCIM descrito neste artigo podem ser ligadas ao Azure Active Directory usando o recurso de "aplicativo externas à Galeria" na Galeria de aplicações do Azure AD. Assim que estiver ligado, do Azure AD é executado um processo de sincronização de todos os 40 minutos em que ele consulta o ponto final SCIM do aplicativo para os utilizadores atribuídos e grupos e cria ou modifica-los, de acordo com os detalhes de atribuição.

**Para ligar uma aplicação que suporta o SCIM:**

1. Inscreva-se no [portal de Diretório Ativo Azure.](https://aad.portal.azure.com) Note que pode ter acesso a um teste gratuito para O Diretório Ativo Azure com licenças P2, inscrevendo-se no programa de [desenvolvimento](https://developer.microsoft.com/office/dev-program)
2. Selecione **aplicações Enterprise** a partir do painel esquerdo. É apresentada uma lista de todas as aplicações configuradas, incluindo aplicações que foram adicionadas a partir da galeria.
3. Selecione **+ Nova aplicação** > **Aplicação Não-galeria** ** > .**
4. Introduza um nome para a sua aplicação e selecione **Adicionar** para criar um objeto de aplicação. A nova aplicação é adicionada à lista de aplicações empresariais e abre-se para o seu ecrã de gestão de aplicações.

   ![Screenshot mostra a galeria de aplicações da AD Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galeria de aplicações da Azure AD*

5. No ecrã de gestão de aplicações, selecione **Provisioning** no painel esquerdo.
6. No menu modo de **provisionamento,** **selecione Automático**.

   ![Exemplo: Uma página de provisionamento de uma aplicação no portal Azure](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Configuração do provisionamento no portal Azure*

7. No campo URL do **Arrendatário,** introduza o URL do ponto final do SCIM da aplicação. Exemplo: https://api.contoso.com/scim/
8. Se o ponto final do SCIM necessitar de um símbolo do portador da OAuth de um emitente que não seja o Azure AD, então copie o símbolo necessário do portador oAuth no campo **opcional secret Token.** Se este campo ficar em branco, a Azure AD inclui um token oauth bearer emitido a partir de Azure AD a cada pedido. As aplicações que utilizam o Azure AD como fornecedor de identidade podem validar este token emitido pelo Azure AD. 
   > [!NOTE]
   > Não ***é*** aconselhável deixar este campo em branco e contar com um símbolo gerado pela Azure AD. Esta opção está disponível principalmente para fins de teste.
9. Selecione **Ligação** de Teste para que o Diretório Ativo Azure tente ligar-se ao ponto final do SCIM. Se a tentativa falhar, a informação de erro é apresentada.  

    > [!NOTE]
    > **Teste De Ligação** consulta o ponto final do SCIM para um utilizador que não existe, utilizando um GUID aleatório como a propriedade correspondente selecionada na configuração da AD Azure. A resposta correta esperada é HTTP 200 OK com uma mensagem SCIM ListResponse vazia.

10. Se as tentativas de ligação à aplicação forem bem sucedidas, então selecione **Save** para salvar as credenciais de administração.
11. Na secção **Mapeamentos,** existem dois conjuntos selecionáveis de [mapeamentos de atributos:](customize-application-attributes.md)um para objetos de utilizador e outro para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os utilizadores e grupos na sua aplicação para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    > [!NOTE]
    > Opcionalmente, pode desativar a sincronização de objetos de grupo, desativando os mapeamento de "grupos".

12. Em **Definições,** o campo **Scope** define quais os utilizadores e grupos sincronizados. Selecione **Sync apenas utilizadores e grupos atribuídos** (recomendado) apenas para sincronizar utilizadores e grupos atribuídos no separador **Utilizadores e grupos.**
13. Uma vez concluída a sua configuração, detete o Estado de **Provisionamento** para **On**.
14. Selecione **Guardar** para iniciar o serviço de provisionamento Azure AD.
15. Se sincronizar apenas os utilizadores e grupos atribuídos (recomendado), certifique-se de selecionar o separador **Utilizadores e grupos** e atribuir os utilizadores ou grupos que pretende sincronizar.

Uma vez iniciado o ciclo inicial, pode selecionar registos de **provisionamento** no painel esquerdo para monitorizar o progresso, o que mostra todas as ações feitas pelo serviço de provisionamento na sua aplicação. Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](check-status-user-account-provisioning.md)de conta de utilizador .

> [!NOTE]
> O ciclo inicial demora mais tempo a realizar do que as sincronizações posteriores, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em execução.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Passo 5: Publique a sua candidatura na galeria de aplicações da AD Azure

Se estiver a construir uma aplicação que será usada por mais de um inquilino, pode disponibilizá-la na galeria de aplicações da AD Azure. Isto facilitará às organizações a descoberta da aplicação e o fornecimento configurado. Publicar a sua aplicação na galeria Azure AD e disponibilizar o provisionamento a outros é fácil. Confira os degraus [aqui.](../develop/howto-app-gallery-listing.md) A Microsoft trabalhará consigo para integrar a sua aplicação na nossa galeria, testar o seu ponto final e lançar [documentação](../saas-apps/tutorial-list.md) de bordo para os clientes usarem. 

### <a name="gallery-onboarding-checklist"></a>Lista de verificação onboarding de galeria
Siga a lista de verificação abaixo para garantir que a sua aplicação está a bordo rapidamente e que os clientes têm uma experiência de implementação suave. A informação será recolhida quando embarcar na galeria. 
> [!div class="checklist"]
> * Suporte um utilizador [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) e ponto final do grupo (apenas um é necessário, mas ambos são recomendados)
> * Apoio de pelo menos 25 pedidos por segundo por inquilino (Obrigatório)
> * Estabelecer contactos de engenharia e suporte para orientar clientes post gallery onboarding (Obrigatório)
> * 3 Credenciais de teste sem validade para a sua aplicação (Necessária)
> * Apoiar a concessão do código de autorização da OAuth ou um token de longa duração, como descrito abaixo (Exigido)
> * Estabeleça um ponto de contacto de engenharia e suporte para apoiar clientes post gallery onboarding (Obrigatório)
> * Suporte a atualizar vários membros do grupo com um único PATCH (Recomendado) 
> * Documente publicamente o seu ponto final do SCIM (Recomendado) 
> * [Apoiar a descoberta do esquema](https://tools.ietf.org/html/rfc7643#section-6) (Recomendado)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorização para provisionamento de conectores na galeria de aplicações
A especificação SCIM não define um regime específico de autenticação e autorização do SCIM. Baseia-se na utilização das normas industriais existentes. O cliente de fornecimento de adsad Azure suporta dois métodos de autorização para pedidos na galeria. 

|Método de autorização|Profissionais de TI|Contras|Suporte|
|--|--|--|--|
|Nome de utilizador e palavra-passe (não recomendado ou suportado pela AD Azure)|Fácil de implementar|Inseguro - [O seu pa$$word não importa](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Suportado caso a caso para apps de galeria. Não suportado para aplicações não-galeria.|
|Símbolo de porta-portadores de longa duração|Fichas de longa duração não requerem que um utilizador esteja presente. São fáceis de utilizar os administradores na criação do provisionamento.|Fichas de longa duração podem ser difíceis de partilhar com um administrador sem usar métodos inseguros como e-mail. |Suportado para apps de galeria e não-galeria. |
|Concessão de código de autorização DaOUth|Os tokens de acesso são muito mais curtos do que as palavras-passe, e têm um mecanismo de atualização automatizado que os tokens portadores de longa duração não têm.  Um utilizador real deve estar presente durante a autorização inicial, adicionando um nível de prestação de contas. |Requer que um utilizador esteja presente. Se o utilizador deixar a organização, o token é inválido e a autorização terá de ser concluída novamente.|Suportado para apps de galeria. O suporte para aplicações não-galeria está em andamento.|
|Bolsa de credenciais de cliente OAuth|Os tokens de acesso são muito mais curtos do que as palavras-passe, e têm um mecanismo de atualização automatizado que os tokens portadores de longa duração não têm. Tanto a concessão do código de autorização como as credenciais de cliente criam o mesmo tipo de ficha de acesso, pelo que a mudança entre estes métodos é transparente para a API.  O provisionamento pode ser completamente automatizado, e novos tokens podem ser solicitados silenciosamente sem a interação do utilizador. ||Não suportado para apps de galeria e não-galeria. O apoio está no nosso atraso.|

[!NOTE] Não é aconselhável deixar o campo simbólico em branco na aplicação personalizada de configuração de configuração de adi. O símbolo gerado está principalmente disponível para fins de teste.

Fluxo de concessão de código de **autorização OAuth:** O serviço de provisionamento apoia a concessão do código de [autorização.](https://tools.ietf.org/html/rfc6749#page-24) Depois de submeter o seu pedido de publicação da sua app na galeria, a nossa equipa trabalhará consigo para recolher as seguintes informações:
*  URL de autorização: Um URL do cliente para obter autorização do proprietário do recurso através de redirecionamento do utilizador-agente. O utilizador é redirecionado para este URL para autorizar o acesso. 
*  URL de troca de token: Um URL do cliente para trocar uma bolsa de autorização para um token de acesso, tipicamente com autenticação do cliente.
*  ID do cliente: O servidor de autorização emite ao cliente registado um identificador de cliente, que é uma cadeia única que representa as informações de registo fornecidas pelo cliente.  O identificador de cliente não é um segredo; é exposto ao proprietário do recurso e **não deve** ser utilizado sozinho para autenticação do cliente.  
*  Segredo do cliente: O segredo do cliente é um segredo gerado pelo servidor de autorização. Deve ser um valor único conhecido apenas pelo servidor de autorização. 

Note que o OAuth v1 não é suportado devido à exposição do segredo do cliente. OAuth v2 é apoiado.  

Boas práticas (recomendadas mas não necessárias):
* Suporte vários URLs redirecionais. Os administradores podem configurar o provisionamento tanto de "portal.azure.com" como de "aad.portal.azure.com". O suporte a vários URLs redirecionados garantirá que os utilizadores podem autorizar o acesso a partir de qualquer um dos portais.
* Apoie vários segredos para garantir uma renovação secreta suave, sem tempo de descanso. 

Há muito vivido o portador de **oAuth tokens:** Se o seu pedido não apoiar o fluxo de concessão de código de autorização OAuth, também pode gerar um token de porta-outh de longa duração do que o que um administrador pode usar para configurar a integração de provisionamento. O símbolo deve ser perpétuo, ou então o trabalho de provisionamento será [colocado em quarentena](application-provisioning-quarantine-status.md) quando o símbolo expirar. Esta ficha deve estar abaixo de 1KB em tamanho.  

Para obter métodos adicionais de autenticação e autorização, informe-nos no [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Lista de verificação de lançamento de galeria go-to-market
Para ajudar a impulsionar a consciencialização e a procura da nossa integração conjunta, recomendamos que atualize a sua documentação existente e amplifique a integração nos seus canais de marketing.  Abaixo é um conjunto de atividades de lista de verificação que recomendamos que complete para apoiar o lançamento

* **Disponibilidade de vendas e apoio ao cliente.** Certifique-se de que as suas equipas de vendas e suporte estão conscientes e podem falar com as capacidades de integração. Informe a sua equipa de vendas e suporte, forneça-lhes PERGUNTAS DE INFORMAÇÃO e inclua a integração nos seus materiais de vendas. 
* **Post de blog e/ou comunicado de imprensa.** Crie uma publicação de blogue ou um comunicado de imprensa que descreva a integração conjunta, os benefícios e como começar. [Exemplo: Imprivata e Azure Ative Directory Press Release](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Redes sociais.** Aproveite as suas redes sociais como o Twitter, Facebook ou LinkedIn para promover a integração aos seus clientes. Certifique-se de incluir @AzureAD para que possamos retweetar o seu post. [Exemplo: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Site de marketing.** Crie ou atualize as suas páginas de marketing (por exemplo, página de integração, página de parceiros, página de preços, etc.) para incluir a disponibilidade da integração conjunta. [Exemplo: Página de integração de pingboard](https://pingboard.com/org-chart-for), [página de integração smartsheet,](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad) [página de preços Monday.com](https://monday.com/pricing/) 
* **Documentação técnica.** Crie um artigo do centro de ajuda ou documentação técnica sobre como os clientes podem começar. [Exemplo: Envoy + Microsoft Azure Ative Directory integração.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Comunicação com o cliente.** Alerte os clientes da nova integração através da comunicação do seu cliente (newsletters mensais, campanhas de email, notas de lançamento de produtos). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Permitir endereços IP utilizados pelo serviço de provisionamento Da Azure Para efazer pedidos sCIM

Certas aplicações permitem o tráfego de entrada na sua aplicação. Para que o serviço de provisionamento da AD Azure funcione como esperado, os endereços IP utilizados devem ser permitidos. Para obter uma lista de endereços IP para cada etiqueta/região de serviço, consulte o ficheiro JSON - [Gamas Ip Azure e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço – Nuvem Pública . Pode descarregar e programar estes IPs na sua firewall, conforme necessário. As gamas IP reservadas para o provisionamento de AD Azure podem ser encontradas em "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Artigos relacionados

* [Automatizar o fornecimento e o desprovisionamento de utilizadores para aplicações SaaS](user-provisioning.md)
* [Personalize mapeamentos de atributos para o fornecimento de utilizadores](customize-application-attributes.md)
* [Expressões de escrita para mapeamento de atributos](functions-for-customizing-application-data.md)
* [Filtros de deteção para o fornecimento do utilizador](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notificações de provisionamento de contas](user-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicações do SaaS](../saas-apps/tutorial-list.md)
