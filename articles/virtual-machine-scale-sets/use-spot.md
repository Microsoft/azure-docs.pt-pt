---
title: Criar um conjunto de dimensionamento que usa VMs de ponto do Azure (versão prévia)
description: Saiba como criar conjuntos de dimensionamento de máquinas virtuais do Azure que usam VMs pontuais para economizar em custos.
services: virtual-machine-scale-sets
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 10/23/2019
ms.author: cynthn
ms.openlocfilehash: d2c8e599e44e48517920862e1fcf83e1a5e24910
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647633"
---
# <a name="preview-azure-spot-vms-for-virtual-machine-scale-sets"></a>Visualização: VMs de ponto do Azure para conjuntos de dimensionamento de máquinas virtuais 

O uso do Azure Spot em conjuntos de dimensionamento permite que você aproveite a capacidade não utilizada a uma economia de custos significativa. Em qualquer momento em que o Azure precise da capacidade de volta, a infraestrutura do Azure removerá instâncias especiais. Portanto, as instâncias Spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lotes, ambientes de desenvolvimento/teste, grandes cargas de trabalho de computação e muito mais.

A quantidade de capacidade disponível pode variar com base no tamanho, região, hora do dia e muito mais. Ao implantar instâncias Spot em conjuntos de dimensionamento, o Azure alocará a instância somente se houver capacidade disponível, mas não há SLA para essas instâncias. Um conjunto de escala de spot é implantado em um único domínio de falha e não oferece nenhuma garantia de alta disponibilidade.

> [!IMPORTANT]
> Atualmente, as instâncias especiais estão em visualização pública.
> Esta versão de visualização não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Para a parte inicial da visualização pública, as instâncias Spot terão um preço fixo, portanto, não haverá nenhuma remoção baseada em preço.

## <a name="pricing"></a>Preços

O preço para instâncias especiais é variável, com base na região e SKU. Para obter mais informações, consulte preços para [Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/). 


Com o preço variável, você tem a opção de definir um preço máximo, em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, o valor `0.98765`seria um preço máximo de $0.98765 USD por hora. Se você definir o preço máximo como `-1`, a instância não será removida com base no preço. O preço da instância será o preço atual para o ponto ou o preço de uma instância padrão, o que nunca é menor, desde que haja capacidade e cota disponível.

## <a name="eviction-policy"></a>Política de remoção

Ao criar conjuntos de escala de spot, você pode definir a política de remoção como *desalocar* (padrão) ou *excluir*. 

A política de *desalocação* move suas instâncias removidas para o estado parado-desalocado, permitindo que você reimplante instâncias removidas. No entanto, não há nenhuma garantia de que a alocação terá sucesso. As VMs desalocadas serão contadas em relação à cota da instância do conjunto de dimensionamento e você será cobrado pelos discos subjacentes. 

Se você quiser que suas instâncias em seu conjunto de escala especial sejam excluídas quando forem removidas, você poderá definir a *política de remoção*a ser excluída. Com a política de remoção definida como excluir, você pode criar novas VMs aumentando a propriedade de contagem de instâncias do conjunto de dimensionamento. As VMs removidas são excluídas junto com seus discos subjacentes e, portanto, você não será cobrado pelo armazenamento. Você também pode usar o recurso de dimensionamento automático dos conjuntos de dimensionamento para tentar e compensar automaticamente as VMs removidas, no entanto, não há nenhuma garantia de que a alocação terá sucesso. É recomendável que você use apenas o recurso de dimensionamento automático em conjuntos de escala de spot ao definir a política de remoção a ser excluída para evitar o custo de seus discos e atingir os limites de cota. 

Os usuários podem optar por receber notificações na VM por meio [do Azure eventos agendados](../virtual-machines/linux/scheduled-events.md). Isso notificará você se suas VMs estiverem sendo removidas e você terá 30 segundos para concluir todos os trabalhos e realizar tarefas de desligamento antes da remoção. 


## <a name="deploying-spot-vms-in-scale-sets"></a>Implantando VMs Spot em conjuntos de dimensionamento

Para implantar VMs pontuais em conjuntos de dimensionamento, você pode definir o novo sinalizador de *prioridade* para *identificar*. Todas as VMs em seu conjunto de dimensionamento serão definidas como spot. Para criar um conjunto de dimensionamento com VMs Spot, use um dos seguintes métodos:
- [Portal do Azure](#portal)
- [CLI do Azure](#azure-cli)
- [O Azure PowerShell](#powershell)
- [Modelos de Azure Resource Manager](#resource-manager-templates)

## <a name="portal"></a>Portal

O processo para criar um conjunto de dimensionamento que usa VMs pontuais é o mesmo detalhado no [artigo de introdução](quick-create-portal.md). Ao implantar um conjunto de dimensionamento, você pode optar por definir o sinalizador de spot e a política de remoção: ![criar um conjunto de dimensionamento com VMs Spot](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>CLI do Azure

O processo para criar um conjunto de dimensionamento com VMs pontuais é o mesmo detalhado no [artigo de introdução](quick-create-cli.md). Basta adicionar o '--priority spot ' e adicionar `--max-price`. Neste exemplo, usamos `-1` para `--max-price` para que a instância não seja removida com base no preço.

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

O processo para criar um conjunto de dimensionamento com VMs pontuais é o mesmo detalhado no [artigo de introdução](quick-create-powershell.md).
Basta adicionar '-priority spot ' e fornecer um `-max-price` ao [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>Modelos do Gestor de Recursos

O processo para criar um conjunto de dimensionamento que usa VMs pontuais é o mesmo detalhado no artigo de introdução para [Linux](quick-create-template-linux.md) ou [Windows](quick-create-template-windows.md). Adicione a propriedade ' priority ' ao tipo de recurso *Microsoft. Compute/virtualMachineScaleSets/virtualMachineProfile* em seu modelo e especifique *Spot* como o valor. Certifique-se de usar a versão de API *2019-03-01* ou superior. 

Para definir a política de remoção como exclusão, adicione o parâmetro ' evictionPolicy ' e defina-o como *excluir*.

O exemplo a seguir cria um conjunto de escala de spot do Linux chamado *Myscaleset* no *Oeste EUA Central*, que *excluirá* as VMs no conjunto de dimensionamento na remoção:

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "myScaleSet",
  "location": "East US 2",
  "apiVersion": "2019-03-01",
  "sku": {
    "name": "Standard_DS2_v2",
    "capacity": "2"
  },
  "properties": {
    "upgradePolicy": {
      "mode": "Automatic"
    },
    "virtualMachineProfile": {
       "priority": "Spot",
       "evictionPolicy": "delete",
       "storageProfile": {
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage"
        },
        "imageReference":  {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04-LTS",
          "version": "latest"
        }
      },
      "osProfile": {
        "computerNamePrefix": "myvmss",
        "adminUsername": "azureuser",
        "adminPassword": "P@ssw0rd!"
      }
    }
  }
}
```
## <a name="faq"></a>FAQ

**P:** Uma vez criada, é uma instância Spot igual à instância padrão?

**R:** Sim, exceto que não há SLA para VMs pontuais e elas podem ser removidas a qualquer momento.


**P:** O que fazer ao ser removido, mas ainda precisa de capacidade?

**R:** Recomendamos que você use VMs padrão em vez de VMs pontuais se precisar de capacidade imediatamente.


**P:** Como a cota é gerenciada para o ponto?

**R:** Instâncias especiais e instâncias padrão terão pools de cotas separados. A cota de spot será compartilhada entre as VMs e as instâncias do conjunto de dimensionamento. Para obter mais informações, veja [Subscrição do Azure e limites, quotas e restrições do serviço](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).


**P:** Posso solicitar uma cota adicional para o ponto?

**R:** Sim, você poderá enviar a solicitação para aumentar sua cota de VMs pontuais por meio do [processo de solicitação de cota padrão](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).


**P:** Posso converter conjuntos de dimensionamento existentes para identificar conjuntos de dimensionamento?

**R:** Não, a definição do sinalizador de `Spot` só tem suporte no momento da criação.


**P:** Se eu estivesse usando `low` para conjuntos de dimensionamento de baixa prioridade, preciso começar a usar `Spot` em vez disso?

**R:** Por enquanto, `low` e `Spot` funcionarão, mas você deve começar a fazer a transição para o usando `Spot`.


**P:** Posso criar um conjunto de dimensionamento com VMs regulares e detectar VMs?

**R:** Não, um conjunto de dimensionamento não pode dar suporte a mais de um tipo de prioridade.


**P:**  Posso usar o dimensionamento automático com conjuntos de escala de spot?

**R:** Sim, você pode definir regras de dimensionamento automático em seu conjunto de escala de spot. Se suas VMs forem removidas, o dimensionamento automático poderá tentar criar novas VMs pontuais. Lembre-se de que você não está garantido essa capacidade no entanto. 


**P:**  O dimensionamento automático funciona com ambas as políticas de remoção (desalocação e exclusão)?

**R:** É recomendável que você defina sua política de remoção para excluir ao usar o dimensionamento automático. Isso ocorre porque as instâncias desalocadas são contadas em relação à contagem de capacidade no conjunto de dimensionamento. Ao usar o dimensionamento automático, você provavelmente atingirá sua contagem de instâncias de destino rapidamente devido às instâncias desalocadas e removidas. 


**P:** Quais canais dão suporte a VMs pontuais?

**R:** Consulte a tabela abaixo para encontrar a disponibilidade da VM.

<a name="channel"></a>

| Canais do Azure               | Disponibilidade de VMs de ponto do Azure       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Sim                               |
| Pay As You Go                | Sim                               |
| Provedor de serviços de nuvem (CSP) | [Entre em contato com seu parceiro](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Vantagens                     | Não disponível                     |
| Patrocínios                    | Não disponível                     |
| Avaliação gratuita                   | Não disponível                     |


**P:** Onde posso postar perguntas?

**R:** Você pode postar e marcar sua pergunta com `azure-spot` em [Q & A](https://docs.microsoft.com/answers/topics/azure-spot.html). 

## <a name="next-steps"></a>Passos seguintes
Agora que você criou um conjunto de dimensionamento com VMs Spot, tente implantar nosso [modelo de dimensionamento automático usando o Spot](https://github.com/Azure/vm-scale-sets/tree/master/preview/lowpri).

Confira a [página de preços do conjunto de dimensionamento de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/) para obter detalhes de preços.
