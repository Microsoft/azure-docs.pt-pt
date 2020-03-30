---
title: Autorize o acesso com uma assinatura de acesso partilhado nos Hubs de Eventos Azure
description: Este artigo fornece informações sobre a autorização de acesso aos recursos do Azure Event Hubs utilizando assinaturas de acesso partilhado (SAS).
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: bdb1896f8a40c6de21ae76b536bfccec316341cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "69992799"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado
Uma assinatura de acesso partilhado (SAS) fornece-lhe uma forma de conceder acesso limitado aos recursos no seu espaço de nome S. Hubs de Eventos. A SAS guarda o acesso aos recursos do Event Hubs com base nas regras de autorização. Estas regras são configuradas num espaço de nome, ou numa entidade (centro de eventos ou tópico). Este artigo fornece uma visão geral do modelo SAS, e analisa as melhores práticas da SAS.

## <a name="what-are-shared-access-signatures"></a>O que são assinaturas de acesso partilhada?
Uma assinatura de acesso partilhado (SAS) fornece acesso delegado aos recursos do Event Hubs com base nas regras de autorização. Uma regra de autorização tem um nome, está associada a direitos específicos, e carrega um par de chaves criptográficas. Usa o nome e a chave da regra através dos clientes do Event Hubs ou no seu próprio código para gerar tokens SAS. Um cliente pode então passar o símbolo aos Centros de Eventos para provar a autorização para a operação solicitada.

O SAS é um mecanismo de autorização baseado em pedidos que utiliza fichas simples. Utilizando sas, as chaves nunca são passadas no fio. As chaves são usadas para assinar criptograficamente informações que podem ser verificadas posteriormente pelo serviço. O SAS pode ser utilizado de forma semelhante a um esquema de nome de utilizador e palavra-passe quando o cliente se encontra na posse imediata de um nome de regra de autorização e de uma chave correspondente. A SAS pode ser usada semelhante a um modelo de segurança federado, onde o cliente recebe um sinal de acesso limitado e assinado a partir de um serviço de fichas de segurança sem nunca entrar na posse da chave de assinatura.

> [!NOTE]
> O Azure Event Hubs apoia a autorização para os recursos do Event Hubs utilizando o Azure Ative Directory (Azure AD). Autorizar utilizadores ou aplicações utilizando o token OAuth 2.0 devolvido pela Azure AD proporciona uma segurança e facilidade de utilização superiores sobre assinaturas de acesso partilhado (SAS). Com a AD Azure, não há necessidade de armazenar os tokens no seu código e arriscar potenciais vulnerabilidades de segurança.
>
> A Microsoft recomenda a utilização de AD Azure com as suas aplicações Azure Event Hubs, quando possível. Para mais informações, consulte [Autorizar o acesso ao recurso Azure Event Hubs utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Os tokens SAS (Shared Access Signatures) são fundamentais para proteger os seus recursos. Ao mesmo tempo que proporciona granularidade, a SAS dá aos clientes acesso aos recursos dos seus Centros de Eventos. Não devem ser partilhados publicamente. Ao partilhar, se necessário por razões de resolução de problemas, considere utilizar uma versão reduzida de quaisquer ficheiros de registo ou apagar as fichas SAS (se presentes) dos ficheiros de registo, e certifique-se de que as imagens também não contêm as informações do SAS.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso partilhado
Cada espaço de nome de Event Hubs e cada entidade do Event Hubs (uma instância de hub de eventos ou um tópico kafka) tem uma política de autorização de acesso partilhada composta por regras. A política a nível do espaço de nome aplica-se a todas as entidades dentro do espaço de nome, independentemente da sua configuração política individual.
Para cada regra da política de autorização, você decide sobre três peças de informação: nome, âmbito e direitos. O nome é um nome único nesse âmbito. O âmbito é o URI do recurso em questão. Para um espaço de nome de Event Hubs, o âmbito é `https://<yournamespace>.servicebus.windows.net/`o nome de domínio totalmente qualificado (FQDN), como .

Os direitos previstos na regra política podem ser uma combinação de:
- **Enviar** – Dá o direito de enviar mensagens à entidade
- **Ouvir** – Dá o direito de ouvir ou receber a entidade
- **Gerir** – Dá o direito de gerir a topologia do espaço de nome, incluindo a criação e supressão de entidades

> [!NOTE]
> O Direito **de Gestão** inclui os direitos **de Envio** e **Escuta.**

Um espaço de nome ou política de entidades pode manter até 12 regras de autorização de acesso partilhado, proporcionando espaço para os três conjuntos de regras, cada um cobrindo os direitos básicos, e a combinação de Enviar e Ouvir. Este limite sublinha que a loja de política SAS não se destina a ser uma loja de conta de utilizador ou de serviço. Se a sua aplicação necessitar de conceder acesso aos recursos do Event Hubs com base em identidades de utilizador ou de serviço, deverá implementar um serviço de token de segurança que emita tokens SAS após uma autenticação e verificação de acesso.

É atribuída uma regra de autorização a uma **chave primária** e uma **chave secundária**. Estas chaves são chaves criptograficamente fortes. Não os perca suisse. Estarão sempre disponíveis no portal Azure. Podes usar qualquer uma das chaves geradas, e podes regenera-las a qualquer momento. Se regenerar ou alterar uma chave na política, todos os tokens anteriormente emitidos com base nessa tecla tornam-se instantaneamente inválidos. No entanto, as ligações em curso criadas com base nesses tokens continuarão a funcionar até que o token expire.

Quando cria um espaço de nome dos Hubs de Eventos, uma regra de política chamada **RootManageSharedAccessKey** é criada automaticamente para o espaço de nome. Esta política tem **permissões** para todo o espaço de nome. Recomenda-se que trate esta regra como uma conta de raiz administrativa e não a use na sua aplicação. Pode criar regras de política adicionais no separador **Configure** para o espaço de nome no portal, via PowerShell ou Azure CLI.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS
Quando utiliza assinaturas de acesso partilhado nas suas aplicações, tem de estar ciente de dois riscos potenciais:

- Se um SAS for vazado, pode ser usado por qualquer pessoa que o obtenha, o que pode potencialmente comprometer os seus recursos do Event Hubs.
- Se um SAS fornecido a uma aplicação de cliente expirar e a aplicação não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade da aplicação poderá ser prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

- Para que os **clientes renovem automaticamente o SAS se necessário**: Os clientes devem renovar o SAS muito antes de expirar, para dar tempo para repetições se o serviço que presta o SAS não estiver disponível. Se o seu SAS for destinado a ser utilizado para um pequeno número de operações imediatas e de curta duração que se espera que sejam concluídas dentro do período de validade, então pode ser desnecessário, uma vez que não se espera que o SAS seja renovado. No entanto, se tem um cliente que está a fazer pedidos de rotina via SAS, então a possibilidade de expiração entra em jogo. A principal consideração é equilibrar a necessidade de o SAS ser de curta duração (como anteriormente declarado) com a necessidade de garantir que o cliente está a solicitar a renovação suficientemente cedo (para evitar perturbações devido à expiração do SAS antes de uma renovação bem sucedida).
- **Tenha cuidado com a hora**de início do SAS : Se definir a hora de início para a SAS até **agora**, então devido ao desvio do relógio (diferenças no tempo atual de acordo com diferentes máquinas), as falhas podem ser observadas intermitentemente durante os primeiros minutos. Em geral, desuma a hora de início para pelo menos 15 minutos no passado. Ou, não o despôs, o que o tornará válido imediatamente em todos os casos. O mesmo aplica-se, em geral, também ao prazo de validade. Lembre-se que poderá observador até 15 minutos de rotação do relógio em qualquer direção a qualquer pedido. 
- **Seja específico com o recurso a aceder**: Uma boa prática de segurança é proporcionar ao utilizador os privilégios mínimos exigidos. Se um utilizador apenas necessitar de ler o acesso a uma única entidade, conceda-lhe acesso a essa entidade única e não leia/escreva/apague o acesso a todas as entidades. Também ajuda a diminuir os danos se um SAS for comprometido porque o SAS tem menos poder nas mãos de um intruso.
- **Nem sempre utilize sas**: Às vezes, os riscos associados a uma determinada operação contra os seus Centros de Eventos superam os benefícios do SAS. Para tais operações, crie um serviço de nível médio que escreva para os seus Centros de Eventos após validação, autenticação e auditoria de regras de negócios.
- **Utilize sempre HTTPs**: Utilize sempre o Https para criar ou distribuir um SAS. Se um SAS for transmitido em HTTP e intercetado, um intruso que executa um andido man-in-the-middle é capaz de ler o SAS e, em seguida, usá-lo da forma que o utilizador pretendido poderia ter, potencialmente comprometendo dados sensíveis ou permitindo a corrupção de dados pelo utilizador malicioso.

## <a name="conclusion"></a>Conclusão
As assinaturas de acesso ao share são úteis para fornecer permissões limitadas aos recursos do Event Hubs aos seus clientes. São parte vital do modelo de segurança para qualquer aplicação que utilize hubs de eventos Azure. Se seguir as melhores práticas listadas neste artigo, pode utilizar o SAS para proporcionar uma maior flexibilidade de acesso aos seus recursos, sem comprometer a segurança da sua aplicação.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos relacionados: 

- [Autenticação solicita aos Hubs de Eventos Azure de uma aplicação que usa o Diretório Ativo Azure](authenticate-application.md)
- [Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs do Evento](authenticate-managed-identity.md)
- [Autenticar pedidos aos Hubs de Eventos Azure usando assinaturas de acesso partilhado](authenticate-shared-access-signature.md)
- [Autorizar acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)


