---
title: Configurar a autoescalação de um caso de gestão API Azure | Microsoft Docs
description: Este tópico descreve como configurar um comportamento de autoescalação para um caso de Gestão API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018257"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Crie automaticamente uma instância de dimensionamento da Gestão de API do Azure  

A exemplo do serviço Azure API Management pode escalar automaticamente com base num conjunto de regras. Este comportamento pode ser ativado e configurado através do Azure Monitor e é suportado apenas nos níveis **Standard** e **Premium** do serviço de Gestão API da Azure.

O artigo percorre o processo de configuração da autoescalação e sugere uma configuração ótima das regras de autoescala.

> [!NOTE]
> Serviço de Gestão API nas escalas de nível **de consumo** automaticamente com base no tráfego - sem necessidade de qualquer configuração adicional.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, deve:

+ Ter uma subscrição ativa do Azure.
+ Tenha um caso de Gestão API da Azure. Para obter mais informações, consulte [Criar uma instância de Gestão API Azure.](get-started-create-service-instance.md)
+ Compreender o conceito de [Capacidade de uma Instância de Gestão API Azure.](api-management-capacity.md)
+ Compreenda [o processo de dimensionamento manual de uma instância de Gestão API da Azure,](upgrade-and-scale.md)incluindo consequências de custos.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitações de autoescala da AZure API Management

Algumas limitações e consequências das decisões de escala devem ser consideradas antes de configurar o comportamento de autoescala.

+ A autoescala só pode ser ativada para os níveis **Standard** e **Premium** do serviço AZure API Management.
+ Os níveis de preços também especificam o número máximo de unidades para uma instância de serviço.
+ O processo de escalagem levará pelo menos 20 minutos.
+ Se o serviço for bloqueado por outra operação, o pedido de escala falhará e tentará novamente.
+ Em caso de serviço com implantações multi-regionais, apenas as unidades na **localização primária** podem ser dimensionadas. Unidades em outros locais não podem ser dimensionadas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Ativar e configurar autoescala para o serviço de gestão API da Azure

Siga os passos abaixo para configurar a autoescala para um serviço de Gestão API Azure:

1. Navegue para **monitorizar** a instância no portal Azure.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Selecione **Autoescala** a partir do menu à esquerda.

    ![Recurso de autoescala do Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Localize o seu serviço de Gestão API Azure com base nos filtros nos menus suspensos.
4. Selecione a instância de serviço de gestão API Azure desejada.
5. Na secção recém-aberta, clique no botão **Enable autoscale.**

    ![Azure Monitor autoscale enable](media/api-management-howto-autoscale/03.png)

6. Na secção **Regras,** clique **+ Adicione uma regra**.

    ![Regra de adicionar automaticamente a azure Monitor](media/api-management-howto-autoscale/04.png)

7. Defina uma nova regra de escala.

   Por exemplo, uma regra de escala pode desencadear uma adição de uma unidade de Gestão API Azure, quando a métrica média de capacidade nos últimos 30 minutos excede 80%. A tabela abaixo fornece configuração para tal regra.

    | Parâmetro             | Valor             | Notas                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas atuais de recursos da Azure API Management.                                                                                                                                                                                                     |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                 |
    | Nome da métrica           | Capacidade          | A métrica da capacidade é uma métrica de Gestão API Azure que reflete a utilização de recursos de um caso de Gestão API Azure.                                                                                                                                                            |
    | Estatística do intervalo de agregação  | Média           |                                                                                                                                                                                                                                                                                 |
    | Operador              | Maior que      |                                                                                                                                                                                                                                                                                 |
    | Limiar             | 80%               | O limiar para a métrica média da capacidade.                                                                                                                                                                                                                                 |
    | Duração (em minutos) | 30                | O tempo para a média da métrica da capacidade é específico para os padrões de utilização. Quanto mais tempo for o período de tempo, mais suave será a reação - os picos intermitentes terão menos efeito na decisão de escala. No entanto, também irá atrasar o gatilho de escala. |
    | *Ação*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operação             | Aumentar a contagem em |                                                                                                                                                                                                                                                                                 |
    | Contagem de instâncias        | 1                 | Dimensione a instância de Gestão da API da Azure por 1 unidade.                                                                                                                                                                                                                          |
    | Repouso (minutos)   | 60                | Leva pelo menos 20 minutos para o serviço de Gestão API da Azure para escalar. Na maioria dos casos, o período de arrefecimento de 60 minutos impede que desencadeie muitas escalas.                                                                                                  |

8. Clique **em Adicionar** para guardar a regra.

    ![Regra de escala de escala do Monitor Azure](media/api-management-howto-autoscale/05.png)

9. Clique novamente **em + Adicione uma regra**.

    Desta vez, uma escala de regra precisa de ser definida. Garantirá que os recursos não sejam desperdiçados, quando a utilização de APIs diminuir.

10. Defina uma nova escala em regra.

    Por exemplo, uma escala de regra poderia desencadear a remoção de uma unidade de Gestão API da Azure, quando a métrica média da capacidade nos últimos 30 minutos foi inferior a 35%. A tabela abaixo fornece configuração para tal regra.

    | Parâmetro             | Valor             | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas atuais de recursos da Azure API Management.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nome da métrica           | Capacidade          | A mesma métrica que a usada para a regra de escala fora.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Estatística do intervalo de agregação  | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operador              | Menor que         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Limiar             | 35%               | Da mesma forma que a regra de escala para fora, este valor depende fortemente dos padrões de utilização da Azure API Management. |
    | Duração (em minutos) | 30                | O mesmo valor que o usado para a regra de escala fora.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Ação*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operação             | Diminuir a contagem em | Ao contrário do que foi usado para a regra da escala.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Contagem de instâncias        | 1                 | O mesmo valor que o usado para a regra de escala fora.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Repouso (minutos)   | 90                | A escala deve ser mais conservadora do que uma escala para fora, por isso o período de arrefecimento deve ser mais longo.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Clique **em Adicionar** para guardar a regra.

    ![Escala de monitor Azure em regra](media/api-management-howto-autoscale/06.png)

12. Desconfie do número **máximo** de unidades de Gestão API da Azure.

    > [!NOTE]
    > A Azure API Management tem um limite de unidades que um caso pode escalar para. O limite depende de um nível de serviço.

    ![Screenshot que realça onde definir o número máximo de unidades de Gestão API da Azure.](media/api-management-howto-autoscale/07.png)

13. Clique em **Guardar**. A sua autoescalada foi configurada.

## <a name="next-steps"></a>Passos seguintes

- [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
- [Otimize e poupe nos gastos na nuvem](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)