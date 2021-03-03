---
title: Janela de Manutenção
description: Compreenda como a janela de manutenção Azure SQL Database e Managed Instance pode ser configurada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 9dc4d17ea95362dd915bd1dfdfd82f4cdec611b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692815"
---
# <a name="maintenance-window-preview"></a>Janela de manutenção (Pré-visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A função de janela de manutenção permite a configuração de horários previsíveis das janelas de manutenção para [a Azure SQL Database](sql-database-paas-overview.md) e [a sql gerida instância](../managed-instance/sql-managed-instance-paas-overview.md). 

Para obter mais informações sobre eventos de manutenção, consulte [o Plano para eventos de manutenção Azure SQL e Azure SQL Managed Instance](planned-maintenance.md).

## <a name="overview"></a>Descrição Geral

O Azure executa atualizações de manutenção planeadas na Base de Dados Azure SQL e nos recursos de Instância Gerida SQL periodicamente que incluem atualizações para hardware subjacente, software incluindo sistema operativo subjacente (OS) e o motor SQL. Durante uma atualização de manutenção, os recursos estão totalmente disponíveis e acessíveis, mas algumas das atualizações de manutenção requerem uma falha, uma vez que o Azure retira instâncias offline por um curto período de tempo para aplicar as atualizações de manutenção (oito segundos de duração, em média).  As atualizações de manutenção planeadas ocorrem uma vez a cada 35 dias, em média, o que significa que o cliente pode esperar aproximadamente um evento de manutenção planeado por mês por base de dados Azure SQL ou sql de gestão, e apenas durante as ranhuras da janela de manutenção selecionadas pelo cliente.   

A janela de manutenção destina-se a cargas de trabalho empresariais que não sejam resilientes a problemas de conectividade intermitentes que possam resultar de eventos de manutenção planeados.

A janela de manutenção pode ser configurada utilizando o portal Azure, PowerShell, CLI ou AZure API. Pode ser configurado na criação ou para bases de dados SQL existentes e instâncias geridas pela SQL.

### <a name="gain-more-predictability-with-maintenance-window"></a>Ganhar mais previsibilidade com janela de manutenção

Por padrão, todas as bases de dados Azure SQL e bases de dados de casos geridos são atualizadas apenas durante as 17:00 às 8:00 horas locais diariamente para evitar interrupções de horas de trabalho de pico. A hora local é determinada pela [região de Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que acolhe o recurso. Pode ajustar ainda mais as atualizações de manutenção para um tempo adequado à sua base de dados, escolhendo entre duas ranhuras adicionais de janela de manutenção:

* **Janela padrão,** 17:00 às 8:00 horas locais de segunda a domingo 
* Janela do dia da semana, das 22h às 6h locais de segunda a quinta-feira
* Janela de fim de semana, 22:00 às 6:00 horas locais sexta-feira - domingo

Uma vez feita a seleção da janela de manutenção, todas as atualizações de manutenção planeadas só ocorrerão durante a janela à sua escolha.   

> [!Note]
> Além das atualizações de manutenção planeadas, em raras circunstâncias eventos de manutenção não planeados podem causar indisponibilidade. 

### <a name="cost-and-eligibility"></a>Custo e elegibilidade

A escolha de uma janela de manutenção é gratuita para os [seguintes tipos](https://azure.microsoft.com/support/legal/offer-details/)de oferta de subscrição : Pay-As-You-Go, Cloud Solution Provider (CSP), Microsoft Enterprise ou Microsoft Customer Agreement.

> [!Note]
> Uma oferta do Azure é o tipo da subscrição do Azure que possui. Por exemplo, uma subscrição com [taxas pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure em Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/), e Visual Studio [Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) são todas as ofertas da Azure. Cada oferta ou plano tem diferentes termos e benefícios. A sua oferta ou plano é mostrado no resumo da subscrição. Para obter mais informações sobre a mudança da sua subscrição para uma oferta diferente, consulte [alterar a subscrição do Azure para uma oferta diferente.](/azure/cost-management-billing/manage/switch-azure-offer)

## <a name="advance-notifications"></a>Notificações antecipadas

As notificações de manutenção podem ser configuradas para alertar os clientes sobre os próximos eventos de manutenção planeados com 24 horas de antecedência, no momento da manutenção, e quando a janela de manutenção estiver completa. Para mais informações, consulte [notificações antecipadas.](advance-notifications.md)

## <a name="availability"></a>Disponibilidade

### <a name="supported-service-level-objectives"></a>Objetivos de nível de serviço apoiados

A escolha de uma janela de manutenção que não seja o padrão está disponível em todos os SLOs, **exceto:**
* Hyperscale 
* Legacy Gen4 vCore
* Básico, S0 e S1 
* DC, Fsv2, Série M

### <a name="azure-region-support"></a>Apoio à região de Azure

A escolha de uma janela de manutenção que não seja o padrão está atualmente disponível nas seguintes regiões:

- Leste da Austrália
- Austrália SouthEast
- Sul do Brasil
- Canadá Central
- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- Leste do Japão
- NorteCentral EUA
- Europa do Norte
- SouthCentral EUA
- Ásia do Sul
- Sul do Reino Unido
- Europa Ocidental
- E.U.A. Oeste
- E.U.A. Oeste 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Manutenção de gateway para base de dados Azure SQL

Para obter o máximo benefício das janelas de manutenção, certifique-se de que as aplicações do seu cliente estão a utilizar a política de ligação de redirecionamento. Redirecionamento é a política de ligação recomendada, onde os clientes estabelecem ligações diretamente ao nó que hospeda a base de dados, levando a uma redução da latência e a uma melhor produção.  

* Na Base de Dados Azure SQL, quaisquer ligações que utilizem a política de ligação por procuração podem ser afetadas tanto pela janela de manutenção escolhida como por uma janela de manutenção do nó de gateway. No entanto, as ligações do cliente utilizando a política de ligação recomendada de redirecionamento não são afetadas por um sistema de manutenção do nó de gateway. 

* No caso gerido pelo Azure SQL, os nós de gateway estão [dentro do cluster virtual](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) e têm a mesma janela de manutenção que a instância gerida, pelo que a utilização da política de ligação por procuração não expõe potencialmente as ligações a uma janela de manutenção adicional.

Para mais informações sobre a política de ligação ao cliente na Base de Dados Azure SQL consulte [a política de Ligação à Base de Dados Azure SQL](../database/connectivity-architecture.md#connection-policy). 

Para mais informações sobre a política de ligação ao cliente em Azure SQL caso gerido consulte [os tipos de conexão Azure SQL Managed Instance](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Passos seguintes

* [Notificações antecipadas](advance-notifications.md)
* [Janela de manutenção configurada](maintenance-window-configure.md)

## <a name="learn-more"></a>Saber mais

* [FaQ da janela de manutenção](maintenance-window-faq.yml)
* [Base de Dados SQL do Azure](sql-database-paas-overview.md) 
* [Instância Gerida do SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Plano para eventos de manutenção Azure em Azure SQL Database e Azure SQL Managed Instance](planned-maintenance.md)




