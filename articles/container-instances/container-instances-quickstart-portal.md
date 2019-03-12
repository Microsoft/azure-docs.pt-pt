---
title: Início rápido - implementar o contentor do Docker no Azure Container Instances - Portal
description: Neste início rápido, vai utilizar o portal do Azure para implementar rapidamente uma aplicação web em contentores que é executado numa instância de contentor do Azure isolado
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 41313a8b140886247b830db7ca9b34a22257de96
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729157"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Início rápido: Implementar uma instância de contentor no Azure com o portal do Azure

Utilize o Azure Container Instances para execução sem servidor contentores do Docker no Azure com a simplicidade e celeridade. Implemente uma aplicação para uma contentor instância sob demanda quando não precisar de uma plataforma de orquestração de contentores completa, como o serviço Kubernetes do Azure.

Neste início rápido, utilize o portal do Azure para implementar um contentor de Docker isolado e disponibilizar a sua aplicação com um nome de domínio completamente qualificado (FQDN). Depois de configurar algumas definições e implementar o contentor, pode navegar para a aplicação em execução:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contentor

Selecione **Criar um recurso** > **Contentores** >  **Container Instances**.

![Começar a criar uma nova instância do contentor no portal do Azure][aci-portal-01]

Introduza os seguintes valores nas caixas de texto **Nome do contentor**, **Imagem do contentor** e **Grupo de recursos**. Deixe os outros valores nas predefinições e, em seguida, selecione **OK**.

* Nome do contentor: `mycontainer`
* Imagem de contentor: `microsoft/aci-helloworld`
* Grupo de recursos: **Criar um novo** > `myResourceGroup`

![Configurar definições básicas para uma nova instância do contentor no portal do Azure][aci-portal-03]

Neste início rápido, deixe a predefinição de **pública** para implementar o público `microsoft/aci-helloworld` imagem. Esta imagem de pacotes de uma pequena aplicação web escrita em node. js, que serve uma página HTML estática.

Em **Configuração**, especifique uma **Etiqueta de nome DNS** para o contentor. O nome tem de ser exclusivo dentro da região do Azure, onde cria a instância de contentor. O contentor estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

Deixe as outras predefinições da **Configuração** e, em seguida, selecione **OK** para validar a configuração.

![Configurar uma nova instância do contentor no portal do Azure][aci-portal-04]

Quando a validação estiver concluída, é apresentado um resumo das definições de contentor. Selecione **OK** para submeter o pedido de implementação do contentor.

![Resumo das definições para uma nova instância do contentor no portal do Azure][aci-portal-05]

Quando inicia a implementação, é apresentado uma notificação que indica que a implementação está em curso. É apresentada outra notificação quando o grupo de contentores tiver sido implementado.

![Progresso da criação de uma nova instância do contentor no portal do Azure][aci-portal-08]

Aceda à descrição geral do grupo de contentores ao navegar até **Grupos de Recursos** > **myResourceGroup** > **mycontainer**. Tome nota do **FQDN** (nome de domínio completamente qualificado) da instância do contentor, bem como do **Estado**.

![Descrição geral do grupo de contentores no portal do Azure][aci-portal-06]

Quando o **Estado** for *Em execução*, navegue para o FQDN do contentor no seu browser.

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

Parabéns! Ao configurar apenas algumas definições, implementou uma aplicação acessível publicamente no Azure Container Instances.

## <a name="view-container-logs"></a>Ver registos do contentor

Ver os registos de uma instância de contentor é útil quando estiver a resolver problemas no contentor ou na aplicação nele executada.

Para ver os registos do contentor, em **Definições**, selecione **Contentores** e, em seguida, **Registos**. Deverá ver o pedido HTTP GET gerado quando visualizou a aplicação no seu browser.

![Registos de contentor no portal do Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o contentor, selecione **Descrição Geral** para a instância de contentor *mycontainer* e, em seguida, selecione **Eliminar**.

![Eliminar instância do contentor no portal do Azure][aci-portal-09]

Selecione **Sim** quando caixa de diálogo de confirmação for apresentada.

![Eliminar confirmação da instância do contentor no portal do Azure][aci-portal-10]

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, criou uma instância de contentor do Azure a partir de uma imagem no registo do Hub do Docker público. Se deseja criar uma imagem do contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

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