---
title: Barramento de serviço do Azure – migrar para autorização de assinatura de acesso compartilhado
description: Saiba mais sobre como migrar do serviço de controle de acesso Azure Active Directory para a autorização de assinatura de acesso compartilhado.
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76774607"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Barramento de serviço-migrar do serviço de controle de acesso Azure Active Directory para autorização de assinatura de acesso compartilhado

Os aplicativos do barramento de serviço tinham uma opção de usar dois modelos de autorização diferentes: o modelo de token [SAS (assinatura de acesso compartilhado)](service-bus-sas.md) fornecido diretamente pelo barramento de serviço e um modelo federado em que o gerenciamento de regras de autorização é gerenciado pelo serviço de controle de acesso (ACS) [Azure Active Directory](/azure/active-directory/) e tokens obtidos do ACS são passados para o barramento de serviço para autorizar o acesso aos recursos deseja

O modelo de autorização do ACS foi substituído por uma [autorização SAS](service-bus-authentication-and-authorization.md) como o modelo preferencial, e toda a documentação, orientação e exemplos usam exclusivamente SAS atualmente. Além disso, não é mais possível criar novos namespaces do barramento de serviço emparelhados com o ACS.

A SAS tem a vantagem de não ser imediatamente dependente de outro serviço, mas pode ser usada diretamente de um cliente sem nenhum intermediário, concedendo ao cliente acesso ao nome da regra SAS e à chave de regra. A SAS também pode ser facilmente integrada com uma abordagem em que um cliente precisa primeiro passar uma verificação de autorização com outro serviço e, em seguida, emitir um token. A última abordagem é semelhante ao padrão de uso do ACS, mas permite emitir tokens de acesso com base em condições específicas do aplicativo que são difíceis de expressar no ACS.

Para todos os aplicativos existentes que dependem do ACS, incentivamos os clientes a migrar seus aplicativos para que dependam de SAS em vez disso.

## <a name="migration-scenarios"></a>Cenários de migração

O ACS e o barramento de serviço são integrados por meio do conhecimento compartilhado de uma *chave de assinatura*. A chave de assinatura é usada por um namespace do ACS para assinar tokens de autorização e é usada pelo barramento de serviço para verificar se o token foi emitido pelo namespace do ACS emparelhado. O namespace do ACS contém identidades de serviço e regras de autorização. As regras de autorização definem qual identidade de serviço ou qual token emitido por um provedor de identidade externo Obtém qual tipo de acesso a uma parte do grafo de namespace do barramento de serviço, na forma de uma correspondência de prefixo mais longo.

Por exemplo, uma regra do ACS pode conceder a Declaração **Send** no prefixo do caminho `/` a uma identidade de serviço, o que significa que um token emitido pelo ACS com base nessa regra concede aos direitos de cliente para enviar a todas as entidades no namespace. Se o prefixo do caminho for `/abc`, a identidade será restrita ao envio para entidades denominadas `abc` ou organizadas abaixo desse prefixo. Supõe-se que os leitores dessas diretrizes de migração já estão familiarizados com esses conceitos.

Os cenários de migração se enquadram em três categorias amplas:

1.  **Padrões inalterados**. Alguns clientes usam um objeto [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) , passando a identidade de serviço do **proprietário** gerada automaticamente e sua chave secreta para o namespace do ACS, emparelhado com o namespace do barramento de serviço e não adicionam novas regras.

2.  **Identidades de serviço personalizadas com regras simples**. Alguns clientes adicionam novas identidades de serviço e concedem a cada nova identidade de serviço **Enviar**, **escutar**e **gerenciar** permissões para uma entidade específica.

3.  **Identidades de serviço personalizadas com regras complexas**. Muito poucos clientes têm conjuntos de regras complexos nos quais os tokens emitidos externamente são mapeados para direitos na retransmissão ou em que uma única identidade de serviço recebe direitos diferenciados em vários caminhos de namespace por meio de várias regras.

Para obter assistência com a migração de conjuntos de regras complexas, você pode contatar o [suporte do Azure](https://azure.microsoft.com/support/options/). Os outros dois cenários permitem uma migração direta.

### <a name="unchanged-defaults"></a>Padrões inalterados

Se o seu aplicativo não tiver alterado os padrões do ACS, você poderá substituir todo o uso de [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) por um objeto [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) e usar o namespace pré-configurado **RootManageSharedAccessKey** em vez da conta do **proprietário** do ACS. Observe que, mesmo com a conta do **proprietário** do ACS, essa configuração foi (e ainda), geralmente não é recomendada, porque essa conta/regra fornece autoridade de gerenciamento completa sobre o namespace, incluindo a permissão para excluir qualquer entidade.

### <a name="simple-rules"></a>Regras simples

Se o aplicativo usar identidades de serviço personalizadas com regras simples, a migração será simples no caso em que uma identidade de serviço do ACS foi criada para fornecer controle de acesso em uma fila específica. Esse cenário geralmente é o caso em soluções em estilo SaaS em que cada fila é usada como uma ponte para um site de locatário ou filial, e a identidade do serviço é criada para esse site específico. Nesse caso, a respectiva identidade de serviço pode ser migrada para uma regra de assinatura de acesso compartilhado, diretamente na fila. O nome da identidade do serviço pode se tornar o nome da regra SAS e a chave de identidade do serviço pode se tornar a chave de regra SAS. Os direitos da regra de SAS são então configurados equivalentes à regra do ACS aplicável para a entidade.

Você pode fazer essa configuração nova e adicional de SAS in-loco em qualquer namespace existente que seja federado com o ACS, e a migração para fora do ACS é executada posteriormente usando [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) em vez de [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider). O namespace não precisa ser desvinculado do ACS.

### <a name="complex-rules"></a>Regras complexas

As regras de SAS não devem ser contas, mas são chaves de assinatura nomeadas associadas a direitos. Dessa forma, cenários nos quais o aplicativo cria muitas identidades de serviço e concede a eles direitos de acesso para várias entidades ou o namespace inteiro ainda exige um intermediário emissor de tokens. Você pode obter diretrizes para tal intermediário [entrando em contato com o suporte](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a autenticação do barramento de serviço, consulte os seguintes tópicos:

* [Autenticação e autorização do barramento de serviço](service-bus-authentication-and-authorization.md)
* [Autenticação do barramento de serviço com assinaturas de acesso compartilhado](service-bus-sas.md)

