---
title: Visão geral das operações de gestão
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a duração das operações de gestão de instância gerida da Azure SQL e as melhores práticas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: overview
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 2da7311e61aa39be69a6a0a29eff686baaad7ebf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91323197"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Descrição geral das operações de gestão do Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Azure SQL Managed Instance fornece operações de gestão que pode utilizar para implementar automaticamente novos casos geridos, atualizar propriedades de instância e apagar casos quando já não é necessário.

## <a name="what-are-management-operations"></a>O que são operações de gestão?

Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implementação de instâncias (criação de nova instância)
- Atualização de instâncias (alterar propriedades de instância, tais como vCores ou armazenamento reservado)
- Eliminação da instância

Para suportar [implementações dentro de redes virtuais Azure](../../virtual-network/virtual-network-for-azure-services.md) e fornecer isolamento e segurança para os clientes, a SQL Managed Instance depende de [clusters virtuais.](connectivity-architecture-overview.md#high-level-connectivity-architecture) O cluster virtual representa um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede de rede virtual do cliente. Essencialmente, cada instância gerida implantada numa sub-rede vazia resulta numa nova construção de cluster virtual.

As operações de gestão subsequentes em casos geridos podem ter impacto no cluster virtual subjacente. As alterações que impactam o cluster virtual subjacente podem afetar a duração das operações de gestão, uma vez que a implementação de máquinas virtuais adicionais vem com uma sobrecarga que deve considerar quando planeia novas implementações ou atualizações para instâncias geridas existentes.


## <a name="duration"></a>Duração

A duração das operações no cluster virtual pode variar, mas normalmente têm a duração mais longa. 

Seguem-se valores que normalmente se podem esperar, com base nos dados de telemetria de serviço existentes:

- **Criação de cluster virtual**: A criação é um passo sincronizado nas operações de gestão de exemplos. <br/> **90% das operações terminam em 4 horas.**
- **Redimensionamento de cluster virtual (expansão ou redução)**: A expansão é um passo sincronizado, enquanto a redução é realizada de forma assíncronea (sem impacto na duração das operações de gestão de instância). <br/>**90% das expansões de cluster terminam em menos de 2,5 horas**.
- **Eliminação virtual do agrupamento**: A eliminação é um passo assíncronos, mas também pode ser [iniciado manualmente](virtual-cluster-delete.md) num aglomerado virtual vazio, caso em que executa sincronizadamente. <br/>**90% das eliminações de clusters virtuais terminam em 1,5 horas**.

Além disso, a gestão de casos pode igualmente incluir uma das operações em bases de dados hospedadas, que resultam em durações mais longas:

- **Anexar ficheiros de base de dados do Azure Storage**: Um passo sincronizado, como o cálculo de escala (vCores), ou o armazenamento para cima ou para baixo no nível de serviço Final geral. <br/>**90% destas operações terminam em 5 minutos**.
- **Sempre Na sementeira do grupo de disponibilidade**: Um passo sincronizado, como o cálculo (vCores), ou o dimensionamento de armazenamento no nível de serviço Business Critical, bem como na alteração do nível de serviço de Propósito Geral para Business Critical (ou vice-versa). A duração desta operação é proporcional ao tamanho total da base de dados, bem como à atividade atual da base de dados (número de transações ativas). A atividade da base de dados ao atualizar uma instância pode introduzir uma variação significativa da duração total. <br/>**90% destas operações executam a 220 GB/hora ou superior**.

Os quadros que se seguem resumem as operações e as durações globais típicas, com base na categoria da operação:

**Categoria: Implantação**

|Operação  |Segmento de longa duração  |Duração estimada  |
|---------|---------|---------|
|Primeira instância numa sub-rede vazia|Criação de cluster virtual|90% das operações terminam em 4 horas.|
|Primeira instância de outra geração de hardware numa sub-rede não vazia (por exemplo, primeira instância da Gen 5 numa sub-rede com instâncias da Gen 4)|Criação de cluster virtual<sup>1</sup>|90% das operações terminam em 4 horas.|
|Criação de instância subsequente dentro da sub-rede não vazia (2º, 3º, etc. instância)|Redimensionamento de cluster virtual|90% das operações terminam em 2,5 horas.|
| | | 

<sup>1</sup> O cluster virtual é construído por geração de hardware.

**Categoria: Atualização**

|Operação  |Segmento de longa duração  |Duração estimada  |
|---------|---------|---------|
|Mudança de propriedade de exemplo (senha de administração, login Azure AD, bandeira de Benefício Híbrido Azure)|N/D|Até 1 minuto.|
|Armazenamento de instâncias para cima/para baixo (nível de serviço para fins gerais)|Anexação de ficheiros de base de dados|90% das operações terminam em 5 minutos.|
|Armazenamento de instâncias escalando para cima/para baixo (nível de serviço Critical empresarial)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora).|
|Cálculo de instância (vCores) escalando para cima e para baixo (Final geral)|- Redimensionamento de cluster virtual<br>- Anexação de ficheiros de base de dados|90% das operações terminam em 2,5 horas.|
|Cálculo de exemplo (vCores) escalando para cima e para baixo (Business Critical)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora).|
|Alteração do nível de serviço de instância (Final geral para Business Critical e vice-versa)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora).|
| | | 

**Categoria: Excluir**

|Operação  |Segmento de longa duração  |Duração estimada  |
|---------|---------|---------|
|Eliminação da instância|Registar backup de cauda para todas as bases de dados|90% das operações terminam em até 1 minuto.<br>Nota: se a última instância na sub-rede for eliminada, esta operação irá agendar a eliminação virtual do cluster após 12 horas. <sup>1</sup>|
|Eliminação de clusters virtuais (como operação iniciada pelo utilizador)|Eliminação de clusters virtuais|90% das operações terminam em até 1,5 horas.|
| | | 

<sup>112</sup>horas é a configuração atual, mas esta está sujeita a alterações no futuro. Se precisar de eliminar um cluster virtual mais cedo (para lançar a sub-rede, por exemplo), consulte [Eliminar uma sub-rede depois de eliminar uma instância gerida](virtual-cluster-delete.md).

## <a name="instance-availability"></a>Disponibilidade de instância

SQL Managed Instance **está disponível durante as operações de atualização**, exceto um curto tempo de inatividade causado pela falha que ocorre no final da atualização. Normalmente dura até 10 segundos, mesmo em caso de transações interrompidas a longo prazo, graças à [recuperação acelerada da base de dados.](../accelerated-database-recovery.md)

A SQL Managed Instance não está disponível para aplicações de clientes durante operações de implantação e eliminação.

> [!IMPORTANT]
> Não é recomendado escalar o cálculo ou armazenamento de Azure SQL Managed Instance ou alterar o nível de serviço ao mesmo tempo que as transações de longa duração (importação de dados, trabalhos de processamento de dados, reconstrução de índices, etc.). A falha da base de dados no final da operação cancela todas as transações em curso. 

## <a name="management-operations-steps"></a>Etapas de gestão de operações

As operações de gestão consistem em múltiplas etapas. Com [a API de Operações introduzidas](management-operations-monitor.md) estes passos estão expostos para subconjunto de operações (implantação e atualização). A operação de implantação consiste em três etapas enquanto a operação de atualização é realizada em seis etapas. Para mais informações sobre a duração das operações, consulte a secção de duração das [operações de gestão.](#duration) Os passos são listados por ordem de execução.

### <a name="managed-instance-deployment-steps"></a>Etapas de implantação de instâncias geridas

|Nome do passo  |Descrição do passo  |
|----|---------|
|Validação de pedidos |Os parâmetros submetidos são validados. Em caso de má resolução, falhará com um erro. |
|Redimensionamento/criação de cluster virtual |Dependendo do estado da sub-rede, o cluster virtual vai para a criação ou redimensionamento. |
|Nova startup de exemplo SQL |O processo SQL é iniciado no cluster virtual implantado. |

### <a name="managed-instance-update-steps"></a>Etapas de atualização de instâncias geridas

|Nome do passo  |Descrição do passo  |
|----|---------|
|Validação de pedidos | Os parâmetros submetidos são validados. Em caso de má resolução, falhará com um erro. |
|Redimensionamento/criação de cluster virtual |Dependendo do estado da sub-rede, o cluster virtual vai para a criação ou redimensionamento. |
|Nova startup de exemplo SQL | O processo SQL é iniciado no cluster virtual implantado. |
|Sementeira de ficheiros de base de dados / anexação de ficheiros de base de dados |Dependendo do tipo de operação de atualização, é realizada a sementeira de bases de dados ou a anexação de ficheiros de bases de dados. |
|Preparação de failover e failover |Depois de os dados terem sido semeados ou os ficheiros de base de dados religados, o sistema está a ser preparado para a falha. Quando tudo está definido, o failover é realizado **com um tempo de inatividade curto**. |
|Limpeza de instâncias SQL antiga |Remoção do antigo processo SQL do cluster virtual |

> [!NOTE]
> Como resultado de casos de escala, o cluster virtual subjacente passará por um processo de libertação da capacidade não utilizada e possível desfragmentação da capacidade, o que poderá afetar casos que não participaram em operações de criação/escala. 


## <a name="management-operations-cross-impact"></a>Impacto transversal das operações de gestão

As operações de gestão num caso gerido podem afetar outras operações de gestão das instâncias colocadas dentro do mesmo cluster virtual:

- **As operações de restauro de longa duração** num cluster virtual colocarão outras operações de criação ou escala na mesma sub-rede em espera.<br/>**Exemplo:** Se houver uma operação de restauro a longo prazo e houver um pedido de criação ou escala na mesma sub-rede, este pedido demorará mais tempo a ser concluído enquanto aguarda que a operação de restauro esteja concluída antes de continuar.

- **Uma operação de criação ou escalonamento subsequente** é posta em espera por uma escala de criação ou instância de instância previamente iniciada que iniciou um redimensionamento do cluster virtual.<br/>**Exemplo:** Se houver múltiplos pedidos de criação e/ou escala na mesma sub-rede sob o mesmo cluster virtual, e um deles iniciar um redimensionado cluster virtual, todos os pedidos que foram submetidos 5+ minutos após o pedido inicial de operação durarão mais tempo do que o esperado, uma vez que estes pedidos terão de esperar que o redimensione seja concluído antes de retomar.

- **As operações de criação/escala submetidas numa janela de 5 minutos** serão em loteadas e executadas em paralelo.<br/>**Exemplo:** Apenas um redimensionamento de cluster virtual será realizado para todas as operações submetidas numa janela de 5 minutos (medindo a partir do momento da execução do primeiro pedido de operação). Se outro pedido for apresentado mais de 5 minutos após a primeira ser submetida, aguardará que o cluster virtual seja reensitado antes do início da execução.

> [!IMPORTANT]
> As operações de gestão que forem postas em suspenso por causa de uma outra operação em curso serão automaticamente retomadas assim que as condições de proceder forem satisfeitas. Não é necessária qualquer ação do utilizador para retomar as operações de gestão temporariamente interrompidas.

## <a name="monitoring-management-operations"></a>Operações de gestão de monitorização

Para aprender a monitorizar o progresso e o estado da operação de gestão, consulte [as operações de gestão de monitorização.](management-operations-monitor.md)

## <a name="canceling-management-operations"></a>Cancelamento de operações de gestão

Para aprender a cancelar a operação de gestão, consulte [operações de gestão de cancelamento.](management-operations-cancel.md)


## <a name="next-steps"></a>Passos seguintes

- Para aprender a criar o seu primeiro exemplo gerido, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as funcionalidades SqL Comum](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)
