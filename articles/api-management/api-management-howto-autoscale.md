---
title: Configure a escala automática de uma instância de Gestão API Azure [ Microsoft Docs
description: Este tópico descreve como configurar o comportamento de escala automática para uma instância de Gestão API Azure.
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
ms.openlocfilehash: 8c1c96fdb1f4f42c7592791881b855f74d411171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70018266"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Crie automaticamente uma instância de dimensionamento da Gestão de API do Azure  

A instância de serviço de Gestão API Azure pode escalar automaticamente com base num conjunto de regras. Este comportamento pode ser ativado e configurado através do Monitor Azure e é suportado apenas em níveis **Standard** e **Premium** do serviço de Gestão API Azure.

O artigo percorre o processo de configuração da escala automática e sugere uma configuração ótima das regras de escala automática.

> [!NOTE]
> Serviço de Gestão API nas escalas do nível de **consumo** automaticamente com base no tráfego - sem qualquer configuração adicional necessária.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, deve:

+ Tenha uma subscrição azure ativa.
+ Tenha uma instância de Gestão API Azure. Para mais informações, consulte Criar uma instância de [Gestão API Azure.](get-started-create-service-instance.md)
+ Compreender o conceito de Capacidade de uma instância de [Gestão API Azure.](api-management-capacity.md)
+ Compreender o processo de escala manual de uma instância de [Gestão API Azure,](upgrade-and-scale.md)incluindo consequências de custos.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitações de autoescala da Azure API Management

Certas limitações e consequências das decisões de escala ção devem ser consideradas antes de configurar o comportamento em escala automática.

+ A escala automática só pode ser ativada para os níveis **Standard** e **Premium** do serviço de Gestão API Azure.
+ Os níveis de preços também especificam o número máximo de unidades para uma instância de serviço.
+ O processo de escala levará pelo menos 20 minutos.
+ Se o serviço estiver bloqueado por outra operação, o pedido de escala falhará e volte a tentar automaticamente.
+ Em caso de serviço com destacamentos multi-regionais, apenas as unidades na **localização primária** podem ser dimensionadas. Unidades em outros locais não podem ser dimensionadas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Ativar e configurar a escala automática para o serviço de gestão da API Azure

Siga os passos abaixo para configurar a escala automática para um serviço de Gestão API Azure:

1. Navegue para **monitorizar** a ocorrência no portal Azure.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. **Selecione escala automática** no menu à esquerda.

    ![Recurso de escala automática Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Localize o seu serviço de Gestão API Azure com base nos filtros nos menus dropdown.
4. Selecione a desejada instância de serviço Azure API Management.
5. Na secção recém-aberta, clique no botão **Ativação de escala automática.**

    ![Habilitação de escala automática Azure Monitor](media/api-management-howto-autoscale/03.png)

6. Na secção **Regras,** clique **+ Adicione uma regra**.

    ![Regra de adição de autoescala Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Defina uma nova regra de escala.

   Por exemplo, uma regra de saída de escala pode desencadear uma adição de uma unidade de Gestão de API Azure, quando a métrica de capacidade média nos últimos 30 minutos ultrapassa os 80%. A tabela abaixo fornece configuração para tal regra.

    | Parâmetro             | Valor             | Notas                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas atuais de recursos da API Management.                                                                                                                                                                                                     |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                 |
    | Nome da métrica           | Capacidade          | A métrica da capacidade é uma métrica de Gestão aPI Azure que reflete o uso de recursos de uma instância de Gestão de API Azure.                                                                                                                                                            |
    | Estatística do intervalo de agregação  | Média           |                                                                                                                                                                                                                                                                                 |
    | Operador              | Maior que      |                                                                                                                                                                                                                                                                                 |
    | Limiar             | 80%               | O limiar para a métrica de capacidade média.                                                                                                                                                                                                                                 |
    | Duração (em minutos) | 30                | A hora máxima para a média da métrica de capacidade é específica dos padrões de utilização. Quanto mais tempo for o período de tempo, mais suave será a reação - os picos intermitentes terão menos efeito na decisão de escala- No entanto, também irá atrasar o gatilho de escala. |
    | *Ação*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operação             | Aumentar a contagem em |                                                                                                                                                                                                                                                                                 |
    | Contagem de instâncias        | 1                 | Esterna a instância de Gestão API Azure por 1 unidade.                                                                                                                                                                                                                          |
    | Repouso (minutos)   | 60                | Leva pelo menos 20 minutos para o serviço de Gestão API Azure se esgotar. Na maioria dos casos, o período de arrefecimento de 60 minutos evita desencadear muitas saídas de escala.                                                                                                  |

8. Clique em **Adicionar** para salvar a regra.

    ![Regras de escala do Monitor Azure](media/api-management-howto-autoscale/05.png)

9. Clique novamente em **+ Adicione uma regra**.

    Desta vez, uma escala de regra tem de ser definida. Garantirá que os recursos não sejam desperdiçados quando o uso de APIs diminuir.

10. Defina uma nova escala em regra.

    Por exemplo, uma escala em regra poderia desencadear a remoção de uma unidade de Gestão de API Azure, quando a métrica de capacidade média nos últimos 30 minutos foi inferior a 35%. A tabela abaixo fornece configuração para tal regra.

    | Parâmetro             | Valor             | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas atuais de recursos da API Management.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nome da métrica           | Capacidade          | A mesma métrica que a usada para a regra da escala.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Estatística do intervalo de agregação  | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operador              | Menor do que         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Limiar             | 35%               | Da mesma forma que a regra da escala, este valor depende fortemente dos padrões de utilização da Gestão aPI Azure. |
    | Duração (em minutos) | 30                | O mesmo valor que o usado para a regra da escala.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Ação*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operação             | Diminuir a contagem em | Em frente ao que foi usado para a regra da escala.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Contagem de instâncias        | 1                 | O mesmo valor que o usado para a regra da escala.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Repouso (minutos)   | 90                | A escala deve ser mais conservadora do que uma escala para fora, por isso o período de arrefecimento deve ser mais longo.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Clique em **Adicionar** para salvar a regra.

    ![Escala de Monitor Azure em regra](media/api-management-howto-autoscale/06.png)

12. Detete o número **máximo** de unidades de Gestão API Azure.

    > [!NOTE]
    > A Azure API Management tem um limite de unidades a que uma instância pode escalar. O limite depende de um nível de serviço.

    ![Escala de Monitor Azure em regra](media/api-management-howto-autoscale/07.png)

13. Clique em **Guardar**. A sua escala automática foi configurada.

## <a name="next-steps"></a>Passos seguintes

+ [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
