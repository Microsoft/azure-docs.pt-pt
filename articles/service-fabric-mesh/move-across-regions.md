---
title: Mover uma aplicação de malha de tecido de serviço para outra região
description: Pode mover os recursos de malha de tecido de serviço implementando uma cópia do seu modelo atual para uma nova região de Azure.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: c842a065f108a924c6bffd70d6c2edbbd31b6dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260149"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Mover uma aplicação de malha de tecido de serviço para outra região do Azure

Este artigo descreve como mover a sua aplicação de Malha de Tecido de Serviço e os seus recursos para uma região Azure diferente. Pode mover os seus recursos para outra região por várias razões. Por exemplo, em resposta a interrupções, para obter funcionalidades ou serviços disponíveis apenas em regiões específicas, para satisfazer os requisitos de política interna e de governação, ou em resposta aos requisitos de planeamento de capacidades.

 [A Malha de Tecido de Serviço não suporta](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) a capacidade de movimentar diretamente recursos através das regiões de Azure. No entanto, pode mover os recursos indiretamente, implantando uma cópia do seu atual modelo de Gestor de Recursos Azure para a nova região-alvo e, em seguida, redirecionando o tráfego e dependências de entrada para a aplicação de malha de tecido de serviço recentemente criada.

## <a name="prerequisites"></a>Pré-requisitos

* Controlador ingress (como [o Application Gateway](../application-gateway/index.yml)) para servir de intermediário para encaminhar o tráfego entre clientes e a sua aplicação de Malha de Tecido de Serviço
* Disponibilidade de malha de tecido de serviço (pré-visualização) na região de Azure alvo `westus` `eastus` (, ou `westeurope` )

## <a name="prepare"></a>Preparação

1. Faça uma "snapshot" do estado atual da sua aplicação de malha de tecido de serviço exportando o modelo e parâmetros do Gestor de Recursos Azure da mais recente implementação. Para isso, siga os passos no [modelo de exportação após a implementação](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) utilizando o portal Azure. Também pode utilizar [Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell,](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)ou [REST API](/rest/api/resources/resourcegroups/exporttemplate).

2. Se for caso disso, [exporte outros recursos no mesmo grupo de recursos](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) para a reafectação na região-alvo.

3. Reveja (e edite, se necessário) o modelo exportado para garantir que os valores de propriedade existentes são os que pretende utilizar na região alvo. A nova `location` (região de Azure) é um parâmetro que irá fornecer durante a reafectação.

## <a name="move"></a>Mover

1. Criar um novo grupo de recursos (ou utilizar um existente) na região alvo.

2. Com o seu modelo exportado, siga os passos em [implementar recursos a partir do modelo personalizado](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) usando o portal Azure. Também pode utilizar [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell,](../azure-resource-manager/templates/deploy-powershell.md)ou [REST API](../azure-resource-manager/templates/deploy-rest.md).

3. Para obter orientações sobre recursos relacionados em movimento, tais como [contas de Armazenamento Azure,](../storage/common/storage-account-move.md)consulte orientações para serviços individuais listados no âmbito do tema [Moving Azure resources across regions](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verificação

1. Quando a implementação estiver concluída, teste o ponto final da aplicação para verificar a funcionalidade da sua aplicação.

2. Também pode verificar o estado da sua aplicação verificando o estado da aplicação[(az mesh app show](/cli/azure/ext/mesh/mesh/app?view=azure-cli-latest#ext-mesh-az-mesh-app-show)) e revendo os registos de aplicações e[(az mesh code-package-log](/cli/azure/ext/mesh/mesh/code-package-log?view=azure-cli-latest)) usando o CLI de malha de tecido de [serviço Azure](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Consolidação

Depois de ter confirmado a funcionalidade equivalente da sua aplicação de Malha de Tecido de Serviço na região alvo, configuure o seu controlador de entrada (por exemplo, [Application Gateway](../application-gateway/redirect-overview.md)) para redirecionar o tráfego para a nova aplicação.

## <a name="clean-up-source-resources"></a>Limpar recursos de origem

Para completar a mudança da aplicação Rede de Malha de Tecido de Serviço, [elimine a aplicação de origem e/ou o grupo de recursos dos pais.](../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Passos seguintes

* [Mova recursos do Azure entre regiões](../azure-resource-manager/management/move-region.md)
* [Apoio à deslocação de recursos do Azure pelas regiões](../azure-resource-manager/management/region-move-support.md)
* [Mover recursos para um novo grupo de recursos ou subscrição](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Suporte da operação de movimentação para recursos](../azure-resource-manager/management/move-support-resources.md
)
