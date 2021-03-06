---
title: Localização de resolução de problemasNotFoundForRoleSize ao implementar um serviço cloud (clássico) para Azure | Microsoft Docs
description: Este artigo mostra como resolver uma exceção LocalizaçãoNotFoundForRoleSize ao implementar um serviço Cloud (clássico) para Azure.
services: cloud-services
author: mamccrea
ms.author: mamccrea
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 54af2387ec0ff6c8f86f96821baad17736e8d85b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877971"
---
# <a name="troubleshoot-locationnotfoundforrolesize-when-deploying-a-cloud-service-classic-to-azure"></a>Localização de resolução de problemasNotFoundForRoleSize ao implementar um serviço cloud (clássico) para Azure

Neste artigo, irá resolver problemas de falhas de atribuição onde o tamanho de uma Máquina Virtual (VM) não está disponível quando implementar um serviço Azure Cloud (clássico).

Quando implementa casos num serviço Cloud (clássico) ou adiciona novas instâncias de funções web ou de trabalhador, o Microsoft Azure atribui recursos de computação.

Ocasionalmente, poderá receber erros durante estas operações mesmo antes de atingir o limite da subscrição do Azure.

> [!TIP]
> A informação também pode ser útil quando planeia a implantação dos seus serviços.

## <a name="symptom"></a>Sintoma

No portal Azure, navegue para o seu serviço Cloud (clássico) e na barra lateral selecione *registo de operação (clássico)* para ver os registos.

![A imagem mostra a lâmina de registo de operação (clássica).](./media/cloud-services-troubleshoot-location-not-found-for-role-size/cloud-services-troubleshoot-allocation-logs.png)

Ao inspecionar os registos do seu serviço Cloud (clássico), verá a seguinte exceção:

|Tipo de Exceção  |Mensagem de Erro  |
|---------|---------|
|LocationNotFoundForRoleSize     |A operação `{Operation ID}` ' ' ' falhou: 'O nível VM solicitado não está atualmente disponível na Região ( `{Region ID}` ) para esta subscrição. Por favor, tente outro nível ou desloque-se para outro local.'.|

## <a name="cause"></a>Causa

Há um problema de capacidade com a região ou aglomerado para o qual está a implantar. A exceção *LocationNotFoundForRoleSize* ocorre quando o recurso SKU que selecionou (tamanho VM) não está disponível para a região especificada.

## <a name="solution"></a>Solução

Neste cenário, deverá selecionar uma região diferente ou SKU para implementar o seu serviço Cloud (clássico) para. Antes de implementar ou atualizar o seu serviço Cloud (clássico), pode determinar quais SKUs estão disponíveis numa região ou zona de disponibilidade. Siga os processos [Azure CLI,](#list-skus-in-region-using-azure-cli) [PowerShell](#list-skus-in-region-using-powershell)ou [REST API](#list-skus-in-region-using-rest-api) abaixo.

### <a name="list-skus-in-region-using-azure-cli"></a>Lista SKUs na região usando Azure CLI

Você pode usar o [az vm list-skus](/cli/azure/vm?view=azure-cli-latest
#<a name="az_vm_list_skus-command"></a>az_vm_list_skus) comando.

- Utilize o `--location` parâmetro para filtrar a saída para o local que está a usar.
- Use o `--size` parâmetro para procurar por um nome de tamanho parcial.
- Para mais informações, consulte o [erro Resolve para o guia SKU não disponível.](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli)

    **Por exemplo:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **Resultados de exemplo:** ![ Azure CLI produção de executar o comando 'az vm list-skus -- localização sulcentralus -- tamanho Standard_F --tabela de saída', que mostra os SKUs disponíveis.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>Lista SKUs na região usando PowerShell

Pode utilizar o comando [Get-AzComputeResourceSku.](/powershell/module/az.compute/get-azcomputeresourcesku)

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

Pode utilizar a operação [Resource Skus - List.](/rest/api/compute/resourceskus/list) Devolve SKUs e regiões disponíveis no seguinte formato:

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
      <<The rest of your file is located here>>
  ]
}
    
```

## <a name="next-steps"></a>Passos seguintes

Para mais soluções de falha de alocação e para entender melhor como são geradas:

> [!div class="nextstepaction"]
> [Falhas de atribuição - Serviço em nuvem (clássico)](cloud-services-allocation-failures.md)

Se a sua edição de Azure não for abordada neste artigo, visite os fóruns Azure na [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema nestes fóruns ou publicar [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Também pode submeter um pedido de apoio ao Azure. Para submeter um pedido de apoio, na página de suporte do [Azure,](https://azure.microsoft.com/support/options/) selecione *Obter suporte*.
