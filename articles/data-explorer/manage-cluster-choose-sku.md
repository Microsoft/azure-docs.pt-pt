---
title: Selecione VM SKU correto para o seu cluster Azure Data Explorer
description: Este artigo descreve como selecionar o tamanho ideal do SKU para o cluster Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561855"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selecione o VM SKU correto para o seu cluster Azure Data Explorer 

Quando cria um novo cluster ou otimiza um cluster para uma carga de trabalho em mudança, o Azure Data Explorer oferece várias SKUs de máquina virtual (VM) para escolher. Os VMs foram cuidadosamente escolhidos para lhe dar o custo mais ideal para qualquer carga de trabalho. 

O tamanho e VM SKU do cluster de gestão de dados são totalmente geridos pelo serviço Azure Data Explorer. São determinados por fatores como o tamanho vm do motor e a carga de trabalho de ingestão. 

Pode alterar o VM SKU para o cluster do motor a qualquer momento, [dimensionando o cluster](manage-cluster-vertical-scaling.md). É melhor começar com o menor tamanho SKU que se adequa ao cenário inicial. Tenha em mente que a escalada do cluster resulta num tempo de paragem de até 30 minutos enquanto o cluster é recriado com o novo VM SKU.

> [!TIP]
> Compute [Reserved Instances (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) é aplicável ao cluster Azure Data Explorer.  

Este artigo descreve várias opções de VM SKU e fornece os detalhes técnicos que podem ajudá-lo a fazer a melhor escolha.

## <a name="select-a-cluster-type"></a>Selecione um tipo de cluster

O Azure Data Explorer oferece dois tipos de clusters:

* **Produção**: Os clusters de produção contêm dois nós para clusters de gestão de motores e dados e são operados ao abrigo do Azure Data Explorer [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/).

* **Dev/Teste (sem SLA)**: Os clusters Dev/Test têm um único nó D11 v2 para o cluster do motor e um único nó D1 para o cluster de gestão de dados. Este tipo de cluster é a configuração de custo mais baixa devido à sua baixa contagem de casos e sem carga de marcação do motor. Não há SLA para esta configuração de cluster, porque falta redundância.

## <a name="sku-types"></a>Tipos de SKU

Quando criar um cluster Azure Data Explorer, selecione o *VM* SKU ideal para a carga de trabalho planeada. Pode escolher entre as duas seguintes famílias Azure Data Explorer SKU:

* **D v2**: O D SKU é otimizado em computação e vem em dois sabores:
    * A própria VM
    * O VM agregado com discos de armazenamento premium

* **LS**: O L SKU está otimizado para armazenamento. Tem um tamanho SSD muito maior do que o SKU de preço semelhante.

As principais diferenças entre os tipos de SKU disponíveis são descritas na tabela seguinte:
 
| Atributo | D SKU | L SKU |
|---|---|---
|**Pequeno SKUs**|Tamanho mínimo é D11 com dois núcleos|Tamanho mínimo é L4 com quatro núcleos |
|**Disponibilidade**|Disponível em todas as regiões (a versão DS+PS tem disponibilidade mais limitada)|Disponível em algumas regiões |
|**Custo&nbsp;por cache GB por núcleo**|Alto com o D SKU, baixo com a versão DS+PS|O mais baixo com a opção Pay-As-You-Go |
|**Preços reservados de instâncias (RI)**|Desconto elevado (mais&nbsp;de 55 por cento para um compromisso de três anos)|Desconto mais baixo&nbsp;(20 por cento para um compromisso de três anos) |  

## <a name="select-your-cluster-vm"></a>Selecione o seu cluster VM 

Para selecionar o seu cluster VM, [configure a escala vertical](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Com várias opções VM SKU para escolher, pode otimizar custos para o desempenho e requisitos de cache quente para o seu cenário. 
* Se precisar do desempenho mais ideal para um volume de consulta elevado, o SKU ideal deve ser otimizado para calcular. 
* Se precisar de consultar grandes volumes de dados com uma carga relativamente mais baixa, o SKU otimizado para armazenamento pode ajudar a reduzir custos e ainda proporcionar um excelente desempenho.

Como o número de instâncias por cluster para as pequenas SKUs é limitado, é preferível usar VMs maiores que têm maior RAM. É necessário mais RAM para alguns tipos de consultas que colocam mais `joins`procura no recurso RAM, como consultas que usam . Portanto, quando estiver a considerar opções de escala, recomendamos que se dimensione para um SKU maior em vez de escalar adicionando mais instâncias.

## <a name="vm-options"></a>Opções VM

As especificações técnicas para os VMs do cluster Azure Data Explorer são descritas na tabela seguinte:

|**Nome**| **Categoria** | **Tamanho SSD** | **Núcleos** | **RAM** | **Discos de armazenamento&nbsp;premium (1 TB)**| **Contagem mínima de instância por cluster** | **Contagem máxima de instância por cluster**
|---|---|---|---|---|---|---|---
|D11 v2| computação otimizada | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (exceto para dev/teste SKU, que é 1)
|D12 v2| computação otimizada | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| computação otimizada | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| computação otimizada | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| armazenamento otimizado | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| armazenamento otimizado | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| armazenamento otimizado | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| armazenamento otimizado | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| armazenamento otimizado | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| armazenamento otimizado | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| armazenamento otimizado | 2.6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Pode ver a lista vm sKU atualizada por região utilizando a [API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)do Explorador de Dados Azure. 
* Saiba mais sobre as [várias SKUs.](/azure/virtual-machines/windows/sizes) 

## <a name="next-steps"></a>Passos seguintes

* Pode [escalar ou reduzir](manage-cluster-vertical-scaling.md) o cluster do motor a qualquer momento, alterando o VM SKU, dependendo das diferentes necessidades. 

* Pode [escalar ou escalar](manage-cluster-horizontal-scaling.md) o tamanho do cluster do motor para alterar a capacidade, dependendo da alteração das exigências.

