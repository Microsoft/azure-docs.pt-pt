---
title: Quickstart - Desdobre o recipiente Docker para a instância do contentor - PowerShell
description: Neste arranque rápido, você usa a Azure PowerShell para implementar rapidamente uma aplicação web contentorizada que funciona em uma instância isolada do recipiente Azure
services: container-instances
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.custom: seodec18, mvc
ms.openlocfilehash: c7002d8a83e58a9089ee3c3840b0397d63e2f198
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89565587"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Quickstart: Implementar uma instância de contentor em Azure utilizando a Azure PowerShell

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente uma aplicação para uma instância de contentor a pedido quando não precisar de uma plataforma completa de orquestração de contentores como o Serviço Azure Kubernetes.

Neste arranque rápido, utiliza-se o Azure PowerShell para implantar um contentor Windows isolado e disponibilizar a sua aplicação com um nome de domínio totalmente qualificado (FQDN). Alguns segundos depois de executar um único comando de implantação, pode navegar para a aplicação em execução no recipiente:

![Aplicação implementada com o Azure Container Instances vista no browser][qs-powershell-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Container Instances, como todos os recursos do Azure, tem de ser implementados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

Em primeiro lugar, crie um grupo de recursos chamado *myResourceGroup* na localização *este* com o seguinte comando [New-AzResourceGroup:][New-AzResourceGroup]

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Criar um contentor

Agora que tem um grupo de recursos, pode executar um contentor no Azure. Para criar uma instância de contentor com a Azure PowerShell, forneça um nome de grupo de recursos, nome de instância de contentor e imagem do recipiente Docker para o cmdlet [New-AzContainerGroup.][New-AzContainerGroup] Neste arranque rápido, usa-se a `mcr.microsoft.com/windows/servercore/iis:nanoserver` imagem pública. Esta imagem embala os Serviços de Informação da Internet (IIS) da Microsoft para funcionar em Nano Server.

Pode expor os seus contentores à Internet, especificando uma ou mais portas a abrir, uma etiqueta de nome DNS ou ambos. Neste arranque rápido, você implanta um recipiente com uma etiqueta de nome DNS para que o IIS seja acessível publicamente.

Execute um comando semelhante ao seguinte para iniciar uma instância de contentor. Desconfiem de um `-DnsNameLabel` valor único dentro da região de Azure, onde se cria o caso. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Alguns segundos depois, deverá receber uma resposta do Azure. O estado do contentor `ProvisioningState` inicialmente é **A criar**, mas deve passar para um **Com êxito** ao fim de um ou dois minutos. Verifique o estado de implantação com o cmdlet [Get-AzContainerGroup:][Get-AzContainerGroup]

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

O estado de aprovisionamento do contentor, o nome de domínio completamente qualificado (FQDN) e o endereço IP aparecem na saída do cmdlet:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

Quando o estado do contentor `ProvisioningState` passar a **Com êxito**, navegue para o `Fqdn` no seu browser. Se lhe for apresentada uma página Web semelhante à seguinte, parabéns! Implementou com êxito uma aplicação em execução num contentor de Docker para Azure.

![IIS implementado com o Azure Container Instances visto no browser][qs-powershell-01]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar o recipiente, retire-o com o cmdlet [Remove-AzContainerGroup:][Remove-AzContainerGroup]

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, criou uma instância de contentor do Azure a partir de uma imagem no registo do Hub do Docker público. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
