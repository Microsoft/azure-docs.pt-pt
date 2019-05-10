---
title: Administrar uma aplicação do Azure IoT Central | Documentos da Microsoft
description: Como administrador, como administrar a sua aplicação do Azure IoT Central
author: viv-liu
ms.author: viviali
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 87ed31836fcda922b085ec951eb6d9d14542db6a
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467534"
---
# <a name="administer-your-iot-central-application"></a>Administrar a sua aplicação do Centro de IoT

Depois de criar uma aplicação IoT Central, pode ir para o **administração** secção para:

- Gerir definições da aplicação
- Gerir utilizadores
- Gerir funções
- Veja a sua fatura
- Converter a versão de avaliação para pay as you go
- Exportar dados
- Gerir ligação de dispositivo
- Utilizar tokens de acesso a ferramentas de programador
- Personalizar a interface do Usuário da sua aplicação
- Personalizar as ligações de ajuda na aplicação
- Gerir o Centro de IoT através de programação

Aceder e utilizar o **Administration** seção, deve estar no **administrador** função para uma aplicação do Azure IoT Central. Se criar uma aplicação do Azure IoT Central, automaticamente estiver atribuído para o **administrador** função para essa aplicação. O [gerir utilizadores](#manage-users) secção deste artigo explica mais sobre como atribuir o **administrador** função a outros utilizadores.

## <a name="manage-application-settings"></a>Gerir definições da aplicação

### <a name="change-application-name-and-url"></a>Alterar o nome do aplicativo e URL
Na **as configurações do aplicativo** página, pode alterar o nome e o URL do seu aplicativo, em seguida, selecione **guardar**.

![Página de definições de aplicação](media/howto-administer/image0-a.png)

Se o administrador cria um tema personalizado para a sua aplicação, esta página inclui uma opção para ocultar os **nome da aplicação** na interface do Usuário. Isto é útil se o logótipo da aplicação no tema personalizado inclui o nome da aplicação. Para obter mais informações, consulte [personalizar o Azure IoT Central da interface do Usuário](./howto-customize-ui.md).

> [!Note]
> Se alterar o URL, o seu URL antigo pode ser realizado por outro cliente do Azure IoT Central. Se isto acontecer, já não está disponível para utilização. Quando altera o URL, o antigo URL já não funciona, e precisa notificar os utilizadores sobre o novo URL a utilizar.

### <a name="prepare-and-upload-image"></a>Preparar e carregar imagem
Para alterar a imagem de aplicação, consulte [preparar e carregar imagens para a sua aplicação do Azure IoT Central](howto-prepare-images.md).

### <a name="copy-an-application"></a>Copiar uma aplicação
Pode criar uma cópia de qualquer aplicativo, menos quaisquer instâncias de dispositivo, o histórico de dados do dispositivo e dados de utilizador. A cópia é um aplicativo de pay as you go, que lhe será cobrado. Não é possível criar uma aplicação de avaliação desta forma.

Selecione **cópia**. Na caixa de diálogo, introduza os detalhes para a nova aplicação de pay as you go. Em seguida, selecione **cópia** para confirmar que pretende continuar. Saiba mais sobre os campos neste formulário na [criar uma aplicação](quick-deploy-iot-central.md) início rápido.

![Página de definições de aplicação](media/howto-administer/appcopy2.png)

Depois da operação de cópia da aplicação for bem-sucedida, pode navegar para a nova aplicação através da ligação.

![Página de definições de aplicação](media/howto-administer/appcopy3a.png)

> [!Note]
> Também copiar um aplicativo copia a definição de regras e ações. Mas uma vez que os utilizadores que têm acesso à sua aplicação original não são copiados para a aplicação de copiado, tem de adicionar manualmente os utilizadores a ações como o e-mail para o qual os usuários são um pré-requisito. Em geral é uma boa idéia para verificar as regras e ações para se certificar de que são atualizados na nova aplicação.

### <a name="delete-an-application"></a>Eliminar uma aplicação

> [!Note]
> Para eliminar uma aplicação, também tem de ter permissões para eliminar recursos na subscrição do Azure que escolheu quando criou a aplicação. Para obter mais informações, consulte [utilizar o controlo de acesso baseado em funções para gerir o acesso aos recursos da sua subscrição do Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

Utilize o **eliminar** botão Eliminar permanentemente a sua aplicação IoT Central. Esta ação elimina permanentemente todos os dados que está associada a aplicação.

## <a name="manage-users"></a>Gerir utilizadores

### <a name="add-users"></a>Adicionar utilizadores

Cada utilizador tem de ter uma conta de utilizador antes de poderem iniciar sessão e aceder a uma aplicação do Azure IoT Central. Accounts da Microsoft (Msa) e contas do Azure Active Directory (Azure AD) são suportadas no Azure IoT Central. Os grupos do Active Directory do Azure não são atualmente suportados no Azure IoT Central.

Para obter mais informações, consulte [ajuda de conta da Microsoft](https://support.microsoft.com/products/microsoft-account?category=manage-account) e [início rápido: Adicionar novos utilizadores ao Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

1. Para adicionar um utilizador a uma aplicação IoT Central, vá para o **usuários** página no **administração** secção.

    ![Lista de utilizadores](media/howto-administer/image1.png)

1. Para adicionar um utilizador, o **usuários** página, selecione **+ adicionar utilizador**.

1. Escolher uma função para o utilizador a partir da **função** menu pendente. Saiba mais sobre as funções no [gerir funções](#manage-roles) seção deste artigo.

    ![Seleção da função](media/howto-administer/image3.png)

    > [!NOTE]
    >  Para adicionar utilizadores em volume, introduza o utilizador IDs de todos os utilizadores que pretende adicionar separados por ponto e vírgula. Escolha uma função a partir da **função** menu pendente. Em seguida, selecione **Guardar**.

### <a name="edit-the-roles-that-are-assigned-to-users"></a>Editar as funções que são atribuídas aos utilizadores

Funções não podem ser alteradas depois de estão atribuídos. Para alterar a função que é atribuída a um utilizador, elimine o utilizador e, em seguida, adicione o utilizador novamente com uma função diferente.

### <a name="delete-users"></a>Eliminar utilizadores

Para eliminar os utilizadores, selecione uma ou mais caixas de verificação sobre o **utilizadores** página. Em seguida, selecione **Eliminar**.

## <a name="manage-roles"></a>Gerir funções

As funções permitem controlar quem na sua organização pode efetuar várias tarefas no IoT Central. Existem três funções, que pode atribuir aos utilizadores da sua aplicação.

### <a name="administrator"></a>Administrador

Os utilizadores a **administrador** função tem acesso a todas as funcionalidades num aplicativo.

O utilizador que cria uma aplicação é atribuído automaticamente para o **administrador** função. Deve sempre haver, pelo menos, um utilizador no **administrador** função.

### <a name="application-builder"></a>Construtor de Aplicações

Os utilizadores a **Application Builder** função pode fazer tudo num aplicativo, exceto a administrar o aplicativo. Construtores de podem criar, editar, eliminar modelos de dispositivos e de dispositivos, gerir conjuntos de dispositivos e executar tarefas e de análise. Construtores não terão acesso para o **administração** seção do aplicativo.

### <a name="application-operator"></a>Operador de Aplicações

Os utilizadores a **operador aplicações** função não pode efetuar alterações aos modelos de dispositivo e não pode administrar o aplicativo. Operadores podem adicionar e eliminar dispositivos, gerir conjuntos de dispositivos e executar tarefas e de análise. Operadores não terão acesso para o **Application Builder** e **administração** páginas.

## <a name="view-your-bill"></a>Veja a sua fatura

Para ver a sua fatura, vá para o **faturação** página no **administração** secção. A página de faturação do Azure é aberto num novo separador, onde pode ver a fatura para cada uma das suas aplicações do Azure IoT Central.

### <a name="convert-your-trial-to-pay-as-you-go"></a>Converter a versão de avaliação para pay as you go

Pode converter a sua aplicação de avaliação para uma aplicação de pay as you go. Estas são as diferenças entre esses tipos de aplicativos.

- **Versão de avaliação** aplicativos são gratuitos durante sete dias antes que ocorra. Podem ser convertidas em Pay As You Go a qualquer momento antes de expirarem.
- **Pay as you go** aplicações são cobradas por dispositivo, com os primeiros cinco dispositivos Gratuitos.

Saiba mais sobre os preços na [página de preços do Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Para concluir este processo self-service, siga estes passos:

1. Vá para o **faturação** página no **administração** secção.

    ![Estado de avaliação](media/howto-administer/freetrialbilling.png)

1. Selecione **converter para pay as you go**.

    ![Converter a versão de avaliação](media/howto-administer/convert.png)

1. Selecione o adequado do Azure Active Directory e, em seguida, a subscrição do Azure para utilizar para a sua aplicação de pay as you go.

1. Depois de selecionar **converter**, seu aplicativo agora é um aplicativo de pay as you go e iniciar a faturadas.

## <a name="export-data"></a>Exportar dados

Pode habilitar **exportação de dados contínua** para exportar as medidas, dispositivos e dados de modelos de dispositivos à sua conta de armazenamento de Blobs do Azure. Saiba mais sobre como [exportar seus dados](howto-export-data.md).

## <a name="manage-device-connection"></a>Gerir ligação de dispositivo

Ligar dispositivos à escala em seu aplicativo usando aqui as chaves e certificados. Saiba mais sobre [ligar dispositivos](concepts-connectivity.md).

## <a name="use-access-tokens"></a>Utilizar tokens de acesso

Gere tokens de acesso para utilizá-los em ferramentas de desenvolvimento. Atualmente, a ferramenta de desenvolvedor apenas disponível é o Explorador do Centro de IoT para monitorização de dispositivos de mensagens e alterações nas definições e propriedades. Saiba mais sobre o [Explorador de IoT Central](howto-use-iotc-explorer.md).

## <a name="customize-your-application"></a>Personalizar a sua aplicação

Para obter mais informações sobre como alterar as cores e ícones na sua aplicação, consulte [personalizar o Azure IoT Central da interface do Usuário](./howto-customize-ui.md).

## <a name="customize-help"></a>Personalizar ajuda

Para obter mais informações sobre a adição de ligações de ajuda personalizada em seu aplicativo, consulte [personalizar o Azure IoT Central da interface do Usuário](./howto-customize-ui.md).

## <a name="manage-programatically"></a>Gerir programaticamente

Pacotes do SDK do IoT Central do Azure Resource Manager estão disponíveis para o nó, Python, c#, Ruby, Java e Go. Pode utilizar esses pacotes para criar, listar, atualizar ou eliminar aplicações de IoT Central. Os pacotes incluem auxiliares para gerir a autenticação e o tratamento de erros.

Pode encontrar exemplos de como utilizar os SDKs do Azure Resource Manager no [ https://github.com/emgarten/iotcentral-arm-sdk-examples ](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Para obter mais informações, consulte o seguintes repositórios do GitHub e os pacotes:

| Idioma | Repositório | Pacote |
| ---------| ---------- | ------- |
| Nó | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Ir Para | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como administrar a sua aplicação do Azure IoT Central, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Configurar o modelo de dispositivo](howto-set-up-template.md)
