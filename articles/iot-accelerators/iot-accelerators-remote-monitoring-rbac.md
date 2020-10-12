---
title: Controlo remoto de acessos - Azure / Microsoft Docs
description: Este artigo fornece informações sobre como pode configurar o controlo de acesso baseado em funções (RBAC) no acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: 817bc7624bb3a6b69d56265e40681287b7fc09fa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969587"
---
# <a name="configure-role-based-access-control-in-the-remote-monitoring-solution-accelerator"></a>Configure o controlo de acesso baseado em funções no acelerador de solução de monitorização remota

Este artigo fornece informações sobre como configurar o controlo de acesso baseado em funções no acelerador de solução de monitorização remota. O controlo de acesso baseado em funções permite restringir o acesso de utilizadores individuais a funcionalidades específicas na solução.

## <a name="default-settings"></a>Predefinições

Quando implementa pela primeira vez a solução de Monitorização Remota, existem duas funções: **Administração** e **Apenas.**

Qualquer utilizador na função **Administração** tem acesso total à solução, incluindo as seguintes permissões abaixo. Um utilizador na função **Read Only** só terá acesso para visualizar a solução.

| Permissão            | Administrador | Só de Leitura |
|----------------       |-------|-----------|
| Ver Solução         | Sim   | Sim       |
| Atualizar alarmes         | Sim   | Não        |
| Apagar alarmes         | Sim   | Não        |
| Criar dispositivos        | Sim   | Não        |
| Atualizar dispositivos        | Sim   | Não        |
| Eliminar dispositivos        | Sim   | Não        |
| Criar grupos de dispositivos  | Sim   | Não        |
| Atualizar grupos de dispositivos  | Sim   | Não        |
| Eliminar grupos de dispositivos  | Sim   | Não        |
| Criar regras          | Sim   | Não        |
| Atualizar regras          | Sim   | Não        |
| Excluir regras          | Sim   | Não        |
| Criar tarefas           | Sim   | Não        |
| Atualizar a gestão do SIM | Sim   | Não        |

Por predefinição, o utilizador que implementou a solução é automaticamente atribuído à função **de Administrador** e é proprietário de uma aplicação Azure Ative Directory. Como proprietário de aplicações, pode atribuir funções a outros utilizadores através do portal Azure. Se pretender que outro utilizador atribua funções na solução, também deve ser definido como proprietário de uma aplicação no portal Azure.

> [!NOTE]
> O utilizador que implementou a solução é a **única pessoa** que pode vê-la imediatamente após a sua criação. Para dar acesso a outros para ver a aplicação como um Função Read Only, Admin ou Custom, consulte as seguintes instruções abaixo no add ou remova os utilizadores.

## <a name="add-or-remove-users"></a>Adicionar ou remover utilizadores

Como proprietário de uma aplicação Azure Ative Directory, pode utilizar o portal Azure para adicionar ou remover um utilizador a uma função da solução de Monitorização Remota. Os passos seguintes utilizam a [aplicação da empresa Azure Ative Directory](../active-directory/manage-apps/view-applications-portal.md) que foi criada quando implementou a solução de Monitorização Remota.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Verifique se o [utilizador está no diretório](../active-directory/fundamentals/add-users-azure-active-directory.md) que está a usar. Escolheu o diretório para utilizar quando inscreveu no site microsoft [Azure IoT Solution Accelerators.](https://www.azureiotsolutions.com/Accelerators) O nome do diretório é visível no canto superior direito da [página.](https://www.azureiotsolutions.com/Accelerators)

1. Encontre a **aplicação Enterprise** para a sua solução no portal Azure. Uma vez lá, filtrar a lista definindo o **Tipo de Aplicação** para **todas as aplicações**. Procure o seu pedido pelo nome da aplicação. O nome da aplicação é o nome da sua solução de Monitorização Remota. Na imagem seguinte, os nomes de visualização da solução e aplicação são **contoso-rm4**.

    ![Aplicação da empresa](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Verifique se é proprietário da aplicação clicando na aplicação e clicando em **Proprietários.** Na imagem seguinte, a **administração Contoso** é proprietária da aplicação **contoso-rm4:**

    ![O Screenshot mostra a opção Manage Owners selecionada, que exibe os proprietários da aplicação contoso r m 4.](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Se não é proprietário, tem de pedir a um proprietário existente que o adicione à lista. Apenas os proprietários podem atribuir funções de aplicação como **Admin** ou **Read Only** a outros utilizadores.

1. Para ver a lista de utilizadores atribuídos a funções na aplicação, clique em **Utilizadores e grupos**.

1. Para adicionar um utilizador, clique **em + Adicionar utilizador**, e depois clique em **Utilizadores e grupos, Nenhum Selecionado** para selecionar um utilizador a partir do diretório.

1. Para atribuir o utilizador a uma função, clique em **Selecionar funções, Nenhum Selecionado** e escolha o papel **Dedmin** ou **Read Only** para o utilizador. Clique **em Selecionar**e, em seguida, clique em **Atribuir**.

    ![Selecionar função](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. O utilizador pode agora aceder à solução de Monitorização Remota com as permissões definidas pela função.

1. Pode eliminar os utilizadores da aplicação na página **de Utilizadores e grupos** no portal.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A solução de Monitorização Remota inclui as funções **de Administrador** e **Read Only** quando é implantada pela primeira vez. Pode adicionar funções personalizadas com diferentes conjuntos de permissões. Para definir um papel personalizado, é necessário:

- Adicione uma nova função à aplicação no portal Azure.
- Defina uma política para o novo papel no microserviço de Autenticação e Autorização.
- Atualize a UI web da solução.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Defina um papel personalizado no portal Azure

Os seguintes passos descrevem como adicionar uma função a uma aplicação no Azure Ative Directory. Neste exemplo, cria-se uma nova função que permite aos membros criar, atualizar e eliminar dispositivos na solução de Monitorização Remota.

1. Encontre o **registo da App** para a sua solução no portal Azure. O nome da aplicação é o nome da sua solução de Monitorização Remota. Na imagem seguinte, os nomes de visualização da solução e aplicação são **contoso-rm4**.

    ![Registo da aplicação](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Selecione a sua aplicação e, em seguida, clique em **Manifesto**. Pode ver as duas funções de [aplicação existentes definidas](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) para a aplicação:

    ![Ver manifesto](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Edite o manifesto para adicionar uma função chamada **ManageDevices** como mostrado no seguinte corte. Você precisa de uma corda única, como um GUID para o novo iD de papel. Pode gerar um novo GUID utilizando um serviço como o [Gerador GUIADO Online:](https://www.guidgenerator.com/)

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Guarde as alterações.

### <a name="define-a-policy-for-the-new-role"></a>Definir uma política para o novo papel

Depois de adicionar o papel à app no portal Azure, é necessário definir uma política em [roles.js](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) para o papel que atribui as permissões necessárias à gestão dos dispositivos.

1. Clone o repositório de [microserviços](https://github.com/Azure/remote-monitoring-services-dotnet) de monitorização remota do GitHub para a sua máquina local.

1. Editar o **auth/Services/data/policies/roles.jsem** ficheiro para adicionar a política para o papel **Dedevices como** mostrado no seguinte corte. Os valores **de ID** e **Role** devem corresponder à definição de função no manifesto da aplicação da secção anterior. A lista de ações permitidas permite que alguém na função **ManageDevices** crie, atualize e elimine dispositivos ligados à solução:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Quando terminar de editar os **Serviços/dados/políticas/roles.jsno** ficheiro, reconstrua e reimplante o microserviço de Autenticação e Autorização para o seu acelerador de solução.

### <a name="how-the-web-ui-enforces-permissions"></a>Como a web UI aplica permissões

A UI web utiliza o [microserviço de Autenticação e Autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) para determinar que ações um utilizador pode tomar e que controlos são visíveis na UI. Por exemplo, se a sua solução for chamada **contoso-rm4,** a UI web recupera uma lista de ações permitidas para o utilizador atual enviando o seguinte pedido:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Para um utilizador chamado **Gestor de Dispositivos** na função **ManageDevices,** a resposta inclui o seguinte JSON no corpo:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

O seguinte corte de [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) na [UI web](https://github.com/Azure/pcs-remote-monitoring-webui/) mostra como as permissões são aplicadas declarativamente:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Para mais informações, consulte [Componentes Protegidos.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md) Pode definir permissões adicionais no ficheiro [authModel.js.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js)

### <a name="how-the-microservices-enforce-permissions"></a>Como os microserviços impõem permissões

Os microserviços também verificam permissões para proteger contra pedidos de API não autorizados. Quando um microserviço recebe um pedido de API, descodifica e valida o token JWT para obter o ID do utilizador e permissões associadas ao papel do utilizador.

O seguinte corte do ficheiro [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) no [microservice IoTHub Manager](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)mostra como as permissões são aplicadas:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu como o controlo de acesso baseado em funções é implementado no acelerador de solução de monitorização remota.

Consulte [os controlos de acesso configurar para o explorador time series Insights](iot-accelerators-remote-monitoring-rbac-tsi.md) para obter informações sobre a gestão do acesso ao explorador de Insights de Séries Temporais no acelerador de solução de monitorização remota.

Para obter mais informações conceptuais sobre o acelerador de solução de monitorização remota, consulte [a arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre a personalização da solução de Monitorização Remota, consulte [Personalizar e redistribuir um microserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->