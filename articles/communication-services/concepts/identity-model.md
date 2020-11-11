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
ms.openlocfilehash: 996f138a14923319381738e7a55cd7ba4e8c4320
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517772"
---
# <a name="identity-model"></a>Modelo de identidade

Azure Communication Services é um serviço agnóstico de identidade. Este design tem múltiplos benefícios:
- Reutilizar as identidades existentes do seu sistema de gestão de identidade
- Flexibilidade para cenários de integração
- Mantém as suas identidades privadas para os Serviços de Comunicação Azure

Em vez de duplicar a informação existente no seu sistema, manterá a relação de mapeamento específica do seu caso de negócio. Por exemplo, mapeamento de identidades 1:1, 1:N, N:1, N:M. Os identificadores externos (tais como números de telefone, utilizadores, dispositivos, aplicações, GUID) não podem ser usados como Identidade de Comunicação Azure. Os tokens de acesso gerados para a identidade do serviço de comunicação Azure são usados para aceder a primitivos como chat ou chamada. 

## <a name="identity"></a>Identidade

As identidades são criadas com a biblioteca da Administração de Serviços de Comunicação Azure. A identidade serve de identificador nas conversas e é usada para a criação de fichas de acesso. A mesma identidade pode participar em múltiplas sessões simultâneas em vários dispositivos. A identidade pode ter vários tokens de acesso ativo ao mesmo tempo. A eliminação de identidade, recurso ou subscrição provoca a invalidação de todos os seus tokens de acesso e eliminação de todos os dados armazenados para esta identidade. A identidade eliminada não pode emitir novos tokens de acesso, nem aceder a dados previamente armazenados (por exemplo, mensagens de chat). 

Não és cobrado pelo número de identidades que tens, mas pelo uso de primitivos. O número de identidades não tem de restringir, como mapear as identidades da sua aplicação para as Identidades dos Serviços de Comunicação Azure. Com a liberdade de mapeamento vem a responsabilidade em termos de privacidade. Quando o utilizador da sua aplicação pretende ser eliminado do seu sistema, tem de eliminar todas as identidades que estavam associadas a esse utilizador.

Os Serviços de Comunicação Azure não fornecem identidades especiais para utilizadores anónimos. Não mantém o mapeamento entre os utilizadores e identidades, não entende se a identidade é anónima. Pode desenhar o conceito para se adaptar às suas necessidades. A nossa recomendação é criar uma nova identidade para o utilizador anónimo de cada aplicação. Com a posse do token de acesso válido, qualquer pessoa pode ter acesso ao conteúdo não apagado da identidade. Por exemplo, mensagens de chat enviadas pelo utilizador. O acesso é restrito apenas a âmbitos, que fazem parte do token de acesso. Mais detalhes sobre os âmbitos estão na secção *Access Token*.

### <a name="mapping-of-identities"></a>Mapeamento de identidades

A Azure Communication Services não está a replicar a funcionalidade do IMS. Não fornece uma forma de os clientes usarem identidades específicas do cliente. Por exemplo, número de telefone ou endereço de e-mail. Em vez disso, fornece identificadores únicos, que pode atribuir às identidades da sua aplicação. A Azure Communication Services não armazena qualquer tipo de informação que possa revelar a identidade real dos seus utilizadores.

Em vez de duplicação, você é encorajado a projetar, como os utilizadores do seu domínio de identidade serão mapeados para identidades do Serviço de Comunicação Azure. Pode seguir qualquer tipo de padrão 1:1, 1:N, N:1 ou M:N. Pode decidir se um único utilizador está mapeado para uma única identidade ou para múltiplas identidades. Quando é criada nova identidade, é encorajado a armazenar o mapeamento desta identidade ao utilizador ou utilizadores da sua aplicação. Como as identidades requerem o acesso aos tokens para uso dos primitivos, a identidade precisa de ser conhecida pelo utilizador ou utilizadores da sua aplicação.

Se estiver a utilizar a base de dados relacional para armazenamento de utilizadores, a implementação pode diferir com base no seu cenário de mapeamento. Para cenários com mapeamento 1:1 ou N:1, pode adicionar uma coluna *CommunicationServicesId* à mesa para armazenar a identidade dos Serviços de Comunicação Azure. Em cenários com a relação 1:N ou N:M, pode considerar a criação de uma tabela separada na base de dados relacional.

## <a name="access-token"></a>Ficha de acesso

O token de acesso é um símbolo JWT que pode ser usado para ter acesso aos primitivos do Serviço de Comunicação Azure. O token de acesso emitido tem proteção contra integridade, as suas alegações não podem ser alteradas após a emissão. Ou seja, a alteração manual de propriedades como identidade, expiração ou âmbitos tornará o token de acesso inválido. O uso de primitivos com fichas invalidadas levará à negação do acesso ao primitivo. 

As propriedades do token de acesso são: *identidade, expiração,* e *âmbitos.* O token de acesso é sempre válido por 24 horas. Após este tempo, o token de acesso é invalidado e não pode ser usado para aceder a qualquer primitivo. A identidade tem de ter uma maneira, como solicitar um novo sinal de acesso do serviço do lado do servidor. O *âmbito do* parâmetro define um conjunto não vazio de primitivos, que podem ser usados. A Azure Communication Services suporta os seguintes âmbitos de acesso aos tokens:

|Nome|Descrição|
|---|---|
|Chat|  Concede a capacidade de participar de um chat|
|VoIP|  Concede a capacidade de chamar identidades e números de telefone|


Se quiser revogar o token de acesso antes da sua expiração, pode utilizar a biblioteca da Administração de Serviços de Comunicação Azure para o fazer. A revogação do símbolo não é imediata e leva até 15 minutos para se propagar. A remoção de identidade, recurso ou subscrição causará a revogação de todos os tokens de acesso. Se desejar remover a capacidade de acesso a funcionalidades específicas de um utilizador, revogue todos os tokens de acesso. Em seguida, emita um novo token de acesso com um conjunto mais limitado de âmbitos.
A rotação das chaves de acesso do Serviço de Comunicações Azure irá causar a revogação de todos os tokens de acesso ativo que foram criados com a antiga chave de acesso. Todas as identidades perderão acesso ao Serviço de Comunicação Azure e são obrigadas a emitir novos tokens de acesso. 

Recomendamos a emissão de fichas de acesso no seu serviço do lado do servidor e não na aplicação do cliente. O raciocínio é que a emissão requer chave de acesso ou para ser gerida identidade. Não é recomendado por razões de segurança partilhar as chaves de acesso com a aplicação do cliente. A aplicação do cliente deve usar o ponto final de serviço fidedignos que pode autenticar os seus clientes e emitir ficha de acesso em seu nome. Mais detalhes sobre a arquitetura podem ser encontrados [aqui.](./client-and-server-architecture.md)

Se cache fichas de acesso a uma loja de apoio, recomendamos a utilização de encriptação. O token de acesso é dados sensíveis e pode ser usado para atividades maliciosas se não estiver protegido. Com a posse do token de acesso, você pode inicializar o SDK e ter acesso à API. A API acessível é restrita apenas com base em âmbitos, que o token de acesso tem. Recomendamos a emissão de fichas de acesso apenas com âmbitos, que sejam necessários.

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução à gestão de tokens de acesso, consulte [Criar e gerir fichas de acesso](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens)
* Para uma introdução à autenticação, consulte [Autenticar os Serviços de Comunicação Azure](https://docs.microsoft.com/azure/communication-services/concepts/authentication)
* Para uma introdução à residência de dados e privacidade, consulte [a disponibilidade da Região e a residência de dados](https://docs.microsoft.com/azure/communication-services/concepts/privacy)