---
title: Quickstart - Desdobre o recipiente Docker para a instância do contentor - Portal
description: Neste arranque rápido, você usa o portal Azure para implementar rapidamente uma aplicação web contentorizada que funciona em uma instância isolada do recipiente Azure
ms.topic: quickstart
ms.date: 03/09/2020
ms.custom: seodec18, mvc, devx-track-javascript
ms.openlocfilehash: 5f36c81abd462f6dfd59c42da71e096e07206cae
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408072"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>Quickstart: Implementar uma instância de contentor em Azure utilizando o portal Azure

Utilize instâncias de contentores Azure para executar recipientes Docker sem servidor em Azure com simplicidade e velocidade. Implemente uma aplicação para uma instância de contentor a pedido quando não precisar de uma plataforma completa de orquestração de contentores como o Serviço Azure Kubernetes.

Neste arranque rápido, utiliza-se o portal Azure para implantar um recipiente estivador isolado e disponibilizar a sua aplicação com um nome de domínio totalmente qualificado (FQDN). Depois de configurar algumas definições e implementar o contentor, pode navegar para a aplicação em execução:

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita][azure-free-account] antes de começar.

## <a name="create-a-container-instance"></a>Criar uma instância de contentor

Selecione as instâncias **de**  >  **recipientes de recursos**para criar um  >  **recurso.**

![Começar a criar uma nova instância do contentor no portal do Azure][aci-portal-01]

Na página **Basics, insira** os seguintes valores nas caixas de texto de imagem do **grupo Recursos,** **nome do recipiente**e caixa de texto de imagem do **Contentor.** Deixe os outros valores nas predefinições e, em seguida, selecione **OK**.

* Grupo de recursos: **Criar novo** > `myresourcegroup`
* Nome do contentor: `mycontainer`
* Fonte de imagem: **Imagens quickstart**
* Imagem de recipiente: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

![Configurar definições básicas para uma nova instância do contentor no portal do Azure][aci-portal-03]

Para este arranque rápido, utilize as definições predefinições para implementar a imagem pública da `aci-helloworld` Microsoft. Esta amostra de imagem Linux embala uma pequena aplicação web escrita em Node.js que serve uma página html estática. Também pode trazer as suas próprias imagens de contentores armazenadas no Registo de Contentores Azure, NoCker Hub ou noutros registos.

Na página **de Networking,** especifique uma **etiqueta de nome DNS** para o seu recipiente. O nome deve ser único na região de Azure onde se cria a instância do contentor. O contentor estará publicamente acessível em `<dns-name-label>.<region>.azurecontainer.io`. Se receber uma mensagem de erro "A etiqueta de nome DNS não está disponível ", experimente uma etiqueta de nome DNS diferente.

![Configurar definições de rede para uma nova instância de contentores no portal Azure][aci-portal-04]

Deixe as outras definições à sua predefinição e, em seguida, selecione **Review + create**.

Quando a validação estiver concluída, é apresentado um resumo das definições de contentor. Selecione **Criar** para submeter o seu pedido de implantação do contentor.

![Resumo das definições para uma nova instância do contentor no portal do Azure][aci-portal-05]

Quando a implementação começa, uma notificação parece indicar que a implantação está em andamento. É apresentada outra notificação quando o grupo de contentores tiver sido implementado.

Abra a visão geral para o grupo de contentores navegando para **os grupos de recursos**  >  **myresourcegroup**  >  **mycontainer**. Tome nota do **FQDN** (nome de domínio completamente qualificado) da instância do contentor, bem como do **Estado**.

![Descrição geral do grupo de contentores no portal do Azure][aci-portal-06]

Quando o **Estado** for *Em execução*, navegue para o FQDN do contentor no seu browser.

![Aplicação implementada com o Azure Container Instances vista no browser][aci-portal-07]

Parabéns! Ao configurar apenas algumas definições, implementou uma aplicação acessível publicamente no Azure Container Instances.

## <a name="view-container-logs"></a>Ver registos de contentor

Ver os registos de uma instância de contentor é útil quando estiver a resolver problemas no contentor ou na aplicação nele executada.

Para visualizar os registos do contentor, em **Definições**, selecione **Contentores,** em seguida, **Regista registos**. Deverá ver o pedido HTTP GET gerado quando visualizou a aplicação no seu browser.

![Registos de contentor no portal do Azure][aci-portal-11]

## <a name="clean-up-resources"></a>Limpar os recursos

Quando tiver terminado com o contentor, selecione **Descrição Geral** para a instância de contentor *mycontainer* e, em seguida, selecione **Eliminar**.

![Eliminar instância do contentor no portal do Azure][aci-portal-09]

Selecione **Sim** quando caixa de diálogo de confirmação for apresentada.

![Eliminar confirmação da instância do contentor no portal do Azure][aci-portal-10]

## <a name="next-steps"></a>Próximos passos

Neste arranque rápido, criou uma instância de contentores Azure a partir de uma imagem pública da Microsoft. Se quiser criar uma imagem de contentor e implementá-la partir de um registo de contentor privado do Azure, prossiga para o tutorial do Azure Container Instances.

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