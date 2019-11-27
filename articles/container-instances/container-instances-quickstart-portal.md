---
title: Guia de início rápido – implantar o contêiner do Docker na instância do contêiner-Portal
description: Neste guia de início rápido, você usa o portal do Azure para implantar rapidamente um aplicativo Web em contêineres que é executado em uma instância de contêiner do Azure isolada
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e0c5ba57c7664a64c1b11bed215f419f31630d39
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533533"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Início rápido: implantar uma instância de contêiner no Azure usando o portal do Azure

Use as instâncias de contêiner do Azure para executar contêineres do Docker sem servidor no Azure com simplicidade e velocidade. Implante um aplicativo em uma instância de contêiner sob demanda quando você não precisar de uma plataforma de orquestração de contêiner completa, como o serviço kubernetes do Azure.

Neste guia de início rápido, você usa o portal do Azure para implantar um contêiner do Docker isolado e tornar seu aplicativo disponível com um FQDN (nome de domínio totalmente qualificado). Depois de configurar algumas definições e implementar o contentor, pode navegar para a aplicação em execução:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contentor

Selecione **Criar um recurso** > **Contentores** >  **Container Instances**.

![Começar a criar uma nova instância do contentor no portal do Azure][aci-portal-01]

Na página **noções básicas** , insira os valores a seguir nas caixas de texto **grupo de recursos**, **nome do contêiner**e imagem de **contêiner** . Deixe os outros valores nas predefinições e, em seguida, selecione **OK**.

* Grupo de recursos: **Criar novo** > `myresourcegroup`
* Nome do contentor: `mycontainer`
* Imagem de contentor: `mcr.microsoft.com/azuredocs/aci-helloworld`

![Configurar definições básicas para uma nova instância do contentor no portal do Azure][aci-portal-03]

Para este guia de início rápido, use a configuração de **tipo de imagem** padrão **pública** para implantar a imagem pública do Microsoft `aci-helloworld`. Esta imagem do Linux empacota um pequeno aplicativo Web escrito em node. js que serve a uma página HTML estática.

Na página **rede** , especifique um **rótulo de nome DNS** para seu contêiner. O nome deve ser exclusivo na região do Azure em que você cria a instância de contêiner. O contentor estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

![Configurar uma nova instância do contentor no portal do Azure][aci-portal-04]

Deixe as outras configurações em seus padrões e selecione **revisar + criar**.

Quando a validação estiver concluída, é apresentado um resumo das definições de contentor. Selecione **criar** para enviar sua solicitação de implantação de contêiner.

![Resumo das definições para uma nova instância do contentor no portal do Azure][aci-portal-05]

Quando inicia a implementação, é apresentado uma notificação que indica que a implementação está em curso. É apresentada outra notificação quando o grupo de contentores tiver sido implementado.

Abra a visão geral do grupo de contêineres navegando até **grupos de recursos** > **myresource** Group > **MyContainer**. Tome nota do **FQDN** (nome de domínio completamente qualificado) da instância do contentor, bem como do **Estado**.

![Descrição geral do grupo de contentores no portal do Azure][aci-portal-06]

Quando o **Estado** for *Em execução*, navegue para o FQDN do contentor no seu browser.

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

Parabéns! Ao configurar apenas algumas definições, implementou uma aplicação acessível publicamente no Azure Container Instances.

## <a name="view-container-logs"></a>Ver registos de contentor

Ver os registos de uma instância de contentor é útil quando estiver a resolver problemas no contentor ou na aplicação nele executada.

Para ver os registos do contentor, em **Definições**, selecione **Contentores** e, em seguida, **Registos**. Deverá ver o pedido HTTP GET gerado quando visualizou a aplicação no seu browser.

![Registos de contentor no portal do Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o contentor, selecione **Descrição Geral** para a instância de contentor *mycontainer* e, em seguida, selecione **Eliminar**.

![Eliminar instância do contentor no portal do Azure][aci-portal-09]

Selecione **Sim** quando caixa de diálogo de confirmação for apresentada.

![Eliminar confirmação da instância do contentor no portal do Azure][aci-portal-10]

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você criou uma instância de contêiner do Azure a partir de uma imagem pública da Microsoft. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

> [!div class="nextstepaction"]
> [Tutorial do Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/