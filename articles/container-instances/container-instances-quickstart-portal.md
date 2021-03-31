---
title: Quickstart - Desdobre o recipiente Docker para a instância do contentor - Portal
description: Neste arranque rápido, você usa o portal Azure para implementar rapidamente uma aplicação web contentorizada que funciona em uma instância isolada do recipiente Azure
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004811"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Quickstart: Implementar uma instância de contentor em Azure utilizando o portal Azure

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente uma aplicação para uma instância de contentor a pedido quando não precisar de uma plataforma completa de orquestração de contentores como o Serviço Azure Kubernetes.

Neste arranque rápido, utiliza-se o portal Azure para implantar um recipiente estivador isolado e disponibilizar a sua aplicação com um nome de domínio totalmente qualificado (FQDN). Depois de configurar algumas definições e implementar o contentor, pode navegar para a aplicação em execução:

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Aplicação implementada com o Azure Container Instances vista no browser":::

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contentor

Selecione as instâncias **de**  >  **recipientes de recursos** para criar um  >  **recurso.**

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Começar a criar uma nova instância do contentor no portal do Azure":::

Na página **Basics, insira** os seguintes valores nas caixas de texto de imagem do **grupo Recursos,** **nome do recipiente** e caixa de texto de imagem do **Contentor.** Deixe os outros valores nas predefinições e, em seguida, selecione **OK**.

* Grupo de recursos: **Criar novo** > `myresourcegroup`
* Nome do contentor: `mycontainer`
* Fonte de imagem: **Imagens quickstart**
* Imagem de recipiente: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Configurar definições básicas para uma nova instância do contentor no portal do Azure":::

Para este arranque rápido, utilize as definições predefinições para implementar a imagem pública da `aci-helloworld` Microsoft. Esta amostra de imagem Linux embala uma pequena aplicação web escrita em Node.js que serve uma página html estática. Também pode trazer as suas próprias imagens de contentores armazenadas no Registo de Contentores Azure, NoCker Hub ou noutros registos.

Na página **de Networking,** especifique uma **etiqueta de nome DNS** para o seu recipiente. O nome deve ser único na região de Azure onde se cria a instância do contentor. O contentor estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Configurar definições de rede para uma nova instância de contentores no portal Azure":::

Deixe as outras definições à sua predefinição e, em seguida, selecione **Review + create**.

Quando a validação estiver concluída, é apresentado um resumo das definições de contentor. Selecione **Criar** para submeter o seu pedido de implantação do contentor.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Resumo das definições para uma nova instância do contentor no portal do Azure":::

Quando a implementação começa, uma notificação parece indicar que a implantação está em andamento. É apresentada outra notificação quando o grupo de contentores tiver sido implementado.

Abra a visão geral para o grupo de contentores navegando para **os grupos de recursos**  >  **myresourcegroup**  >  **mycontainer**. Tome nota do **FQDN** (nome de domínio completamente qualificado) da instância do contentor, bem como do **Estado**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Descrição geral do grupo de contentores no portal do Azure":::

Quando o **Estado** for *Em execução*, navegue para o FQDN do contentor no seu browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Aplicação implementada com o Azure Container Instances vista no browser":::

Parabéns! Ao configurar apenas algumas definições, implementou uma aplicação acessível publicamente no Azure Container Instances.

## <a name="view-container-logs"></a>Ver registos de contentor

Ver os registos de uma instância de contentor é útil quando estiver a resolver problemas no contentor ou na aplicação nele executada.

Para visualizar os registos do contentor, em **Definições**, selecione **Contentores,** em seguida, **Regista registos**. Deverá ver o pedido HTTP GET gerado quando visualizou a aplicação no seu browser.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Registos de contentor no portal do Azure":::


## <a name="clean-up-resources"></a>Limpar os recursos

Quando tiver terminado com o contentor, selecione **Descrição Geral** para a instância de contentor *mycontainer* e, em seguida, selecione **Eliminar**.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Apagar a instância do contentor no portal Azure]":::

Selecione **Sim** quando caixa de diálogo de confirmação for apresentada.

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Eliminar a confirmação de uma instância de contentor no portal Azure]":::

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma instância de contentores Azure a partir de uma imagem pública da Microsoft. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/