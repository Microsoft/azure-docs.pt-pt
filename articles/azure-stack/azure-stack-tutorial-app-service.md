---
title: Disponibilizar aplicações de API web e aos seus utilizadores do Azure Stack | Documentos da Microsoft
description: Tutorial para instalar o fornecedor de recursos do serviço de aplicações e criar oferece que dê aos utilizadores do Azure Stack a capacidade de criar, aplicações web e API.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: f09ed70647eaa0275020bdac66e0fcf84d2ff00a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760380"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tutorial: disponibilizar aplicações de API web e aos seus utilizadores do Azure Stack

Como um administrador de nuvem do Azure Stack, pode criar ofertas que permita que os utilizadores (inquilinos) criem aplicações de funções do Azure e da web e de API. Ao fornecer aos utilizadores acesso a estas aplicações a pedido, com base na cloud, pode guardá-los tempo e recursos.

Para configurar isso, irá:

> [!div class="checklist"]
> * Implementar o fornecedor de recursos do serviço de aplicações
> * Criar uma oferta
> * Testar a oferta

## <a name="deploy-the-app-service-resource-provider"></a>Implementar o fornecedor de recursos do serviço de aplicações

1. [Preparar o anfitrião do Kit de desenvolvimento do Azure Stack](azure-stack-app-service-before-you-get-started.md). Isto inclui a implementar o fornecedor de recursos do SQL Server, que é necessário para a criação de algumas aplicações.
2. [Transferir os scripts de instalador e auxiliar](azure-stack-app-service-deploy.md).
3. [Execute o script de programa auxiliar para criar os certificados necessários](azure-stack-app-service-deploy.md).
4. [Instalar o fornecedor de recursos do serviço de aplicações](azure-stack-app-service-deploy.md) (vai demorar algumas horas para instalar e para todas as funções de trabalho apareça.)
5. [Validar a instalação](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Criar uma oferta

Por exemplo, pode criar uma oferta que permite aos usuários criar sistemas de gerenciamento de conteúdo web DNN. Ele exige que o serviço do SQL Server que já ativada ao instalar o fornecedor de recursos do SQL Server.

1.  [Define uma quota](azure-stack-setting-quotas.md) e o nomeio *AppServiceQuota*. Selecione **Microsoft. Web** para o **espaço de nomes** campo.
2.  [Criar um plano](azure-stack-create-plan.md). Atribua o nome *TestAppServicePlan*, selecione a **Microsoft. SQL** serviço e o **Quota do serviço de aplicações** quota.

    > [!NOTE]
    > Para permitir aos utilizadores criar outras aplicações, os outros serviços poderão ser necessárias no plano. Por exemplo, as funções do Azure requer o **Microsoft. Storage** serviço no plano, embora exija Wordpress **Microsoft.MySQL**.

3.  [Criar uma oferta](azure-stack-create-offer.md), nomeie- **TestAppServiceOffer** e selecione o **TestAppServicePlan** plano.

## <a name="test-the-offer"></a>Testar a oferta

Agora que implementou o fornecedor de recursos do serviço de aplicações e criou uma oferta, pode iniciar sessão como um utilizador, subscreva a oferta e criar uma aplicação.

Neste exemplo, vamos criar um sistema de gerenciamento de conteúdo de plataforma DNN. Primeiro, crie uma base de dados SQL e, em seguida, a aplicação web DNN.

### <a name="subscribe-to-the-offer"></a>Subscreva a oferta

1. Inicie sessão no portal do Azure Stack (https://portal.local.azurestack.external) como um inquilino.
2. Selecione **obter uma subscrição** >, introduza **TestAppServiceSubscription** sob **nome a apresentar** > **selecionar uma oferta**  >  **TestAppServiceOffer** > **criar**.

### <a name="create-a-sql-database"></a>Criar uma base de dados SQL

1. Selecione **+**  >  **dados + armazenamento** > **base de dados SQL**.
2. Mantenha os valores predefinidos, exceto para os seguintes campos:

    - **Nome de base de dados**: DNNdb
    - **Tamanho máximo em MB**: 100
    - **Subscrição**: TestAppServiceOffer
    - **Grupo de recursos**: DNN-RG

3. Selecione **definições de início de sessão**, introduza as credenciais para a base de dados e, em seguida, selecione **OK**. Irá utilizar estas credenciais mais tarde neste tutorial.
4. Sob **SKU** > selecione o SKU de SQL que criou para o servidor de alojamento de SQL > e, em seguida, selecione **OK**.
5. Selecione **Criar**.

### <a name="create-a-dnn-app"></a>Criar uma aplicação DNN

1. Selecione **+**  >  **ver tudo** > **pré-visualização de plataforma DNN** > **criar** .
2. Introduza *DNNapp* sob **nome da aplicação** e selecione **TestAppServiceOffer** sob **subscrição**.
3. Selecione **configurar definições necessárias** > **criar nova** > introduza um **plano do App Service** nome.
4. Selecione **escalão de preço** > **F1 gratuito** > **selecionar** > **OK**.
5. Selecione **base de dados** e introduza as credenciais para a base de dados do SQL que criou anteriormente.
6. Selecione **Criar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implementar o fornecedor de recursos do serviço de aplicações
> * Criar uma oferta
> * Testar a oferta

Avance para o próximo tutorial para saber como:

> [!div class="nextstepaction"]
> [Implementar aplicações no Azure e o Azure Stack](user/azure-stack-solution-pipeline.md)
