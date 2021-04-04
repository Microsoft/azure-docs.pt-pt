---
title: Tutorial - Implantação do registo geo-replicado
description: Implemente uma aplicação web baseada em Linux em duas regiões diferentes de Azure usando uma imagem de recipiente a partir de um registo de contentores Azure geo-replicado. Parte dois de uma série com três partes.
ms.topic: tutorial
ms.date: 08/20/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a203bfc9b1317bc258e4a93ae4ac03ecbdc7a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92148422"
---
# <a name="tutorial-deploy-a-web-app-from-a-geo-replicated-azure-container-registry"></a>Tutorial: Implementar uma aplicação web a partir de um registo de contentores Azure geo-replicado

Esta é a parte dois de um tutorial de três partes. Na [parte um](container-registry-tutorial-prepare-registry.md), foi criado um registo de contentor privado e georreplicado, e foi criada uma imagem de contentor da origem e publicada no registo. Neste artigo, irá tirar partido da proximidade da rede do registo georreplicado ao implementar o contentor em instâncias de Aplicações Web em duas regiões diferentes do Azure. Em seguida, cada instância obtém a imagem de contentor do registo mais próximo.

Neste tutorial, a segunda parte da série:

> [!div class="checklist"]
> * Implemente uma imagem de contentor em duas instâncias de *Aplicações Web para Contentores*
> * Verificar a aplicação implementada

Se ainda não criou um registo georreplicado nem enviou a imagem da aplicação de exemplo em contentor para o registo, regresse ao tutorial anterior da série, [Preparar um registo de contentor georreplicado do Azure](container-registry-tutorial-prepare-registry.md).

No artigo seguinte, irá atualizar a aplicação e, em seguida, enviar a imagem de contentor atualizada para o registo. Por fim, navegue para cada instância de Aplicação Web em execução para ver a alteração automaticamente refletida em ambos os casos, mostrando os webhooks e a georreplicação do Azure Container Registry em ação.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Implementação automática para Aplicações Web para Contentores

O Azure Container Registry fornece suporte para implementar aplicações em contentores diretamente nas [Aplicações Web para Contentores](../app-service/index.yml). Neste tutorial, irá utilizar o portal do Azure para implementar a imagem de contentor criada no tutorial anterior em dois planos de aplicações Web situados em regiões do Azure diferentes.

Quando implementar uma aplicação Web a partir de uma imagem de contentor no seu registo e tiver um registo georreplicado na mesma região, o Azure Container Registry cria um [webhook](container-registry-webhook.md) de implementação de imagem por si. Quando enviar uma nova imagem para o seu repositório de contentor, o webhook deteta a alteração e implementa automaticamente a nova imagem de contentor na sua aplicação Web.

## <a name="deploy-a-web-app-for-containers-instance"></a>Implementar uma instância de Aplicação Web para Contentores

Neste passo, crie uma instância de Aplicação Web para Contentores na região *E.U.A. Oeste*.

Inicie sessão no [Portal do Azure](https://portal.azure.com) e navegue para o registo criado no tutorial anterior.

Selecione **Repositórios**  >  **acr-helloworld,** em seguida, clique com o botão direito na etiqueta **v1** em **Tags** e selecione **Implementar para aplicação web**:

![Implementar no serviço de aplicações no portal do Azure][deploy-app-portal-01]

Se a opção "Implementar na aplicação Web" estiver desativada, poderá não ter ativado o utilizador administrador do registo, conforme indicado em [Criar um registo de contentor](container-registry-tutorial-prepare-registry.md#create-a-container-registry) no primeiro tutorial. Pode ativar o utilizador administrativo nas teclas de acesso **de definições**  >   no portal Azure.

Na **Aplicação Web para Contentores** apresentada depois de selecionar "Implementar na aplicação Web", especifique os seguintes valores para cada definição:

| Definição | Valor |
|---|---|
| **Nome do Site** | Um nome exclusivo global para a aplicação Web. Neste exemplo, utilizamos o formato `<acrName>-westus` para identificar o registo e a região da qual a aplicação Web é implementada. |
| **Grupo de Recursos** | **Utilizar a existência** > `myResourceGroup` |
| **Plano de serviço de aplicações/localização** | Crie um novo plano com o nome `plan-westus` na região **E.U.A. Oeste**. |
| **Imagem** | `acr-helloworld:v1` |
| **Sistema operativo** | Linux |

> [!NOTE]
> Quando cria um novo plano de serviço de aplicações para implementar a sua aplicação contentorizada, é automaticamente selecionado um plano padrão para hospedar a sua aplicação. O plano predefinido depende da definição do sistema operativo.

Selecione **Criar** para aprovisionar a aplicação Web na região *E.U.A. Oeste*.

![A screenshot mostra a Aplicação Web para Recipientes com o botão Criar realçado.][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Ver a aplicação Web implementada

Quando a implementação estiver concluída, pode ver a aplicação em execução ao navegar para o respetivo URL no seu browser.

No portal, selecione **Serviços de Aplicações** e, em seguida, a aplicação Web que aprovisionou no passo anterior. Neste exemplo, a aplicação Web tem o nome *uniqueregistryname-westus*.

Selecione o URL hiperligado da aplicação Web no canto superior esquerdo da descrição geral do **Serviço de Aplicações** para ver a aplicação em execução no seu browser.

![O Screenshot mostra a visão geral do Serviço de Aplicações com URL de aplicação web em destaque.][deploy-app-portal-04]

Após a imagem do Docker ser implementada a partir do seu registo de contentor georreplicado, o site mostra uma imagem a representar a região do Azure que aloja o registo de contentor.

![O screenshot mostra a aplicação web implementada visualizada num browser.][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Implementar a segunda instância de Aplicação Web para Contentores

Utilize o procedimento descrito na secção anterior para implementar uma segunda aplicação Web na região *E.U.A. Leste*. Em **Aplicação Web para Contentores**, especifique os seguintes valores:

| Definição | Valor |
|---|---|
| **Nome do Site** | Um nome exclusivo global para a aplicação Web. Neste exemplo, utilizamos o formato `<acrName>-eastus` para identificar o registo e a região da qual a aplicação Web é implementada. |
| **Grupo de Recursos** | **Utilizar a existência** > `myResourceGroup` |
| **Plano de serviço de aplicações/localização** | Crie um novo plano com o nome `plan-eastus` na região **E.U.A. Leste**. |
| **Imagem** | `acr-helloworld:v1` |
| **Sistema operativo** | Linux |

Selecione **Criar** para aprovisionar a aplicação Web na região *E.U.A. Leste*.

![Screenshot mostra a Aplicação Web para Recipientes Criar janela com o botão Criar realçado.][deploy-app-portal-06]

## <a name="view-the-second-deployed-web-app"></a>Ver a segunda aplicação web implementada

Conforme anteriormente, pode ver a aplicação em execução ao navegar para o respetivo URL no seu browser.

No portal, selecione **Serviços de Aplicações** e, em seguida, a aplicação Web que aprovisionou no passo anterior. Neste exemplo, a aplicação Web tem o nome *uniqueregistryname-eastus*.

Selecione o URL hiperligado da aplicação web no topo-direito da **visão geral** do Serviço de Aplicações para ver a aplicação de execução no seu navegador.

![Configuração de aplicação Web no Linux no portal do Azure][deploy-app-portal-07]

Após a imagem do Docker ser implementada a partir do seu registo de contentor georreplicado, o site mostra uma imagem a representar a região do Azure que aloja o registo de contentor.

![Aplicação Web implementada visualizada num browser][deployed-app-eastus]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou duas instâncias de Aplicação Web para Contentores a partir de um registo de contentor georreplicado do Azure.

Avance para o próximo tutorial para atualizar e, em seguida, implementar uma nova imagem de contentor no registo de contentor e, em seguida, verifique que as aplicações Web executadas em ambas as regiões foram automaticamente atualizadas.

> [!div class="nextstepaction"]
> [Implementar uma atualização a uma imagem de contentor georreplicada](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png