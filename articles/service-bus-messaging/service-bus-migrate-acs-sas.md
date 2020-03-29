---
title: Azure Service Bus - Migrar para autorização de assinatura de acesso partilhado
description: Saiba sobre a migração do Serviço de Controlo de Acesso ao Diretório Ativo azure para a autorização de assinatura de acesso partilhado.
services: service-bus-messaging
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 532bbaf0b983b2d4310780686777cbe895afebe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774607"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Service Bus - Migrar do Serviço de Controlo de Acesso ao Diretório Ativo azure para autorização de assinatura de acesso partilhado

As aplicações de ônibus de serviço já tiveram a opção de usar dois modelos de autorização diferentes: o modelo de assinatura de [acesso partilhado (SAS)](service-bus-sas.md) fornecido diretamente pela Service Bus, e um modelo federado onde a gestão das regras de autorização é gerida no interior pelo [Azure Ative Directory](/azure/active-directory/) Access Control Service (ACS), e as fichas obtidas da ACS são passadas para a Service Bus para autorizar o acesso às funcionalidades desejadas.

O modelo de autorização ACS foi há muito substituído pela [autorização da SAS](service-bus-authentication-and-authorization.md) como modelo preferido, e toda a documentação, orientação e amostras utilizam exclusivamente a SAS hoje em dia. Além disso, já não é possível criar novos espaços de nome sinuosos de ônibus de serviço que são emparelhados com ACS.

A SAS tem a vantagem de não depender imediatamente de outro serviço, mas pode ser utilizada diretamente de um cliente sem qualquer intermediário, dando ao cliente acesso ao nome e chave de regras da Regra SAS. A SAS também pode ser facilmente integrada com uma abordagem em que um cliente tem primeiro de passar um cheque de autorização com outro serviço e depois é emitido um símbolo. Esta última abordagem é semelhante ao padrão de utilização do ACS, mas permite a emissão de fichas de acesso baseadas em condições específicas da aplicação que são difíceis de expressar em ACS.

Para todas as aplicações existentes que dependem da ACS, instamos os clientes a migrarem as suas aplicações para confiarem no SAS.

## <a name="migration-scenarios"></a> Cenários de migração

A ACS e a Service Bus são integradas através do conhecimento partilhado de uma *chave de assinatura.* A chave de assinatura é usada por um espaço de nome ACS para assinar fichas de autorização, e é usada pela Service Bus para verificar se o símbolo foi emitido pelo espaço de nome ACS emparelhado. O espaço de nome ACS detém identidades de serviço e regras de autorização. As regras de autorização definem qual a identidade de serviço ou qual o símbolo emitido por um fornecedor de identidade externo obtém qual o tipo de acesso a uma parte do gráfico de espaço de nome do Bus de Serviço, sob a forma de uma correspondência de prefixo mais longa.

Por exemplo, uma regra ACS pode conceder a `/` reclamação de **Envio** sobre o prefixo do caminho para uma identidade de serviço, o que significa que um símbolo emitido pela ACS com base nessa regra concede o direito do cliente de enviar a todas as entidades no espaço de nome. Se o prefixo `/abc`do caminho for, a identidade `abc` limita-se ao envio para entidades nomeadas ou organizadas por baixo desse prefixo. Presume-se que os leitores desta orientação migratória já estão familiarizados com estes conceitos.

Os cenários de migração enquadram-se em três grandes categorias:

1.  **Incumprimentos inalterados.** Alguns clientes usam um objeto [SharedSecretTokenProvider,](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) passando a identidade de serviço do **proprietário** gerada automaticamente e a sua chave secreta para o espaço de nome ACS, emparelhado com o espaço de nome do Bus de Serviço, e não adicionam novas regras.

2.  **Identidades de serviço personalizadas com regras simples.** Alguns clientes adicionam novas identidades de serviço e concedem a cada nova identidade de serviço **Enviar**, **Ouvir**e **Gerir** permissões para uma entidade específica.

3.  **Identidades de serviço personalizadas com regras complexas.** Muito poucos clientes têm conjuntos de regras complexos em que tokens emitidos externamente são mapeados para direitos sobre a Relay, ou quando uma única identidade de serviço é atribuída a direitos diferenciados em vários caminhos espaço de nome através de múltiplas regras.

Para assistência na migração de conjuntos de regras complexos, pode contactar o [suporte do Azure.](https://azure.microsoft.com/support/options/) Os outros dois cenários permitem uma migração simples.

### <a name="unchanged-defaults"></a>Incumprimentos inalterados

Se a sua aplicação não tiver alterado as predefinições do ACS, pode substituir todo o uso [do SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) por um objeto [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) e utilizar o espaço de nome sinuoso **RootManageSharedAccessKey** em vez da conta **do proprietário** do ACS. Note que mesmo com a conta do **proprietário** da ACS, esta configuração não foi (e ainda é) geralmente recomendada, porque esta conta/regra fornece autoridade de gestão completa sobre o espaço de nome, incluindo permissão para apagar quaisquer entidades.

### <a name="simple-rules"></a>Regras simples

Se a aplicação utilizar identidades de serviço personalizadas com regras simples, a migração é simples no caso de ter sido criada uma identidade de serviço ACS para fornecer controlo de acesso numa fila específica. Este cenário é frequentemente o caso em soluções ao estilo SaaS onde cada fila é usada como ponte para um site de inquilinos ou filial, e a identidade de serviço é criada para esse site em particular. Neste caso, a respetiva identidade de serviço pode ser migrada para uma regra de Assinatura de Acesso Partilhado, diretamente na fila. O nome de identidade de serviço pode tornar-se o nome da regra SAS e a chave de identidade de serviço pode tornar-se a chave de regra SAS. Os direitos da regra SAS são então configurados equivalentes à regra ACS, respectivamente aplicável, para a entidade.

Pode fazer esta nova e adicional configuração do SAS em vigor em qualquer espaço de nome existente que seja federado com ACS, e a migração para longe do ACS é subsequentemente realizada utilizando [sharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) em vez de [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). O espaço de nome não precisa de ser desvinculado do ACS.

### <a name="complex-rules"></a>Regras complexas

As regras sas não são para ser contas, mas são nomeadas chaves de assinatura associadas aos direitos. Como tal, cenários em que a aplicação cria muitas identidades de serviço e lhes concede direitos de acesso para várias entidades ou todo o espaço de nome ainda requerem um intermediário emissor de tokens. Pode obter orientação para tal intermediário [contactando](https://azure.microsoft.com/support/options/)o suporte .

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a autenticação do Service Bus, consulte os seguintes tópicos:

* [Autenticação e autorização do Service Bus](service-bus-authentication-and-authorization.md)
* [Autenticação de ônibus de serviço com assinaturas de acesso partilhado](service-bus-sas.md)

