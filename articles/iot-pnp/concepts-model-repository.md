---
title: Compreender conceitos do Repositório do Modelo Azure IoT Microsoft Docs
description: Como desenvolvedor de soluções ou profissional de TI, conheça os conceitos básicos do Repositório de Modelos Azure IoT.
author: prashmo
ms.author: prashmo
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5d07257d1e23ee792aa996e31a2c28c17bc23d34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715077"
---
# <a name="azure-iot-model-repository"></a>Repositório de modelos do Azure IoT

O repositório de modelos do Azure IoT permite que os construtores de dispositivos façam a gestão e partilhem modelos de dispositivos IoT Plug and Play. Os modelos do dispositivo são documentos JSON LD definidos utilizando a [Linguagem de Modelação Digital Twins (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Os modelos armazenados no serviço de repositório de modelos podem ser partilhados com desenvolvedores de soluções, quer privadamente através do controlo de acessos, quer publicamente sem exigir qualquer autenticação para integrar e desenvolver a solução IoT Plug e Play cloud.

> [!NOTE]
> Os construtores de dispositivos podem optar por implementar modelos de dispositivos IoT Plug e Play diretamente num dispositivo, utilizar módulos ou num módulo IoT Edge.

Pode aceder ao repositório do modelo utilizando o:

- [Portal de repositório de modelo Azure IoT](https://aka.ms/iotmodelrepo)
- [Azure IoT modelo repositório API](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync)
- [Comandos de repositório de modelo Azure CLI IoT](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest&preserve-view=true)

## <a name="public-models"></a>Modelos públicos

Os modelos digitais digitais de twin armazenados no repositório de modelos estão disponíveis para todos consumirem e integrarem na sua aplicação sem qualquer autenticação. Além disso, os modelos públicos possibilitam um ecossistema aberto para os construtores de dispositivos e desenvolvedores de soluções partilharem e reutilizarem os seus modelos IoT Plug e Play device.

Consulte a secção ['Publicar um Modelo'](#publish-a-model) nos **Modelos da Empresa** para obter instruções sobre como publicar um modelo no repositório do modelo para o tornar público.

Para visualizar um modelo público utilizando o portal de repositório de modelos:

1. Vá ao [portal de repositório do modelo Azure IoT](https://aka.ms/iotmodelrepo).

1. Selecione em **Ver modelos públicos**.

    ![Ver modelos públicos](./media/concepts-model-repository/public-models.png)

Para visualizar um modelo público programáticamente utilizando a API REST, consulte a documentação da API [do Model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST.

```csharp
var httpClient = new HttpClient();
httpClient.BaseAddress = new Uri("https://repo.azureiotrepository.com");

var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Para visualizar um modelo público utilizando o CLI, consulte o comando Azure CLI [Get Model.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true)

## <a name="company-models"></a>Modelos da empresa

O repositório de modelos da empresa é inquilino no repositório do modelo Azure IoT para a sua organização criar e gerir modelos digitais gémeos da autoria de utilizadores dentro da sua empresa ou organização. Os modelos da empresa estão disponíveis apenas para utilizadores autenticados da sua empresa ou organização. Um administrador de inquilino de repositório modelo pode atribuir permissões e controlar o acesso de outros utilizadores na empresa ou organização aos modelos do repositório modelo da empresa.

### <a name="set-up-your-company-model-repository"></a>Configurar o repositório de modelo da sua empresa

Utilize o seu *trabalho ou a conta Azure Ative Directory (Azure AD)* para aceder ao repositório de modelos. Se a sua organização já tiver um inquilino da AD Azure, pode utilizar a conta de utilizador e os principais de serviço deste inquilino da Azure AD.

Para aprender a configurar um inquilino AZure AD e como criar um utilizador ou diretor de serviço num inquilino AZure AD, consulte secção [de informações adicionais.](#additional-information)

- Se for o primeiro utilizador da sua organização a aceder ao repositório de modelos ou a entrar no portal, é-lhe concedida a função **de Administrador do Inquilino.** Esta função permite-lhe atribuir funções a outros utilizadores no inquilino de repositório da sua organização.

- Pode ser-lhe atribuídas outras funções por um **Administrador de Inquilinos,** como **Modelos de Leitura** ou Criar **Modelos.**

### <a name="understand-access-management"></a>Compreender a gestão de acessos

A tabela seguinte resume as capacidades suportadas no repositório do modelo da empresa e as suas permissões associadas:

| Funcionalidade  | Permissão| Descrição|
|-------------|-----------|------------|
|Ler Modelos|Ler Modelos|Por padrão, todos os utilizadores do arrendatário da empresa podem ver os seus modelos da empresa. Além disso, o utilizador também pode ver os modelos(s) privados que lhes são partilhados por outras empresas.|
|Gerir o Acesso|Gerir o Acesso|Gerir a atribuição da função do utilizador (adicionar ou remover) para outros utilizadores da organização.|
|Criar Modelos|Criar Modelos|Crie modelos no repositório de modelos da empresa.|
|Publicar Modelos|Publicar Modelos|Publique modelos para torná-los públicos para qualquer um ver o modelo.|

A tabela seguinte resume as funções apoiadas e as suas capacidades no repositório de modelos que podem ser utilizados para a gestão de acessos.

|Função|Funcionalidade|
|----|----------|
|InquilinoAdministrator|Gerir o Acesso, Ler Modelos|
|Creator|Criar modelos, ler modelos|
|Publisher|Publicar modelos, ler modelos|

#### <a name="passing-a-security-token-when-accessing-company-models-with-a-rest-api"></a>Passar um token de segurança ao aceder a modelos da empresa com uma API REST

Quando ligar para as APIs REST para gerir modelos da empresa privados ou partilhados, tem de fornecer um sinal de autorização para o utilizador ou diretor de serviço em formato JWT. Consulte a secção [de informações adicionais](#additional-information) para saber como obter um token JWT para um utilizador ou diretor de serviço.

O token JWT deve ser passado no cabeçalho HTTP de Autorização na API quando se destina a modelos da empresa ou modelos partilhados. O símbolo do JWT não é necessário para direcionar modelos públicos.

```csharp
// sample token
var authorizationToken = "eyJhbGciOiJIUzI1NiIsInR5cCTI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationToken);
```

### <a name="view-company-or-shared-models"></a>Ver empresa ou modelos partilhados

Você deve ser um membro do papel de *leitor* do inquilino do repositório ou o modelo deve ser partilhado consigo para ler um modelo. Pode ver uma lista de modelos inéditos que foram partilhados consigo e uma lista de modelos publicados que foram partilhados consigo. Por padrão, os utilizadores podem ler os modelos da sua empresa, modelos que foram partilhados com eles por outras empresas, e todos os modelos públicos.

Para visualizar uma empresa ou modelo partilhado usando o portal:

1. Inscreva-se no [portal de repositório do modelo Azure IoT](https://aka.ms/iotmodelrepo).

1. Expandir **modelos da empresa** – **Inédito** no painel esquerdo

    ![Ver modelos da empresa](./media/concepts-model-repository/view-company-models.png)

1. Expandir **modelos Compartilhados – Inédito** no painel esquerdo

    ![Ver modelos partilhados](./media/concepts-model-repository/view-shared-models.png)

Para visualizar uma empresa ou modelo partilhado utilizando a API REST, consulte a documentação [get model](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/getmodelasync/getmodelasync) REST API. Consulte [a passagem de um token de segurança ao aceder a modelos da empresa com uma API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) para obter informações sobre como passar num cabeçalho de autorização JWT no pedido HTTP.

```csharp
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.GetAsync($"/models/{modelId}?api-version=2020-05-01-preview").ConfigureAwait(false);
```

Para visualizar um modelo da empresa ou um modelo partilhado utilizando o CLI, consulte o comando Azure CLI [Get Model.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-show&preserve-view=true)

### <a name="manage-roles"></a>Gerir funções

O administrador do arrendatário pode atribuir funções aos utilizadores no arrendatário para que possam criar modelos privados para a empresa ou organização, publicar modelos ou gerir funções para outros utilizadores.

Para adicionar um utilizador a um modelo de função de inquilino repositório utilizando o portal:

1. Inscreva-se no [portal de repositório do modelo Azure IoT](https://aka.ms/iotmodelrepo).

1. Selecione **a gestão** do acesso no painel esquerdo e, em seguida, selecione **+Adicionar**. No painel **'Adicionar Permissão',** digite o endereço de trabalho do utilizador que pretende adicionar à função:

    ![Adicionar endereço de trabalho](./media/concepts-model-repository/add-user.png)

1. Escolha a função a que pretende adicionar o utilizador a partir do dropdown **Role.** Em seguida, **selecione Guardar:**

    ![Escolha o papel](./media/concepts-model-repository/choose-role.png)

### <a name="upload-a-model"></a>Faça upload de um modelo

Você deve ser um membro do papel de **Criador** do inquilino do repositório para enviar um modelo para o repositório modelo da empresa.

Estes modelos não são publicados e apenas são acessíveis por padrão pelos utilizadores dentro da sua organização. Também pode partilhar um ou mais modelos inéditos com utilizadores externos.

Os modelos carregados são imutáveis.

Os IDs de modelo para estes modelos devem ser globalmente únicos em todos os inquilinos do repositório para todos os modelos carregados.

Para carregar um modelo utilizando o portal:

1. Inscreva-se no [portal de repositório do modelo Azure IoT](https://aka.ms/iotmodelrepo).

1. Expandir **modelos da empresa** no painel esquerdo e selecionar Criar **modelo**. Em seguida, **selecione Import Json**.

    ![Criar modelo](./media/concepts-model-repository/create-model.png)

1. Selecione o ficheiro que pretende carregar. Se o portal validar com sucesso o seu modelo, **selecione Guardar**.

Para carregar um modelo utilizando a API REST, consulte a [API Do Modelo](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) Criar. Consulte [a passagem de um token de segurança ao aceder a modelos da empresa com uma API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) para obter informações sobre como passar num cabeçalho de autorização JWT no pedido HTTP.

```csharp
var httpContent = new StringContent(jsonLdModel, Encoding.UTF8, "application/json");
var modelId = "dtmi:com:mxchip:model;1";
var response = await httpClient.PutAsync($"/models/{modelId}?api-version=2020-05-01-preview", httpContent).ConfigureAwait(false);
```

Para carregar um modelo utilizando o CLI, consulte o comando Azure CLI [Criar um modelo.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-create&preserve-view=true)

### <a name="publish-a-model"></a>Publicar um modelo

Para publicar um modelo, os seguintes requisitos têm de ser cumpridos:

1. A sua organização precisa de ser membro do [Microsoft Partner Network](https://docs.microsoft.com/partner-center/) para publicar modelos. Para criar uma conta do centro de parceiros, veja [Criar uma conta do centro de parceiros](https://docs.microsoft.com/partner-center/mpn-create-a-partner-center-account). Uma vez aprovada a conta, poderá publicar os modelos. Para obter mais informações, veja as [FAQs do Centro de Parceiros](https://support.microsoft.com/help/4340639/partner-center-account-faqs).

2. O utilizador deve ser membro do papel de *Editor* do repositório.

Os modelos criados e publicados pelos utilizadores dentro da sua organização são visíveis como *modelos publicados.* Estes modelos são públicos e podem ser encontrados por qualquer pessoa sob **modelos públicos.**

Para publicar um modelo com o portal:

1. Inscreva-se no [portal de repositório do modelo Azure IoT](https://aka.ms/iotmodelrepo).

2. Expanda os **Modelos da Empresa** no painel esquerdo e selecione o modelo que pretende publicar. Em seguida, selecione **Publicar**.

    ![Publicar o modelo](./media/concepts-model-repository/publish-model.png)

> [!NOTE]
> Se receber uma notificação a dizer que não tem um ID do Microsoft Partner (MPN), siga as etapas de registo na notificação. Para mais informações, consulte os requisitos no início desta secção.

Para publicar um modelo utilizando a API REST, consulte a [Documentação](https://docs.microsoft.com/rest/api/iothub/digitaltwinmodelrepositoryservice/createorupdateasync/createorupdateasync) API do modelo REST. Forneça o parâmetro de cadeia de consulta `update-metadata=true` para publicar um modelo utilizando a API REST. Consulte [a passagem de um token de segurança ao aceder a modelos da empresa com uma API REST](#passing-a-security-token-when-accessing-company-models-with-a-rest-api) para obter informações sobre como passar num cabeçalho de autorização JWT no pedido HTTP.

Para publicar um modelo utilizando o CLI, consulte o comando Azure CLI [Publicar um Modelo.](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp/model?view=azure-cli-latest#ext-azure-iot-az-iot-pnp-model-publish&preserve-view=true)

> [!NOTE]
> Os modelos devem ser publicados no repositório do modelo antes de executar os testes de certificação. Para saber mais, consulte [como certificar os dispositivos IoT Plug e Play](howto-certify-device.md).

### <a name="share-a-model"></a>Partilhar um modelo

Pode partilhar modelos da empresa que criou com utilizadores de organizações externas. Desta forma, pode permitir que os colaboradores vejam e desenvolvam soluções com os seus modelos de empresa privada.

Por exemplo, um fabricante de dispositivos pode querer manter modelos privados para a empresa ou organização. Podem ter clientes que exigem que as suas capacidades de dispositivo permaneçam confidenciais.

A partilha de modelos entre empresas ou organizações permite um acesso seguro a modelos que não são públicos.

Para partilhar um modelo de empresa utilizando o portal:

- Se for o criador de um modelo, o **Share** e o **Shared com** botões estão ativos quando vê o modelo na secção **de modelos da Empresa.**

    ![Modelo de partilha](./media/concepts-model-repository/share-model.png)

- Para partilhar o modelo com um utilizador externo, selecione **Partilhar**. No painel **de modelos Share,** insira o endereço de e-mail do utilizador externo e selecione **Guardar**.

- Para ver os utilizadores com quem partilhou o modelo, **selecione Shared with**.

- Para parar de partilhar o modelo com um utilizador específico, selecione o utilizador da lista de utilizadores no **painel partilhado com** painel. Em seguida, **selecione Remover** e confirmar a sua escolha quando solicitado.

    ![Pare de partilhar](./media/concepts-model-repository/stop-sharing.png)

## <a name="additional-information"></a>Informações adicionais

Poderá encontrar os seguintes tópicos úteis ao trabalhar com a Azure AD:

- Para criar um novo inquilino AD Azure, consulte [Criar um novo inquilino em Azure AD.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant) A maioria das organizações já terá inquilinos da AD Azure.

- Para adicionar utilizadores ou utilizadores convidados a um inquilino AZure AD, consulte [Adicionar ou apagar utilizadores usando Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory).

- Para adicionar um principal de serviço a um inquilino da AD Azure, consulte [como usar o portal para criar uma aplicação AD AZure e um diretor de serviço que possa aceder aos recursos.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

- Para aprender a obter um token JWT da AD AZure para usar ao ligar para ASPis de REST, consulte [Adquirir um símbolo da AD AZure para autorizar pedidos de uma aplicação do cliente.](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app)

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é rever a [arquitetura IoT Plug and Play](concepts-architecture.md).
