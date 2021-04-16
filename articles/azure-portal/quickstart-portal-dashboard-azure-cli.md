---
title: Criar um painel de porta-porta Azure com Azure CLI
description: 'Quickstart: Aprenda a criar um dashboard no portal Azure utilizando o Azure CLI. Um dashboard é uma visão focada e organizada dos seus recursos em nuvem.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: d951c692c7d3c282ae68c5f9b53e9cda5407df10
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481026"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Quickstart: Criar um painel de porta-porta Azure com Azure CLI

Um dashboard no portal Azure é uma visão focada e organizada dos seus recursos em nuvem. Este artigo centra-se no processo de utilização do Azure CLI para criar um dashboard.
O painel de instrumentos mostra o desempenho de uma máquina virtual (VM), bem como algumas informações estáticas e ligações.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Se tiver várias subscrições do Azure, escolha a subscrição adequada para faturar os recursos.
Selecione uma subscrição utilizando o comando [conjunto de conta az:](/cli/azure/account#az_account_set)

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Criar um [grupo de recursos Azure](../azure-resource-manager/management/overview.md) utilizando o [grupo az criar](/cli/azure/group#az_group_create) comando ou utilizar um grupo de recursos existente:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual utilizando o comando [az vm create:](/cli/azure/vm#az_vm_create)

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> A senha deve ser complexa.
> Este é um novo nome de utilizador e senha.
> Não é, por exemplo, a conta que usa para assinar no Azure.
> Para mais informações, consulte [os requisitos do nome de utilizador](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) e os [requisitos de senha.](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

A implantação começa e normalmente demora alguns minutos a ser concluída.
Após a colocação concluída, passe para a secção seguinte.

## <a name="download-the-dashboard-template"></a>Descarregue o modelo do painel de instrumentos

Uma vez que os dashboards Azure são recursos, podem ser representados como JSON.
Para mais informações, consulte [a estrutura dos Dashboards Azure.](./azure-portal-dashboards-structure.md)

Descarregue o seguinte ficheiro: [portal-dashboard-template-testvm.jsem](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Personalize o modelo descarregado alterando os seguintes valores para os seus valores:

* `<subscriptionID>`: A sua subscrição
* `<rgName>`: Grupo de recursos, por exemplo `myResourceGroup`
* `<vmName>`: Nome da máquina virtual, por exemplo `SimpleWinVM`
* `<dashboardTitle>`: Título do painel, por exemplo `Simple VM Dashboard`
* `<location>`: A sua região azul, por exemplo, `centralus`

Para obter mais informações, consulte a [referência do modelo do portal do Microsoft.](/azure/templates/microsoft.portal/dashboards)

## <a name="deploy-the-dashboard-template"></a>Implemente o modelo do painel de instrumentos

Agora pode implementar o modelo a partir de Azure CLI.

1. Executar o [painel de instrumentos do portal az criar](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) comando para implementar o modelo:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Verifique se o painel de instrumentos foi criado com sucesso executando o comando [do painel de instrumentos do portal AZ:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show)

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Para ver todos os dashboards para a subscrição atual, utilize [a lista do painel de instrumentos do portal Az](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Também pode ver todos os dashboards para um grupo de recursos:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Pode atualizar um dashboard utilizando o comando de atualização do painel de instrumentos do [portal Az:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update)

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover a máquina virtual e o painel de instrumentos associado, elimine o grupo de recursos que os contém.

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurecli
az group delete --name myResourceGroup
```

Para remover apenas o painel de instrumentos, utilize o comando de eliminação do [painel de instrumentos do portal az:](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete)

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o suporte do Azure CLI para os dashboards, consulte [o painel de instrumentos do portal Az](/cli/azure/ext/portal/portal/dashboard).
