---
title: Ligar instâncias de infraestrutura BareMetal em Azure
description: Saiba como identificar e interagir com casos BareMetal no portal Azure ou no Azure CLI.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871656"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Ligar instâncias de infraestrutura BareMetal em Azure

Este artigo mostra como o [portal Azure](https://portal.azure.com/) exibe [instâncias BareMetal](concepts-baremetal-infrastructure-overview.md). Este artigo também mostra o que pode fazer no portal Azure com as suas instâncias de Infraestruturas BareMetal implementadas. 
 
## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos
Um fornecedor de recursos Azure para instâncias BareMetal fornece visibilidade dos casos no portal Azure. Por predefinição, a subscrição Azure que utiliza para implementações de instâncias BareMetal regista o fornecedor de recursos *BareMetalInfrastructure.* Se não vir as suas instâncias BareMetal implementadas, tem de registar o fornecedor de recursos com a sua subscrição. 

Pode registar o fornecedor de recursos de instância BareMetal utilizando o portal Azure ou O Azure CLI.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Terá de listar a sua subscrição no portal Azure e, em seguida, clicar duas vezes na subscrição utilizada para implementar as suas instâncias BareMetal.
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **de todos os serviços,** **introduza a subscrição** e, em seguida, selecione **Subscrições**.

1. Selecione a subscrição da lista de subscrição.

1. Selecione **fornecedores de recursos** e **introduza a estrutura BareMetalInfra** na pesquisa. O fornecedor de recursos deve ser **registado,** como mostra a imagem.
 
>[!NOTE]
>Se o fornecedor de recursos não estiver registado, **selecione Registar-se**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Screenshot mostrando as instâncias BareMetal registadas.":::

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar O Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Inscreva-se na subscrição Azure que utiliza para a implementação de instância BareMetal através do CLI Azure. Registe o `BareMetalInfrastructure` fornecedor de recursos com o comando de registo do fornecedor [az:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Pode utilizar o comando [da lista de fornecedores az](/cli/azure/provider#az_provider_list) para ver todos os fornecedores disponíveis.

---

Para obter mais informações sobre os fornecedores de recursos, consulte [os fornecedores e tipos de recursos Azure](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>Casos BareMetal no portal Azure
 
Quando submeter um pedido de implementação de instância BareMetal, irá especificar a subscrição Azure que está a ligar às instâncias BareMetal. Utilize a mesma subscrição que utiliza para implantar a camada de aplicação que funciona contra as instâncias BareMetal.
 
Durante a implementação dos seus casos BareMetal, um novo [grupo de recursos Azure](../azure-resource-manager/management/manage-resources-portal.md) é criado na subscrição Azure que usou no pedido de implementação. Este novo grupo de recursos lista todos os casos BareMetal que implementou nessa subscrição.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Na subscrição BareMetal, no portal Azure, selecione **Grupos de Recursos**.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Screenshot mostrando a lista de grupos de recursos.":::

1. Na lista, localize o novo grupo de recursos.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Screenshot mostrando a instância BareMetal numa lista de grupos de recursos filtrados." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Pode filtrar a subscrição utilizada para implantar a instância BareMetal. Depois de filtrar para a subscrição adequada, poderá ter uma longa lista de grupos de recursos. Procure um com uma pós-correcção de **-Txxx** onde xxx é três dígitos como **-T250**.

1. Selecione o novo grupo de recursos para ver os seus detalhes. A imagem mostra um exemplo BareMetal implantado.
   
   >[!NOTE]
   >Se você implantou vários inquilinos de instância BareMetal sob a mesma assinatura Azure, você verá vários grupos de recursos Azure.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para ver todas as suas instâncias BareMetal, execute o comando [da lista de baremetalinsance az](/cli/azure/baremetalinstance#az_baremetalinstance_list) para o seu grupo de recursos:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> O `--output` parâmetro é um parâmetro global, disponível para todos os comandos. O valor **da tabela** apresenta a saída num formato amigável. Para obter mais informações, consulte [os formatos de saída para comandos Azure CLI](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Ver os atributos de uma única instância

Pode ver os detalhes de uma única instância.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Na lista de casos BareMetal, selecione o único caso que pretende visualizar.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Screenshot mostrando os atributos de exemplo BareMetal de uma única instância." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Os atributos na imagem não parecem muito diferentes dos atributos da máquina virtual Azure (VM). À esquerda, você verá o grupo de Recursos, região de Azure, e nome de assinatura e ID. Se designou etiquetas, vai vê-las aqui também. Por padrão, as instâncias BareMetal não têm etiquetas atribuídas.
 
À direita, verá o nome da instância BareMetal, sistema operativo (OS), endereço IP e SKU que mostra o número de fios e memória do CPU. Você também verá a versão power state e hardware (revisão do carimbo de instância BareMetal). O estado de alimentação indica se a unidade de hardware está ligado ou desligado. Os detalhes do sistema operativo, no entanto, não indicam se está em funcionamento.
 
As possíveis revisões de hardware são:

* Revisão 3 (Rev 3)

* Revisão 4 (Rev 4)
 
* Revisão 4.2 (Rev. 4.2)
 
>[!NOTE]
>Rev 4.2 é a mais recente infraestrutura baremetal rebranded usando a arquitetura rev 4 existente. O Rev 4 proporciona uma maior proximidade com os anfitriões da máquina virtual Azure (VM). Tem melhorias significativas na latência da rede entre os exemplos de Azure VMs e SAP HANA. Pode aceder e gerir as suas instâncias BareMetal através do portal Azure. Para mais informações, consulte [a Infraestruturas BareMetal em Azure.](concepts-baremetal-infrastructure-overview.md)

 
Além disso, no lado direito, você encontrará o nome [do grupo de colocação de proximidade Azure,](../virtual-machines/co-location.md) que é criado automaticamente para cada instância BareMetal implementada. Faça referência ao grupo de colocação de proximidade quando implementar os VMs Azure que acolhem a camada de aplicação. Quando utiliza o grupo de colocação de proximidade associado à instância BareMetal, certifique-se de que os VMs Azure são implantados perto da instância BareMetal.
 
>[!TIP]
>Para localizar a camada de aplicação no mesmo datacenter Azure que a Revisão 4.x, consulte [os grupos de colocação de proximidade do Azure para uma latência ótima da rede](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para ver detalhes de um caso BareMetal, executar o comando [do show de baremetalinstance az:](/cli/azure/baremetalinstance#az_baremetalinstance_show)

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Se não tem a certeza do nome da instância, faça o `az baremetalinstance list` comando, descrito acima.

---
 
## <a name="check-activities-of-a-single-instance"></a>Verificar atividades de uma única instância
 
Pode verificar as atividades de um único caso BareMetal. Uma das principais atividades registadas são o recomeço do caso. Os dados listados incluem o estado da atividade, o timetamp da atividade desencadeada, o ID de subscrição e o utilizador Azure que desencadeou a atividade.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Screenshot mostrando as atividades do caso BareMetal." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
As alterações aos metadados da instância no Azure também são registadas no registo de Atividade. Além do recomeço iniciado, pode ver a atividade de **Write BareMetallnstances**. Esta atividade não altera a própria instância BareMetal, mas documenta as alterações aos metadados da unidade em Azure.
 
Outra atividade que é gravada é quando adiciona ou apaga uma [etiqueta](../azure-resource-manager/management/tag-resources.md) a um caso.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Adicione e elimine uma etiqueta Azure a um caso

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Pode adicionar tags Azure a uma instância BareMetal ou eliminá-las. As etiquetas são atribuídas tal como fazem ao atribuir etiquetas a VMs. Tal como acontece com os VMs, as etiquetas existem nos metadados Azure. As etiquetas têm as mesmas restrições para os casos BareMetal do que para os VMs.
 
A eliminação de etiquetas também funciona da mesma forma que para os VMs. A aplicação e eliminação de uma etiqueta está listada no registo de atividade da instância BareMetal.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A atribuição de tags a casos BareMetal funciona da mesma forma que a atribuição de tags para máquinas virtuais. Tal como acontece com os VMs, as etiquetas existem nos metadados Azure. As etiquetas têm as mesmas restrições para os casos BareMetal do que para os VMs.

Para adicionar tags a uma instância BareMetal, executar o comando [de atualização de az baremetalinstance:](/cli/azure/baremetalinstance#az_baremetalinstance_update)

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Utilize o mesmo comando para remover uma etiqueta:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Verificar propriedades de um caso
 
Quando adquirir as ocorrências, pode ir à secção Propriedades para ver os dados recolhidos sobre as ocorrências. Os dados recolhidos incluem a conectividade Azure, backend de armazenamento, ID do circuito ExpressRoute, ID de recurso único e o ID de subscrição. Utilizará esta informação em pedidos de suporte ou ao configurar a configuração do instantâneo de armazenamento.
 
Outra informação crítica que verá é o endereço IP de armazenamento NFS. Isola o seu armazenamento para o seu **inquilino** na pilha de casos BareMetal. Utilizará este endereço IP quando editar o [ficheiro de configuração para cópias de segurança instantâneas](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)de armazenamento .
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Screenshot mostrando as definições de propriedade de exemplo BareMetal." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Reinicie um exemplo BareMetal através do portal Azure

Há várias situações em que o SO não termina um recomeço, o que requer um reinício de energia da instância BareMetal.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Pode reiniciar a potência do caso diretamente a partir do portal Azure:
 
Selecione **Reiniciar** e, em seguida, **Sim** para confirmar o reinício.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Screenshot mostrando como reiniciar a instância BareMetal.":::
 
Quando reiniciares uma instância BareMetal, vais sentir um atraso. Durante este atraso, o estado de energia passa de **Iniciar** para **Começar**, o que significa que o SO começou completamente. Como resultado, após um reinício, só pode entrar na unidade uma vez que o estado mude para **Iniciado**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para reiniciar uma instância BareMetal, utilize o comando de reinício da [baremetalinização az:](/cli/azure/baremetalinstance#az_baremetalinstance_restart)

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Dependendo da quantidade de memória no seu caso BareMetal, um reinício e um reinício do hardware e do sistema operativo podem demorar até uma hora.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Abrir um pedido de apoio para instâncias BareMetal
 
Pode submeter pedidos de apoio especificamente para casos BareMetal.
1. No portal Azure, no âmbito **da Ajuda + Suporte,** crie um **[novo pedido de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o bilhete:
 
   - **Tipo de emissão:** Selecione um tipo de problema.
 
   - **Assinatura:** Selecione a sua subscrição.
 
   - **Serviço:** Infraestrutura BareMetal
 
   - **Recurso:** Fornecer o nome do caso.
 
   - **Resumo:** Forneça um resumo do seu pedido.
 
   - **Tipo de problema:** Selecione um tipo de problema.
 
   - **Subtipo de problema:** Selecione um subtipo para o problema.

1. Selecione o separador **Soluções** para encontrar uma solução para o seu problema. Se não consegue encontrar uma solução, vá para o próximo passo.

1. Selecione o separador **Detalhes** e selecione se o problema está com VMs ou instâncias BareMetal. Esta informação ajuda a direcionar o pedido de apoio aos especialistas corretos.

1. Indicar quando o problema começou e selecione a região de instância.

1. Forneça mais detalhes sobre o pedido e carrene um ficheiro se necessário.

1. Selecione **Review + Criar** para submeter o pedido.
 
Leva até cinco dias úteis para um representante de apoio confirmar o seu pedido.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre cargas de trabalho:

- [O que é SAP HANA em Azure (Grandes Instâncias)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
