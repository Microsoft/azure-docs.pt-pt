---
title: 'Portal Azure: Implementar servidor FHIR de código aberto para Azure - Azure API para FHIR'
description: Este quickstart explica como implementar o servidor FHIR do Microsoft Open Source utilizando o portal Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90978608"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>Quickstart: Implementar o servidor FHIR open source usando o portal Azure

Neste arranque rápido, você vai aprender a implementar um Servidor FHIR Open Source em Azure usando o portal Azure. Usaremos links de implementação fáceis no [repositório Open Source](https://github.com/Microsoft/fhir-server)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="github-open-source-repository"></a>Repositório GitHub Open Source

Navegue na [página de implantação](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md) do GitHub e localize os botões "Implementar para Azure":

>[!div class="mx-imgBorder"]
>![Página de implementação de código aberto](media/quickstart-oss-portal/deployment-page-oss.png)

Clique no botão de implementação e o portal Azure abre.

## <a name="fill-in-deployment-parameters"></a>Preencha os parâmetros de implantação

Opte por criar um novo grupo de recursos e dar-lhe um nome. Apenas outros parâmetros necessários são um nome para o serviço e a Senha de Administrador Sql.

>[!div class="mx-imgBorder"]
>![Parâmetros de implementação personalizados](media/quickstart-oss-portal/deployment-custom-parameters.png)

Depois de preencher os detalhes, pode iniciar a implantação.

## <a name="validate-fhir-server-is-running"></a>Validar o Servidor FHIR está em execução

Uma vez concluída a implementação, pode apontar o seu navegador `https://SERVICENAME.azurewebsites.net/metadata` para obter uma declaração de capacidade. Levará um minuto ou mais para o servidor responder pela primeira vez.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não é necessário, pode eliminar o grupo de recursos e todos os recursos relacionados. Para tal, selecione o grupo de recursos que contém os recursos aprovisionados, selecione **Delete resource group**, em seguida, confirme o nome do grupo de recursos para eliminar.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, implementou o Microsoft Open Source FHIR Server para Azure na sua subscrição. Para aprender a aceder à API FHIR usando o Carteiro, dirija-se ao tutorial do Carteiro.
 
>[!div class="nextstepaction"]
>[Acesso FHIR API usando Carteiro](access-fhir-postman-tutorial.md)