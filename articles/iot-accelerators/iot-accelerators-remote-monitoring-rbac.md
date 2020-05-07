---
title: Controlo de acesso à monitorização remota - Azure / Microsoft Docs
description: Este artigo fornece informações sobre como pode configurar controlos de acesso baseados em funções (RBAC) no acelerador de soluções de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: 2774fc1374bf7fa3ed171258e8b1b51cfdb4b8b1
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612950"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Configure controlos de acesso baseados em funções no acelerador de solução de monitorização remota

Este artigo fornece informações sobre como configurar controlos de acesso baseados em funções no acelerador de soluções de monitorização remota. Os controlos de acesso baseados em funções permitem restringir o acesso de utilizadores individuais a funcionalidades específicas na solução.

## <a name="default-settings"></a>Predefinições

Quando implementa pela primeira vez a solução de Monitorização Remota, existem duas funções: **Administrador e** **Ler Apenas**.

Qualquer utilizador na função **Admin** tem acesso total à solução, incluindo as seguintes permissões abaixo. Um utilizador na função **Read Only** só terá acesso para visualizar a solução.

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
| Atualizar gestão sim | Sim   | Não        |

Por predefinição, o utilizador que implementou a solução é automaticamente atribuído à função **Admin** e é proprietário de aplicações do Azure Ative Directory. Como proprietário de aplicações, pode atribuir funções a outros utilizadores através do portal Azure. Se quiser que outro utilizador atribua funções na solução, devem também ser definidos como proprietário de aplicações no portal Azure.

> [!NOTE]
> O utilizador que implementou a solução é a **única pessoa** que pode vê-la imediatamente após a sua criação. Para conceder aos outros acesso para ver a aplicação como um Read Only, Admin ou uma função Personalizada, consulte as seguintes direções abaixo em adicionar ou remover utilizadores.

## <a name="add-or-remove-users"></a>Adicionar ou remover utilizadores

Como proprietário de aplicações Azure Ative Directory, pode utilizar o portal Azure para adicionar ou remover um utilizador para uma função a partir da solução de Monitorização Remota. Os seguintes passos utilizam a [aplicação empresarial Azure Ative Directory](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) que foi criada quando implementou a solução de Monitorização Remota.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Verifique se o [utilizador está no diretório](../active-directory/fundamentals/add-users-azure-active-directory.md) que está a usar. Escolheu o diretório para usar quando inscreveu-se no site [do Microsoft Azure IoT Solution Accelerators.](https://www.azureiotsolutions.com/Accelerators) O nome do diretório é visível no canto superior direito da [página](https://www.azureiotsolutions.com/Accelerators).

1. Encontre a **aplicação Enterprise** para a sua solução no portal Azure. Uma vez lá, filtre a lista definindo o Tipo de **Aplicação** para **todas as aplicações**. Procure a sua aplicação pelo nome da aplicação. O nome da aplicação é o nome da sua solução de Monitorização Remota. Na imagem a seguir, os nomes de solução e exibição de aplicações são **contoso-rm4**.

    ![Aplicação da empresa](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Verifique se é proprietário da aplicação clicando na aplicação e clicando em **Proprietários**. Na seguinte imagem, a administração da **Contoso** é proprietária da aplicação **contoso-rm4:**

    ![Proprietários](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Se não é proprietário, precisa pedir a um proprietário existente que o adicione à lista. Apenas os proprietários podem atribuir funções de aplicação, como **Administrador** ou **Ler Apenas** a outros utilizadores.

1. Para ver a lista de utilizadores atribuídos a funções na aplicação, clique em **Utilizadores e grupos.**

1. Para adicionar um utilizador, clique em + **Adicionar utilizador**, e depois clicar em Utilizadores **e grupos, Nenhum Selecionado** para selecionar um utilizador do diretório.

1. Para atribuir ao utilizador uma função, clique em **Selecionar a função, nenhuma selecionada** e escolher a função **'Administrador'** ou **Ler apenas** para o utilizador. Clique em **Selecionar**e, em seguida, clique em **Atribuir**.

    ![Selecionar função](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. O utilizador pode agora aceder à solução de Monitorização Remota com as permissões definidas pela função.

1. Pode eliminar utilizadores da aplicação na página **Utilizadores e grupos** no portal.

## <a name="create-a-custom-role"></a>Criar uma função personalizada

A solução de Monitorização Remota inclui as funções **de Administrador** e **Leitura apenas** quando é implantada pela primeira vez. Pode adicionar papéis personalizados com diferentes conjuntos de permissões. Para definir um papel personalizado, é preciso:

- Adicione um novo papel à aplicação no portal Azure.
- Defina uma política para o novo papel no microserviço de Autenticação e Autorização.
- Atualize a UI web da solução.

### <a name="define-a-custom-role-in-the-azure-portal"></a>Defina um papel personalizado no portal Azure

Os seguintes passos descrevem como adicionar um papel a uma aplicação no Azure Ative Directory. Neste exemplo, cria-se uma nova função que permite aos membros criar, atualizar e eliminar dispositivos na solução de Monitorização Remota.

1. Encontre o registo da **App** para a sua solução no portal Azure. O nome da aplicação é o nome da sua solução de Monitorização Remota. Na imagem a seguir, os nomes de solução e exibição de aplicações são **contoso-rm4**.

    ![Registo da aplicação](media/iot-accelerators-remote-monitoring-rbac/app-registration-2.png)

1. Selecione a sua aplicação e, em seguida, clique em **Manifestar**. Pode ver as duas funções de [aplicação](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) existentes definidas para a aplicação:

    ![Ver manifesto](media/iot-accelerators-remote-monitoring-rbac/view-manifest.png)

1. Editar o manifesto para adicionar uma função chamada **ManageDevices,** como mostrado no seguinte corte. Você precisa de uma corda única, como um GUID para o novo role ID. Pode gerar um novo GUID utilizando um serviço como o [Gerador GUIA Online:](https://www.guidgenerator.com/)

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

Depois de adicionar o papel à app no portal Azure, é necessário definir uma política em [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) para o papel que atribui as permissões necessárias para gerir os dispositivos.

1. Clone o repositório [de microserviços](https://github.com/Azure/remote-monitoring-services-dotnet) de monitorização remota do GitHub para a sua máquina local.

1. Editar o ficheiro **auth/Services/data/policies/roles.json** para adicionar a política para a função **ManageDevices,** como mostrado no seguinte corte. Os valores **de ID** e **Role** devem corresponder à definição de função no manifesto da aplicação da secção anterior. A lista de ações permitidas permite que alguém na função **ManageDevices** crie, atualize e elimine dispositivos ligados à solução:

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

1. Quando terminar de editar o ficheiro **Serviços/dados/políticas/funções.json,** reconstrói e reimplanta o microserviço de Autenticação e Autorização para o seu acelerador de soluções.

### <a name="how-the-web-ui-enforces-permissions"></a>Como a UI web aplica permissões

A UI web utiliza o microserviço de [Autenticação e Autorização](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) para determinar que ações um utilizador pode tomar e quais os controlos visíveis na UI. Por exemplo, se a sua solução for chamada **contoso-rm4,** a Web UI recupera uma lista de ações permitidas para o utilizador atual, enviando o seguinte pedido:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Para um utilizador chamado Gestor de **Dispositivos** na função **ManageDevices,** a resposta inclui o seguinte JSON no organismo:

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

O seguinte corte do [dispositivoDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) na [Web UI](https://github.com/Azure/pcs-remote-monitoring-webui/) mostra como as permissões são aplicadas declarativamente:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Para mais informações, consulte [Componentes Protegidos](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Pode definir permissões adicionais no ficheiro [authModel.js.](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js)

### <a name="how-the-microservices-enforce-permissions"></a>Como os microserviços impõem permissões

Os microserviços também verificam permissões para proteger contra pedidos de API não autorizados. Quando um microserviço recebe um pedido de API, descodifica e valida o símbolo JWT para obter o ID do utilizador e permissões associadas ao papel do utilizador.

O seguinte corte do ficheiro [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) no [microserviço IoTHub Manager,](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager)mostra como as permissões são aplicadas:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu como os controlos de acesso baseados em papéis são implementados no acelerador de soluções de monitorização remota.

Consulte os controlos de [acesso configure para o explorador](iot-accelerators-remote-monitoring-rbac-tsi.md) de Insights da Série De Tempo para obter informações sobre a gestão do acesso ao explorador de Insights da Série De Tempo no acelerador de soluções de monitorização remota.

Para obter informações mais conceptuais sobre o acelerador de soluções de monitorização remota, consulte [a arquitetura de Monitorização Remota](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Para obter mais informações sobre a personalização da solução de Monitorização Remota, consulte [Personalizar e recolocar um microserviço](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->