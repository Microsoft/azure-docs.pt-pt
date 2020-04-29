---
title: 'Tutorial: Escala uma aplicação na Nuvem de primavera Azul [ Microsoft Docs'
description: Neste tutorial, você aprende a escalar uma aplicação com Azure Spring Cloud no portal Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: f08a3b5d0a03b0e898457bbb783dd5031c4b0f27
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76277481"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Escala uma aplicação em Azure Spring Cloud

Este tutorial demonstra como escalar qualquer aplicação de microserviço utilizando o painel azure Spring Cloud no portal Azure.

Escala a sua aplicação para cima e para baixo modificando o seu número de CPUs virtuais (vCPUs) e quantidade de memória. Escala a sua aplicação para dentro e para fora, modificando o número de instâncias de aplicação.

Depois de terminar, saberá como fazer alterações manuais rápidas em cada aplicação no seu serviço. A escala faz efeito em segundos e não requer alterações de código ou redistribuição.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância de serviço Azure Spring Cloud implantada.  Acompanhe o nosso arranque rápido na implementação de [uma aplicação através do Azure CLI](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Pelo menos uma aplicação já criada na sua instância de serviço.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navegue para a página Escala no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá à sua página de **visão geral** da Nuvem de primavera Azure.

1. Selecione o grupo de recursos que contém o seu serviço.

1. Selecione o separador **Apps** em **Definições** no menu do lado esquerdo da página.

1. Selecione a aplicação que pretende escalar. Neste exemplo, selecione a aplicação denominada **serviço de conta**. Em seguida, deve ver a página **de visão geral** da aplicação.

1. Vá ao separador **Escala** em **Definições** no menu do lado esquerdo da página. Deve ver opções para a escala dos atributos mostrados na secção seguinte.

## <a name="scale-your-application"></a>Escala a sua aplicação

Se modificar os atributos de escala, tenha em mente as seguintes notas:

* **CPUs**: O número máximo de CPUs por instância de candidatura é de quatro. O número total de CPUs para uma aplicação é o valor fixado aqui multiplicado pelo número de instâncias de aplicação.

* **Memória/GB**: A quantidade máxima de memória por instância de aplicação é de 8 GB. A quantidade total de memória para uma aplicação é o valor fixado aqui multiplicado pelo número de instâncias de aplicação.

* **Contagem de instâncias**de aplicações : No nível Standard, pode aumentar para um máximo de 20 instâncias. Este valor altera o número de instâncias de execução separadas da aplicação de microserviços.

Certifique-se de selecionar **Guardar** para aplicar as definições de escala.

![O serviço Scale no portal Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Após alguns segundos, as alterações efetuadas são apresentadas na página **'Visão Geral',** com mais detalhes disponíveis no separador **de instâncias de Aplicação.**

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a escalar manualmente as suas aplicações Azure Spring Cloud. Para aprender a monitorizar a sua aplicação, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como monitorizar uma aplicação](spring-cloud-tutorial-distributed-tracing.md)
