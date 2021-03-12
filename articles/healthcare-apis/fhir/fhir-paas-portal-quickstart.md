---
title: 'Quickstart: Implementar AZure API para FHIR usando o portal Azure'
description: Neste arranque rápido, você aprenderá a implementar API AZure para FHIR e configurar configurações usando o portal Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019617"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>Quickstart: Implementar AZure API para FHIR usando o portal Azure

Neste arranque rápido, você vai aprender a implementar AZure API para FHIR usando o portal Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-new-resource"></a>Criar novos recursos

Abra o [portal Azure](https://portal.azure.com) e clique em **Criar um recurso**

![Criar um recurso](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Pesquisa de Azure API para FHIR

Você pode encontrar Azure API para FHIR digitando "FHIR" na caixa de pesquisa:

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="Pesquisa de APIs de Cuidados de Saúde":::

## <a name="create-azure-api-for-fhir-account"></a>Criar Azure API para conta FHIR

Selecione **Criar** para criar uma nova API Azure para conta FHIR:

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Criar Azure API para conta FHIR":::

## <a name="enter-account-details"></a>Inserir detalhes da conta

Selecione um grupo de recursos existente ou crie um novo, escolha um nome para a conta e, finalmente, clique em **Rever + criar**:

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="Novos detalhes da api dos cuidados de saúde":::

Confirme a criação e aguarde a implantação da FHIR API.

## <a name="additional-settings-optional"></a>Definições adicionais (opcional)

Também pode clicar em **Seguinte: Definições adicionais** para visualizar as definições de autenticação. A configuração padrão para a API Azure para FHIR é [usar o Azure RBAC para atribuir funções de plano de dados](configure-azure-rbac.md). Quando configurado neste modo, a "Autoridade" para o serviço FHIR será definida para o inquilino do Diretório Ativo Azure da subscrição:

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="Definições de autenticação predefinido":::

Note que a caixa para introduzir IDs de objetos permitidos está acinzentada, uma vez que usamos O RBAC Azure para configurar atribuições de funções neste caso.

Se desejar configurar o serviço FHIR para utilizar um inquilino externo ou secundário do Azure Ative Directory, pode alterar a Autoridade e introduzir IDs de objetos para utilizador e grupos que devem ter acesso ao servidor. Para mais informações, consulte o guia [de configuração local do RBAC.](configure-local-rbac.md)

## <a name="fetch-fhir-api-capability-statement"></a>Obter declaração de capacidade da API FHIR

Para validar que a nova conta FHIR API é anteada, obter uma declaração de capacidade apontando um navegador para `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` .

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não é necessário, pode eliminar o grupo de recursos, a Azure API para fHIR, e todos os recursos relacionados. Para tal, selecione o grupo de recursos que contém a API Azure para a conta FHIR, selecione **Delete resource group**, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste guia de arranque rápido, colocou a API Azure para fHIR na sua subscrição. Para definir definições adicionais na sua API Azure para FHIR, proceda às definições adicionais como orientar. Se estiver pronto para começar a usar a API Azure para FHIR, leia mais sobre como registar as aplicações.

>[!div class="nextstepaction"]
>[Definições adicionais em API Azure para FHIR](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[Visão geral das candidaturas do registo](fhir-app-registration.md)
