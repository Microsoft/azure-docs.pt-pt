---
title: Janela de Manutenção
description: Compreenda como a Base de Dados Azure SQL e a janela de manutenção de instâncias gerida podem ser configuradas.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/05/2021
ms.openlocfilehash: b658fa9f2df6e8a88df89f9e8ccc1cf6b68cec39
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426064"
---
# <a name="maintenance-window-preview"></a>Janela de manutenção (Pré-visualização)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A função de janela de manutenção permite configurar o horário de manutenção da [Azure SQL Database](sql-database-paas-overview.md) e [do Azure SQL geridos](../managed-instance/sql-managed-instance-paas-overview.md) recursos de instância tornando eventos de manutenção impactante previsíveis e menos disruptivos para a sua carga de trabalho. 

> [!Note]
> A função de janela de manutenção não protege de eventos não planeados, como falhas de hardware, que podem causar interrupções de ligação curtas.

## <a name="overview"></a>Descrição Geral

O Azure realiza periodicamente a [manutenção planeada](planned-maintenance.md) da Base de Dados SQL e dos recursos de instância geridos pela SQL. Durante o evento de manutenção do Azure SQL, as bases de dados estão totalmente disponíveis, mas podem estar sujeitas a curtos failovers dentro das respetivas disponibilidades SLAs para [a SqL Database](https://azure.microsoft.com/support/legal/sla/sql-database) e [sql gestduation instance](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance), uma vez que a reconfiguração de recursos é necessária em alguns casos.

A janela de manutenção destina-se a cargas de trabalho de produção que não sejam resilientes a falhas na base de dados ou em casos de falha e que não possam absorver interrupções de ligação curtas causadas por eventos de manutenção planeados. Ao escolher uma janela de manutenção que preferir, pode minimizar o impacto da manutenção planeada, uma vez que ocorrerá fora do seu horário de trabalho. Cargas de trabalho resistentes e cargas de trabalho não produtivos podem depender da política de manutenção padrão da Azure SQL.

A janela de manutenção pode ser configurada na criação ou para os recursos Azure SQL existentes. Pode ser configurado usando o portal Azure, PowerShell, CLI ou AZure API.

> [!Important]
> A janela de manutenção configurante é uma operação assíncrona de longa duração, semelhante à alteração do nível de serviço do recurso Azure SQL. O recurso está disponível durante a operação, exceto uma pequena falha que ocorre no final da operação e que normalmente dura até 8 segundos, mesmo em caso de transações interrompidas de longa duração. Para minimizar o impacto do failover, deve efetuar a operação fora das horas de ponta.

### <a name="gain-more-predictability-with-maintenance-window"></a>Ganhar mais previsibilidade com janela de manutenção

Por padrão, a política de manutenção do Azure SQL bloqueia atualizações impactantes durante o período **das 8h às 17h locais todos os dias** para evitar quaisquer perturbações durante as horas de pico típicas. A hora local é determinada pela localização da região de [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) que acolhe o recurso e pode observar o horário de verão de acordo com a definição de fuso horário local. 

Pode ajustar ainda mais as atualizações de manutenção para um tempo adequado aos seus recursos Azure SQL, escolhendo entre duas ranhuras adicionais de janela de manutenção:
 
* Janela do dia da semana, das 22h às 6h locais de segunda a quinta-feira
* Janela de fim de semana, 22:00 às 6:00 horas locais sexta-feira - domingo

Uma vez feita a seleção da janela de manutenção e a configuração de serviço concluída, a manutenção planeada só ocorrerá durante a janela à sua escolha.   

> [!Important]
> Em circunstâncias muito raras, em que qualquer adiamento de ação pode causar um impacto sério, como a aplicação de um patch de segurança crítico, a janela de manutenção configurada pode ser temporariamente sobrevalada. 

### <a name="cost-and-eligibility"></a>Custo e elegibilidade

Configurar e utilizar a janela de manutenção é gratuito para todos os tipos de [ofertas](https://azure.microsoft.com/support/legal/offer-details/)elegíveis : Pay-As-You-Go, Cloud Solution Provider (CSP), Microsoft Enterprise Agreement ou Microsoft Customer Agreement.

> [!Note]
> Uma oferta do Azure é o tipo da subscrição do Azure que possui. Por exemplo, uma subscrição com [taxas pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/), [Azure em Open](https://azure.microsoft.com/offers/ms-azr-0111p/), e Visual Studio [Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) são todas as ofertas da Azure. Cada oferta ou plano tem diferentes termos e benefícios. A sua oferta ou plano é mostrado no resumo da subscrição. Para obter mais informações sobre a mudança da sua subscrição para uma oferta diferente, consulte [alterar a subscrição do Azure para uma oferta diferente.](/azure/cost-management-billing/manage/switch-azure-offer)

## <a name="advance-notifications"></a>Notificações antecipadas

As notificações de manutenção podem ser configuradas para alertá-lo sobre os próximos eventos de manutenção planeados para a sua Base de Dados Azure SQL com 24 horas de antecedência, no momento da manutenção, e quando a manutenção estiver concluída. Para mais informações, consulte [notificações antecipadas.](advance-notifications.md)

## <a name="availability"></a>Disponibilidade

### <a name="supported-service-level-objectives"></a>Objetivos de nível de serviço apoiados

A escolha de uma janela de manutenção que não seja o padrão está disponível em todos os SLOs, **exceto:**
* Hyperscale 
* Conjuntos de instâncias
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

* No caso gerido pelo Azure SQL, os nós de gateway são alojados [dentro do cluster virtual](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) e têm a mesma janela de manutenção que a instância gerida, mas a utilização da política de ligação de redirecionamento ainda é recomendada para minimizar o número de interrupções durante o evento de manutenção.

Para obter mais informações sobre a política de ligação ao cliente na Base de Dados [Azure SQL, consulte a política de Ligação de Bases de Dados Azure SQL](../database/connectivity-architecture.md#connection-policy). 

Para mais informações sobre a política de ligação ao cliente em Azure SQL caso gerido consulte [Azure SQL gerenciado tipos de conexão de instância](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Considerações para a Azure SQL caso gerido

O exemplo gerido pelo Azure SQL consiste em componentes de serviço alojados num conjunto dedicado de máquinas virtuais isoladas que funcionam dentro da sub-rede de rede virtual do cliente. Estas máquinas virtuais formam [clusters virtuais](/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture) que podem acolher várias instâncias geridas. A janela de manutenção configurada em casos de uma sub-rede pode influenciar o número de aglomerados virtuais dentro da sub-rede e a distribuição de instâncias entre clusters virtuais. Isto pode exigir uma consideração de poucos efeitos.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>A configuração da janela de manutenção é uma operação de longa duração 
Todas as instâncias hospedadas num cluster virtual partilham a janela de manutenção. Por predefinição, todas as instâncias geridas são hospedadas no cluster virtual com a janela de manutenção predefinida. Especificar outra janela de manutenção para a sua criação ou depois significa que deve ser colocada em aglomerado virtual com a respetiva janela de manutenção. Se não houver tal cluster virtual na sub-rede, um novo deve ser criado primeiro para acomodar o caso. Acomodar instâncias adicionais no cluster virtual existente pode requerer o redimensionamento do cluster. Ambas as operações contribuem para a duração da janela de manutenção configurada para uma instância gerida.
A duração prevista da janela de manutenção configurada em instância gerida pode ser calculada com base na [duração estimada das operações de gestão de instâncias](/azure/azure-sql/managed-instance/management-operations-overview#duration).

> [!Important]
> Uma pequena falha ocorre no final da operação de manutenção e normalmente dura até 8 segundos, mesmo em caso de transações interrompidas de longa duração. Para minimizar o impacto do failover, deverá agendar a operação fora das horas de ponta.

### <a name="ip-address-space-requirements"></a>Requisitos de espaço de endereço IP
Cada novo cluster virtual na sub-rede requer endereços IP adicionais de acordo com a [atribuição de endereços IP de cluster virtual](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size). A alteração da janela de manutenção para a instância gerida existente também requer [capacidade de IP adicional temporária](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios) como no cenário de escala vCores para o nível de serviço correspondente.

### <a name="ip-address-change"></a>Alteração de endereço IP
Configurar e alterar a janela de manutenção provoca a alteração do endereço IP do caso, dentro do intervalo de endereço IP da sub-rede.

> [!Important]
>  Certifique-se de que as regras de NSG e firewall não bloqueiam o tráfego de dados após a alteração do endereço IP. 

## <a name="next-steps"></a>Passos seguintes

* [Notificações antecipadas](advance-notifications.md)
* [Janela de manutenção configurada](maintenance-window-configure.md)

## <a name="learn-more"></a>Saber mais

* [FaQ da janela de manutenção](maintenance-window-faq.yml)
* [Base de Dados SQL do Azure](sql-database-paas-overview.md) 
* [SQL caso gerido](../managed-instance/sql-managed-instance-paas-overview.md)
* [Plano para eventos de manutenção Azure em Azure SQL Database e Azure SQL gerido instância](planned-maintenance.md)





