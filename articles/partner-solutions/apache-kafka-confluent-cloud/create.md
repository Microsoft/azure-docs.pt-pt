---
title: Criar Apache Kafka para Soluções de Parceiros Confluentes - Azure
description: Este artigo descreve como usar o portal Azure para criar um exemplo de Apache Kafka para a Nuvem Confluente.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253797"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>QuickStart: Começa com Apache Kafka para a Nuvem Confluente

Neste arranque rápido, você usará o portal Azure para criar uma instância de Apache Kafka para a Nuvem Confluente.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).
- Deve ter a função _Proprietário_ ou _Contribuinte_ para a sua subscrição Azure. A integração entre a Azure e a Confluent só pode ser criada por utilizadores com acesso _proprietário_ ou _colaborador._ Antes de começar, [confirme que tem o acesso adequado](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Encontre oferta

Use o portal Azure para encontrar a aplicação Apache Kafka para Cloud Confluente.

1. Num navegador web, vá ao [portal Azure](https://portal.azure.com/) e inscreva-se.

1. Se visitou o **Marketplace** numa sessão recente, selecione o ícone das opções disponíveis. Caso contrário, procure _no Marketplace._

    :::image type="content" source="media/marketplace.png" alt-text="Ícone do mercado.":::

1. Na página **do Marketplace,** tem duas opções baseadas no tipo de plano que pretende. Pode inscrever-se num plano de pagamento ou plano de compromisso. Pay-as-you-go está disponível ao público. O plano de compromisso está disponível para clientes que tenham sido aprovados para uma oferta privada.

   - Para clientes **que pagam,** procure _apache Kafka na Nuvem Confluente._ Selecione a oferta para Apache Kafka em Confluent Cloud.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="pesquisar oferta do Azure Marketplace.":::

   - Para clientes **com compromisso,** selecione o link para **ver ofertas privadas.** O compromisso requer que se inscreva por um valor mínimo de gasto. Utilize esta opção apenas quando souber que necessita do serviço por um período prolongado.

     :::image type="content" source="media/view-private-offers.png" alt-text="ver ofertas privadas.":::

     Procure _Por Apache Kafka na Nuvem Confluente._

     :::image type="content" source="media/select-from-private-offers.png" alt-text="selecione oferta privada.":::

## <a name="create-resource"></a>Criar um recurso

Depois de ter selecionado a oferta para Apache Kafka na Nuvem Confluente, está pronto para configurar a aplicação.

1. Se selecionou ofertas privadas na secção anterior, terá duas opções para os tipos de planos:

    - Nuvem Confluente - Pay-as-you-go
    - Compromisso - para o plano de compromisso

   Se não selecionou ofertas privadas, só terá a opção pay-as-you-go.

   Escolha o plano para usar e selecione **Configurar + subscrever**.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Configurar e subscrever.":::

1. Na página basics **Create Confluent Cloud Resource,** forneça os seguintes valores. Quando terminar, selecione **Seguinte: Tags**.

    :::image type="content" source="media/setup-basics.png" alt-text="Forma para configurar o recurso Cloud Confluent.":::

    | Propriedade | Descrição |
    | ---- | ---- |
    | **Subscrição** | A partir do menu suspenso, selecione a subscrição Azure para implementar. Tem de ter acesso _ao Proprietário_ ou _colaborador._ |
    | **Grupo de recursos** | Especifique se pretende criar um novo grupo de recursos ou utilizar um grupo de recursos existente. Um grupo de recursos é um contentor que detém recursos relacionados para uma solução do Azure. Para obter mais informações, veja [Descrição geral do Grupo de Recursos do Azure](../../azure-resource-manager/management/overview.md). |
    | **Nome da organização confluente** | Nomeie o software como recurso de serviço (SaaS). |
    | **Região** | A partir do menu suspenso, selecione uma destas regiões: <br/><br/> Austrália Leste, Canadá Central, Eua Central, Leste DOS EUA, Leste DOS EUA 2, França Central, Norte da Europa, Sudeste Asiático, Reino Unido Sul, Oeste Central DOS EUA, Europa Ocidental, Eua Ocidental 2 |
    | **Planear** | Selecione **Pay à medida que vai** ou **Compromisso**. |
    | **Termo de faturação** | Preenchido com base no plano de faturação selecionado. |
    | **Preço** | Preenchido com base no plano Confluente selecionado. |

1. Nas **Etiquetas,** forneça o **nome** e os pares **de valor** para as etiquetas que pretende aplicar aos recursos. Depois de introduzir as tags, selecione **Review + Create**.

    :::image type="content" source="media/setup-tags.png" alt-text="Adicione etiquetas de projeto.":::

1. Reveja as definições que forneceu. Quando estiver pronto, **selecione Criar**.

1. Leva alguns minutos para criar o recurso. Pode ver o estado de implantação nas **Notificações**. Depois de concluída a implementação, selecione o recurso para visualizar a página **'Vista Geral'.**

    :::image type="content" source="media/deployment-status.png" alt-text="Estado de implantação.":::

   Se tiver um erro, consulte [a Resolução de Problemas apache Kafka para soluções Cloud Confluent](troubleshoot.md).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir o recurso Cloud Confluent](manage.md)
