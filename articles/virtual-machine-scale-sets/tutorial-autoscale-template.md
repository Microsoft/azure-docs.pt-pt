---
title: Tutorial - Autoescala um conjunto de escala com modelos Azure
description: Saiba como utilizar os modelos do Azure Resource Manager para dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais, à medida que a CPU exige aumentos e diminuições
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: fae86e13be624d7a5304aa04b82432e1163b1244
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84629541"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-an-azure-template"></a>Tutorial: Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais com um modelo do Azure
Quando criar um conjunto de dimensionamento, pode definir o número de instâncias de VM que quer executar. À medida que a sua aplicação exige alterações, pode aumentar ou reduzir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite-lhe manter-se a par da exigência do cliente ou responder às alterações de desempenho durante todo o ciclo de vida da aplicação. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e utilizar regras de dimensionamento automático
> * Teste de esforço das instâncias e acionar as regras de dimensionamento automático
> * Voltar ao dimensionamento automático à medida que a exigência diminui

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.29 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)]( /cli/azure/install-azure-cli). 


## <a name="define-an-autoscale-profile"></a>Definir um perfil de dimensionamento automático
Defina um perfil de dimensionamento automático num modelo do Azure com o fornecedor de recursos *Microsoft.insights/autoscalesettings*. Um *perfil* fornece detalhes sobre a capacidade do conjunto de dimensionamento e quaisquer regras associadas. O exemplo seguinte define um perfil com o nome *Dimensionamento automático por percentagem com base na utilização da CPU* e define a capacidade predefinida e mínima de *2* instâncias de VM e um máximo de *10*:

```json
{
"type": "Microsoft.insights/autoscalesettings",
"name": "Autoscale",
"apiVersion": "2015-04-01",
"location": "[variables('location')]",
"scale": null,
"properties": {
  "profiles": [
    {
      "name": "Autoscale by percentage based on CPU usage",
      "capacity": {
        "minimum": "2",
        "maximum": "10",
        "default": "2"
      }
    }
  ]
}
```


## <a name="define-a-rule-to-autoscale-out"></a>Definir uma regra para dimensionamento automático para aumentar horizontalmente
Se a exigência da aplicação aumentar, a carga sobre as instâncias de VM no conjunto de dimensionamento também aumenta. Se este aumento de carga for consistente, em vez de ser apenas uma breve exigência, pode configurar regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando estas instâncias de VM forem criadas e as aplicações forem implementadas, o conjunto de dimensionamento começa a distribuir o tráfego pelas mesmas através do balanceador de carga. Controla as métricas a monitorizar, como a CPU ou o disco, quando a carga da aplicação tem de cumprir um determinado limiar e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

No exemplo seguinte, é definida uma regra que aumenta o número de instâncias de VM num conjunto de dimensionamento quando a carga média da CPU é superior a 70% durante um período de 5 minutos. Quando a regra for acionada, o número de instâncias de VM é aumentado em três.

Os parâmetros seguintes são utilizados para esta regra:

| Parâmetro         | Explicação                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *nome métrico*      | A métrica de desempenho para monitorizar e aplicar ações ao conjunto de dimensionamento.                                                   | Percentagem da CPU  |
| *timeGrain*       | Frequência com que as métricas são recolhidas para análise.                                                                   | 1 minuto        |
| *timeAggregation* | Define a forma como as métricas recolhidas devem ser agregadas para análise.                                                | Média         |
| *timeWindow*      | A quantidade de tempo monitorizado antes dos valores de métrica e limiar serem comparados.                                   | 5 minutos       |
| *operador*        | Operador utilizado para comparar os dados de métrica relativamente ao limiar.                                                     | Maior Que    |
| *limiar*       | O valor que faz com que a regra de dimensionamento automático acione uma ação.                                                      | 70%             |
| *direção*       | Define se o conjunto de dimensionamento deve aumentar e reduzir verticalmente quando a regra se aplicar.                                              | Aumentar        |
| *tipo*            | Indica que o número de instâncias de VM deve ser alterado por um valor específico.                                    | Alterar Contagem    |
| *value*           | Quantas instâncias de VM devem ser aumentadas ou reduzidas horizontalmente quando a regra se aplicar.                                             | 3               |
| *tempo de arrefecimento*        | A quantidade de tempo de espera antes de a regra ser aplicada novamente para que as ações de dimensionamento automático tenham tempo de entrar em vigor. | 5 minutos       |

A regra seguinte seria adicionada à secção de perfil do fornecedor de recursos *Microsoft.insights/autoscalesettings* da secção anterior:

```json
"rules": [
  {
    "metricTrigger": {
      "metricName": "Percentage CPU",
      "metricNamespace": "",
      "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
      "metricResourceLocation": "[variables('location')]",
      "timeGrain": "PT1M",
      "statistic": "Average",
      "timeWindow": "PT5M",
      "timeAggregation": "Average",
      "operator": "GreaterThan",
      "threshold": 70
    },
    "scaleAction": {
      "direction": "Increase",
      "type": "ChangeCount",
      "value": "3",
      "cooldown": "PT5M"
    }
  }
]
```


## <a name="define-a-rule-to-autoscale-in"></a>Definir uma regra para dimensionamento automático para reduzir horizontalmente
À noite ou ao fim de semana, a exigência da aplicação pode diminuir. Se esta diminuição de carga for consistente durante um certo período de tempo, pode configurar regras de dimensionamento automático para diminuir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de dimensionamento para reduzir horizontalmente reduz o custo de execução do conjunto de dimensionamento, uma vez que apenas executa o número de instâncias necessário para satisfazer a exigência atual.

O exemplo seguinte define uma regra para reduzir horizontalmente o número de instâncias de VM para um quando a CPU média carregar e, em seguida, cair abaixo de 30% durante um período de 5 minutos. Esta regra seria adicionada ao perfil de dimensionamento automático após a regra anterior para aumentar horizontalmente:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', variables('vmssName'))]",
    "metricResourceLocation": "[variables('location')]",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-an-autoscaling-scale-set"></a>Criar um conjunto de dimensionamento automático
Vamos utilizar um modelo de exemplo para criar um conjunto de dimensionamento e aplicar regras de dimensionamento automático. Pode [rever o modelo completo](https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json) ou [ver a secção *Microsoft.insights/autoscalesettings* do fornecedor de recursos](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/autoscale.json#L220) do modelo.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [az group deployment create](/cli/azure/group/deployment). Quando lhe for pedido, forneça o seu nome de utilizador, como *azureuser*e a palavra-passe que são utilizados como as credenciais para cada instância de VM:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/autoscale.json
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="generate-cpu-load-on-scale-set"></a>Gerar a carga da CPU no conjunto de dimensionamento
Para testar as regras de dimensionamento automático, gere a carga de CPU nas instâncias de VM no conjunto de dimensionamento. Esta carga de CPU simulada faz com que as regras de dimensionamento automático aumentem horizontalmente e aumentem o número de instâncias de VM. Uma vez que a carga de CPU simulada é diminuída, as regras de dimensionamento automático reduzem horizontalmente e reduzem o número de instâncias de VM.

Em primeiro lugar, liste o endereço e as portas para ligar a instâncias de VM num conjunto de dimensionamento com [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

O seguinte resultado de exemplo mostra o nome da instância, o endereço IP público do balanceador de carga e o número da porta para o qual as regras NAT (Tradução de Endereços de Rede) encaminham o tráfego:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH para a sua primeira instância de VM. Especifique o seu endereço IP público e o número da porta com o parâmetro `-p`, conforme mostrado no comando anterior:

```console
ssh azureuser@13.92.224.66 -p 50001
```

Depois de iniciar sessão, instale o utilitário **stress**. Inicie *10 funções de trabalho de * **stress** que geram carga da CPU. Estas funções de trabalho são executadas durante *420* segundos, que é o suficiente para fazer com que as regras de dimensionamento automático implementem a ação pretendida.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Quando o **stress** mostrar um resultado semelhante a *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, prima a tecla *Enter* para regressar à linha de comandos.

Para confirmar que o **stress** gera carga de CPU, examine a carga de sistema ativa com o utilitário **top**:

```console
top
```

Saia de **top** e, em seguida, feche a ligação à instância de VM. O **stress** continua a ser executado na instância de VM.

```console
Ctrl-c
exit
```

Ligue à segunda instância de VM com o número de porta listado do anterior [az vmss list-instance-connection-info](/cli/azure/vmss):

```console
ssh azureuser@13.92.224.66 -p 50003
```

Instale e execute o **stress** e, em seguida, inicie dez funções de trabalho nesta segunda instância de VM.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Novamente, quando o **stress** mostrar um resultado semelhante a *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, prima a tecla *Enter* para regressar à linha de comandos.

Feche a ligação à segunda instância de VM. O **stress** continua a ser executado na instância de VM.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorizar as regras de dimensionamento automático ativas
Para monitorizar o número de instâncias de VM no conjunto de dimensionamento, utilize **watch**. Demora 5 minutos até as regras de dimensionamento automático iniciarem o processo de aumentar horizontalmente para a carga de CPU gerada por **stress** em cada uma das instâncias de VM:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

Assim que tiver sido cumprido o limiar de CPU, as regras de dimensionamento automático aumentam o número de instâncias de VM no conjunto de dimensionamento. O resultado seguinte mostra três VMs criadas à medida que o conjunto de dimensionamento aumenta horizontalmente:

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Assim que o **stress** para nas instâncias de VM iniciais, a carga de CPU média volta ao normal. Após mais 5 minutos, as regras de dimensionamento automático reduzem horizontalmente o número de instâncias de VM. As ações para reduzir horizontalmente removem primeiro as instâncias de VM com os IDs mais elevados. Quando um conjunto de dimensionamento utiliza Conjuntos de Disponibilidade ou Zonas de Disponibilidade, as ações de redução horizontal são distribuídas uniformemente nessas instâncias de VMs. O resultado de exemplo seguinte mostra uma instância de VM eliminada à medida que o conjunto de dimensionamento reduz horizontalmente de forma automática:

```output
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Saia de *watch* com `Ctrl-c`. O conjunto de dimensionamento continua a reduzir horizontalmente a cada 5 minutos e remove uma instância de VM até ser atingida uma contagem mínima de duas instâncias.


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o seu conjunto de escalas e recursos adicionais, elimine o grupo de recursos e todos os seus recursos com [o grupo AZ eliminar:](/cli/azure/group)

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a aumentar e reduzir automaticamente um conjunto de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Utilizar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e utilizar regras de dimensionamento automático
> * Teste de esforço das instâncias e acionar as regras de dimensionamento automático
> * Voltar ao dimensionamento automático à medida que a exigência diminui
