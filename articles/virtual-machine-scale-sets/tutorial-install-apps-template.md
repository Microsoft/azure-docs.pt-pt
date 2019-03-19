---
title: Tutorial - Instalar aplicações num conjunto de dimensionamento com modelos do Azure | Microsoft Docs
description: Saiba como utilizar os modelos do Azure Resource Manager para instalar aplicações em conjuntos de dimensionamento de máquinas virtuais com a Extensão de Script Personalizado
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 176cf31d7a87b08755ee2acb94aea23684647213
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996580"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Tutorial: Instalar aplicações em conjuntos de dimensionamento de máquina virtual com um modelo do Azure
Para executar aplicações em instâncias de máquina virtual (VM) num conjunto de dimensionamento, primeiro tem de instalar os componentes da aplicação e os ficheiros necessários. Num tutorial anterior, aprendeu a criar e utilizar uma imagem de VM personalizada para implementar as suas instâncias de VM. Esta imagem personalizada inclui configurações e instalações de aplicações manuais. Pode também automatizar a instalação de aplicações num conjunto de dimensionamento após cada instância de VM ser implementada ou atualizar uma aplicação que já é executada num conjunto de dimensionamento. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Instalar automaticamente aplicações no seu conjunto de dimensionamento
> * Utilizar a Extensão de Script Personalizado do Azure
> * Atualizar uma aplicação em execução num conjunto de dimensionamento

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI localmente, este tutorial requer a execução da versão 2.0.29 ou posterior da CLI do Azure. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).


## <a name="what-is-the-azure-custom-script-extension"></a>O que é a Extensão de Script Personalizado do Azure?
A Extensão de Script Personalizado transfere e executa scripts em VMs do Azure. Esta extensão é útil para a configuração pós-implementação, instalação de software ou qualquer outra tarefa de gestão/configuração. Os scripts podem ser transferidos a partir do armazenamento do Azure ou do GitHub, ou fornecidos para o portal do Azure no runtime da extensão.

A extensão de Script Personalizado é integrada em modelos do Azure Resource Manager e pode também ser utilizada com a CLI do Azure, o Azure PowerShell, o portal do Azure ou a API REST. Para obter mais informações, veja a [Descrição geral da Extensão de Script Personalizado](../virtual-machines/linux/extensions-customscript.md).

Para ver a Extensão de Script Personalizado em ação, crie um conjunto de dimensionamento que instala o servidor Web NGINX e dá como resultado o nome de anfitrião da instância de VM do conjunto de dimensionamento. A seguinte definição da Extensão de Script Personalizado transfere um script de exemplo do GitHub, instala os pacotes necessários e escreve o nome de anfitrião da instância de VM numa página HTML simples.


## <a name="create-custom-script-extension-definition"></a>Criar definição da Extensão de Script Personalizado
Ao definir um conjunto de dimensionamento de máquinas virtuais com um modelo do Azure, o fornecedor de recursos *Microsoft.Compute/virtualMachineScaleSets* pode incluir uma secção sobre extensões. O *extensionsProfile* fornece detalhes sobre o que é aplicado às instâncias de VM num conjunto de dimensionamento. Para utilizar a Extensão de Script Personalizado, tem de especificar um publicador de *Microsoft.Azure.Extensions* e um tipo de *CustomScript*.

A propriedade *fileUris* é utilizada para definir os scripts ou pacotes de instalação de origem. Para iniciar o processo de instalação, os scripts necessários estão definidos em *commandToExecute*. O exemplo seguinte define um script de exemplo do GitHub que instala e configura o servidor Web NGINX:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

Para obter um exemplo completo de um modelo do Azure que implementa um conjunto de dimensionamento e a Extensão de Script Personalizado, veja [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Este modelo de exemplo é utilizado na secção seguinte.


## <a name="create-a-scale-set"></a>Criar um conjunto de dimensionamento
Vamos utilizar o modelo de exemplo para criar um conjunto de dimensionamento e aplicar a Extensão de Script Personalizado. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group). O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [az group deployment create](/cli/azure/group/deployment). Quando lhe for pedido, forneça o seu nome de utilizador e a palavra-passe que são utilizados como as credenciais para cada instância de VM:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

A criação e configuração de todas as VMs e recursos do conjunto de dimensionamento demora alguns minutos.

Cada instância de VM no conjunto de dimensionamento transfere e executa o script do GitHub. Num exemplo mais complexo, podem ser instalados vários ficheiros e componentes de aplicações. Se o conjunto de dimensionamento for aumentado verticalmente, as novas instâncias de VM aplicam automaticamente a mesma definição da Extensão de Script Personalizado e instalam a aplicação necessária.


## <a name="test-your-scale-set"></a>Testar o seu conjunto de dimensionamento
Para ver o seu servidor Web em ação, obtenha o endereço IP público do seu balanceador de carga com [az network public-ip show](/cli/azure/network/public-ip). O exemplo seguinte obtém o endereço IP para *myScaleSetPublicIP*, criado como parte do conjunto de dimensionamento:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Introduza o endereço IP público do balanceador de carga num browser. O balanceador de carga distribui o tráfego para uma das suas instâncias de VM, conforme mostra o exemplo seguinte:

![Página Web básica no Nginx](media/tutorial-install-apps-template/running-nginx.png)

Deixe o seu browser aberto para que possa ver uma versão atualizada no próximo passo.


## <a name="update-app-deployment"></a>Atualizar a implementação da aplicação
Ao longo do ciclo de vida de um conjunto de dimensionamento, poderá precisar de implementar uma versão atualizada da sua aplicação. Com a Extensão de Script Personalizado, pode referenciar um script de implementação atualizado e, em seguida, voltar a aplicar a extensão ao seu conjunto de dimensionamento. Quando o conjunto de dimensionamento foi criado num passo anterior, o *upgradePolicy* foi definido como *automática*. Esta definição permite que as instâncias de VM no conjunto de dimensionamento atualizem e apliquem automaticamente a versão mais recente da sua aplicação.

Para atualizar a definição da Extensão de Script Personalizado, edite o modelo para fazer referência a um novo script de instalação. Deve ser utilizado um novo nome de ficheiro para que a Extensão de Script Personalizado reconheça a alteração. A Extensão de Script Personalizado não examina o conteúdo do script para determinar as alterações. A definição seguinte utiliza um script de instalação atualizado com *_v2* anexado ao respetivo nome:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Aplique a configuração da Extensão de Script Personalizado às instâncias de VM no seu conjunto de dimensionamento novamente, com [az group deployment create](/cli/azure/group/deployment). Este modelo *azuredeployv2.json* é utilizado para aplicar a versão atualizada da aplicação. Na prática, tem de editar o modelo *azuredeploy.json* existente para fazer referência ao script de instalação atualizado, conforme mostrado na secção anterior. Quando lhe for pedido, introduza as mesmas credenciais de nome de utilizador e palavra-passe que utilizou ao criar o conjunto de dimensionamento:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Todas as instâncias de VM no conjunto de dimensionamento são automaticamente atualizadas com a versão mais recente da página Web de exemplo. Para ver a versão atualizada, atualize o site no seu browser:

![Página Web atualizada no Nginx](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o seu conjunto de dimensionamento e recursos adicionais, elimine o grupo de recursos e todos os respetivos recursos com [az group delete](/cli/azure/group). O parâmetro `--no-wait` devolve o controlo à linha de comandos, sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que pretende eliminar os recursos sem uma linha de comandos adicional para fazê-lo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, aprendeu a instalar e atualizar automaticamente aplicações no seu conjunto de dimensionamento com modelos do Azure:

> [!div class="checklist"]
> * Instalar automaticamente aplicações no seu conjunto de dimensionamento
> * Utilizar a Extensão de Script Personalizado do Azure
> * Atualizar uma aplicação em execução num conjunto de dimensionamento

Prossiga para o próximo tutorial para saber como dimensionar automaticamente o seu conjunto de dimensionamento.

> [!div class="nextstepaction"]
> [Dimensionar automaticamente os seus conjuntos de dimensionamento](tutorial-autoscale-template.md)
