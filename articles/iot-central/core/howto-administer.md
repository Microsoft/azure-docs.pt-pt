---
title: Gerenciar seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como administrador, como gerenciar seu aplicativo de IoT Central do Azure alterando o nome do aplicativo, a URL, o upload de imagem, a cópia e a exclusão de um aplicativo
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4c729d2502464df021df4d03e989d489b2d1ecea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72954594"
---
# <a name="manage-your-iot-central-application"></a>Gerenciar seu aplicativo IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como administrador, você pode gerenciar o aplicativo alterando o nome e a URL do aplicativo, carregando a imagem, também pode aprender a copiar e excluir um aplicativo no aplicativo IoT Central do Azure.

Para acessar e usar a seção **Administração** , você deve estar na função **administrador** para um aplicativo IOT central do Azure. Se você criar um aplicativo de IoT Central do Azure, você será automaticamente atribuído à função de **administrador** para esse aplicativo. 

## <a name="change-application-name-and-url"></a>Alterar nome e URL do aplicativo

Na página **configurações do aplicativo** , você pode alterar o nome e a URL do seu aplicativo e, em seguida, selecionar **salvar**.

![Página Configurações do aplicativo](media/howto-administer/image0-a.png)

Se o administrador criar um tema personalizado para seu aplicativo, essa página incluirá uma opção para ocultar o **nome do aplicativo** na interface do usuário. Isso será útil se o logotipo do aplicativo no tema personalizado incluir o nome do aplicativo. Para obter mais informações, consulte [Personalizar a interface do usuário IOT central do Azure](./howto-customize-ui.md).

> [!Note]
> Se você alterar a URL, a URL antiga poderá ser utilizada por outro cliente do Azure IoT Central. Se isso acontecer, ele não estará mais disponível para uso. Quando você altera a URL, a URL antiga não funciona mais, e você precisa notificar seus usuários sobre a nova URL a ser usada.

## <a name="prepare-and-upload-image"></a>Preparar e carregar imagem

Para alterar a imagem do aplicativo, consulte [preparar e carregar imagens para o aplicativo de IOT central do Azure](howto-prepare-images.md).

## <a name="copy-an-application"></a>Copiar um aplicativo

Você pode criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivo, histórico de dados do dispositivo e dados do usuário. A cópia é um aplicativo pago conforme o uso para o qual você será cobrado. Você não pode criar um aplicativo de avaliação dessa maneira.

Selecione **copiar**. Na caixa de diálogo, insira os detalhes do novo aplicativo pago conforme o uso. Em seguida, selecione **copiar** para confirmar que deseja continuar. Saiba mais sobre os campos neste formulário em [criar um aplicativo](quick-deploy-iot-central.md) de início rápido.

![Página Configurações do aplicativo](media/howto-administer/appcopy2.png)

Depois que a operação de cópia do aplicativo for realizada com sucesso, você poderá navegar para o novo aplicativo usando o link.

![Página Configurações do aplicativo](media/howto-administer/appcopy3a.png)

Copiar um aplicativo também copia a definição de regras e a ação de email. Algumas ações como fluxo, aplicativos lógicos etc. são vinculadas a regras específicas por meio da ID de regra. Quando uma regra é copiada para um aplicativo diferente, ela obtém sua própria ID de regra. Nesse caso, os usuários terão que criar uma nova ação e associar a nova regra a ela. Em geral, é uma boa ideia verificar as regras e ações para certificar-se de que elas estão atualizadas no novo aplicativo.

> [!WARNING]
> Se um painel inclui blocos que exibem informações sobre dispositivos específicos, esses blocos mostram que **o recurso solicitado não foi encontrado** no novo aplicativo. Você deve reconfigurar esses blocos para exibir informações sobre dispositivos em seu novo aplicativo.

## <a name="delete-an-application"></a>Excluir um aplicativo

Use o botão **excluir** para excluir permanentemente seu aplicativo IOT central. Essa ação exclui permanentemente todos os dados associados ao aplicativo.

> [!Note]
> Para excluir um aplicativo, você também deve ter permissões para excluir recursos na assinatura do Azure que você escolheu ao criar o aplicativo. Para saber mais, confira [usar o controle de acesso baseado em função para gerenciar o acesso aos recursos de assinatura do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).


## <a name="manage-programatically"></a>Gerenciar programaticamente

IoT Central pacotes do SDK do Azure Resource Manager estão disponíveis para o Node C#, Python,, Ruby, Java e go. Você pode usar esses pacotes para criar, listar, atualizar ou excluir aplicativos IoT Central. Os pacotes incluem auxiliares para gerenciar a autenticação e o tratamento de erros.

Você pode encontrar exemplos de como usar os SDKs de Azure Resource Manager em [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Para saber mais, confira os seguintes repositórios e pacotes do GitHub:

| Linguagem | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passos seguintes
 
Agora que você aprendeu sobre como administrar seu aplicativo de IoT Central do Azure, a próxima etapa sugerida é aprender a [gerenciar usuários e funções](howto-manage-users-roles.md) no Azure IOT central.