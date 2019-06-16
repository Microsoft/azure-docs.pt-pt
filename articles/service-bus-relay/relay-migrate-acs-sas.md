---
title: Migrar do serviço de controle de acesso do Azure Active Directory para autorização de assinatura de acesso partilhado | Documentos da Microsoft
description: Migrar aplicativos do serviço de controlo de acesso para SAS
services: service-bus-relay
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: 7f71b6884413309e6806658f25313c22e074a71b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64686386"
---
# <a name="migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Migrar do serviço de controle de acesso do Azure Active Directory para autorização de assinatura de acesso partilhado

Aplicações de reencaminhamento do Azure, historicamente, tinham uma opção de usar dois modelos de autorização diferentes: os [assinatura de acesso partilhado (SAS)](../service-bus-messaging/service-bus-sas.md) fornecidas diretamente pelo serviço de reencaminhamento de modelo de token e um modelo de federado onde o gerenciamento de regras de autorização é gerido dentro de, pelo [do Azure Active Directory](/azure/active-directory/) Access Control Service (ACS) e tokens de obteve do ACS são transmitidos para o reencaminhamento para autorizar o acesso aos recursos desejados.

O modelo de autorização de ACS há muito tempo foi substituído pelas [autorização de SAS](../service-bus-messaging/service-bus-authentication-and-authorization.md) como o modelo preferencial e toda a documentação, exemplos e documentação de orientação exclusivamente utilizam SAS hoje mesmo. Além disso, já não é possível criar novos espaços de nomes de reencaminhamento que são emparelhados com o ACS.

SAS tem a vantagem em que não é imediatamente dependente de outro serviço, mas pode ser utilizado diretamente a partir de um cliente sem qualquer intermediários ao dar o acesso de cliente para a chave de regra e o nome da regra SAS. SAS também podem ser facilmente integrada com uma abordagem em que um cliente tem de passar primeiro por uma verificação de autorização com outro serviço e, em seguida, é emitido um token. A última abordagem é semelhante ao padrão de utilização de ACS, mas permite que a emissora tokens de acesso com base em condições específicas do aplicativo que são difíceis de express no ACS.

Para todos os aplicativos existentes que dependem dos ACS, recomendamos vivamente que os clientes a migrar os aplicativos contar com SAS em vez disso.

## <a name="migration-scenarios"></a>Cenários de migração

ACS e o reencaminhamento são integradas por meio do conhecimento partilhado de um *chave de assinatura*. A chave de assinatura é utilizada por um espaço de nomes do ACS para assinar os tokens de autorização e é utilizado pelo reencaminhamento do Azure para verificar que o token foi emitido por espaço de nomes emparelhado do ACS. O espaço de nomes do ACS armazena identidades de serviço e regras de autorização. As regras de autorização definem a identidade de serviço ou o fornecedor de identidade de qual token emitido por um externo obtém o tipo de acesso a uma parte do espaço de nomes reencaminhamento gráfico, na forma de uma correspondência de prefixo mais longo.

Por exemplo, uma regra de ACS pode conceder a **enviar** o prefixo do caminho de afirmação `/` para uma identidade de serviço, que significa que um token emitido por ACS com base na regra concede o cliente direitos para enviar para todas as entidades no espaço de nomes. Se o prefixo do caminho estiver `/abc`, a identidade é restrita a enviar para entidades com o nome `abc` ou organizadas sob esse prefixo. Pressupõe-se que os leitores desta orientação de migração já estão familiarizados com esses conceitos.

Os cenários de migração enquadram-se em três categorias principais:

1.  **Inalterado predefinições**. Alguns clientes usam uma [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objeto, passando o gerado automaticamente **proprietário** serviço de identidade e a chave secreta para o espaço de nomes do ACS, emparelhado com o espaço de nomes do reencaminhamento e fazer Não adicione novas regras.

2.  **Identidades de serviço personalizado com regras simples**. Alguns clientes adicionar novas identidades de serviço e conceder a cada nova identidade de serviço **enviar**, **escutar**, e **gerir** permissões de uma entidade específica.

3.  **Identidades de serviço personalizado com regras complexas**. Muito poucos clientes têm regra complexa conjuntos em quais tokens emitidos externamente são mapeados para direitos no reencaminhamento, ou onde é atribuída uma identidade de serviço único diferenciados direitos em vários caminhos de espaço de nomes através de várias regras.

Para obter assistência com a migração de conjuntos de regras complexas, pode contactar [suporte do Azure](https://azure.microsoft.com/support/options/). Os outros dois cenários ative a migração direta.

### <a name="unchanged-defaults"></a>Predefinições inalteradas

Se seu aplicativo não tiver sido alterado predefinições de ACS, pode substituir todas as [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) utilização com um [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) de objeto e usar o namespace pré-configurada  **RootManageSharedAccessKey** em vez do ACS **proprietário** conta. Tenha em atenção que, mesmo com o ACS **proprietário** conta, esta configuração foi (e continua a ser) geralmente não recomendada, uma vez que esta conta/regra fornece a autoridade de gestão completa sobre o espaço de nomes, incluindo a permissão para eliminar quaisquer entidades.

### <a name="simple-rules"></a>Regras simples

Se a aplicação utilizar identidades de serviço personalizado com regras simples, a migração é simples no caso em que uma identidade de serviço ACS foi criada para fornecer controlo de acesso num reencaminhamento específico. Este cenário é muitas vezes o caso em soluções de SaaS estilo em que cada reencaminhamento serve como uma ponte para um site de inquilino ou filial e a identidade de serviço é criada para esse site específico. Neste caso, a identidade de serviço correspondentes pode ser migrada para uma regra de assinatura de acesso partilhado, diretamente no reencaminhamento do. O nome do serviço de identidade pode tornar-se o nome da regra SAS e a chave de identidade de serviço pode tornar-se a chave de regra SAS. Os direitos da regra SAS são, em seguida, o equivalente configurado para o ACS respectivamente aplicável de regra para a entidade.

Pode fazer esta configuração nova e adicional de SAS no local em qualquer espaço de nomes existente que está Federado com o ACS e a migração para fora do ACS, em seguida, é efetuada utilizando [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) em vez de [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). O espaço de nomes não precisa de ser desassociado do ACS.

### <a name="complex-rules"></a>Regras complexas

Regras SAS não devem ser contas, mas são nomeadas associadas com direitos de chaves de assinatura. Como tal, os cenários em que a aplicação cria várias identidades de serviço e lhe concede direitos de acesso para várias entidades ou todo o espaço de nomes ainda exigem um intermediário de emissão de token. Pode obter orientações para tal um intermediário pela [contactar o suporte](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a autenticação de reencaminhamento do Azure, consulte os seguintes tópicos:

* [Autorização e autenticação de reencaminhamento do Azure](relay-authentication-and-authorization.md)
* [Autenticação do Service Bus com assinaturas de acesso partilhado](../service-bus-messaging/service-bus-sas.md)


