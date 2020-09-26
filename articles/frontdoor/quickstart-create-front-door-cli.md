---
title: 'Quickstart: Configurar alta disponibilidade com Azure Front Door - Azure CLI'
description: Este quickstart irá mostrar-lhe como usar a Porta Frontal Azure para criar uma aplicação web global de alta disponibilidade e alto desempenho usando O Azure CLI.
services: front-door
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: 6aa960837a3bfc7f8a04ca1f554fb10d635c2ea2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91349142"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Quickstart: Criar uma porta frontal para uma aplicação web global altamente disponível usando O Azure CLI

Começa com a Azure Front Door usando o Azure CLI para criar uma aplicação web global altamente disponível e de alto desempenho.

A Porta da Frente direciona o tráfego web para recursos específicos numa piscina de backend. Definiu o domínio frontend, adicionou recursos a uma piscina de backend e criou uma regra de encaminhamento. Este artigo utiliza uma configuração simples de um pool de backend com dois recursos de aplicações web e uma regra de encaminhamento único usando o caminho padrão correspondente "/*".

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure CLI instalado localmente ou Azure Cloud Shell
- Certifique-se de que a extensão da porta da frente é adicionada ao seu Azure CLI

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0.28 ou posterior do Azure CLI. Para localizar a versão, execute `az --version`. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Em Azure, aloca recursos relacionados a um grupo de recursos. Pode utilizar um grupo de recursos existente ou criar um novo.

Para este arranque rápido, precisa de dois grupos de recursos. Um nos *EUA centrais* e o segundo no *Centro-Sul dos EUA.*

Criar um grupo de recursos com [a criação de grupo az:](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create&preserve-view=true)

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Criar duas instâncias de uma aplicação web

São necessários dois casos de uma aplicação web que funciona em diferentes regiões do Azure para este arranque rápido. Ambas as instâncias de aplicação web são executadas no modo Ative/Ative, para que qualquer um possa servir o tráfego.

Se ainda não tiver uma aplicação web, use o seguinte script para configurar duas aplicações web de exemplo.

### <a name="create-app-service-plans"></a>Criar planos de serviço de aplicações

Antes de poder criar as aplicações web, necessitará de dois planos de serviço de aplicações, um no *Centro-Americano* e o segundo no *Centro-Americano.*

Crie planos de serviço de aplicações com [plano de appservice az criar:](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az_appservice_plan_create&preserve-view=true)

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
-resource-groupg myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Criar aplicativos web

Executar os seguintes comandos criará uma aplicação web em cada um dos planos de serviço de aplicações no passo anterior. Os nomes das aplicações web têm de ser globalmente únicos.

Crie aplicativo web com [a criação de webapp az:](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az_webapp_create&preserve-view=true)

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Tome nota do nome de anfitrião predefinido de cada aplicação web para que possa definir os endereços de backend quando implementar a Porta frontal no passo seguinte.

## <a name="create-the-front-door"></a>Criar a Porta da Frente

Crie uma porta frontal básica com definições de equilíbrio de carga predefinidos, sonda de saúde e regras de encaminhamento, correndo para seguir:

Criar porta frontal com [ação de rede porta frontal criar:](https://docs.microsoft.com/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext_front_door_az_network_front_door_create&preserve-view=true)

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--grupo de recursos:** Especifique um grupo de recursos onde pretende implantar a Porta frontal.

**--nome:** Especifique um nome globalmente único para a sua Porta Frontal Azure. 

**-protocolos aceites:** Os valores aceites são **http** e **https**. Se quiser usar ambos, específico separado por um espaço.

**--endereço de backend:** Defina o nome do anfitrião de ambas as aplicações web aqui separados por um espaço.

Uma vez concluída a implementação com sucesso, tome nota do nome do anfitrião na secção *frontEndpoints.*

## <a name="test-the-front-door"></a>Teste a porta da frente

Abra um navegador web e introduza o nome anfitrião obtido a partir dos comandos. A Porta da Frente irá direcionar o seu pedido para um dos recursos de backend.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Página de teste da porta da frente":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com a Porta da Frente, elimine ambos os grupos de recursos. Quando elimina o grupo de recursos, também elimina a Porta frontal e todos os seus recursos relacionados. 

Para eliminar o grupo de recursos utilize [o grupo Az eliminar:](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az_group_delete&preserve-view=true)

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um:
* Front Door
* Duas aplicações web

Para aprender a adicionar um domínio personalizado à sua Porta da Frente, continue para os tutoriais da Porta da Frente.

> [!div class="nextstepaction"]
> [Adicionar um domínio personalizado](front-door-custom-domain.md)
