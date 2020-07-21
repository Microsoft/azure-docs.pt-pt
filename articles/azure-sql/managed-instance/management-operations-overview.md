---
title: Operações de Gestão
titleSuffix: Azure SQL Managed Instance
description: Saiba mais sobre a duração das operações de gestão de instância gerida da Azure SQL e as melhores práticas.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: urosmil
ms.author: urmilano
ms.reviewer: sstein, carlrab, MashaMSFT
ms.date: 07/10/2020
ms.openlocfilehash: 5cff54b1f71d30f7932c4ead722d1dda0a7aec57
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531992"
---
# <a name="overview-of-azure-sql-managed-instance-management-operations"></a>Visão geral das operações de gestão de instâncias geridas Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

## <a name="what-are-management-operations"></a>O que são operações de gestão?
A Azure SQL Managed Instance fornece operações de gestão que pode utilizar para implementar automaticamente novos casos geridos, atualizar propriedades de instância e apagar casos quando já não é necessário.

Para suportar [implementações dentro de redes virtuais Azure](../../virtual-network/virtual-network-for-azure-services.md) e fornecer isolamento e segurança para os clientes, a SQL Managed Instance depende de [clusters virtuais.](connectivity-architecture-overview.md#high-level-connectivity-architecture) O cluster virtual representa um conjunto dedicado de máquinas virtuais isoladas implantadas dentro da sub-rede de rede virtual do cliente. Essencialmente, cada implementação de instâncias geridas numa sub-rede vazia resulta numa nova construção de cluster virtual.

As operações subsequentes em casos geridos implantados podem também ter efeitos no cluster virtual subjacente. Estas operações afetam a duração das operações de gestão, uma vez que a implantação de máquinas virtuais adicionais vem com uma sobrecarga que precisa de ser considerada quando planeia novas implementações ou atualizações para instâncias geridas existentes.

Todas as operações de gestão podem ser categorizadas da seguinte forma:

- Implantação de instância (criação de novos casos).
- Atualização de instâncias (alterar propriedades de instância, tais como vCores ou armazenamento reservado).
- Eliminação de exemplos.

## <a name="management-operations-duration"></a>Duração das operações de gestão
Normalmente, as operações em aglomerados virtuais demoram mais tempo. A duração das operações em clusters virtuais varia – abaixo estão os valores que normalmente se pode esperar, com base nos dados de telemetria de serviço existentes:

- **Criação de cluster virtual**: A criação é um passo sincronizado nas operações de gestão de exemplos. **90% das operações terminam em 4 horas.**
- **Redimensionamento de cluster virtual (expansão ou redução)**: A expansão é um passo sincronizado, enquanto a redução é realizada de forma assíncronea (sem impacto na duração das operações de gestão de instância). **90% das expansões de cluster terminam em menos de 2,5 horas**.
- **Eliminação virtual do agrupamento**: A eliminação é um passo assíncronos, mas também pode ser [iniciado manualmente](virtual-cluster-delete.md) num aglomerado virtual vazio, caso em que executa sincronizadamente. **90% das eliminações de clusters virtuais terminam em 1,5 horas**.

Além disso, a gestão de casos pode igualmente incluir uma das operações em bases de dados hospedadas, que resultam em durações mais longas:

- **Anexar ficheiros de base de dados do Azure Storage**: Um passo sincronizado, como o cálculo (vCores), ou o armazenamento a escalonar para cima ou para baixo no nível de serviço Final geral. **90% destas operações terminam em 5 minutos**.
- **Sempre Na sementeira do grupo de disponibilidade**: Um passo sincronizado, como o cálculo (vCores), ou o dimensionamento de armazenamento no nível de serviço Business Critical, bem como na alteração do nível de serviço de Propósito Geral para Business Critical (ou vice-versa). A duração desta operação é proporcional ao tamanho total da base de dados, bem como à atividade atual da base de dados (número de transações ativas). A atividade da base de dados ao atualizar uma instância pode introduzir uma variação significativa da duração total. **90% destas operações executam a 220 GB/hora ou superior**.

O quadro que se segue resume as operações e as durações globais típicas:

|Categoria  |Operação  |Segmento de longa duração  |Duração estimada  |
|---------|---------|---------|---------|
|**Implementação** |Primeira instância numa sub-rede vazia|Criação de cluster virtual|90% das operações terminam em 4 horas.|
|Implementação |Primeira instância de outra geração de hardware numa sub-rede não vazia (por exemplo, primeira instância da Gen 5 numa sub-rede com instâncias da Gen 4)|Criação de cluster virtual*|90% das operações terminam em 4 horas.|
|Implementação |Criação de 4 vCores em uma sub-rede vazia ou não vazia|Criação de cluster virtual**|90% das operações terminam em 4 horas.|
|Implementação |Criação de instância subsequente dentro da sub-rede não vazia (2º, 3º, etc. instância)|Redimensionamento de cluster virtual|90% das operações terminam em 2,5 horas.|
|**Atualizar** |Mudança de propriedade de exemplo (senha de administração, login Azure AD, bandeira de Benefício Híbrido Azure)|N/D|Até 1 minuto.|
|Atualizar |Armazenamento de instâncias para cima/para baixo (nível de serviço para fins gerais)|Anexação de ficheiros de base de dados|90% das operações terminam em 5 minutos.|
|Atualizar |Armazenamento de instâncias escalando para cima/para baixo (nível de serviço Critical empresarial)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora).|
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Final geral)|- Redimensionamento de cluster virtual<br>- Anexação de ficheiros de base de dados|90% das operações terminam em 2,5 horas.|
|Atualizar |Cálculo de exemplo (vCores) escalando para cima e para baixo (Business Critical)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora).|
|Atualizar |Alteração do nível de serviço de instância (Final geral para Business Critical e vice-versa)|- Redimensionamento de cluster virtual<br>- Sempre na sementeira de grupo de disponibilidade|90% das operações terminam em 2,5 horas + tempo para semear todas as bases de dados (220 GB/hora).|
|**Eliminação**|Eliminação da instância|Registar backup de cauda para todas as bases de dados|90% das operações terminam em até 1 minuto.<br>Nota: se a última instância na sub-rede for eliminada, esta operação irá agendar a eliminação do cluster virtual após 12 horas.**|
|Eliminação|Eliminação de clusters virtuais (como operação iniciada pelo utilizador)|Eliminação de clusters virtuais|90% das operações terminam em até 1,5 horas.|

\*O cluster virtual é construído por geração de hardware.

\*\*12 horas é a configuração atual, mas isso pode mudar no futuro, por isso não tenhas uma dependência difícil. Se precisar de eliminar um cluster virtual mais cedo (para lançar a sub-rede, por exemplo), consulte [Eliminar uma sub-rede depois de eliminar uma instância gerida](virtual-cluster-delete.md).

## <a name="instance-availability-during-management-operations"></a>Disponibilidade de instâncias durante operações de gestão

SQL Managed Instance **está disponível durante as operações de atualização**, exceto um curto tempo de inatividade causado pela falha que ocorre no final da atualização. Normalmente dura até 10 segundos, mesmo em caso de transações interrompidas a longo prazo, graças à [recuperação acelerada](../accelerated-database-recovery.md)da base de dados.

> [!IMPORTANT]
> Não é recomendado escalar o cálculo ou armazenamento de Azure SQL Managed Instance ou alterar o nível de serviço ao mesmo tempo com as transações de longo prazo (importação de dados, trabalhos de processamento de dados, reconstrução de índices, etc.). A falha na base de dados que será realizada no final da operação irá cancelar todas as transações em curso.

A SQL Managed Instance não está disponível para aplicações de clientes durante operações de implantação e eliminação.

## <a name="management-operations-cross-impact"></a>Impacto transversal das operações de gestão

As operações de gestão num caso gerido podem afetar outras operações de gestão das instâncias colocadas dentro do mesmo cluster virtual:

- **As operações de restauro de longa duração** num cluster virtual colocarão em espera outras operações de criação ou escala na mesma sub-rede.<br/>**Exemplo:** Se houver uma operação de restauro a longo prazo e houver um pedido de criação ou escala na mesma sub-rede, este pedido demorará mais tempo a ser concluído, uma vez que aguardará que a operação de restauro esteja concluída antes de continuar.
    
- **Uma operação de criação ou escalonamento subsequente** é posta em espera por uma escala de criação ou instância de instância iniciada anteriormente que iniciou o redimensionamento do cluster virtual.<br/>**Exemplo:** Se houver múltiplos pedidos de criação e/ou escala na mesma sub-rede sob o mesmo cluster virtual, e um deles iniciar um redimensionado cluster virtual, todos os pedidos que foram submetidos 5+ minutos após o que exigia o redimensionado cluster virtual durará mais do que o esperado, uma vez que estes pedidos terão de esperar que o redimensionar seja concluído antes de retomar.

- **As operações de criação/escala submetidas numa janela de 5 minutos** serão em loteadas e executadas em paralelo.<br/>**Exemplo:** Apenas um redimensionamento de cluster virtual será realizado para todas as operações submetidas numa janela de 5 minutos (medindo a partir do momento da execução do primeiro pedido de operação). Se outro pedido for apresentado mais de 5 minutos após a primeira ser submetida, aguardará que o cluster virtual seja reensitado antes do início da execução.

> [!IMPORTANT]
> As operações de gestão que forem postas em suspenso por causa de uma outra operação em curso serão automaticamente retomadas assim que as condições para proceder forem satisfeitas. Não é necessária qualquer ação do utilizador para retomar as operações de gestão temporariamente interrompidas.

## <a name="canceling-management-operations"></a>Cancelamento de operações de gestão

O quadro que se segue resume a capacidade de cancelar operações de gestão específicas e as durações globais típicas:

Categoria  |Operação  |Cancelável  |Duração estimada do cancelamento  |
|---------|---------|---------|---------|
|Implementação |Criação de exemplos |Não |  |
|Atualizar |Armazenamento de instâncias para cima/para baixo (Final geral) |Não |  |
|Atualizar |Armazenamento de instâncias escalando para cima/para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Cálculo de instância (vCores) escalando para cima e para baixo (Final geral) |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Cálculo de exemplo (vCores) escalando para cima e para baixo (Business Critical) |Sim |90% das operações terminam em 5 minutos. |
|Atualizar |Alteração do nível de serviço de instância (Final geral para Business Critical e vice-versa) |Sim |90% das operações terminam em 5 minutos. |
|Eliminar |Eliminação da instância |Não |  |
|Eliminar |Eliminação de clusters virtuais (como operação iniciada pelo utilizador) |Não |  |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para cancelar a operação de gestão, vá à lâmina de visão geral e clique na caixa de notificação da operação em curso. Do lado direito, aparecerá um ecrã com a operação em curso e haverá botão para cancelar a operação. Após o primeiro clique, ser-lhe-á pedido que clique novamente e confirme que pretende cancelar a operação.

[![Cancelar operação](./media/management-operations-overview/canceling-operation.png)](./media/management-operations-overview/canceling-operation.png#lightbox)

Depois de um pedido de cancelamento ter sido submetido e processado, receberá uma notificação se a submissão do cancelamento tiver sido bem sucedida ou não.

Se o pedido de cancelamento apresentado for bem sucedido, a operação de gestão será cancelada em alguns minutos, resultando em uma falha.

![Cancelamento do resultado da operação](./media/management-operations-overview/canceling-operation-result.png)

Se o pedido de cancelamento falhar ou o botão de cancelamento não estiver ativo, significa que a operação de gestão entrou em estado não cancelável e que terminará em alguns minutos. A operação de gestão continuará a sua execução até que esteja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Se ainda não tiver o Azure PowerShell instalado, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Para cancelar a operação de gestão, tem de especificar o nome da operação de gestão. Portanto, primeiro use obtenha o comando para recuperar a lista de operação e, em seguida, cancelar a operação específica.

```powershell-interactive
$managedInstance = "yourInstanceName"
$resourceGroup = "yourResourceGroupName"

$managementOperations = Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup

foreach ($mo in $managementOperations ) {
    if($mo.State -eq "InProgress" -and $mo.IsCancellable){
        $cancelRequest = Stop-AzSqlInstanceOperation -ResourceGroupName $resourceGroup -ManagedInstanceName $managedInstance -Name $mo.Name
        Get-AzSqlInstanceOperation -ManagedInstanceName $managedInstance  -ResourceGroupName $resourceGroup -Name $mo.Name
    }
}
```

Para obter uma explicação detalhada dos comandos, consulte [a Get-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstanceoperation) e [a Stop-AzSqlInstanceOperation](https://docs.microsoft.com/powershell/module/az.sql/stop-azsqlinstanceoperation).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Se ainda não tiver o Azure CLI instalado, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest).

Para cancelar a operação de gestão, tem de especificar o nome da operação de gestão. Portanto, primeiro use obtenha o comando para recuperar a lista de operação e, em seguida, cancelar a operação específica.

```azurecli-interactive
az sql mi op list -g yourResourceGroupName --mi yourInstanceName --query "[?state=='InProgress' && isCancellable].{Name: name}" -o tsv |
while read -r operationName; do
    az sql mi op cancel -g yourResourceGroupName --mi yourInstanceName -n $operationName
done
```

Para obter uma explicação detalhada dos comandos, consulte [az sql mi op](https://docs.microsoft.com/cli/azure/sql/mi/op?view=azure-cli-latest).

---

## <a name="next-steps"></a>Próximos passos
- Para aprender a criar o seu primeiro exemplo gerido, consulte o [guia Quickstart](instance-create-quickstart.md).
- Para obter uma lista de funcionalidades e comparação, consulte [as características comuns sql](../database/features-comparison.md).
- Para obter mais informações sobre a configuração VNet, consulte [a configuração VNet de instância gerida SQL](connectivity-architecture-overview.md).
- Para um arranque rápido que cria uma instância gerida e restaura uma base de dados a partir de um ficheiro de backup, consulte [Criar uma instância gerida](instance-create-quickstart.md).
- Para obter um tutorial sobre a utilização do Serviço de Migração da Base de Dados Azure para migração, consulte [a migração de instâncias geridas SQL utilizando o Serviço de Migração de Bases de Dados.](../../dms/tutorial-sql-server-to-managed-instance.md)
