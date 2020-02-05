---
title: Gerencie a sua aplicação Azure IoT Central / Microsoft Docs
description: Como administrador, como gerir a sua aplicação Azure IoT Central alterando o nome da aplicação, URL, imagem de upload, cópia e eliminação de uma aplicação
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 89c4dd294cbbf0953545e1055e32adfc5f7cce28
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990798"
---
# <a name="manage-your-iot-central-application"></a>Gerencie a sua aplicação IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como administrador, pode gerir a aplicação alterando o nome da aplicação e url, carregando imagem, podendo também aprender a copiar e eliminar uma aplicação na sua aplicação Azure IoT Central.

Para aceder e utilizar a secção **Administração,** deve estar na função **de Administrador** para uma aplicação Azure IoT Central. Se criar uma aplicação Azure IoT Central, é automaticamente designado para o papel de **Administrador** para essa aplicação. 

## <a name="change-application-name-and-url"></a>Alterar o nome da aplicação e o URL

Na página Definições de **Aplicação,** pode alterar o nome e o URL da sua aplicação e, em seguida, selecionar **Guardar**.

![Página de definições de aplicação](media/howto-administer/image0-a.png)

Se o seu administrador criar um tema personalizado para a sua aplicação, esta página inclui uma opção para ocultar o Nome de **Aplicação** na UI. Isto é útil se o logótipo da aplicação no tema personalizado incluir o nome da aplicação. Para mais informações, consulte [Personalize o Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Se alterar o seu URL, o seu url antigo pode ser tomado por outro cliente Central Azure IoT. Se isso acontecer, já não está disponível para si. Quando muda o seu URL, o URL antigo já não funciona e precisa de notificar os seus utilizadores sobre o novo URL a utilizar.

## <a name="prepare-and-upload-image"></a>Preparar e carregar imagem

Para alterar a imagem da aplicação, consulte [Preparar e carregar imagens para a sua aplicação Central Azure IoT](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copiar um pedido

Pode criar uma cópia de qualquer aplicação, menos quaisquer instâncias do dispositivo, histórico de dados do dispositivo e dados do utilizador. A cópia usa um plano de preços padrão para o qual será cobrado. Não pode criar uma aplicação que utilize o plano de preços gratuitos desta forma.

Selecione **Copiar**. Na caixa de diálogo, introduza os detalhes para a nova aplicação. Em seguida, selecione **Copy** para confirmar que pretende continuar. Saiba mais sobre os campos desta forma em Criar um arranque rápido de [aplicação.](quick-deploy-iot-central.md)

![Página de definições de aplicação](media/howto-administer/appcopy2.png)

Após o sucesso da operação de cópia da aplicação, pode navegar para a nova aplicação utilizando o link.

![Página de definições de aplicação](media/howto-administer/appcopy3a.png)

Copiar uma aplicação também copia a definição de regras e ação por e-mail. Algumas ações como Flow, Logic Apps, etc. estão ligadas a regras específicas através do ID da regra. Quando uma regra é copiada para uma aplicação diferente, obtém o seu próprio ID de regra. Neste caso, os utilizadores terão de criar uma nova ação e, em seguida, associar a nova regra com a sua. Em geral, é uma boa ideia verificar as regras e ações para garantir que estão atualizadas na nova app.

> [!WARNING]
> Se um dashboard inclui azulejos que exibem informações sobre dispositivos específicos, então esses azulejos mostram **que o recurso solicitado não foi encontrado** na nova aplicação. Tem de reconfigurar estes azulejos para exibir informações sobre dispositivos na sua nova aplicação.

## <a name="delete-an-application"></a>Apagar uma aplicação

Utilize o botão **Eliminar** para eliminar permanentemente a sua aplicação IoT Central. Esta ação elimina permanentemente todos os dados associados à aplicação.

> [!Note]
> Para eliminar uma aplicação, deve também ter permissões para eliminar recursos na subscrição do Azure que escolheu quando criou a aplicação. Para saber mais, consulte Use o [controlo de acesso baseado em papéis para gerir o acesso aos seus recursos de subscrição Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Gerir programaticamente

Os pacotes SDK do IoT Central Azure Resource C#Manager estão disponíveis para os pacotes Node, Python, Ruby, Java e Go. Pode utilizar estes pacotes para criar, listar, atualizar ou eliminar aplicações IoT Central. Os pacotes incluem ajudantes para gerir a autenticação e o manuseamento de erros.

Pode encontrar exemplos de como utilizar os SDKs do Gestor de Recursos Azure em [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Para saber mais, consulte os seguintes repositórios e pacotes gitHub:

| Linguagem | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passos seguintes
 
Agora que aprendeu a administrar a sua aplicação Azure IoT Central, o próximo passo sugerido é aprender sobre [gerir utilizadores e papéis](howto-manage-users-roles.md) no Azure IoT Central.