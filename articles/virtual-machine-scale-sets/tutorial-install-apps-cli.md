---
title: Tutorial - Instalar aplicações em escala definida com Azure CLI
description: Saiba como utilizar a CLI do Azure para instalar aplicações nos conjuntos de dimensionamento de máquinas virtuais com a Extensão de Script Personalizado
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: cf383d41df5d41ea52065f3651b2068861631116
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934619"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Tutorial: Instalar aplicações em conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure
Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Num tutorial anterior, aprendeu a criar e utilizar uma imagem de VM personalizada para implementar as suas instâncias de VM. Esta imagem personalizada inclui configurações e instalações de aplicações manuais. Pode também automatizar a instalação de aplicações num conjunto de dimensionamento após cada instância de VM ser implementada ou atualizar uma aplicação que já é executada num conjunto de dimensionamento. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar automaticamente aplicações no seu conjunto de dimensionamento
> * Utilizar a Extensão de Script Personalizado do Azure
> * Atualizar uma aplicação em execução num conjunto de dimensionamento

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.29 ou posterior do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada. 


## <a name="what-is-the-azure-custom-script-extension"></a>O que é a Extensão de Script Personalizado do Azure?
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão.

A extensão de Script Personalizado é integrada em modelos do Azure Resource Manager e pode também ser utilizada com a CLI do Azure, o Azure PowerShell, o portal do Azure ou a API REST. Para obter mais informações, veja a [Descrição geral da Extensão de Script Personalizado](../virtual-machines/extensions/custom-script-linux.md).

Para utilizar a Extensão de Script Personalizado com a CLI do Azure, crie um ficheiro JSON que defina que ficheiros obter e comandos executar. Estas definições de JSON podem ser reutilizadas em implementações de conjuntos de dimensionamento para aplicar instalações de aplicações consistentes.


## <a name="create-custom-script-extension-definition"></a>Criar a definição da Extensão de Script Personalizado
Para ver a Extensão de Script Personalizado em ação, vamos criar um conjunto de dimensionamento que instala o servidor Web NGINX e dá como resultado o nome de anfitrião da instância de VM do conjunto de dimensionamento. A seguinte definição da Extensão de Script Personalizado transfere um script de exemplo do GitHub, instala os pacotes necessários e escreve o nome de anfitrião da instância de VM numa página HTML simples.

Na shell atual, crie um ficheiro com o nome *customConfig.json* e cole a seguinte configuração. Por exemplo, crie o ficheiro no Cloud Shell, não no seu computador local. Pode utilizar qualquer editor que desejar. Introduza `sensible-editor customConfig.json` no Cloud Shell para criar o ficheiro e ver uma lista dos editores disponíveis.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

> [!CAUTION]
> Poderá ser necessário inverter a utilização das cotações únicas (') e duplas (") dentro do bloco JSON se decidir fazer referência diretamente ao JSON (em vez de fazer referência à *customConfig.jsno* ficheiro) no parâmetro *de definições abaixo.* 


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss). O exemplo seguinte cria um conjunto nomeado de dimensionamento *myScaleSet* e gera chaves SSH, caso não existam:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.


## <a name="apply-the-custom-script-extension"></a>Aplicar a Extensão de Script Personalizado
Aplique a configuração de Extensão de Script Personalizado às instâncias de VM no seu conjunto de dimensionamento com [az vmss extension set](/cli/azure/vmss/extension). O exemplo seguinte aplica a configuração *customConfig.json* às instâncias de VM *myScaleSet* no grupo de recursos com o nome *myResourceGroup*:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

Cada instância de VM no conjunto de dimensionamento transfere e executa o script do GitHub. Num exemplo mais complexo, podem ser instalados vários ficheiros e componentes de aplicações. Se o conjunto de dimensionamento for aumentado verticalmente, as novas instâncias de VM aplicam automaticamente a mesma definição da Extensão de Script Personalizado e instalam a aplicação necessária.


## <a name="test-your-scale-set"></a>Testar o seu conjunto de dimensionamento
Para permitir que o tráfego alcance o servidor Web, crie uma regra de balanceador de carga com [az network lb rule create](/cli/azure/network/lb/rule). O exemplo seguinte cria uma regra com o nome *myLoadBalancerRuleWeb*:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Para ver o seu servidor Web em ação, obtenha o endereço IP público do seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo seguinte obtém o endereço IP para *myScaleSetLBPublicIP* criada como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Introduza o endereço IP público do balanceador de carga num browser. O balanceador de carga distribui o tráfego para uma das suas instâncias de VM, conforme mostra o exemplo seguinte:

![Página Web básica no Nginx](media/tutorial-install-apps-cli/running-nginx.png)

Deixe o seu browser aberto para que possa ver uma versão atualizada no próximo passo.


## <a name="update-app-deployment"></a>Atualizar a implementação da aplicação
Ao longo do ciclo de vida de um conjunto de dimensionamento, poderá precisar de implementar uma versão atualizada da sua aplicação. Com a Extensão de Script Personalizado, pode referenciar um script de implementação atualizado e, em seguida, voltar a aplicar a extensão ao seu conjunto de dimensionamento. Quando o conjunto de escala foi criado num passo anterior, o `--upgrade-policy-mode` foi definido como *automático*. Esta definição permite que as instâncias de VM no conjunto de dimensionamento atualizem e apliquem automaticamente a versão mais recente da sua aplicação.

Na shell atual, crie um ficheiro com o nome *customConfigv2.json* e cole a seguinte configuração. Esta definição executa uma versão *v2* atualizada do script de instalação da aplicação:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Aplique a configuração da Extensão de Script Personalizado às instâncias de VM no seu conjunto de dimensionamento novamente com [az vmss extension set](/cli/azure/vmss/extension). O *customConfigv2.json* é utilizado para aplicar a versão atualizada da aplicação:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

Todas as instâncias de VM no conjunto de dimensionamento são automaticamente atualizadas com a versão mais recente da página Web de exemplo. Para ver a versão atualizada, atualize o site no seu browser:

![Página Web atualizada no Nginx](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Limpar os recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, aprendeu a instalar e atualizar automaticamente aplicações no seu conjunto de dimensionamento com a CLI do Azure:

> [!div class="checklist"]
> * Instalar automaticamente aplicações no seu conjunto de dimensionamento
> * Utilizar a Extensão de Script Personalizado do Azure
> * Atualizar uma aplicação em execução num conjunto de dimensionamento

Prossiga para o próximo tutorial para saber como dimensionar automaticamente o seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Dimensionar automaticamente os seus conjuntos de dimensionamento](tutorial-autoscale-cli.md)
