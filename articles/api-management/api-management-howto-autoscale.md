---
title: Configurar o dimensionamento automático de uma instância do gerenciamento de API do Azure | Microsoft Docs
description: Este tópico descreve como configurar o comportamento de dimensionamento automático para uma instância de gerenciamento de API do Azure.
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
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018266"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Dimensionar automaticamente uma instância do gerenciamento de API do Azure  

A instância do serviço de gerenciamento de API do Azure pode ser dimensionada automaticamente com base em um conjunto de regras. Esse comportamento pode ser habilitado e configurado por meio de Azure Monitor e tem suporte apenas nas camadas **Standard** e **Premium** do serviço de gerenciamento de API do Azure.

O artigo percorre o processo de configuração de dimensionamento automático e sugere a configuração ideal de regras de dimensionamento automático.

> [!NOTE]
> O serviço de gerenciamento de API na camada de **consumo** é dimensionado automaticamente com base no tráfego, sem nenhuma configuração adicional necessária.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste artigo, você deve:

+ Ter uma assinatura ativa do Azure.
+ Ter uma instância de gerenciamento de API do Azure. Para obter mais informações, consulte [criar uma instância de gerenciamento de API do Azure](get-started-create-service-instance.md).
+ Entenda o conceito de [capacidade de uma instância de gerenciamento de API do Azure](api-management-capacity.md).
+ Entenda [o processo de dimensionamento manual de uma instância de gerenciamento de API do Azure](upgrade-and-scale.md), incluindo consequências de custo.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitações de dimensionamento automático do gerenciamento de API do Azure

Determinadas limitações e consequências de decisões de dimensionamento precisam ser consideradas antes da configuração do comportamento de dimensionamento automático.

+ O dimensionamento automático só pode ser habilitado para as camadas **Standard** e **Premium** do serviço de gerenciamento de API do Azure.
+ Os tipos de preço também especificam o número máximo de unidades para uma instância de serviço.
+ O processo de dimensionamento levará pelo menos 20 minutos.
+ Se o serviço estiver bloqueado por outra operação, a solicitação de dimensionamento falhará e tentará novamente de forma automática.
+ No caso de um serviço com implantações de várias regiões, somente as unidades no **local principal** podem ser dimensionadas. Unidades em outros locais não podem ser dimensionadas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Habilitar e configurar o dimensionamento automático para o serviço de gerenciamento de API do Azure

Siga as etapas abaixo para configurar o dimensionamento automático para um serviço de gerenciamento de API do Azure:

1. Navegue até a instância do **Monitor** no portal do Azure.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Selecione **dimensionamento automático** no menu à esquerda.

    ![Azure Monitor recurso de dimensionamento automático](media/api-management-howto-autoscale/02.png)

3. Localize o serviço de gerenciamento de API do Azure com base nos filtros em menus suspensos.
4. Selecione a instância do serviço de gerenciamento de API do Azure desejada.
5. Na seção aberta recentemente, clique no botão **Habilitar dimensionamento automático** .

    ![Habilitar autoescala Azure Monitor](media/api-management-howto-autoscale/03.png)

6. Na seção **regras** , clique em **+ Adicionar uma regra**.

    ![Azure Monitor autoescala Adicionar regra](media/api-management-howto-autoscale/04.png)

7. Defina uma nova regra de expansão.

   Por exemplo, uma regra de expansão poderia disparar uma adição de uma unidade de gerenciamento de API do Azure, quando a métrica de capacidade média nos últimos 30 minutos exceder 80%. A tabela a seguir fornece a configuração para essa regra.

    | Parâmetro             | Value             | Notas                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas de recurso atuais de gerenciamento de API do Azure.                                                                                                                                                                                                     |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregação de tempo      | Average           |                                                                                                                                                                                                                                                                                 |
    | Nome da métrica           | Capacidade          | A métrica de capacidade é uma métrica de gerenciamento de API do Azure que reflete o uso de recursos de uma instância de gerenciamento de API do Azure.                                                                                                                                                            |
    | Estatística de intervalo de agregação  | Average           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Maior que      |                                                                                                                                                                                                                                                                                 |
    | Limiar             | 80%               | O limite para a métrica de capacidade média.                                                                                                                                                                                                                                 |
    | Duração (em minutos) | 30                | O período de tempo para a média da métrica de capacidade sobre é específico dos padrões de uso. Quanto mais tempo for o período, mais suave será a reação-picos intermitentes terão menos efeito na decisão de expansão. No entanto, ele também atrasará o gatilho de expansão. |
    | *ação*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operação             | Aumentar contagem em |                                                                                                                                                                                                                                                                                 |
    | Contagem de instâncias        | 1                 | Escalar horizontalmente a instância do gerenciamento de API do Azure em 1 unidade.                                                                                                                                                                                                                          |
    | Resfriamento (minutos)   | 60                | Leva pelo menos 20 minutos para que o serviço de gerenciamento de API do Azure Escale horizontalmente. Na maioria dos casos, o período de resfriamento de 60 minutos impede que o dispare muitos limites de escalabilidade.                                                                                                  |

8. Clique em **Adicionar** para salvar a regra.

    ![Regra de scale out Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Clique novamente em **+ Adicionar uma regra**.

    Desta vez, é necessário definir uma escala na regra. Ele garantirá que os recursos não sejam desperdiçados, quando o uso de APIs diminuir.

10. Defina uma nova escala na regra.

    Por exemplo, uma escala na regra poderia disparar uma remoção de uma unidade de gerenciamento de API do Azure, quando a métrica de capacidade média nos últimos 30 minutos tiver sido inferior a 35%. A tabela a seguir fornece a configuração para essa regra.

    | Parâmetro             | Value             | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas de recurso atuais de gerenciamento de API do Azure.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregação de tempo      | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nome da métrica           | Capacidade          | Mesma métrica como aquela usada para a regra de expansão.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Estatística de intervalo de agregação  | Average           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Menor que         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Limiar             | 35%               | Da mesma forma que a regra de expansão, esse valor depende muito dos padrões de uso do gerenciamento de API do Azure. |
    | Duração (em minutos) | 30                | Mesmo valor que aquele usado para a regra de expansão.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *ação*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operação             | Diminuir contagem em | Oposto ao que foi usado para a regra de expansão.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Contagem de instâncias        | 1                 | Mesmo valor que aquele usado para a regra de expansão.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Resfriamento (minutos)   | 90                | A escala em deve ser mais conservadora do que uma escala horizontal, portanto, o período de resfriamento deve ser maior.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Clique em **Adicionar** para salvar a regra.

    ![Azure Monitor dimensionar na regra](media/api-management-howto-autoscale/06.png)

12. Defina o número **máximo** de unidades de gerenciamento de API do Azure.

    > [!NOTE]
    > O gerenciamento de API do Azure tem um limite de unidades às quais uma instância pode ser expandida. O limite depende de uma camada de serviço.

    ![Azure Monitor dimensionar na regra](media/api-management-howto-autoscale/07.png)

13. Clique em **Guardar**. O dimensionamento automático foi configurado.

## <a name="next-steps"></a>Passos Seguintes

+ [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
