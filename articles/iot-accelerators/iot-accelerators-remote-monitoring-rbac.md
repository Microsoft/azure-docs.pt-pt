---
title: Controlo de acesso de monitorização remoto - Azure | Documentos da Microsoft
description: Este artigo fornece informações sobre como pode configurar os controlos de acesso baseado em funções (RBAC) do solution Accelerator monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 9accb41cdb4d780bf137d6872cca022226f902e6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180760"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Configurar os controlos de acesso baseado em funções no solution accelerator monitorização remota

Este artigo fornece informações sobre como configurar os controlos de acesso baseado em funções no acelerador de solução de monitorização remota. Controlos de acesso baseado em funções permitem-lhe restringir o acesso para utilizadores individuais a recursos específicos da solução.

## <a name="default-settings"></a>Predefinições

Ao implementar a solução de monitorização remota em primeiro lugar, existem duas funções: **Admin** e **só de leitura**.

Nenhum utilizador dos **administrador** função tem acesso total à solução, incluindo as seguintes permissões abaixo. Um utilizador a **só de leitura** função apenas terá acesso para ver a solução.

| Permissão            | Administração | Só de Leitura |
|----------------       |-------|-----------|
| Solução de vista         | Sim   | Sim       |
| Alarmes de atualização         | Sim   | Não        |
| Eliminar alarmes         | Sim   | Não        |
| Crie dispositivos        | Sim   | Não        |
| Atualizar os dispositivos        | Sim   | Não        |
| Eliminar dispositivos        | Sim   | Não        |
| Criar grupos de dispositivos  | Sim   | Não        |
| Grupos de atualizações de dispositivo  | Sim   | Não        |
| Eliminar grupos de dispositivos  | Sim   | Não        |
| Criar regras          | Sim   | Não        |
| Regras de atualização          | Sim   | Não        |
| Eliminar regras          | Sim   | Não        |
| Criar tarefas           | Sim   | Não        |
| Gestão de atualizações de SIM | Sim   | Não        |

Por predefinição, o utilizador que implementou a solução é atribuído automaticamente a **administrador** função e é um proprietário da aplicação do Azure Active Directory. Como proprietário da aplicação, pode atribuir funções para outros utilizadores através do portal do Azure. Se pretender que o outro utilizador para atribuir funções na solução, também deve ser definidas como um proprietário da aplicação no portal do Azure.

> [!NOTE]
> O utilizador que implementou a solução é o **apenas a pessoa** quem pode visualizá-lo imediatamente após a respetiva foi criado. Para conceder outras pessoas acesso para ver a aplicação como um só de leitura, administrador ou uma função personalizada, consulte as orientações a seguir abaixo em Adicionar ou remover utilizadores.

## <a name="add-or-remove-users"></a>Adicionar ou remover utilizadores

Como proprietário da aplicação do Azure Active Directory, pode utilizar o portal do Azure para adicionar ou remover um utilizador a uma função de solução de monitorização remota. Os passos seguintes utilizam o [aplicação do Azure Active Directory empresarial](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) que foi criada quando implementou a solução de monitorização remota.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Verifique os [utilizador está no diretório](../active-directory/fundamentals/add-users-azure-active-directory.md) estiver a utilizar. Escolheu o diretório a utilizar quando iniciar sessão para o [Aceleradores de soluções do Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators) site. O nome do diretório está visível no canto superior direito do [página](https://www.azureiotsolutions.com/Accelerators).

1. Encontrar o **aplicação empresarial** para a sua solução no portal do Azure. Uma vez, filtrar a lista definindo **tipo de aplicação** ao **todos os aplicativos**. Procurar por nome da sua aplicação por aplicação. O nome da aplicação é o nome da sua solução de monitorização remota. Na captura de ecrã seguinte, são os nomes de exibição de solução e aplicativo **contoso-rm4**.

    ![Aplicação empresarial](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Verifique a é um proprietário do aplicativo clicando com o aplicativo e, em seguida, clicar em **proprietários**. Na captura de ecrã seguinte, **administrador da Contoso** for um proprietário da **contoso-rm4** aplicação:

    ![Proprietários](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Se não tenha um proprietário, terá de pedir um proprietário existente para o adicionar à lista. Apenas os proprietários podem atribuir funções de aplicação, tal como **administrador** ou **só de leitura** a outros utilizadores.

1. Para ver a lista de utilizadores atribuídos a funções na aplicação, clique em **utilizadores e grupos**.

1. Para adicionar um utilizador, clique em **+ adicionar utilizador**e, em seguida, clique em **utilizadores e grupos, nenhum selecionado** para selecionar um utilizador do diretório.

1. Para atribuir o utilizador a uma função, clique em **selecionar função, nenhuma selecionado** e escolha o o **administração** ou **só de leitura** função do utilizador. Clique em **selecionar**e, em seguida, clique em **atribuir**.

    ![Selecionar função](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. O utilizador pode agora aceder a solução de monitorização remota com as permissões definidas pela função.

1. Pode eliminar os utilizadores da aplicação no **utilizadores e grupos** página no portal.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A solução de monitorização remota inclui o **administrador** e **só de leitura** funções quando é implementada pela primeira vez. Pode adicionar funções personalizadas com diferentes conjuntos de permissões. Para definir uma função personalizada, terá de:

- Adicione uma nova função para a aplicação no portal do Azure.
- Defina uma política para a nova função em microsserviços a autenticação e autorização.
- Atualize a IU da web da solução.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Definir uma função personalizada no portal do Azure

Os passos seguintes descrevem como adicionar uma função a uma aplicação no Azure Active Directory. Neste exemplo, vai criar uma nova função que permite que os membros criar, atualizar e eliminar dispositivos na solução de monitorização remota.

1. Encontrar o **registo de aplicações** para a sua solução no portal do Azure. O nome da aplicação é o nome da sua solução de monitorização remota. Na captura de ecrã seguinte, são os nomes de exibição de solução e aplicativo **contoso-rm4**.

    ![Registo de aplicações](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Selecione a aplicação e, em seguida, clique em **manifesto**. Pode ver os dois existente [funções de aplicação](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) definidas para a aplicação:

    ![Manifesto do Vista](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Editar o manifesto para adicionar uma função chamada **ManageDevices** conforme mostrado no seguinte fragmento. Precisará de uma cadeia de caracteres como um GUID exclusiva para o novo ID de função. Pode gerar um novo GUID com um serviço, como o [gerador de GUID Online](https://www.guidgenerator.com/):

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

### <a name="define-a-policy-for-the-new-role"></a>Definir uma política para a nova função

Depois de adicionar a função para a aplicação no portal do Azure, terá de definir uma política no [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) para a função que atribui as permissões necessárias para gerir dispositivos.

1. Clone o [Microsserviços de monitorização remota](https://github.com/Azure/remote-monitoring-services-dotnet) repositório do GitHub no seu computador local.

1. Editar a **auth/Services/data/policies/roles.json** ficheiro para adicionar a política para o **ManageDevices** função, conforme mostrado no seguinte fragmento. O **ID** e **função** valores têm de corresponder a definição de função no manifesto do aplicativo da secção anterior. A lista de ações permitidas permite que alguém o **ManageDevices** função para criar, atualizar e eliminar dispositivos ligados à solução:

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

1. Quando terminar de editar a **Services/data/policies/roles.json** de ficheiros, reconstruir e voltar a implementar a autenticação e autorização microsserviços do solution accelerator.

### <a name="how-the-web-ui-enforces-permissions"></a>Como a IU da web impõe permissões

A web utiliza a interface do Usuário a [microsserviços de autenticação e autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) para determinar quais ações de um utilizador tem permissão para realizar e que controlos estão visíveis na interface do Usuário. Por exemplo, se a sua solução é chamada **contoso-rm4**, a IU da web obtém uma lista de ações permitidas para o utilizador atual ao enviar o pedido seguinte:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Para um utilizador chamado **Gestor de dispositivos** no **ManageDevices** função, a resposta inclui o seguinte JSON no corpo:

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

O seguinte fragmento de [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) no [da interface do Usuário da web](https://github.com/Azure/pcs-remote-monitoring-webui/) mostra como as permissões são aplicadas declarativamente:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Para obter mais informações, consulte [protegidos componentes](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Pode definir permissões adicionais no [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) ficheiro.

### <a name="how-the-microservices-enforce-permissions"></a>Como os microsserviços impõem permissões

Os microsserviços também verificar permissões para proteger contra pedidos não autorizados de API. Quando um microsserviço recebe um pedido de API, ele decodifica e valida o token JWT para obter o ID de utilizador e as permissões associadas a função do utilizador.

O seguinte fragmento do [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) de ficheiros a [IoTHub Manager microsserviços](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager), mostra como é que as permissões são aplicadas:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como baseado em funções acesso controles são implementados no acelerador de solução de monitorização remota.

Ver [configurar controlos de acesso para o Explorador do Time Series Insights](iot-accelerators-remote-monitoring-rbac-tsi.md) para obter informações sobre a gestão de acesso para o Explorador do Time Series Insights no solution accelerator monitorização remota.

Para obter mais informações concetuais sobre o acelerador de solução de monitorização remota, consulte [arquitetura de monitorização remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre como personalizar a solução de monitorização remota, consulte [personalizar e Reimplementar um microsserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->