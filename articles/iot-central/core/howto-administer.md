---
title: Alterar as definições de aplicações da Azure IoT Central | Microsoft Docs
description: Como administrador, como gerir a sua aplicação Azure IoT Central alterando o nome da aplicação, URL, upload de imagem e eliminando uma aplicação
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: fde4f236a48e00b20817a812810fc3ad7d4b227f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521888"
---
# <a name="change-iot-central-application-settings"></a>Alterar definições de aplicação IoT Central



Este artigo descreve como, como administrador, pode gerir a aplicação alterando o nome da aplicação e URL, enviando imagem e eliminando uma aplicação na sua aplicação Azure IoT Central.

Para aceder e utilizar a secção **Administração,** deve estar na função **de Administrador** para uma aplicação Azure IoT Central. Se criar uma aplicação Azure IoT Central, é automaticamente atribuída à função **administrador para** essa aplicação.

## <a name="change-application-name-and-url"></a>Alterar nome de aplicação e URL

Na página Definições de **Aplicação,** pode alterar o nome e o URL da sua aplicação e, em seguida, selecionar **Guardar**.

![Página de definições de aplicação](media/howto-administer/image0-a.png)

Se o seu administrador criar um tema personalizado para a sua aplicação, esta página inclui uma opção para ocultar o **Nome de Aplicação** na UI. Esta opção é útil se o logótipo da aplicação no tema personalizado incluir o nome da aplicação. Para mais informações, consulte [Personalizar o Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Se alterar o seu URL, o seu antigo URL pode ser tomado por outro cliente Azure IoT Central. Se isso acontecer, já não está disponível para usar. Quando altera o url, o URL antigo já não funciona e precisa de notificar os seus utilizadores sobre o novo URL para utilizar.

## <a name="delete-an-application"></a>Eliminar uma aplicação

Utilize o botão **Eliminar** para eliminar permanentemente a sua aplicação IoT Central. Esta ação elimina permanentemente todos os dados associados à aplicação.

> [!Note]
> Para eliminar uma aplicação, também deve ter permissões para eliminar recursos na subscrição Azure que escolheu quando criou a aplicação. Para saber mais, consulte [o controlo de acesso baseado em funções para gerir o acesso aos seus recursos de subscrição Azure.](../../role-based-access-control/role-assignments-portal.md)

## <a name="manage-programmatically"></a>Gerir programaticamente

Os pacotes SDK do IoT Central Resource Manager estão disponíveis para Node, Python, C#, Ruby, Java e Go. Pode utilizar estes pacotes para criar, listar, atualizar ou eliminar aplicações IoT Central. As embalagens incluem ajudantes para gerir a autenticação e o manuseamento de erros.

Pode encontrar exemplos de como utilizar os SDKs do Azure Resource Manager em [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) .

Para saber mais, consulte os seguintes repositórios e pacotes GitHub:

| Linguagem | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como administrar a sua aplicação Azure IoT Central, o próximo passo sugerido é aprender sobre [gerir utilizadores e funções](howto-manage-users-roles.md) no Azure IoT Central.
