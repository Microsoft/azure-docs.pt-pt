---
title: Como solicitar aumento de quota para os recursos DB da Azure Cosmos
description: Saiba como solicitar um aumento de quota para os recursos DB da Azure Cosmos. Também aprenderá a permitir que uma subscrição aceda a uma região.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e7ec71220b75647e789508c760e50957b3b497fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93090040"
---
# <a name="how-to-request-quota-increase-for-azure-cosmos-db-resources"></a>Como solicitar aumento de quota para os recursos DB da Azure Cosmos
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os recursos em Azure Cosmos DB têm [quotas/limites predefinidos.](concepts-limits.md) No entanto, pode haver um caso em que a sua carga de trabalho precisa de mais quota do que o valor padrão. Nesse caso, você deve contactar a equipa DB da Azure Cosmos para solicitar um aumento de quota. Este artigo explica como solicitar um aumento de quota para os recursos DB da Azure Cosmos. Também aprenderá a permitir que uma subscrição aceda a uma região.

## <a name="create-a-new-support-request"></a>Criar um novo pedido de apoio

Para solicitar um aumento de quota, deve criar um novo pedido de apoio com os seus dados de carga de trabalho. A equipa DB da Azure Cosmos irá então avaliar o seu pedido e aprová-lo. Utilize os seguintes passos para criar um novo pedido de apoio a partir do portal Azure:

1. Inicie sessão no portal do Azure.

1. A partir do menu da esquerda, selecione **Help + support** e, em seguida, selecione Novo pedido de **suporte**.

1. No separador Basics preencha os **seguintes** detalhes:

   * Para **Tipo de problema**, selecione **Limites de serviço e subscrição (quotas)**
   * Para **Subscrição**, selecione a subscrição para a qual pretende aumentar a quota.
   * Para **o tipo de quota**, selecione **Cosmos DB**

   :::image type="content" source="./media/create-support-request-quota-increase/create-quota-increase-request.png" alt-text="Criar um novo pedido de apoio ao Cosmos DB para aumento de quotas":::

1. No separador **Detalhes,** insira os detalhes correspondentes ao seu pedido de quota. As Informações fornecidas neste separador serão utilizadas para avaliar melhor o seu problema e ajudar o engenheiro de suporte a resolver o problema.

1. Preencha os seguintes detalhes neste formulário:

   * **Descrição**: Forneça uma breve descrição do seu pedido, como a sua carga de trabalho, por que os valores predefinidos não são suficientes. Com base no tipo de recurso para o qual pretende aumentar a quota, é obrigatório que forneça os seguintes detalhes dentro do campo **Descrição:**

     **Pedidos de regiões** Se o seu pedido corresponder à adição de uma região à lista permitida, certifique-se de fornecer os seguintes valores:

        * Nome da região
        * ID da subscrição

     **Pedidos de limite de produção** Se o seu pedido corresponder ao aumento da quota para a produção, certifique-se de fornecer os seguintes valores:

        * Nome da base de dados
        * ID da subscrição
        * Novo limite de produção

     **Pedidos de limite de conta de base de dados** Se o seu pedido corresponder ao aumento do contingente para o número de contas de base de dados numa subscrição, certifique-se de fornecer os seguintes valores:

       * ID da subscrição
       * Novo limite de conta de base de dados

   * **Upload de ficheiros:** Faça upload dos ficheiros de diagnóstico ou de quaisquer outros ficheiros que considere relevantes para o pedido de suporte. Para saber mais sobre a orientação do upload do ficheiro, consulte o artigo de suporte do [Azure.]( ../azure-portal/supportability/how-to-manage-azure-support-request.md#upload-files)

   * **Severidade**: Escolha um dos níveis de gravidade disponíveis com base no impacto do negócio.

   * **Método de contacto preferido**: Pode optar por ser contactado por **e-mail** ou por **telefone.**

1. Preencha os restantes detalhes, tais como a sua disponibilidade, linguagem de suporte, informações de contacto, e-mail e número de telefone no formulário.

1. Selecione **Seguinte: Review+Create**. Validar as informações fornecidas e selecionar **Criar** para criar um pedido de suporte.

Dentro de 24 horas, a equipa de apoio da Azure Cosmos irá avaliar o seu pedido e voltar a falar consigo.

## <a name="next-steps"></a>Passos seguintes

* Veja as [quotas de serviço padrão da Azure Cosmos](concepts-limits.md)
