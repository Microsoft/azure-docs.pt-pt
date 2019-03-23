---
title: Início rápido - implementar o contentor do Docker no Azure Container Instances - PowerShell
description: Neste início rápido, vai utilizar do Azure PowerShell para implementar rapidamente uma aplicação web em contentores que é executado numa instância de contentor do Azure isolado
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 8c50a3069ea8b1303e45c571425a6f4c9b4c0d5b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368193"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Início rápido: Implementar uma instância de contentor no Azure com o Azure PowerShell

Utilize o Azure Container Instances para execução sem servidor contentores do Docker no Azure com a simplicidade e celeridade. Implemente uma aplicação para uma contentor instância sob demanda quando não precisar de uma plataforma de orquestração de contentores completa, como o serviço Kubernetes do Azure.

Neste início rápido, utilizar o Azure PowerShell para implementar um contentor isolado do Windows e disponibilizar a sua aplicação com um nome de domínio completamente qualificado (FQDN). Alguns segundos depois de executar um comando de implementação única, pode navegar para o aplicativo em execução no contentor:

![Aplicação implementada com o Azure Container Instances vista no browser][qs-powershell-01]

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se optar por instalar e utilizar o PowerShell localmente, este tutorial requer o módulo Azure PowerShell. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de atualizar, veja [Install Azure PowerShell module (Instalar o módulo do Azure PowerShell)](/powershell/azure/install-Az-ps). Se estiver a executar localmente o PowerShell, também terá de executar o `Connect-AzAccount` para criar uma ligação com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Container Instances, como todos os recursos do Azure, tem de ser implementados num grupo de recursos. Os grupos de recursos permitem organizar e gerir recursos relacionados do Azure.

Em primeiro lugar, crie um grupo de recursos chamado *myResourceGroup* no *eastus* localização com o seguinte [New-AzResourceGroup] [ New-AzResourceGroup] comando:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Criar um contentor

Agora que tem um grupo de recursos, pode executar um contentor no Azure. Para criar uma instância de contentor com o Azure PowerShell, forneça um nome de grupo de recursos, o nome da instância de contentor e a imagem de contentor do Docker para o [New-AzContainerGroup] [ New-AzContainerGroup] cmdlet. Neste início rápido, vai utilizar o público `mcr.microsoft.com/windows/servercore/iis:nanoserver` imagem. Esta imagem de pacotes Microsoft Internet Information Services (IIS) para ser executado no servidor Nano.

Pode expor os seus contentores à Internet, especificando uma ou mais portas a abrir, uma etiqueta de nome DNS ou ambos. Neste início rápido, vai implementar um contentor com uma etiqueta de nome DNS para que o IIS está publicamente acessível.

Execute um comando semelhante ao seguinte para iniciar uma instância de contentor. Definir um `-DnsNameLabel` valor que é exclusivo na região do Azure onde criar a instância. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Alguns segundos depois, deverá receber uma resposta do Azure. O estado do contentor `ProvisioningState` inicialmente é **A criar**, mas deve passar para um **Com êxito** ao fim de um ou dois minutos. Verificar o estado de implementação com o [Get-AzContainerGroup] [ Get-AzContainerGroup] cmdlet:

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o contêiner, removê-lo com o [Remove-AzContainerGroup] [ Remove-AzContainerGroup] cmdlet:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma instância de contentor do Azure a partir de uma imagem no registo do Hub do Docker público. Se deseja criar uma imagem do contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
