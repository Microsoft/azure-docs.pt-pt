---
title: Registo de Esquemas do Azure nos Hubs de Eventos (Pré-visualização)
description: Este artigo fornece uma visão geral do suporte do Registo de Schema por Azure Event Hubs (Preview).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330500"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Registo de Esquemas do Azure nos Hubs de Eventos (Pré-visualização)
Em muitos cenários de streaming e mensagens de eventos, a carga útil do evento ou da mensagem contém dados estruturados que estão a ser serializados ou deseralizados usando um formato orientado por esquemas como o Apache Avro. Tanto os remetentes como os recetores podem querer validar a integridade dos dados com um documento de esquema como com o esquema JSON. No que diz sobre os formatos orientados por esquemas, a disponibilização do esquema ao consumidor de mensagens é um pré-requisito para que o consumidor possa deserizar os dados. 

O **Registo Azure Schema** é uma característica do Event Hubs, que fornece um repositório central para documentos de esquema para aplicações centradas em eventos e mensagens. Proporciona a flexibilidade para que o seu produtor e aplicações de consumo troquem dados sem ter de gerir e partilhar o esquema entre eles e também evoluir a diferentes taxas. O Registo de Schema também fornece um quadro de governação simples para esquemas reutilizáveis e define a relação entre esquemas através de uma construção de agrupamento (grupos de esquemas).

> [!NOTE]
> - A funcionalidade **de Registo de Schema** está atualmente em **pré-visualização,** e não é recomendada para cargas de trabalho de produção.
> - A funcionalidade está disponível apenas em níveis **standard** e **dedicados,** não no nível **básico.**

Com quadros de serialização orientados por esquemas como o Apache Avro, externalizar metadados de serialização em esquemas partilhados também pode ajudar a reduzir drasticamente a sobrecarga por mensagem de informações de tipo e nomes de campo incluídos em todos os conjuntos de dados, como é o caso de formatos marcados como o JSON. Ter esquemas armazenados ao lado dos eventos e dentro da infraestrutura de eventos garante que os metadados necessários para serialização/des-serialização estão sempre ao alcance e os esquemas não podem ser deslocados. 

## <a name="event-hubs-namespace"></a>Espaço de nomes dos Event Hubs
Um espaço de nomes de Event Hubs pode agora acolher grupos de esquemas ao lado de centros de eventos (ou tópicos kafka). Acolhe um registo de esquemas e pode ter vários grupos de esquemas. Apesar de ser hospedado no Azure Event Hubs, o registo de esquemas pode ser usado universalmente com todos os serviços de mensagens Azure e qualquer outro corretor de mensagens ou eventos. Cada um destes grupos de esquemas é um repositório separadamente securável para um conjunto de esquemas. Os grupos podem ser alinhados com uma determinada aplicação ou uma unidade organizacional. 

## <a name="schema-groups"></a>Grupos de schema
O grupo Schema é um grupo lógico de esquemas semelhantes baseados nos seus critérios de negócio. Um grupo de esquemas pode conter várias versões de um esquema. A definição de execução da compatibilidade num grupo de esquemas pode ajudar a garantir que as versões de esquemas mais recentes sejam compatíveis com o retro-esquema.

A fronteira de segurança imposta pelo mecanismo de agrupamento ajuda a garantir que os segredos comerciais não vazem inadvertidamente através de metadados em situações em que o espaço de nome é partilhado entre vários parceiros. Também permite que os proprietários de aplicações gerem esquemas independentes de outras aplicações que partilham o mesmo espaço de nome.


## <a name="schemas"></a>Esquemas
Os esquemas definem o contrato entre os produtores e os consumidores. Um esquema definido num registo de esquemas do Event Hubs ajuda a gerir o contrato fora dos dados do evento, removendo assim a sobrecarga da carga útil. Um esquema tem um nome, tipo (exemplo: gravar, array, e assim por diante.), modo de compatibilidade (nenhum, para a frente, para trás, cheio) e tipo de serialização (apenas Avro por enquanto). Pode criar várias versões de um esquema e recuperar e usar uma versão específica de um esquema. 

## <a name="client-sdks"></a>SDKs do Cliente
Pode utilizar uma das seguintes bibliotecas que incluem um serializador Avro, que pode utilizar para serializar e deserizar cargas que contenham identificadores de esquemas de Registo de Schema e dados codificados pela Avro.

- [.NET - Microsoft.Azure.data.schemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java - azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python - azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript - @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) - Executar serializers e deserializadores Apache Avro integrados em Kafka, apoiados pelo Registo Azure Schema. O serializer de cliente Apache Kafka do cliente Java para o Registo Azure Schema pode ser usado em qualquer cenário Apache Kafka e com qualquer serviço de implantação ou nuvem ® Apache Kafka. 

A imagem a seguir mostra o fluxo de informação do registo de esquemas com os Centros de Eventos: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Diagrama de fluxo":::

## <a name="standard-vs-dedicated-limits"></a>Padrão vs. limites dedicados
Para limites (por exemplo: número de grupos de esquemas num espaço de nome) que são os mesmos e diferentes para níveis padrão e dedicados de Centros de Eventos, consulte [os limites do Registo de Schema](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)

## <a name="azure-role-based-access-control"></a>Controlo de acesso baseado em funções do Azure
Ao aceder programaticamente ao registo de esquemas, é necessário registar uma aplicação no Azure Ative Directory (Azure AD) e adicionar o principal de segurança da aplicação a uma das funções de controlo de acesso baseado em funções Azure (Azure RBAC):

| Função | Descrição | 
| ---- | ----------- | 
| Proprietário | Leia, escreva e elimine os grupos e esquemas do Registo de Schema. |
| Contribuinte | Leia, escreva e elimine os grupos e esquemas do Registo de Schema. |
| [Leitor de Registo de Schema (Pré-visualização)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Leia e liste grupos de registo de Schema e esquemas. |
| [Contribuinte do Registo de Schema (Pré-visualização)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Leia, escreva e elimine os grupos e esquemas do Registo de Schema. |

Para obter instruções sobre a criação de uma aplicação através do portal Azure, consulte [Registar uma aplicação com Azure AD](../active-directory/develop/quickstart-register-app.md). Note a identificação do cliente (ID da aplicação), a identificação do inquilino e o segredo a utilizar no código. 

## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar um registo de esquemas utilizando o portal Azure, consulte [criar um registo de esquemas de Centros de Eventos utilizando o portal Azure](create-schema-registry.md).
- Consulte as seguintes amostras **da biblioteca do cliente do Registo Schema Avro.**
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Integração kafka Avro para registo de Azure Schema](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
