---
title: Unidades de Exemplo BareMetal em Azure
description: Saiba como identificar e interagir com unidades de Exemplo BareMetal através do portal Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: shortpatti
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/18/2020
ms.author: v-patsho
ms.openlocfilehash: 8d94b54d909b6d0528150e6a678dd6e819ea78cd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97675981"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Gerir casos BareMetal através do portal Azure
 
Este artigo mostra como o [portal Azure](https://portal.azure.com/) exibe Instâncias BareMetal. Este artigo também mostra as atividades que pode fazer no portal Azure com as suas unidades de Exemplo BareMetal implantadas. 
 
## <a name="register-the-resource-provider"></a>Registar o fornecedor de recursos
Um fornecedor de recursos Azure para a BareMetal Instances fornece visibilidade das instâncias no portal Azure, atualmente em pré-visualização pública. Por predefinição, a subscrição Azure que utiliza para implementações bareMetal Instance regista o fornecedor de recursos *BareMetalInfrastructure.* Se não vir as suas unidades Desnudas Desmedidas, tem de registar o fornecedor de recursos com a sua subscrição. Existem duas formas de registar o fornecedor de recursos BareMetal Instance:
 
* [CLI do Azure](#azure-cli)
 
* [Portal do Azure](#azure-portal)
 
### <a name="azure-cli"></a>CLI do Azure
 
Inscreva-se na subscrição Azure que utiliza para a implementação bareMetal Instance através do Azure CLI. Pode registar o fornecedor de recursos de estruturação BareMetalInfra com:

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
Para obter mais informações, consulte o artigo [Fornecedores e tipos de recursos Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).
 
### <a name="azure-portal"></a>Portal do Azure
 
Pode registar o fornecedor de recursos de estrutura bareMetalInfra através do portal Azure.
 
Terá de listar a sua subscrição no portal Azure e, em seguida, clicar duas vezes na subscrição utilizada para implementar as suas unidades BareMetal Instance.
 
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **de todos os serviços,** **introduza a subscrição** e, em seguida, selecione **Subscrições**.

1. Selecione a subscrição da lista de subscrição para visualizar.

1. Selecione **fornecedores de recursos** e **introduza a estrutura BareMetalInfra** na pesquisa. O fornecedor de recursos deve ser **registado,** como mostra a imagem.
 
>[!NOTE]
>Se o fornecedor de recursos não estiver registado, **selecione Registar-se**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Screenshot que mostra a unidade BareMetal Instance registada":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Unidades de Exemplo BareMetal no portal Azure
 
Quando submeter um pedido de implementação bareMetal Instance, irá especificar a subscrição Azure que está a ligar às Instâncias BareMetal. Utilize a mesma subscrição que utiliza para implantar a camada de aplicação que funciona contra as unidades BareMetal Instance.
 
Durante a implementação do seu BareMetal Instances, um novo [grupo de recursos Azure](../../../azure-resource-manager/management/manage-resources-portal.md) é criado na subscrição Azure que usou no pedido de implementação. Este novo grupo de recursos lista todas as suas unidades Desmedidas que implementou na subscrição específica.

1. Na subscrição BareMetal, no portal Azure, selecione **Grupos de Recursos**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Screenshot que mostra a lista de Grupos de Recursos":::

1. Na lista, localize o novo grupo de recursos.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Screenshot que mostra a unidade BareMetal Instance numa lista de grupos de recursos filtrados" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >Pode filtrar a subscrição utilizada para implantar o Caso BareMetal. Depois de filtrar para a subscrição adequada, poderá ter uma longa lista de grupos de recursos. Procure um com uma pós-correcção de **-Txxx** onde xxx é três dígitos como **-T250**.

1. Selecione o novo grupo de recursos para mostrar os detalhes do mesmo. A imagem mostra uma unidade de Instância BareMetal implantada.
   
   >[!NOTE]
   >Se você implantasse vários inquilinos da BareMetal Instance sob a mesma assinatura Azure, você veria vários grupos de recursos Azure.
 
## <a name="view-the-attributes-of-a-single-instance"></a>Ver os atributos de uma única instância
 
Pode ver os detalhes de uma única unidade. Na lista do caso BareMetal, selecione o único caso que pretende visualizar.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Screenshot que mostra os atributos da unidade BareMetal Instance de uma única instância" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
Os atributos na imagem não parecem muito diferentes dos atributos da máquina virtual Azure (VM). À esquerda, você verá o grupo de Recursos, região de Azure, e nome de assinatura e ID. Se designou etiquetas, vai vê-las aqui também. Por predefinição, as unidades de Instância BareMetal não têm etiquetas atribuídas.
 
À direita, verá o nome da unidade, sistema operativo (OS), endereço IP e SKU que mostra o número de fios e memória do CPU. Você também verá a versão power state e hardware (revisão do carimbo BareMetal Instance). O estado de alimentação indica se a unidade de hardware está ligado ou desligado. Os detalhes do sistema operativo, no entanto, não indicam se está em funcionamento.
 
As possíveis revisões de hardware são:
 
* Revisão 3
 
* Revisão 4
 
* Revisão 4.2
 
>[!NOTE]
>A Revisão 4.2 é a mais recente infraestrutura baremetal remarcada utilizando a arquitetura Revisão 4. Tem melhorias significativas na latência da rede entre as unidades de instância Azure VMs e BareMetal implantadas na Revisão de 4 selos ou linhas.
 
Além disso, no lado direito, encontrará o nome do Grupo de [Colocação de Proximidade Azure,](../../../virtual-machines/linux/co-location.md) que é criado automaticamente para cada unidade de Instância BareMetal implantada. Faça referência ao Grupo de Colocação de Proximidade quando implementar os VMs Azure que acolhem a camada de aplicação. Quando utilizar o Grupo de Colocação de Proximidade associado à unidade BareMetal Instance, certifique-se de que os VMs Azure são implantados perto da unidade BareMetal Instance.
 
>[!TIP]
>Para localizar a camada de aplicação no mesmo datacenter Azure que a Revisão 4.x, consulte [os grupos de colocação de proximidade do Azure para uma latência ótima da rede](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).
 
## <a name="check-activities-of-a-single-instance"></a>Verificar atividades de uma única instância
 
Pode verificar as atividades de uma única unidade. Uma das principais atividades registadas são o recomeço da unidade. Os dados listados incluem o estado da atividade, o timetamp da atividade desencadeada, o ID de subscrição e o utilizador Azure que desencadeou a atividade.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Screenshot que mostra as atividades da unidade BareMetal Instance" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
As alterações aos metadados da unidade no Azure também são registadas no registo de Atividade. Além do recomeço iniciado, pode ver a atividade de **Write BareMetallnstances**. Esta atividade não altera a própria unidade BareMetal Instance, mas documenta as alterações aos metadados da unidade em Azure.
 
Outra atividade que é gravada é quando adiciona ou apaga uma [etiqueta](../../../azure-resource-manager/management/tag-resources.md) a um caso.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Adicione e elimine uma etiqueta Azure a um caso
 
Pode adicionar tags Azure a uma unidade Desnucação de Instância ou eliminá-las. A forma como as etiquetas são atribuídas não difere da atribuição de etiquetas a VMs. Tal como acontece com os VMs, as etiquetas existem nos metadados Azure, e para as Instâncias BareMetal, têm as mesmas restrições que as etiquetas para VMs.
 
Eliminar etiquetas funciona da mesma forma que com os VMs. A aplicação e eliminação de uma etiqueta estão listadas no registo de atividade da unidade BareMetal Instance.
 
## <a name="check-properties-of-an-instance"></a>Verificar propriedades de um caso
 
Quando adquirir as ocorrências, pode ir à secção Propriedades para ver os dados recolhidos sobre as ocorrências. Os dados recolhidos incluem a conectividade Azure, backend de armazenamento, ID do circuito ExpressRoute, ID de recurso único e o ID de subscrição. Utilizará esta informação em pedidos de suporte ou ao configurar a configuração do instantâneo de armazenamento.
 
Outra informação crítica que verá é o endereço IP de armazenamento NFS. Isola o seu armazenamento para o seu **inquilino** na pilha De Exemplo BareMetal. Utilizará este endereço IP quando editar o [ficheiro de configuração para cópias de segurança instantâneas](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)de armazenamento .
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Screenshot que mostra as definições de propriedade BareMetal Instance" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Reiniciar uma unidade através do portal Azure
 
Há várias situações em que o SO não termina um reinício, o que requer um reinício de energia da unidade BareMetal Instance. Pode reiniciar a unidade diretamente a partir do portal Azure:
 
Selecione **Reiniciar** e, em seguida, **Sim** para confirmar o reinício da unidade.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Screenshot que mostra como reiniciar a unidade BareMetal Instance":::
 
Quando reiniciar uma unidade Desmômeal Instance, sentirá um atraso. Durante este atraso, o estado de energia passa de **Iniciar** para **Começar**, o que significa que o SO começou completamente. Como resultado, após um reinício, não pode entrar na unidade assim que o estado mudar para **Iniciar**.
 
>[!IMPORTANT]
>Dependendo da quantidade de memória na sua unidade BareMetal Instance, um reinício e um reinício do hardware e do sistema operativo podem demorar até uma hora.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Abrir um pedido de apoio para casos BareMetal
 
Pode submeter pedidos de apoio especificamente para uma unidade Desmesudo Caso.
1. No portal Azure, no âmbito **da Ajuda + Suporte,** crie um **[novo pedido de suporte](https://rc.portal.azure.com/#create/Microsoft.Support)** e forneça as seguintes informações para o bilhete:
 
   - **Tipo de emissão:** Selecione um tipo de problema
 
   - **Assinatura:** Selecione a sua subscrição
 
   - **Serviço:** Infraestrutura BareMetal
 
   - **Recurso:** Fornecer o nome da instância
 
   - **Resumo:** Fornecer um resumo do seu pedido
 
   - **Tipo de problema:** Selecione um tipo de problema
 
   - **Subtipo de problema:** Selecione um subtipo para o problema

1. Selecione o separador **Soluções** para encontrar uma solução para o seu problema. Se não consegue encontrar uma solução, vá para o próximo passo.

1. Selecione o separador **Detalhes** e selecione se o problema está com VMs ou as unidades BareMetal Instance. Esta informação ajuda a direcionar o pedido de apoio aos especialistas corretos.

1. Indicar quando o problema começou e selecione a região de instância.

1. Forneça mais detalhes sobre o pedido e carrene um ficheiro se necessário.

1. Selecione **Review + Criar** para submeter o pedido.
 
Leva até cinco dias úteis para um representante de apoio confirmar o seu pedido.

## <a name="next-steps"></a>Passos seguintes

Se quiser saber mais sobre o BareMetal, consulte [os tipos de carga de trabalho BareMetal](../../../virtual-machines/workloads/sap/get-started.md).