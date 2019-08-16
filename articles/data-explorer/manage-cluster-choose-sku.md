---
title: Selecione o SKU de VM correto para o cluster de Data Explorer do Azure
description: Este artigo descreve como selecionar o tamanho de SKU ideal para o cluster de Data Explorer do Azure.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2eb23a65196ac4f6456f50dbbbfd9e4b484ad171
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515722"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Selecione o SKU de VM correto para o cluster de Data Explorer do Azure 

Quando você cria um novo cluster ou otimiza um cluster para uma carga de trabalho em alteração, o Azure Data Explorer oferece várias SKUs de VM (máquina virtual) para escolher. As VMs foram cuidadosamente escolhidas para fornecer o custo mais adequado para qualquer carga de trabalho. 

O tamanho e o SKU da VM do cluster de gerenciamento de dados são totalmente gerenciados pelo serviço de Data Explorer do Azure. Eles são determinados por fatores como o tamanho da VM do mecanismo e a carga de trabalho de ingestão. 

Você pode alterar o SKU da VM para o cluster do mecanismo a qualquer momento ao [escalar verticalmente o cluster](manage-cluster-vertical-scaling.md). É melhor começar com o menor tamanho de SKU que se ajusta ao cenário inicial. Tenha em mente que a expansão do cluster resulta em um tempo de inatividade de até 30 minutos enquanto o cluster é recriado com o novo SKU de VM.

> [!TIP]
> As [instâncias reservadas de computação (ri)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) são aplicáveis ao cluster de data Explorer do Azure.  

Este artigo descreve várias opções de SKU de VM e fornece os detalhes técnicos que podem ajudá-lo a fazer a melhor escolha.

## <a name="select-a-cluster-type"></a>Selecionar um tipo de cluster

O Azure Data Explorer oferece dois tipos de clusters:

* **Produção**: Os clusters de produção contêm dois nós para o mecanismo e os clusters de gerenciamento de dados e são operados sob o [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)de data Explorer do Azure.

* **Desenvolvimento/teste (sem SLA)** : Os clusters de desenvolvimento/teste têm um único nó do D11 v2 para o cluster do mecanismo e um único nó D1 para o cluster de gerenciamento de dados. Esse tipo de cluster é a configuração de custo mais baixo devido à sua contagem de instâncias baixa e nenhuma cobrança de marcação do mecanismo. Não há SLA para essa configuração de cluster, pois ela não tem redundância.

## <a name="sku-types"></a>Tipos de SKU

Ao criar um cluster de Data Explorer do Azure, selecione o SKU de VM *ideal* para a carga de trabalho planejada. Você pode escolher entre as duas famílias de SKU de Data Explorer do Azure a seguir:

* **D v2**: A SKU D é otimizada para computação e vem em dois tipos:
    * A própria VM
    * A VM agrupada com discos de armazenamento Premium

* **LS**: A SKU L é otimizada para armazenamento. Ele tem um tamanho de SSD muito maior do que o SKU D com preço semelhante.

As principais diferenças entre os tipos de SKU disponíveis são descritas na tabela a seguir:
 
| Atributo | SKU D | SKU L |
|---|---|---
|**SKUs pequenos**|O tamanho mínimo é D11 com dois núcleos|O tamanho mínimo é L4 com quatro núcleos |
|**Disponibilidade**|Disponível em todas as regiões (a versão DS + PS tem disponibilidade mais limitada)|Disponível em algumas regiões |
|**Custo por&nbsp;cache de GB por núcleo**|Alta com a SKU D, baixa com a versão do DS + PS|Mais baixo com a opção pago pelo uso |
|**Preços de instâncias reservadas (RI)**|Alto desconto (mais de&nbsp;55 por cento para um compromisso de três anos)|Desconto inferior (20&nbsp;por cento para um compromisso de três anos) |  

## <a name="select-your-cluster-vm"></a>Selecione a VM do cluster 

Para selecionar a VM do cluster, [Configure o dimensionamento vertical](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Com várias opções de SKU de VM para escolher, você pode otimizar os custos para o desempenho e os requisitos de cache dinâmico para seu cenário. 
* Se você precisar do desempenho mais adequado para um alto volume de consulta, o SKU ideal deverá ser otimizado para computação. 
* Se você precisar consultar grandes volumes de dados com uma carga de consulta relativamente menor, o SKU com otimização de armazenamento pode ajudar a reduzir os custos e ainda fornecer um desempenho excelente.

Como o número de instâncias por cluster para as pequenas SKUs é limitado, é preferível usar VMs maiores com RAM maior. Mais RAM é necessária para alguns tipos de consulta que colocam mais demanda no recurso de RAM, como consultas que usam `joins`. Portanto, quando estiver considerando opções de dimensionamento, recomendamos que você Escale verticalmente para uma SKU maior em vez de expandir adicionando mais instâncias.

## <a name="vm-options"></a>Opções de VM

As especificações técnicas para as VMs do cluster Data Explorer do Azure são descritas na tabela a seguir:

|**Name**| **Categoria** | **Tamanho do SSD** | **Núcleos** | **EXISTENTE** | **Discos de armazenamento Premium (&nbsp;1 TB)**| **Contagem mínima de instâncias por cluster** | **Contagem máxima de instâncias por cluster**
|---|---|---|---|---|---|---|---
|D11 v2| otimizado para computação | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (exceto para desenvolvimento/teste SKU, que é 1)
|D12 v2| otimizado para computação | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| otimizado para computação | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| otimizado para computação | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| otimizado para armazenamento | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| otimizado para armazenamento | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| otimizado para armazenamento | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| otimizado para armazenamento | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| otimizado para armazenamento | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| otimizado para armazenamento | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| otimizado para armazenamento | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Você pode exibir a lista de SKU de VM atualizada por região usando a API do Azure Data Explorer [ListSkus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet). 
* Saiba mais sobre as [várias SKUs de computação](/azure/virtual-machines/windows/sizes-compute). 

## <a name="next-steps"></a>Passos seguintes

* Você pode [escalar ou](manage-cluster-vertical-scaling.md) reduzir verticalmente o cluster do mecanismo a qualquer momento alterando a SKU da VM, dependendo das necessidades diferentes. 

* Você pode [reduzir ou escalar](manage-cluster-horizontal-scaling.md) horizontalmente o tamanho do cluster do mecanismo para alterar a capacidade, dependendo das demandas em constante mudança.

