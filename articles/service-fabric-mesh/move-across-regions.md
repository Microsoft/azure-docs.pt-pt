---
title: Mova uma aplicação de malha de tecido de serviço para outra região
description: Pode mover os recursos de malha de tecido de serviço, implementando uma cópia do seu modelo atual para uma nova região do Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 376808a6d8f61d4dc03d17061323a473d48053a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908166"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Mover uma aplicação de malha de tecido de serviço para outra região do Azure

Este artigo descreve como mover a sua aplicação de malha de tecido de serviço e os seus recursos para uma região azure diferente. Pode transferir os seus recursos para outra região por uma série de razões. Por exemplo, em resposta a interrupções, para ganhar funcionalidades ou serviços disponíveis apenas em regiões específicas, para satisfazer os requisitos de política interna e governação, ou em resposta aos requisitos de planeamento de capacidades.

 [A Malha de Tecido de Serviço não suporta](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) a capacidade de movimentar diretamente recursos através das regiões de Azure. No entanto, pode mover recursos indiretamente, implementando uma cópia do seu modelo atual do Gestor de Recursos Azure para a nova região alvo e, em seguida, redirecionando o tráfego de ingressos e dependências para a recém-criada aplicação de malha de tecido de serviço.

## <a name="prerequisites"></a>Pré-requisitos

* Controlador de ingresso (como [Application Gateway)](https://docs.microsoft.com/azure/application-gateway/)para servir de intermediário para encaminhamento de tráfego entre clientes e a sua aplicação de malha de tecido de serviço
* Disponibilidade de malha de tecido de serviço (pré-visualização) na região-alvo de Azure (`westus`, `eastus`ou `westeurope`)

## <a name="prepare"></a>Preparação

1. Tome uma "imagem instantânea" do estado atual da sua aplicação de malha de tecido de serviço exportando o modelo e parâmetros do Gestor de Recursos Azure da mais recente implementação. Para isso, siga os passos no [modelo de exportação após a implantação](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) utilizando o portal Azure. Também pode utilizar [o Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [O PowerShell Azure](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou o [REST API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate).

2. Se aplicável, [exportar outros recursos no mesmo grupo](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal#export-template-from-a-resource-group) de recursos para reafectação na região-alvo.

3. Reveja (e edite, se necessário) o modelo exportado para garantir que os valores de propriedade existentes são os que pretende utilizar na região alvo. O `location` novo (região de Azure) é um parâmetro que irá fornecer durante a redistribuição.

## <a name="move"></a>Mover

1. Crie um novo grupo de recursos (ou utilize um existente) na região alvo.

2. Com o seu modelo exportado, siga os passos em [Deploy recursos a partir do modelo personalizado](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal#deploy-resources-from-custom-template) usando o portal Azure. Também pode utilizar [o Azure CLI,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli) [O PowerShell Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)ou o [REST API](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-rest).

3. Para obter orientações sobre a movimentação de recursos relacionados, como as contas de [Armazenamento Azure,](../storage/common/storage-account-move.md)consulte a orientação para os serviços individuais listados no âmbito do tema [Moving Azure resources across regions](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verificar

1. Quando a implementação estiver concluída, teste o ponto final da aplicação para verificar a funcionalidade da sua aplicação.

2. Também pode verificar o estado da sua aplicação verificando o estado da aplicação ([az mesh app show](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) e revendo os registos de aplicação e ([az malha code-package-log](https://docs.microsoft.com/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) usando o [Azure Service Fabric Mesh CLI](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-quickstart-deploy-container#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Consolidação

Depois de ter confirmado a funcionalidade equivalente da sua aplicação de malha de tecido de serviço na região alvo, configure o seu controlador de entrada (por exemplo, [Application Gateway)](../application-gateway/redirect-overview.md)para redirecionar o tráfego para a nova aplicação.

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Para completar o movimento da aplicação malha de tecido de serviço, [elimine a aplicação de origem e/ou o grupo de recursos dos pais](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Passos seguintes

* [Mover recursos Azure através de regiões](../azure-resource-manager/management/move-region.md)
* [Apoio à movimentação de recursos azure em regiões](../azure-resource-manager/management/region-move-support.md)
* [Move resources to a new resource group or subscription](../azure-resource-manager/management/move-resource-group-and-subscription.md) (Mover recursos para um grupo de recursos ou uma subscrição nova)
* [Suporte da operação de movimentação para recursos](../azure-resource-manager/management/move-support-resources.md
)
