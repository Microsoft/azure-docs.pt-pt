---
title: Resolução de problemas ConstrainedAllocationFailed ao implementar um serviço de nuvem para Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção condicionada Desafetada ao implementar um serviço de nuvem para a Azure.
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100521172"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Resolução de problemas ConstrainedAfailed ao implementar um serviço de nuvem para Azure

Neste artigo, irá resolver problemas de falhas de alocação onde os Serviços Azure Cloud não podem implementar devido a constrangimentos.

O Microsoft Azure atribui quando está:

- Atualizar instâncias de serviços em nuvem

- Adicionar novos casos de função web ou de trabalhador

- Implantação de instâncias para um serviço de nuvem

Pode ocasionalmente receber erros durante estas operações mesmo antes de atingir o limite de subscrição do Azure.

> [!TIP]
> A informação também pode ser útil quando planeia a implantação dos seus serviços.

## <a name="symptom"></a>Sintoma

No portal Azure, navegue para o seu serviço de nuvem e na barra lateral selecione *registos de operação (clássico)* para visualizar os registos.

Ao inspecionar os registos do seu serviço na nuvem, verá a seguinte exceção:

|Tipo de Exceção  |Mensagem de Erro  |
|---------|---------|
|ConstrangimentoAllocationFailed     |A operação Azure `{Operation ID}` ' '' falhou com código Compute.ConstrainedAllocationFailed. Detalhes: A atribuição falhou; incapaz de satisfazer constrangimentos a pedido. A implementação do novo serviço solicitada está vinculada a um Grupo de Afinidade, tem como destino uma Rede Virtual ou há uma implementação existente neste serviço alojado. Qualquer uma destas condições limita a nova implantação a recursos específicos da Azure. Reforce mais tarde ou tente reduzir o tamanho de VM ou o número de instâncias de função. Em alternativa, se possível, remova as restrições mencionadas anteriormente ou tente implementar numa região diferente.|

## <a name="cause"></a>Causa

Há um problema de capacidade com a região ou aglomerado para o qual está a implantar. Ocorre quando o recurso SKU selecionado não está disponível para o local especificado.

> [!NOTE]
> Quando o primeiro nó de um serviço de nuvem é implantado, é *fixado* a um conjunto de recursos. Um conjunto de recursos pode ser um único cluster, ou um grupo de aglomerados.
>
> Com o tempo, os recursos neste conjunto de recursos podem tornar-se totalmente utilizados. Se um serviço de nuvem fizer um pedido de alocação de recursos adicionais quando não há recursos suficientes disponíveis no conjunto de recursos fixados, o pedido resultará numa [falha de atribuição](cloud-services-allocation-failures.md).

## <a name="solution"></a>Solução

Neste cenário, deve selecionar uma região diferente ou SKU para implementar o seu serviço de nuvem para. Antes de implementar ou atualizar o seu serviço na nuvem, pode determinar quais SKUs estão disponíveis numa região ou zona de disponibilidade. Siga os processos [Azure CLI,](#list-skus-in-region-using-azure-cli) [PowerShell](#list-skus-in-region-using-powershell)ou [REST API](#list-skus-in-region-using-rest-api) abaixo.

### <a name="list-skus-in-region-using-azure-cli"></a>Lista SKUs na região usando Azure CLI

Pode utilizar o comando [az vm list-skus.](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus)

- Utilize o `--location` parâmetro para filtrar a saída para o local que está a usar.
- Use o `--size` parâmetro para procurar por um nome de tamanho parcial.
- Para mais informações, consulte o [erro Resolve para o guia SKU não disponível.](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli)

    **Por exemplo:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Resultados de exemplo:** ![ Azure CLI produção de executar o comando 'az vm list-skus -- localização sulcentralus -- tamanho Standard_F --tabela de saída', que mostra os SKUs disponíveis.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lista SKUs na região usando PowerShell

Pode utilizar o comando [Get-AzComputeResourceSku.](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku)

- Filtrar os resultados por localização.
- Deve ter a mais recente versão do PowerShell para este comando.
- Para mais informações, consulte o [erro Resolve para o guia SKU não disponível.](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell)

**Por exemplo:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**Outros comandos úteis:**

Filtrar os locais que contêm tamanho (Standard_DS14_v2):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

Filtrar todos os locais que contêm tamanho (V3):

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>Lista DE SKUs na região utilizando REST API

Pode utilizar a operação [Resource Skus - List.](https://docs.microsoft.com/rest/api/compute/resourceskus/list) Devolve SKUs e regiões disponíveis no seguinte formato:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>Passos seguintes

Para mais soluções de falha de alocação e para entender melhor como são geradas:

> [!div class="nextstepaction"]
> [Falhas de atribuição (serviços na nuvem)](cloud-services-allocation-failures.md)

Se a sua edição de Azure não for abordada neste artigo, visite os fóruns Azure na [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou publicar [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Também pode submeter um pedido de apoio ao Azure. Para submeter um pedido de apoio, na página de suporte do [Azure,](https://azure.microsoft.com/support/options/) selecione *Obter suporte*.
