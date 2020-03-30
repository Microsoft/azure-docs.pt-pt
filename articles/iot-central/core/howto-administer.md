---
title: Alterar definições de aplicação Central Azure IoT [ Microsoft Docs
description: Como administrador, como gerir a sua aplicação Azure IoT Central alterando o nome da aplicação, URL, imagem de upload e eliminando uma aplicação
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158691"
---
# <a name="change-iot-central-application-settings"></a>Alterar as definições de aplicação IoT Central



Este artigo descreve como, como administrador, pode gerir a aplicação alterando o nome da aplicação e url, carregando imagem e eliminando uma aplicação na sua aplicação Azure IoT Central.

Para aceder e utilizar a secção **Administração,** deve estar na função **de Administrador** para uma aplicação Azure IoT Central. Se criar uma aplicação Azure IoT Central, é automaticamente designado para o papel de **Administrador** para essa aplicação.

## <a name="change-application-name-and-url"></a>Alterar o nome da aplicação e o URL

Na página Definições de **Aplicação,** pode alterar o nome e o URL da sua aplicação e, em seguida, selecionar **Guardar**.

![Página de definições de aplicação](media/howto-administer/image0-a.png)

Se o seu administrador criar um tema personalizado para a sua aplicação, esta página inclui uma opção para ocultar o Nome de **Aplicação** na UI. Esta opção é útil se o logótipo da aplicação no tema personalizado incluir o nome da aplicação. Para mais informações, consulte [Personalize o Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Se alterar o seu URL, o seu url antigo pode ser tomado por outro cliente Central Azure IoT. Se isso acontecer, já não está disponível para si. Quando muda o seu URL, o URL antigo já não funciona e precisa de notificar os seus utilizadores sobre o novo URL a utilizar.

## <a name="delete-an-application"></a>Eliminar uma aplicação

Utilize o botão **Eliminar** para eliminar permanentemente a sua aplicação IoT Central. Esta ação elimina permanentemente todos os dados associados à aplicação.

> [!Note]
> Para eliminar uma aplicação, deve também ter permissões para eliminar recursos na subscrição do Azure que escolheu quando criou a aplicação. Para saber mais, consulte Use o [controlo de acesso baseado em papéis para gerir o acesso aos seus recursos de subscrição Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Gerir programaticamente

Os pacotes SDK do IoT Central Azure Resource Manager estão disponíveis para os pacotes Node, Python, C#, Ruby, Java e Go. Pode utilizar estes pacotes para criar, listar, atualizar ou eliminar aplicações IoT Central. Os pacotes incluem ajudantes para gerir a autenticação e o manuseamento de erros.

Pode encontrar exemplos de como utilizar os SDKs [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples)do Gestor de Recursos Azure em .

Para saber mais, consulte os seguintes repositórios e pacotes gitHub:

| Idioma | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Ir | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a administrar a sua aplicação Azure IoT Central, o próximo passo sugerido é aprender sobre [gerir utilizadores e papéis](howto-manage-users-roles.md) no Azure IoT Central.
