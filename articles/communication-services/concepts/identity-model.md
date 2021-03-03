---
title: Modelo de identidade
titleSuffix: An Azure Communication Services concept
description: Conheça as identidades e fichas de acesso
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 254d35331459e70ad56bcef43569f51ff6f50a93
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656215"
---
# <a name="identity-model"></a>Modelo de identidade

A Azure Communication Services é um serviço de identidade-agnóstico. Este design oferece múltiplos benefícios:

- Reutiliza as identidades existentes do seu sistema de gestão de identidade
- Proporciona flexibilidade para cenários de integração
- Mantém as suas identidades privadas nos Serviços de Comunicação Azure

Em vez de duplicar informação no seu sistema, manterá a relação de mapeamento que o seu caso de negócio requer. Por exemplo, pode mapear identidades 1:1, 1:N, N:1, N:M. Identificadores externos como números de telefone, utilizadores, dispositivos, aplicações e GUIDs não podem ser usados para identidade nos Serviços de Comunicação Azure. Os tokens de acesso que são gerados para uma identidade dos Serviços de Comunicação Azure são usados para aceder a primitivos como chat ou chamada.

## <a name="identity"></a>Identidade

Pode criar identidades utilizando a biblioteca de identidade dos Serviços de Comunicação Azure. Uma identidade serve como identificador nas conversas. É usado para criar fichas de acesso. A mesma identidade pode participar em múltiplas sessões simultâneas em vários dispositivos. Uma identidade pode ter vários tokens de acesso ativo ao mesmo tempo.

A supressão de uma identidade, recurso ou subscrição invalida todos os seus tokens de acesso. Esta ação também elimina todos os dados armazenados para a identidade. Uma identidade eliminada não pode criar novos tokens de acesso ou aceder a dados previamente armazenados (por exemplo, mensagens de chat).

Não é cobrado pelo número de identidades que tem. Em vez disso, és acusado de usar primitivos. O número das suas identidades não tem de restringir a forma como mapeia as identidades da sua aplicação para as identidades dos Serviços de Comunicação Azure.

Com a liberdade de mapeamento vem a responsabilidade de privacidade. Se um utilizador quiser ser eliminado do seu sistema, então tem de eliminar todas as identidades que estão associadas a esse utilizador.

Os Serviços de Comunicação Azure não fornecem identidades especiais para utilizadores anónimos. Não mantém o mapeamento entre os utilizadores e identidades, e não consegue determinar se uma identidade é anónima. Pode desenhar o conceito de identidade para se adaptar às suas necessidades. A nossa recomendação é criar uma nova identidade para cada utilizador anónimo em cada aplicação.

Qualquer pessoa que tenha um token de acesso válido pode aceder ao conteúdo de identidade atual. Por exemplo, os utilizadores podem aceder a mensagens de chat que enviaram. O acesso é restrito apenas a âmbitos que fazem parte do token de acesso. Para mais informações, consulte a secção [De Acesso a Fichas](#access-tokens) neste artigo.

### <a name="identity-mapping"></a>Mapeamento de identidade

Os Serviços de Comunicação Azure não replicam a funcionalidade do sistema de gestão de identidade Azure. Não fornece uma forma de os clientes usarem identidades específicas do cliente. Por exemplo, os clientes não podem usar um número de telefone ou endereço de e-mail. Em vez disso, os Serviços de Comunicação Azure fornecem identificadores únicos. Pode atribuir estes identificadores únicos às identidades da sua aplicação. A Azure Communication Services não armazena qualquer tipo de informação que possa revelar a identidade real dos seus utilizadores.

Para evitar a duplicação de informações no seu sistema, planeie como mapear os utilizadores do seu domínio de identidade para identidades dos Serviços de Comunicação Azure. Pode seguir qualquer tipo de padrão. Por exemplo, pode usar 1:1, 1:N, N:1 ou M:N. Decida se um único utilizador está mapeado para uma única identidade ou para múltiplas identidades.

Quando uma nova identidade for criada, guarde o seu mapeamento para o utilizador ou utilizadores da sua aplicação. Como as identidades requerem acesso a tokens para usar primitivos, a identidade precisa de ser conhecida do utilizador ou utilizador da sua aplicação.

Se utilizar uma base de dados relacional para armazenar informações do utilizador, poderá ajustar o seu design com base no seu cenário de mapeamento. Para cenários que mapeiem 1:1 ou N:1, é melhor adicionar uma `CommunicationServicesId` coluna à mesa para armazenar a identidade dos Serviços de Comunicação Azure. Em cenários que usam a relação 1:N ou N:M, você pode considerar criar uma tabela separada na base de dados relacional.

## <a name="access-tokens"></a>Tokens de acesso

Um token de acesso é um JSON Web Token (JWT) que pode ser usado para ter acesso a primitivos do Serviço de Comunicação Azure. Um símbolo de acesso emitido tem proteção contra a integridade. Ou seja, as suas alegações não podem ser alteradas depois de emitidas. Assim, uma mudança manual de propriedades como identidade, expiração ou âmbitos invalidará o token de acesso. Se os primitivos forem usados com fichas invalidadas, então o acesso será negado aos primitivos.

As propriedades de um token de acesso são:
* A identidade.
* Expiração.
* As miras.

Um token de acesso é sempre válido por 24 horas. Depois de expirar, o token de acesso é invalidado e não pode ser usado para aceder a qualquer primitivo.

Uma identidade precisa de uma forma de solicitar um novo sinal de acesso a partir de um serviço do lado do servidor. O parâmetro *de âmbito* define um conjunto de primitivos que podem ser usados. A Azure Communication Services suporta os seguintes âmbitos de acesso aos tokens.

|Nome|Descrição|
|---|---|
|Chat|  Concede a capacidade de participar de um chat|
|VoIP|  Concede a capacidade de chamar identidades e números de telefone|


Para revogar um token de acesso antes do seu prazo de validade, utilize a biblioteca de identidade dos serviços de comunicação Azure. A revogação simbólica não é imediata. Leva até 15 minutos para se propagar. A remoção de uma identidade, recurso ou subscrição revoga todos os tokens de acesso.

Se pretender remover a capacidade de um utilizador aceder a funcionalidades específicas, revogue todos os tokens de acesso. Em seguida, emita um novo token de acesso que tem um conjunto mais limitado de âmbitos.

Nos Serviços de Comunicação Azure, uma rotação de chaves de acesso revoga todos os tokens de acesso ativo que foram criados utilizando uma antiga chave de acesso. Todas as identidades perdem acesso aos Serviços de Comunicação Azure, e devem emitir novos tokens de acesso.

Recomendamos a emissão de fichas de acesso no seu serviço do lado do servidor e não na aplicação do cliente. O raciocínio é que a emissão requer uma chave de acesso ou uma identidade gerida. Por razões de segurança, não é recomendável partilhar chaves de acesso com a aplicação do cliente.

A aplicação do cliente deve utilizar um ponto final de serviço de confiança que possa autenticar os seus clientes. O ponto final deve emitir fichas de acesso em seu nome. Para mais informações, consulte [a arquitetura do Cliente e do servidor.](./client-and-server-architecture.md)

Se cache fichas de acesso a uma loja de apoio, recomendamos a utilização de encriptação. Um sinal de acesso são dados sensíveis. Pode ser usado para atividades maliciosas se não estiver protegido. Alguém que tenha acesso pode iniciar o SDK e aceder à API. A API acessível é restrita apenas com base nos âmbitos que o token de acesso tem. Recomendamos a emissão de fichas de acesso que tenham apenas os âmbitos necessários.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à gestão de tokens de acesso, consulte [Criar e gerir fichas de acesso.](../quickstarts/access-tokens.md)
* Para uma introdução à autenticação, consulte [Authenticate to Azure Communication Services](./authentication.md).
* Para uma introdução à residência de dados e privacidade, consulte [a disponibilidade da Região e a residência de dados.](./privacy.md)