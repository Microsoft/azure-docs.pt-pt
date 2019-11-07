---
title: 'Tutorial: dimensionar um aplicativo no Azure Spring Cloud | Microsoft Docs'
description: Neste tutorial, você aprenderá a dimensionar um aplicativo no Azure Spring Cloud no portal do Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: 31b2322bdf7b7c03ae8974d57ee1b44c2f6137b9
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607513"
---
# <a name="tutorial-scale-an-application-in-azure-spring-cloud"></a>Tutorial: dimensionar um aplicativo no Azure Spring Cloud

Este tutorial demonstra como dimensionar qualquer aplicativo de microserviço usando o painel do Azure Spring Cloud no portal do Azure. Dimensione seu aplicativo para cima e para baixo modificando o número de CPUs virtuais (vCPUs) e a quantidade de memória. Dimensione seu aplicativo para dentro e para fora modificando o número de instâncias do aplicativo. Quando terminar, você saberá como fazer ajustes manuais rápidos em cada aplicativo em seu serviço. O dimensionamento entra em vigor em segundos e não requer nenhuma alteração de código ou reimplantação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:
* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância do serviço de nuvem do Azure Spring implantada.  Siga nosso [início rápido](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Pelo menos um aplicativo já criado nessa instância de serviço.


## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navegue até a página escala na portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Navegue até sua página de **visão geral** do Azure Spring Cloud.

1. Vá para a guia **aplicativos** no título **configurações** no menu no lado esquerdo.

1. Selecione o aplicativo que você deseja dimensionar. Neste exemplo, iremos dimensionar o aplicativo chamado "Account-Service". Isso deve levar você à página de **visão geral** do aplicativo.

1. Vá para a guia **escala** no título **configurações** no menu no lado esquerdo. Você deve ver um formulário com linhas para cada um dos atributos de dimensionamento que mencionamos anteriormente.

## <a name="scale-your-application"></a>Dimensionar a sua aplicação

Você pode modificar os atributos de dimensionamento. Lembre-se das seguintes observações.

* **CPUs**: o número máximo de CPUs permitidas é 4 por instância de aplicativo. O número total de CPUs para um aplicativo será o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Memória/GB**: a quantidade máxima de memória permitida é de 8 GB por instância do aplicativo.  A quantidade total de memória para um aplicativo será o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Contagem de instâncias**: você pode escalar horizontalmente até 20 instâncias na camada Standard. Esse valor altera o número de instâncias em execução separadas do aplicativo de microserviço.

Certifique-se de clicar no botão **salvar** para aplicar suas configurações de dimensionamento.

Depois de alguns segundos, as alterações feitas serão exibidas na página **visão geral** , com mais detalhes disponíveis na guia instâncias do **aplicativo** . o dimensionamento não requer nenhuma alteração de código ou reimplantação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a dimensionar manualmente seus aplicativos de nuvem Spring do Azure.  Para saber como monitorar seu aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como monitorar seu aplicativo](spring-cloud-tutorial-distributed-tracing.md)
