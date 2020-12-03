---
title: Escalar uma aplicação em Azure Spring Cloud Microsoft Docs
description: Saiba como escalar uma aplicação com a Azure Spring Cloud no portal Azure
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 5632f9a6126615255306cc89425bd08a9ffa9753
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531807"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Dimensione uma aplicação em Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Esta documentação demonstra como escalar qualquer aplicação de microserviços utilizando o painel Azure Spring Cloud no portal Azure.

Dimensione a sua aplicação para cima e para baixo modificando o seu número de CPUs virtuais (vCPUs) e quantidade de memória. Dimensione a sua aplicação para dentro e para fora modificando o número de instâncias de aplicação.

Depois de terminar, saberá como escamar alterações manuais rápidas em cada aplicação do seu serviço. A escala faz efeito em segundos e não requer alterações de código ou redistribuição.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir estes procedimentos, é necessário:

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 
* Uma instância de serviço Azure Spring Cloud implantada.  Siga o [quickstart na implementação de uma aplicação através do CLI Azure](spring-cloud-quickstart.md) para começar.
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

Após alguns segundos, as alterações efetuadas são apresentadas na página **'Vista Geral',** com mais detalhes disponíveis no separador **Instâncias de Aplicação.** A escala não requer alterações de código ou redistribuição.

## <a name="upgrade-to-the-standard-tier"></a>Atualizar para o escalão Standard
Se estiver no nível Básico e limitado por um ou mais [destes limites,](spring-cloud-quotas.md)pode fazer upgrade para o nível Standard. Para isso, aceda ao menu de nível de preços, selecionando primeiro a coluna standard e clicando no botão **Upgrade.**

## <a name="next-steps"></a>Passos seguintes

Este exemplo explicou como escalar manualmente uma aplicação Azure Spring Cloud. Para aprender a monitorizar uma aplicação configurando alertas, consulte [configurar autoescala](spring-cloud-tutorial-setup-autoscale.md).

> [!div class="nextstepaction"]
> [Saiba como configurar alertas](spring-cloud-tutorial-alerts-action-groups.md)
