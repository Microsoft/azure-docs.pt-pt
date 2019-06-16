---
title: Configurar o dimensionamento automático de uma instância de gestão de API do Azure | Documentos da Microsoft
description: Este tópico descreve como configurar o comportamento de dimensionamento automático para uma instância de gestão de API do Azure.
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
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123472"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Dimensionar automaticamente uma instância de gestão de API do Azure  

Gestão de API do Azure, instância de serviço pode ser dimensionado automaticamente com base num conjunto de regras. Esse comportamento pode ser habilitado e configurado através do Azure Monitor e só é suportada no **padrão** e **Premium** escalões do serviço de gestão de API do Azure.

O artigo explica o processo de configuração de dimensionamento automático e sugere a configuração ideal de regras de dimensionamento automático.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir os passos deste artigo, tem de:

+ Ter uma subscrição do Azure Active Directory.
+ Ter uma instância de gestão de API do Azure. Para obter mais informações, consulte [criar uma instância de gestão de API do Azure](get-started-create-service-instance.md).
+ Compreender o conceito de [capacidade de uma instância de gestão de API do Azure](api-management-capacity.md).
+ Compreender [manual dimensionar o processo de uma instância de gestão de API do Azure](upgrade-and-scale.md), incluindo as conseqüências de custo.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Limitações de dimensionamento automático de gestão de API do Azure

Determinadas limitações e as conseqüências das decisões de dimensionamento precisam de ser considerados antes de configurar o comportamento de dimensionamento automático.

+ Dimensionamento automático pode ser ativado apenas para **padrão** e **Premium** escalões de serviço de gestão de API do Azure.
+ Os escalões de preços também especificar o número máximo de unidades para uma instância de serviço.
+ Dimensionar o processo demorará, pelo menos, 20 minutos.
+ Se o serviço está bloqueado por outra operação, pedido de dimensionamento falharão e repetir a ação automaticamente.
+ Em caso de um serviço com implementações com várias regionais, apenas unidades do **localização primária** podem ser dimensionados. Unidades de outros locais não podem ser reduzidas.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Ativar e configurar o dimensionamento automático para o serviço de gestão de API do Azure

Siga os passos abaixo para configurar o dimensionamento automático para um serviço de gestão de API do Azure:

1. Navegue para **Monitor** instância no portal do Azure.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Selecione **dimensionamento automático** no menu à esquerda.

    ![Recurso de dimensionamento automático do Azure Monitor](media/api-management-howto-autoscale/02.png)

3. Localize o serviço de gestão de API do Azure com base nos filtros nos menus de lista pendente.
4. Selecione a instância do serviço de Azure API Management pretendida.
5. Na secção recém aberta, clique a **ativar o dimensionamento automático** botão.

    ![Ativar de dimensionamento automático do Azure Monitor](media/api-management-howto-autoscale/03.png)

6. Na **regras** secção, clique em **+ adicionar uma regra**.

    ![Dimensionamento automático de Monitor do Azure-Adicionar regra](media/api-management-howto-autoscale/04.png)

7. Defina uma nova regra de aumento horizontal.

   Por exemplo, uma regra de aumento horizontal poderia acionar uma adição de uma unidade de API Management do Azure, quando a métrica de capacidade média durante os últimos 30 minutos excede 80%. A tabela abaixo fornece uma configuração para esta regra.

    | Parâmetro             | Value             | Notas                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas de recurso de API Management do Azure atuais.                                                                                                                                                                                                     |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                 |
    | Nome da métrica           | Capacidade          | Métrica de capacidade é uma métrica de API Management do Azure que reflete a utilização de recursos de uma instância de gestão de API do Azure.                                                                                                                                                            |
    | Estatística de intervalo de agregação de tempo  | Média           |                                                                                                                                                                                                                                                                                 |
    | Operador              | Maior que      |                                                                                                                                                                                                                                                                                 |
    | Limiar             | 80%               | O limiar para a métrica de capacidade média.                                                                                                                                                                                                                                 |
    | Duração (em minutos) | 30                | O período de tempo para calcular a média a métrica de capacidade sobre é específico para padrões de utilização. Quanto mais tempo o período de tempo, o mais suave será a reação - picos intermitentes terão menos efeito sobre a decisão de escalamento horizontal. No entanto, também irá atrasar o acionador de escalamento horizontal. |
    | *ação*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operação             | Aumentar contagem em |                                                                                                                                                                                                                                                                                 |
    | Contagem de instâncias        | 1                 | Aumentar horizontalmente a instância de gestão de API do Azure por 1 unidade.                                                                                                                                                                                                                          |
    | Repouso (minutos)   | 60                | Demora pelo menos de 20 minutos para que o serviço de gestão de API do Azure aumentar horizontalmente. Na maioria dos casos, o período de 60 minutos de início de arrefecimento impede que o acionamento do muitas implementações de escala.                                                                                                  |

8. Clique em **adicionar** para guardar a regra.

    ![O Azure Monitor regra de aumento horizontal](media/api-management-howto-autoscale/05.png)

9. Clique novamente em **+ adicionar uma regra**.

    Desta vez, uma regra de dimensionamento tem de ser definido. Ele garantirá a recursos não estão sendo desperdiçados, quando a utilização de APIs diminui.

10. Defina uma nova escala na regra.

    Por exemplo, uma regra de dimensionamento poderia acionar uma remoção de uma unidade de API Management do Azure, quando a métrica de capacidade média durante os últimos 30 minutos foi inferior a 35%. A tabela abaixo fornece uma configuração para esta regra.

    | Parâmetro             | Value             | Notas                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Origem métrica         | Recurso atual  | Defina a regra com base nas métricas de recurso de API Management do Azure atuais.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Critérios*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregação de tempo      | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nome da métrica           | Capacidade          | Mesma métrica que utilizou para a regra de aumento horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Estatística de intervalo de agregação de tempo  | Média           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operador              | Menor que         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Limiar             | 35%               | Da mesma forma para a regra de aumento horizontal, este valor intensamente depende de padrões de utilização da gestão de API do Azure. |
    | Duração (em minutos) | 30                | Mesmo valor que utilizou para a regra de aumento horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *ação*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operação             | Diminuir contagem em | Diferentes dos padrões convencionais o que foi utilizado para a regra de aumento horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Contagem de instâncias        | 1                 | Mesmo valor que utilizou para a regra de aumento horizontal.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Repouso (minutos)   | 90                | Reduzir horizontalmente deve ser mais conservadora que uma escala, para que o período de arrefecimento deve ter mais.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Clique em **adicionar** para guardar a regra.

    ![O Azure Monitor regra de dimensionamento](media/api-management-howto-autoscale/06.png)

12. Definir o **máximo** o número de unidades de API Management do Azure.

    > [!NOTE]
    > Gestão de API do Azure tem um limite de uma instância pode aumentar horizontalmente para unidades. O limite depende de uma camada de serviços.

    ![O Azure Monitor regra de dimensionamento](media/api-management-howto-autoscale/07.png)

13. Clique em **Guardar**. O dimensionamento automático foi configurado.

## <a name="next-steps"></a>Passos Seguintes

+ [Como implementar uma instância de serviço da Gestão de API do Azure em várias regiões do Azure](api-management-howto-deploy-multi-region.md)
