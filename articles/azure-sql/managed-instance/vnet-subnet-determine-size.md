---
title: Determinar o tamanho da sub-rede necessária & gama
titleSuffix: Azure SQL Managed Instance
description: Este tópico descreve como calcular o tamanho da sub-rede onde a Azure SQL Managed Instance será implantada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012460"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>Determinar o tamanho da sub-rede necessária & gama para Azure SQL Gestded Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance deve ser implantado dentro de uma rede virtual Azure [(VNet)](../../virtual-network/virtual-networks-overview.md).

O número de casos geridos que podem ser implantados na sub-rede de um VNet depende do tamanho da sub-rede (gama de sub-redes).

Ao criar uma instância gerida, o Azure atribui uma série de máquinas virtuais dependendo do nível selecionado durante o provisionamento. Como estas máquinas virtuais estão associadas à sua sub-rede, necessitam de endereços IP. Para garantir uma elevada disponibilidade durante as operações regulares e manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários numa sub-rede é maior do que o número de casos geridos nessa sub-rede.

Por predefinição, uma instância gerida precisa de um mínimo de 32 endereços IP numa sub-rede. Como resultado, pode utilizar uma máscara de sub-rede mínima de/27 ao definir os seus intervalos de IP de sub-rede. É recomendado realizar um planeamento cuidadoso do tamanho da sub-rede para as suas implementações de instância gerida. Os fatores que devem ser tomados em consideração durante o planeamento são:

- Número de casos geridos, incluindo parâmetros de exemplo:
  - nível de serviço
  - geração de hardware
  - número de vCores
- Planos para escalar/descer ou alterar o nível de serviço

> [!IMPORTANT]
> Um tamanho de sub-rede com 16 endereços IP (máscara de sub-rede /28) permitirá a implantação de instâncias geridas no seu interior, mas deve ser utilizado apenas para implantar um único exemplo utilizado para avaliação ou em cenários dev/teste, nos quais as operações de escala não serão realizadas.

## <a name="determine-subnet-size"></a>Determinar o tamanho da sub-rede

Dimensione a sua sub-rede de acordo com as necessidades futuras de implantação e dimensionamento. Os parâmetros seguintes podem ajudá-lo a formar um cálculo:

- A Azure utiliza cinco endereços IP na sub-rede para as suas próprias necessidades
- Cada cluster virtual atribui um número adicional de endereços 
- Cada instância gerida usa o número de endereços que depende do nível de preços e da geração de hardware

> [!IMPORTANT]
> Se existir algum recurso na sub-rede, não será possível alterar o intervalo de endereços da sub-rede. Também não é possível mover instâncias geridas de uma sub-rede para outra. Sempre que possível, considere usar sub-redes maiores em vez de menores para evitar problemas no futuro.

GP = propósito geral; BC = crítica de negócios; VC = cluster virtual

| **Género de hardware** | **Escalão de preço** | **Utilização do Azure** | **Utilização de VC** | **Utilização de exemplos** | **Total** _ |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \_ O total da coluna exibe o número de endereços que seriam tomados quando uma instância é implantada na sub-rede. Cada instância adicional na sub-rede adiciona o número de endereços representados com coluna de utilização de exemplos. Os endereços representados com a coluna de utilização Azure são partilhados em vários clusters virtuais, enquanto os endereços representados com coluna de utilização de VC são partilhados em todos os casos colocados nesse cluster virtual.

A operação de atualização normalmente requer redimensionar o cluster virtual. Em algumas circunstâncias, a operação de atualização exigirá a criação de clusters virtuais (para mais detalhes, verifique [o artigo de gestão de operações).](sql-managed-instance-paas-overview.md#management-operations) Em caso de criação de cluster virtual, o número de endereços adicionais necessários é igual ao número de endereços representados pela coluna de utilização VC resumida com endereços necessários para os casos colocados nesse cluster virtual (coluna de utilização de exemplo).

**Exemplo 1**: Planeia ter uma instância gerida para fins gerais (hardware Gen4) e uma instância de gestão crítica de negócios (hardware Gen5). Isto significa que precisa de um mínimo de 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 endereços IP para poder implantar. Como os intervalos IP são definidos na potência de 2, a sua sub-rede requer um intervalo mínimo de IP de 32 (2^5) para esta implementação.<br><br>
Como mencionado acima, em algumas circunstâncias, a operação de atualização requer criação de cluster virtual. Isto significa que, por exemplo, no caso de uma atualização para o caso de gestão crítica de negócios da Gen5 que requer uma criação de cluster virtual, você precisará ter mais 6 + 5 = 11 endereços IP disponíveis. Uma vez que já está a utilizar 22 dos 32 endereços, não existem endereços disponíveis para esta operação. Por isso, é necessário reservar a sub-rede com máscara de sub-rede de /26 (64 endereços).

**Exemplo 2**: Planeia ter três propósitos gerais (hardware Gen5) e dois casos de gestão crítica de negócios (hardware Gen5) colocados na mesma sub-rede. Isto significa que precisa de 5 + 6 + 3 * 3 + 2 * 5 = 30 endereços IP. Por isso, é necessário reservar a sub-rede com a máscara de sub-rede de /26. A seleção de uma máscara de sub-rede de /27 faria com que o número restante de endereços fosse de 2 (32 - 30), o que impediria as operações de atualização para todos os casos, uma vez que são necessários endereços adicionais na sub-rede para a realização de dimensionamento de exemplos.

**Exemplo 3**: Planeia ter uma instância gerida para fins gerais (hardware Gen5) e executar a operação de atualização vCores de vez em quando. Isto significa que precisa de 5 + 6 + 1 * 3 + 3 = 17 endereços IP. Como os intervalos IP são definidos na potência de 2, você precisa do intervalo IP de 32 (2^5) endereços IP. Por isso, é necessário reservar a sub-rede com a máscara de sub-rede de /27.

### <a name="address-requirements-for-update-scenarios"></a>Requisitos de endereço para cenários de atualização

Durante a escala de casos de operação requerem temporariamente capacidade ip adicional que depende do nível de preços e geração de hardware

| **Género de hardware** | **Escalão de preço** | **Cenário** | **Endereços adicionais** _ |
| --- | --- | --- | --- |
| Gen4 | GP ou BC | Escala vCores | 5 |
| Gen4 | GP ou BC | Armazenamento de escalonamento | 5 |
| Gen4 | GP ou BC | Passando de GP para BC ou BC para GP | 5 |
| Gen4 | GP | Mudar para Gen5_ | 9 |
| Gen4 | BC | Mudar para Gen5* | 11 |
| Gen5 | GP | Escala vCores | 3 |
| Gen5 | GP | Armazenamento de escalonamento | 0 |
| Gen5 | GP | Mudar para BC | 5 |
| Gen5 | BC | Escala vCores | 5 |
| Gen5 | BC | Armazenamento de escalonamento | 5 |
| Gen5 | BC | Mudar para GP | 3 |

  \* O hardware da Gen4 está a ser eliminado e já não está disponível para novas implementações. Atualize a geração de hardware da Gen4 para a Gen5 para tirar partido das capacidades específicas da geração de hardware da Gen5.

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral, veja [o que é Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md). .
- Saiba mais sobre [arquitetura de conectividade para SQL Managed Instance](connectivity-architecture-overview.md).
- Veja como [criar um VNet onde irá implementar o SQL Managed Instance](virtual-network-subnet-create-arm-template.md).
- Para questões dns, consulte [Configurar um DNS personalizado](custom-dns-configure.md).
