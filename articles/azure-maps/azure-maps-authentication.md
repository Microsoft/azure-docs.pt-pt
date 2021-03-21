---
title: Autenticação com Microsoft Azure Maps
titleSuffix: Azure Maps
description: 'Saiba mais sobre duas formas de autenticar pedidos no Azure Maps: autenticação de chaves partilhadas e autenticação do Azure Ative Directory (Azure AD).'
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9c973859c8b7a3d04693946f50377837c3538b85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094107"
---
# <a name="authentication-with-azure-maps"></a>Autenticação com o Azure Maps

O Azure Maps suporta duas formas de autenticar pedidos: autenticação de chave partilhada e autenticação [do Azure Ative Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md) Este artigo explica ambos os métodos de autenticação para ajudar a orientar a sua implementação dos serviços Azure Maps.

> [!NOTE]
> Para melhorar a comunicação segura com o Azure Maps, apoiamos agora a Segurança da Camada de Transporte (TLS) 1.2, e estamos a retirar o suporte para TLS 1.0 e 1.1. Se utilizar atualmente o TLS 1.x, avalie a sua prontidão TLS 1.2 e desenvolva um plano de migração com os testes descritos na [resolução do problema TLS 1.0](/security/solving-tls1-problem).

## <a name="shared-key-authentication"></a>Autenticação de chave partilhada

 As chaves primárias e secundárias são geradas após a criação da conta Azure Maps. Você é encorajado a usar a chave primária como chave de subscrição ao ligar para Azure Maps com autenticação de chave partilhada. A autenticação da Chave Partilhada passa uma chave gerada por uma conta Azure Maps para um serviço Azure Maps. Para cada pedido aos serviços do Azure Maps, adicione a *chave de subscrição* como parâmetro ao URL. A chave secundária pode ser usada em cenários como alterações de chaves rolantes.  

Para obter informações sobre a visualização das suas chaves no portal Azure, consulte [a autenticação Manage](./how-to-manage-authentication.md#view-authentication-details).

> [!TIP]
> Por razões de segurança, recomenda-se que rode entre as suas teclas primárias e secundárias. Para rodar as teclas, atualize a sua aplicação para utilizar a tecla secundária, desloque-a e, em seguida, pressione o botão ciclo/atualização ao lado da tecla primária para gerar uma nova tecla primária. A velha chave primária será desativada. Para obter mais informações sobre a rotação da chave, consulte [Configurar o Cofre da Chave Azure com rotação e auditoria de chaves](../key-vault/secrets/tutorial-rotation-dual.md)

## <a name="azure-ad-authentication"></a>Autenticação do Azure AD

As Assinaturas Azure são fornecidas com um inquilino Azure AD para permitir o controlo de acesso fino. O Azure Maps oferece autenticação para os serviços Azure Maps utilizando a Azure AD. A Azure AD fornece autenticação baseada em identidade para utilizadores e aplicações registadas no inquilino AZURE AD.

O Azure Maps aceita fichas de acesso **OAuth 2.0** para inquilinos AD Azure associados a uma subscrição do Azure que contém uma conta Azure Maps. Azure Maps também aceita fichas para:

* Utilizadores da AD AZure
* Aplicações de parceiros que utilizam permissões delegadas pelos utilizadores
* Identidades geridas dos recursos do Azure

O Azure Maps gera um *identificador único (ID do cliente)* para cada conta Azure Maps. Pode solicitar fichas da Azure AD quando combinar este ID do cliente com parâmetros adicionais.

Para obter mais informações sobre como configurar a Azure AD e solicitar fichas para Azure Maps, consulte [a autenticação Manage no Azure Maps](./how-to-manage-authentication.md).

Para obter informações gerais sobre a autenticação com Azure AD, veja [o que é a autenticação?](../active-directory/develop/authentication-vs-authorization.md)

### <a name="managed-identities-for-azure-resources-and-azure-maps"></a>Identidades geridas para recursos Azure e Azure Maps

[Identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) fornecem aos serviços Azure um principal de segurança baseado em aplicações gerida automaticamente que pode autenticar com Azure AD. Com o controlo de acesso baseado em funções Azure (Azure RBAC), o diretor de segurança de identidade gerido pode ser autorizado a aceder aos serviços do Azure Maps. Alguns exemplos de identidades geridas incluem: Azure App Service, Azure Functions e Azure Virtual Machines. Para obter uma lista de identidades geridas, consulte [identidades geridas para os recursos da Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

### <a name="configuring-application-azure-ad-authentication"></a>Configuração da aplicação Azure AD autenticação

As candidaturas autenticar-se-ão junto do inquilino Azure AD utilizando um ou mais cenários apoiados fornecidos pela Azure AD. Cada cenário de aplicação AD Azure representa diferentes requisitos baseados nas necessidades do negócio. Algumas aplicações podem requerer experiências de inscrição do utilizador e outras aplicações podem exigir uma experiência de inscrição na aplicação. Para mais informações, consulte [fluxos de autenticação e cenários de aplicação](../active-directory/develop/authentication-flows-app-scenarios.md).

Após a aplicação receber um token de acesso, o SDK e/ou o pedido envia um pedido HTTPS com o seguinte conjunto de cabeçalhos HTTP necessários, além de outros cabeçalhos REST API HTTP:

| Nome do cabeçalho    | Valor               |
| :------------- | :------------------ |
| x-ms-cliente-id | 30d7cc....9f55        |
| Autorização  | Portador eyJ0e....HNIVN |

> [!NOTE]
> `x-ms-client-id` é o GUID baseado em conta do Azure Maps que aparece na página de autenticação do Azure Maps.

Aqui está um exemplo de um pedido de rota Azure Maps que usa um símbolo do portador de OAuth Azure AD:

```http
GET /route/directions/json?api-version=1.0&query=52.50931,13.42936:52.50274,13.43872
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

Para obter informações sobre a visualização do ID do seu cliente, consulte [os detalhes da autenticação](./how-to-manage-authentication.md#view-authentication-details).

## <a name="authorization-with-role-based-access-control"></a>Autorização com controlo de acesso baseado em funções

O Azure Maps suporta o acesso a todos os tipos principais de [controlo de acesso baseado em funções Azure (Azure RBAC),](../role-based-access-control/overview.md) incluindo: utilizadores individuais de AD Azure, grupos, aplicações, recursos Azure e identidades geridas Azure. Os principais tipos recebem um conjunto de permissões, também conhecidas como definição de função. Uma definição de papel fornece permissões para as ações da API REST. A aplicação de acesso a uma ou mais contas do Azure Maps é conhecida como um âmbito. Ao aplicar um principal, definição de função e âmbito, então uma atribuição de papel é criada. 

As próximas secções discutem conceitos e componentes da integração do Azure Maps com o Azure RBAC. Como parte do processo de criação da sua conta Azure Maps, um diretório AD Azure está associado à subscrição Azure que a conta Azure Maps reside. 

Ao configurar o Azure RBAC, escolha um diretor de segurança e aplique-o a uma tarefa de função. Para aprender a adicionar atribuições de funções no portal Azure, consulte [as funções De Atribuição Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="picking-a-role-definition"></a>Escolher uma definição de papel

Existem os seguintes tipos de definição de função para apoiar cenários de aplicação.

| Definição de papel azul       | Description                                                                                              |
| :-------------------------- | :------------------------------------------------------------------------------------------------------- |
| Leitor de dados Azure Maps      | Fornece acesso a APIs de REST Imutável do Azure Maps.                                                       |
| Colaborador de dados do Azure Maps | Fornece acesso a AZure Maps REST APIs mutáveis. A mutabilidade é definida pelas ações: escrever e apagar. |
| Definição de função personalizada      | Crie um papel trabalhado para permitir o acesso flexível e restrito às APIs REST do Azure Maps.                      |

Alguns serviços da Azure Maps podem necessitar de privilégios elevados para executar ações de escrita ou eliminação em APIs de REST Azure Maps. A azure Maps Data Contributor é necessária para serviços que fornecem ações de escrita ou eliminação. A tabela a seguir descreve quais os serviços para os quais o Azure Maps Data Contributor é aplicável ao utilizar ações de escrita ou de eliminação no serviço dado. Se apenas forem utilizadas ações de leitura no serviço, então o Azure Maps Data Reader pode ser utilizado em vez de Azure Maps Data Contributor.

| Serviço Azure Maps | Definição de função de mapas Azure  |
| :----------------- | :-------------------------- |
| Dados               | Colaborador de dados do Azure Maps |
| Creator            | Colaborador de dados do Azure Maps |
| Espacial            | Colaborador de dados do Azure Maps |

Para obter informações sobre a visualização das suas definições de RBAC Azure, consulte [Como configurar o Azure RBAC para Azure Maps](./how-to-manage-authentication.md).

#### <a name="custom-role-definitions"></a>Definições de função personalizadas

Um aspeto da segurança das aplicações é aplicar o princípio do menor privilégio. Este princípio implica que o diretor de segurança só deve ter acesso necessário e não ter acesso adicional. A criação de definições de funções personalizadas pode suportar casos de uso que requerem maior granularidade para aceder ao controlo. Para criar uma definição de função personalizada, pode selecionar ações de dados específicas para incluir ou excluir para a definição.

A definição de função personalizada pode então ser usada numa atribuição de funções para qualquer diretor de segurança. Para saber mais sobre as definições de funções personalizadas da Azure, consulte [os papéis personalizados da Azure.](../role-based-access-control/custom-roles.md)

Aqui estão alguns cenários de exemplo onde as funções personalizadas podem melhorar a segurança da aplicação.

| Scenario                                                                                                                                                                                                                 | Ação de dados de funções personalizadas                                                                                                                  |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------ |
| Uma página web de inscrição pública ou interativa com azulejos de mapa base e nenhuma outra APIs de REST.                                                                                                                              | `Microsoft.Maps/accounts/services/render/read`                                                                                              |
| Uma aplicação que requer apenas geocoding inverso e nenhuma outra APIs de REST.                                                                                                                                             | `Microsoft.Maps/accounts/services/search/read`                                                                                              |
| Um papel para um principal de segurança que solicita a leitura de dados do mapa baseados em Azure Maps Creator e mapa base de azulejos REST APIs.                                                                                                 | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/render/read`                                                |
| Um papel para um principal de segurança que requer leitura, escrita e eliminação de dados do mapa baseados no Criador. Isto pode ser definido como um papel de editor de dados de mapa, mas não permite o acesso a outras APIs de REST como telhas de mapa base. | `Microsoft.Maps/accounts/services/data/read`, `Microsoft.Maps/accounts/services/data/write`, `Microsoft.Maps/accounts/services/data/delete` |

### <a name="understanding-scope"></a>Compreender o âmbito

Ao criar uma atribuição de papel, é definido dentro da hierarquia de recursos Azure. No topo da hierarquia está um grupo de [gestão](../governance/management-groups/overview.md) e o mais baixo é um recurso Azure, como uma conta Azure Maps.
Atribuir uma atribuição de funções a um grupo de recursos pode permitir o acesso a várias contas ou recursos do Azure Maps no grupo.

> [!TIP]
> A recomendação geral da Microsoft é atribuir acesso ao âmbito da conta Azure Maps porque impede **o acesso não intencional a outras contas do Azure Maps existentes** na mesma subscrição do Azure.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Azure RBAC, veja
> [!div class="nextstepaction"]
> [Controlo de acesso baseado em funções do Azure](../role-based-access-control/overview.md)

Para saber mais sobre a autenticação de uma aplicação com Azure AD e Azure Maps, consulte
> [!div class="nextstepaction"]
> [Gerir a autenticação em Azure Maps](./how-to-manage-authentication.md)

Para saber mais sobre a autenticação do Azure Maps Map Control com AZure AD, consulte
> [!div class="nextstepaction"]
> [Use o controlo do mapa dos mapas Azure Maps](./how-to-use-map-control.md)