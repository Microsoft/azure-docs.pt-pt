---
title: Autorizar o acesso com uma assinatura de acesso partilhado nos Hubs de Eventos Azure
description: Este artigo fornece informações sobre a autorização de acesso aos recursos do Azure Event Hubs utilizando assinaturas de acesso partilhado (SAS).
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 6a2d7385f82864e8d378055333377fb9c3f73c19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85323124"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado
Uma assinatura de acesso partilhado (SAS) fornece-lhe uma forma de conceder acesso limitado aos recursos no seu espaço de nomes De Event Hubs. A SAS guarda o acesso aos recursos do Event Hubs com base nas regras de autorização. Estas regras são configuradas num espaço de nome, ou numa entidade (centro de eventos ou tópico). Este artigo fornece uma visão geral do modelo SAS e analisa as melhores práticas da SAS.

## <a name="what-are-shared-access-signatures"></a>O que são assinaturas de acesso partilhado?
Uma assinatura de acesso partilhado (SAS) proporciona acesso delegado aos recursos do Event Hubs com base nas regras de autorização. Uma regra de autorização tem um nome, está associada a direitos específicos, e tem um par de chaves criptográficas. Utiliza o nome e a chave da regra através dos clientes Do Event Hubs ou no seu próprio código para gerar fichas SAS. Um cliente pode então passar o token para o Event Hubs para provar a autorização para a operação solicitada.

SAS é um mecanismo de autorização baseado em sinistros que utiliza fichas simples. Utilizando SAS, as chaves nunca são transmitidas no fio. As chaves são usadas para assinar criptograficamente informações que podem ser verificadas posteriormente pelo serviço. O SAS pode ser usado semelhante a um esquema de nome de utilizador e senha onde o cliente está na posse imediata de um nome de regra de autorização e uma chave correspondente. O SAS pode ser usado semelhante a um modelo de segurança federado, onde o cliente recebe um token de acesso limitado e assinado a partir de um serviço de ficha de segurança sem nunca ter entrado na posse da chave de assinatura.

> [!NOTE]
> O Azure Event Hubs suporta autorizar recursos de Event Hubs usando o Azure Ative Directory (Azure AD). Autorizar utilizadores ou aplicações utilizando o símbolo OAuth 2.0 devolvido pela Azure AD proporciona uma segurança superior e facilidade de utilização sobre assinaturas de acesso partilhada (SAS). Com o Azure AD, não há necessidade de armazenar os tokens no seu código e arriscar potenciais vulnerabilidades de segurança.
>
> A Microsoft recomenda a utilização do Azure AD com as suas aplicações Azure Event Hubs, quando possível. Para obter mais informações, consulte [o Acesso autorizado ao recurso Azure Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> Os tokens SAS (Shared Access Signatures) são fundamentais para proteger os seus recursos. Ao fornecer granularidade, a SAS concede aos clientes acesso aos seus recursos Desovio hubs. Não devem ser partilhados publicamente. Ao partilhar, se necessário por razões de resolução de problemas, considere utilizar uma versão reduzida de quaisquer ficheiros de registo ou eliminar os tokens SAS (se presentes) dos ficheiros de registo, e certifique-se de que as imagens também não contêm as informações SAS.

## <a name="shared-access-authorization-policies"></a>Políticas de autorização de acesso partilhado
Cada espaço de nomes de Event Hubs e cada entidade de Event Hubs (uma instância de centro de eventos ou um tópico Kafka) tem uma política de autorização de acesso partilhada composta por regras. A política ao nível do espaço de nome aplica-se a todas as entidades dentro do espaço de nome, independentemente da sua configuração de política individual.
Para cada regra de política de autorização, você decide sobre três peças de informação: nome, âmbito e direitos. O nome é um nome único nesse âmbito. O âmbito é o URI do recurso em questão. Para um espaço de nomes de Event Hubs, o âmbito é o nome de domínio totalmente qualificado (FQDN), como `https://<yournamespace>.servicebus.windows.net/` .

Os direitos previstos na regra política podem ser uma combinação de:
- **Enviar** – Dá o direito de enviar mensagens à entidade
- **Ouvir** – Dá o direito de ouvir ou receber a entidade
- **Gerir** – Dá o direito de gerir a topologia do espaço de nomes, incluindo a criação e eliminação de entidades

> [!NOTE]
> O direito **De gestão** inclui os direitos **de Enviar** e **Ouvir.**

Uma política de espaço de nome ou de entidade pode manter até 12 regras de autorização de acesso partilhado, proporcionando espaço para os três conjuntos de regras, cada uma cobrindo os direitos básicos, e a combinação de Enviar e Ouvir. Este limite sublinha que a loja de política SAS não se destina a ser uma loja de conta de utilizador ou de serviço. Se a sua aplicação precisar de conceder acesso aos recursos do Event Hubs com base em identidades de utilizador ou serviço, deverá implementar um serviço de fichas de segurança que emite fichas SAS após uma verificação de autenticação e acesso.

Uma regra de autorização é atribuída a uma **chave primária** e uma **chave secundária**. Estas chaves são chaves criptograficamente fortes. Não os perca nem os divulgue. Estarão sempre disponíveis no portal Azure. Podes usar qualquer uma das chaves geradas, e podes regenera-las a qualquer momento. Se regenerar ou alterar uma chave na política, todos os tokens previamente emitidos com base nessa chave tornam-se instantaneamente inválidos. No entanto, as ligações em curso criadas com base em tais fichas continuarão a funcionar até que o token expire.

Quando cria um espaço de nomes de Event Hubs, uma regra de política chamada **RootManageSharedAccessKey** é criada automaticamente para o espaço de nomes. Esta política tem permissões **de gestão** para todo o espaço de nome. Recomenda-se que trate esta regra como uma conta de raiz administrativa e não a utilize na sua aplicação. Pode criar regras de política adicionais no **separador Configurar** para o espaço de nomes no portal, via PowerShell ou Azure CLI.

## <a name="best-practices-when-using-sas"></a>Práticas recomendadas ao usar SAS
Quando utiliza assinaturas de acesso partilhado nas suas aplicações, tem de estar ciente de dois riscos potenciais:

- Se um SAS for vazado, pode ser usado por qualquer pessoa que o obtenha, o que pode potencialmente comprometer os seus recursos de Event Hubs.
- Se um SAS fornecido a uma aplicação de cliente expirar e a aplicação não conseguir recuperar um novo SAS do seu serviço, então a funcionalidade da aplicação pode ser prejudicada.

As seguintes recomendações para a utilização de assinaturas de acesso partilhado podem ajudar a mitigar estes riscos:

- **Ter os clientes a renovar automaticamente o SAS se necessário**: Os clientes devem renovar o SAS bem antes de expirar, para dar tempo para as retrações se o serviço de fornecimento do SAS estiver indisponível. Se o seu SAS for destinado a ser utilizado para um pequeno número de operações imediatas e de curta duração que se espera que sejam concluídas dentro do período de validade, então pode ser desnecessário, uma vez que não se espera que o SAS seja renovado. No entanto, se tiver cliente que está a fazer pedidos rotineiramente através do SAS, então a possibilidade de expiração entra em jogo. A principal consideração é equilibrar a necessidade de o SAS ser de curta duração (como anteriormente declarado) com a necessidade de garantir que o cliente está a solicitar a renovação suficientemente cedo (para evitar perturbações devido à expiração do SAS antes de uma renovação bem sucedida).
- **Tenha cuidado com a hora de início do SAS**: Se definir a hora de início para o SAS **até agora**, então devido a distorção do relógio (diferenças no tempo atual de acordo com diferentes máquinas), as avarias podem ser observadas intermitentemente durante os primeiros minutos. Em geral, desema esta hora de início para pelo menos 15 minutos no passado. Ou não o desemoquila, o que o tornará válido imediatamente em todos os casos. O mesmo se aplica, em geral, também ao prazo de validade. Lembre-se de que pode observadorar até 15 minutos de relógio em qualquer direção em qualquer pedido. 
- **Seja específico com o recurso a ser acedido:** Uma boa prática de segurança é proporcionar ao utilizador os privilégios mínimos exigidos. Se um utilizador apenas precisar de ter acesso lido a uma única entidade, então conceda-lhes que leia o acesso a essa entidade única e não leia/escreva/apague o acesso a todas as entidades. Também ajuda a diminuir os danos se um SAS estiver comprometido porque o SAS tem menos poder nas mãos de um intruso.
- **Nem sempre utilize SAS**: Por vezes, os riscos associados a uma determinada operação contra os seus Centros de Eventos superam os benefícios da SAS. Para tais operações, crie um serviço de nível médio que escreva para os seus Centros de Eventos após validação, autenticação e auditoria às regras de negócio.
- **Utilize sempre HTTPs**: Utilize sempre https para criar ou distribuir um SAS. Se um SAS for ultrapassado http e intercetado, um intruso que executa uma ligação homem-no-meio é capaz de ler o SAS e, em seguida, usá-lo como o utilizador pretendido poderia ter, potencialmente comprometendo dados sensíveis ou permitindo a corrupção de dados pelo utilizador malicioso.

## <a name="conclusion"></a>Conclusão
As assinaturas de acesso ao share são úteis para fornecer permissões limitadas aos recursos do Event Hubs aos seus clientes. São parte vital do modelo de segurança para qualquer aplicação utilizando os Azure Event Hubs. Se seguir as melhores práticas listadas neste artigo, pode utilizar o SAS para proporcionar uma maior flexibilidade de acesso aos seus recursos, sem comprometer a segurança da sua aplicação.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos relacionados: 

- [Autenticar pedidos aos Azure Event Hubs a partir de uma aplicação utilizando o Azure Ative Directory](authenticate-application.md)
- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos dos Hubs de Eventos](authenticate-managed-identity.md)
- [Autenticar pedidos para Azure Event Hubs usando Assinaturas de Acesso Partilhado](authenticate-shared-access-signature.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md)


