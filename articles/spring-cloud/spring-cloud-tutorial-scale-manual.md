---
title: 'Tutorial: dimensionar um aplicativo no Azure Spring Cloud | Microsoft Docs'
description: Neste tutorial, você aprenderá a dimensionar um aplicativo com o Azure Spring Cloud no portal do Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: jeconnoc
author: jpconnock
ms.date: 10/06/2019
ms.openlocfilehash: eaf7e7ec39555e5b933020835f3bb96429e3aa81
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75461430"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Dimensionar um aplicativo no Azure Spring Cloud

Este tutorial demonstra como dimensionar qualquer aplicativo de microserviço usando o painel do Azure Spring Cloud no portal do Azure.

Dimensione seu aplicativo para cima e para baixo modificando seu número de CPUs virtuais (vCPUs) e quantidade de memória. Dimensione seu aplicativo para dentro e para fora modificando o número de instâncias do aplicativo.

Depois de concluir, você saberá como fazer alterações manuais rápidas em cada aplicativo em seu serviço. O dimensionamento entra em vigor em segundos e não requer nenhuma alteração de código ou reimplantação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância do serviço de nuvem do Azure Spring implantada.  Siga nosso [início rápido na implantação de um aplicativo por meio do CLI do Azure](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Pelo menos um aplicativo já criado em sua instância de serviço.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navegue até a página escala na portal do Azure

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Acesse sua página de **visão geral** do Azure Spring Cloud.

1. Selecione o grupo de recursos que contém o serviço.

1. Selecione a guia **aplicativos** em **configurações** no menu no lado esquerdo da página.

1. Selecione o aplicativo que você deseja dimensionar. Neste exemplo, selecione o aplicativo chamado **Account-Service**. Em seguida, você verá a página **visão geral** do aplicativo.

1. Vá para a guia **escala** em **configurações** no menu no lado esquerdo da página. Você deve ver as opções para o dimensionamento dos atributos mostrados na seção a seguir.

## <a name="scale-your-application"></a>Dimensionar a sua aplicação

Se você modificar os atributos de dimensionamento, lembre-se das seguintes observações:

* **CPUs**: o número máximo de CPUs por instância de aplicativo é quatro. O número total de CPUs para um aplicativo é o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Memória/GB**: a quantidade máxima de memória por instância de aplicativo é de 8 GB. A quantidade total de memória para um aplicativo é o valor definido aqui multiplicado pelo número de instâncias do aplicativo.

* **Contagem de instâncias do aplicativo**: na camada Standard, você pode escalar horizontalmente para um máximo de 20 instâncias. Esse valor altera o número de instâncias em execução separadas do aplicativo de microserviço.

Certifique-se de selecionar **salvar** para aplicar as configurações de dimensionamento.

![O serviço de dimensionamento no portal do Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Depois de alguns segundos, as alterações feitas são exibidas na página **visão geral** , com mais detalhes disponíveis na guia **instâncias do aplicativo** . o dimensionamento não exige nenhuma alteração de código ou reimplantação.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a dimensionar manualmente seus aplicativos de nuvem Spring do Azure. Para saber como monitorar seu aplicativo, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como monitorar um aplicativo](spring-cloud-tutorial-distributed-tracing.md)
