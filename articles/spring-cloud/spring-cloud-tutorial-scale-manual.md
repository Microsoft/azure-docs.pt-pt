---
title: 'Tutorial: Escalar uma aplicação em Azure Spring Cloud Microsoft Docs'
description: Neste tutorial, você aprende a escalar uma aplicação com Azure Spring Cloud no portal Azure
ms.service: spring-cloud
ms.topic: tutorial
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.openlocfilehash: 93cefd0c71e2d51187e68c6f5f02777d158e95a4
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/15/2020
ms.locfileid: "84792069"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Dimensione uma aplicação em Azure Spring Cloud

Este tutorial demonstra como escalar qualquer aplicação de microserviços utilizando o painel Azure Spring Cloud no portal Azure.

Dimensione a sua aplicação para cima e para baixo modificando o seu número de CPUs virtuais (vCPUs) e quantidade de memória. Dimensione a sua aplicação para dentro e para fora modificando o número de instâncias de aplicação.

Depois de terminar, saberá como escamar alterações manuais rápidas em cada aplicação do seu serviço. A escala faz efeito em segundos e não requer alterações de código ou redistribuição.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância de serviço Azure Spring Cloud implantada.  Acompanhe o nosso [quickstart na implementação de uma aplicação através do CLI Azure](spring-cloud-quickstart-launch-app-cli.md) para começar.
* Pelo menos uma aplicação já criada na sua instância de serviço.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Navegue para a página escala no portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Vá à sua página de **visão geral da** nuvem de primavera Azure.

1. Selecione o grupo de recursos que contém o seu serviço.

1. Selecione o separador **Apps** em **Definições** no menu no lado esquerdo da página.

1. Selecione a aplicação desejada para escalar. Neste exemplo, selecione a aplicação denominada **conta-serviço**. Em seguida, deverá consultar a **página** geral da aplicação.

1. Aceda ao separador **Escala** em **Definições** no menu do lado esquerdo da página. Deve ver opções para a escala dos atributos apresentados na secção seguinte.

## <a name="scale-your-application"></a>Dimensione a sua aplicação

Se modificar os atributos de escala, lembre-se das seguintes notas:

* **CPUs**: O número máximo de CPUs por instância de aplicação é de quatro. O número total de CPUs para uma aplicação é o valor aqui definido multiplicado pelo número de instâncias de aplicação.

* **Memória/GB**: A quantidade máxima de memória por instância de aplicação é de 8 GB. A quantidade total de memória para uma aplicação é o valor aqui definido multiplicado pelo número de instâncias de aplicação.

* **Contagem de instâncias de aplicação**: No nível Standard, pode escalar até um máximo de 20 instâncias. Este valor altera o número de instâncias de execução separadas da aplicação de micro-serviço.

Certifique-se de **selecionar Guardar** para aplicar as suas definições de escala.

![O serviço Escala no portal Azure](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Após alguns segundos, as alterações efetuadas são apresentadas na página **'Vista Geral',** com mais detalhes disponíveis no separador **Instâncias de Aplicação.**

## <a name="upgrade-to-the-standard-tier"></a>Atualizar para o escalão Standard
Se estiver no nível Básico e limitado por um ou mais [destes limites,](spring-cloud-quotas.md)pode fazer upgrade para o nível Standard. Para isso, aceda ao menu de nível de preços, selecionando primeiro a coluna standard e clicando no botão **Upgrade.**

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a escalar manualmente as suas aplicações Azure Spring Cloud. Para aprender a monitorizar a sua aplicação, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Saiba como monitorizar uma aplicação](spring-cloud-tutorial-distributed-tracing.md)
