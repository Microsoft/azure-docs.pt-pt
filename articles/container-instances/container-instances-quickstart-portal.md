---
title: Quickstart - Desloque o contentor Docker para a instância do contentor - Portal
description: Neste arranque rápido, você usa o portal Azure para implementar rapidamente uma aplicação web contentorizada que funciona em um caso isolado de contentores Azure
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a872e955db46b76d3b12f8ffc38d4a8e497ea63
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087956"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Quickstart: Implementar uma instância de contentores em Azure utilizando o portal Azure

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente uma aplicação para um contentor a pedido quando não precisar de uma plataforma de orquestração de contentores completa como o Serviço Azure Kubernetes.

Neste arranque rápido, utiliza o portal Azure para implantar um recipiente Docker isolado e disponibilizar a sua aplicação com um nome de domínio totalmente qualificado (FQDN). Depois de configurar algumas definições e implementar o contentor, pode navegar para a aplicação em execução:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Se não tiver uma subscrição Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contentor

Selecione as**instâncias** > de**contentores** **de transporte de recursos** > .

![Começar a criar uma nova instância do contentor no portal do Azure][aci-portal-01]

Na página Basics, introduza os **seguintes valores** no **grupo Recurso**, nome **do contentor**e caixas de texto de **imagem contentor.** Deixe os outros valores nas predefinições e, em seguida, selecione **OK**.

* Grupo de recursos: **Criar novos** > `myresourcegroup`
* Nome do contentor: `mycontainer`
* Fonte de imagem: **Imagens Quickstart**
* Imagem do `mcr.microsoft.com/azuredocs/aci-helloworld` recipiente: (Linux)

![Configurar definições básicas para uma nova instância do contentor no portal do Azure][aci-portal-03]

Para este arranque rápido, utilize definições `aci-helloworld` predefinidas para implementar a imagem pública da Microsoft. Esta amostra de imagem Linux embala uma pequena aplicação web escrita no Node.js que serve uma página html estática. Também pode trazer as suas próprias imagens de contentores armazenadas no Registo de Contentores Azure, NoT O Hub ou outros registos.

Na página **de Networking,** especifique uma **etiqueta de nome DNS** para o seu recipiente. O nome deve ser único dentro da região de Azure, onde se cria a instância do contentor. O contentor estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

![Configurar as definições de rede para uma nova instância de contentores no portal Azure][aci-portal-04]

Deixe as outras definições nas suas definições e, em seguida, selecione **Rever + criar**.

Quando a validação estiver concluída, é apresentado um resumo das definições de contentor. Selecione **Criar** para submeter o seu pedido de implantação do recipiente.

![Resumo das definições para uma nova instância do contentor no portal do Azure][aci-portal-05]

Quando a implementação começa, uma notificação parece indicar que a implementação está em andamento. É apresentada outra notificação quando o grupo de contentores tiver sido implementado.

Abra a visão geral para o grupo de contentores navegando para **grupos** > de recursos**myresourcegroup** > **mycontainer**. Tome nota do **FQDN** (nome de domínio completamente qualificado) da instância do contentor, bem como do **Estado**.

![Descrição geral do grupo de contentores no portal do Azure][aci-portal-06]

Quando o **Estado** for *Em execução*, navegue para o FQDN do contentor no seu browser.

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

Parabéns! Ao configurar apenas algumas definições, implementou uma aplicação acessível publicamente no Azure Container Instances.

## <a name="view-container-logs"></a>Ver registos de contentor

Ver os registos de uma instância de contentor é útil quando estiver a resolver problemas no contentor ou na aplicação nele executada.

Para ver os registos do recipiente, em **Definições,** selecione **Recipientes,** em seguida, **regista**. Deverá ver o pedido HTTP GET gerado quando visualizou a aplicação no seu browser.

![Registos de contentor no portal do Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com o contentor, selecione **Descrição Geral** para a instância de contentor *mycontainer* e, em seguida, selecione **Eliminar**.

![Eliminar instância do contentor no portal do Azure][aci-portal-09]

Selecione **Sim** quando caixa de diálogo de confirmação for apresentada.

![Eliminar confirmação da instância do contentor no portal do Azure][aci-portal-10]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou um contentor Azure a partir de uma imagem pública da Microsoft. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

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