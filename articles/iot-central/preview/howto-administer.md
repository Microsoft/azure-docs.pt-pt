---
title: Alterar as configurações do aplicativo IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar seu aplicativo de IoT Central do Azure alterando o nome do aplicativo, URL, carregar imagem e excluir um aplicativo
author: viv-liu
ms.author: viviali
ms.date: 10/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5d14ed31b41deb0db44ba452470c45d69a0ec781
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73896063"
---
# <a name="change-iot-central-application-settings"></a>Alterar IoT Central configurações do aplicativo

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como administrador, você pode gerenciar o aplicativo alterando o nome e a URL do aplicativo, carregando a imagem e excluindo um aplicativo em seu aplicativo de IoT Central do Azure.

Para acessar e usar a seção **Administração** , você deve estar na função **administrador** para um aplicativo IOT central do Azure. Se você criar um aplicativo de IoT Central do Azure, você será automaticamente atribuído à função de **administrador** para esse aplicativo.

## <a name="change-application-name-and-url"></a>Alterar nome e URL do aplicativo

Na página **configurações do aplicativo** , você pode alterar o nome e a URL do seu aplicativo e, em seguida, selecionar **salvar**.

![Página Configurações do aplicativo](media/howto-administer/image0-a.png)

Se o administrador criar um tema personalizado para seu aplicativo, essa página incluirá uma opção para ocultar o **nome do aplicativo** na interface do usuário. Essa opção será útil se o logotipo do aplicativo no tema personalizado incluir o nome do aplicativo. Para obter mais informações, consulte [Personalizar a interface do usuário IOT central do Azure](./howto-customize-ui.md).

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Se isso acontecer, ele não estará mais disponível para uso. Quando você altera a URL, a URL antiga não funciona mais, e você precisa notificar seus usuários sobre a nova URL a ser usada.

## <a name="delete-an-application"></a>Excluir um aplicativo

Use o botão **excluir** para excluir permanentemente seu aplicativo IOT central. Essa ação exclui permanentemente todos os dados associados ao aplicativo.

> [!Note]
> Para excluir um aplicativo, você também deve ter permissões para excluir recursos na assinatura do Azure que você escolheu ao criar o aplicativo. Para saber mais, confira [usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Gerir programaticamente

IoT Central pacotes do SDK do Azure Resource Manager estão disponíveis para o Node C#, Python,, Ruby, Java e go. Você pode usar esses pacotes para criar, listar, atualizar ou excluir aplicativos IoT Central. Os pacotes incluem auxiliares para gerenciar a autenticação e o tratamento de erros.

Você pode encontrar exemplos de como usar os SDKs de Azure Resource Manager em [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Para saber mais, confira os seguintes repositórios e pacotes do GitHub:

| Idioma | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Ir | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu sobre como administrar seu aplicativo de IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar usuários e funções](howto-manage-users-roles.md) no Azure IOT central.
