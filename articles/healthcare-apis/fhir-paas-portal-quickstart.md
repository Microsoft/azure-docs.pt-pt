---
title: 'Quickstart: Implementar AZure API para FHIR usando o portal Azure'
description: Neste arranque rápido, você aprenderá a implementar API AZure para FHIR e configurar configurações usando o portal Azure.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e729597e9d83c4e6096fe52b577b052d94ca4799
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "84820253"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Quickstart: Implementar AZure API para FHIR usando o portal Azure

Neste arranque rápido, você vai aprender a implementar AZure API para FHIR usando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-new-resource"></a>Criar novos recursos

Abra o [portal Azure](https://portal.azure.com) e clique em **Criar um recurso**

![Criar um recurso](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Pesquisa de Azure API para FHIR

Você pode encontrar Azure API para FHIR digitando "FHIR" na caixa de pesquisa:

![Pesquisa de APIs de Cuidados de Saúde](media/quickstart-paas-portal/portal-search-healthcare-apis.png)

## <a name="create-azure-api-for-fhir-account"></a>Criar Azure API para conta FHIR

Selecione **Criar** para criar uma nova API Azure para conta FHIR:

![Criar Azure API para conta FHIR](media/quickstart-paas-portal/portal-create-healthcare-apis.png)

## <a name="enter-account-details"></a>Inserir detalhes da conta

Selecione um grupo de recursos existente ou crie um novo, escolha um nome para a conta e, finalmente, clique em **Rever + criar**:

![Novos detalhes da api dos cuidados de saúde](media/quickstart-paas-portal/portal-new-healthcareapi-details.png)

Confirme a criação e aguarde a implantação da FHIR API.

## <a name="additional-settings"></a>Definições adicionais

Clique **em seguida: Definições adicionais** para configurar a autoridade, audiência, iDs de objeto de identidade que devem ser autorizados a aceder a este API Azure para FHIR, ativar SMART em FHIR, se necessário, e configurar a produção da base de dados:

- **Autoridade:** Você pode especificar diferente inquilino AZure AD daquele em que você está registrado como autoridade de autenticação para o serviço.
- **Público:** A melhor prática, e a definição padrão, é que o público está definido para o URL do servidor FHIR. Pode mudar isto aqui. O público identifica o destinatário para o qual o símbolo se destina. Neste contexto, deve ser definido como algo que represente a própria FHIR API.
- **IDs de objeto permitidos:** Pode especificar iDs de objeto de identidade que devem ser autorizados a aceder a este API Azure para FHIR. Pode aprender mais sobre a descoberta do ID do objeto para utilizadores e principais de serviço no [Find identity object IDs](find-identity-object-ids.md) como orientar.  
- **Smart on FHIR proxy:** Pode ativar smart on FHIR proxy. Para mais detalhes sobre como configurar smart on FHIR proxy consulte [tutorial Azure API para FHIR SMART em proxy FHIR](https://docs.microsoft.com/azure/healthcare-apis/use-smart-on-fhir-proxy)  
- **Produção provisão (RU/s):** Aqui pode especificar as definições de produção para a base de dados subjacente para a sua API Azure para FHIR. Pode alterar esta definição mais tarde na lâmina base de dados. Para mais detalhes, consulte a página [de configuração das definições da base de dados.](configure-database.md)


![Configurar iDs de objeto permitidos](media/quickstart-paas-portal/configure-audience.png)

## <a name="fetch-fhir-api-capability-statement"></a>Obter declaração de capacidade da API FHIR

Para validar que a nova conta FHIR API é anteada, obter uma declaração de capacidade apontando um navegador para `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode eliminar o grupo de recursos, a Azure API para fHIR, e todos os recursos relacionados. Para tal, selecione o grupo de recursos que contém a API Azure para a conta FHIR, selecione **Delete resource group**, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, colocou a API Azure para fHIR na sua subscrição. Para definir definições adicionais na sua API Azure para FHIR, proceda às definições adicionais como orientar.

>[!div class="nextstepaction"]
>[Definições adicionais em API Azure para FHIR](azure-api-for-fhir-additional-settings.md)
