---
title: Implementar a recuperação de desastre usando backup e restauração no gerenciamento de API
titleSuffix: Azure API Management
description: Saiba como usar o backup e a restauração para executar a recuperação de desastre no gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: fccb9dfe88d39849fb87bdce4b81ac9ee22fada5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430702"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação de desastre usando o backup e a restauração do serviço no gerenciamento de API do Azure

Ao publicar e gerenciar suas APIs por meio do gerenciamento de API do Azure, você está aproveitando os recursos de tolerância a falhas e infraestrutura que, de outra forma, criaria, implementaria e gerenciaria manualmente. A plataforma Azure reduz uma grande fração de falhas potenciais a uma fração do custo.

Para se recuperar de problemas de disponibilidade que afetam a região que hospeda o serviço de gerenciamento de API, esteja pronto para reconstituir seu serviço em outra região a qualquer momento. Dependendo do objetivo do tempo de recuperação, talvez você queira manter um serviço em espera em uma ou mais regiões. Você também pode tentar manter a configuração e o conteúdo em sincronia com o serviço ativo de acordo com o objetivo do ponto de recuperação. Os recursos de backup e restauração do serviço fornecem os blocos de construção necessários para implementar a estratégia de recuperação de desastres.

As operações de backup e restauração também podem ser usadas para replicar a configuração do serviço de gerenciamento de API entre ambientes operacionais, por exemplo, desenvolvimento e preparo. Lembre-se de que os dados de tempo de execução como usuários e assinaturas também serão copiados, o que talvez nem sempre seja desejável.

Este guia mostra como automatizar as operações de backup e restauração e como garantir a autenticação bem-sucedida de solicitações de backup e restauração por Azure Resource Manager.

> [!IMPORTANT]
> A operação de restauração não altera a configuração de nome de host personalizado do serviço de destino. É recomendável usar o mesmo nome de host e TLS personalizados para serviços ativos e em espera, de modo que, após a conclusão da operação de restauração, o tráfego possa ser redirecionado para a instância em espera por uma alteração simples de CNAME de DNS.
>
> A operação de backup não captura dados de log previamente agregados usados em relatórios mostrados na folha de análise no portal do Azure.

> [!WARNING]
> Cada backup expira após 30 dias. Se você tentar restaurar um backup depois que o período de expiração de 30 dias tiver expirado, a restauração falhará com uma mensagem de `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticando solicitações de Azure Resource Manager

> [!IMPORTANT]
> A API REST para backup e restauração usa Azure Resource Manager e tem um mecanismo de autenticação diferente das APIs REST para gerenciar suas entidades de gerenciamento de API. As etapas nesta seção descrevem como autenticar Azure Resource Manager solicitações. Para obter mais informações, consulte [Autenticando solicitações de Azure Resource Manager](/rest/api/index).

Todas as tarefas que você faz em recursos usando o Azure Resource Manager devem ser autenticadas com Azure Active Directory usando as seguintes etapas:

-   Adicione um aplicativo ao locatário Azure Active Directory.
-   Defina permissões para o aplicativo que você adicionou.
-   Obtenha o token para autenticar solicitações para Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo Azure Active Directory

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Usando a assinatura que contém a instância do serviço de gerenciamento de API, navegue até a guia **registros de aplicativo** em **Azure Active Directory** (Azure Active Directory > gerenciar/registros de aplicativo).

    > [!NOTE]
    > Se o Azure Active Directory diretório padrão não estiver visível para sua conta, entre em contato com o administrador da assinatura do Azure para conceder as permissões necessárias para sua conta.

3. Clique em **Novo registo de aplicação**.

    A janela **criar** é exibida à direita. É aí que você insere as informações relevantes do aplicativo do AAD.

4. Introduza um nome para a aplicação.
5. Para o tipo de aplicativo, selecione **nativo**.
6. Insira uma URL de espaço reservado como `http://resources` para o **URI de redirecionamento**, pois é um campo obrigatório, mas o valor não é usado posteriormente. Clique na caixa de seleção para salvar o aplicativo.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar uma aplicação

1. Depois que o aplicativo for criado, clique em **configurações**.
2. Clique em **permissões necessárias**.
3. Clique em **+ Adicionar**.
4. Pressione **selecionar uma API**.
5. Escolha **API de gerenciamento de serviços do Windows Azure**.
6. Pressione **selecionar**.

    ![Adicionar permissões](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Clique em **permissões delegadas** ao lado do aplicativo recém-adicionado, marque a caixa para **acessar o gerenciamento de serviços do Azure (versão prévia)** .
8. Pressione **selecionar**.
9. Clique em **conceder permissões**.

### <a name="configuring-your-app"></a>Configurar a sua aplicação

Antes de chamar as APIs que geram o backup e restaurá-la, você precisa obter um token. O exemplo a seguir usa o pacote NuGet [Microsoft. IdentityModel. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) para recuperar o token.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Substitua `{tenant id}`, `{application id}`e `{redirect uri}` usando as seguintes instruções:

1. Substitua `{tenant id}` pela ID de locatário do aplicativo de Azure Active Directory que você criou. Você pode acessar a ID clicando em **Registros de aplicativo** -> **pontos de extremidade**.

    ![Pontos Finais][api-management-endpoint]

2. Substitua `{application id}` pelo valor obtido navegando até a página **configurações** .
3. Substitua o `{redirect uri}` pelo valor da guia **URIs de redirecionamento** do seu aplicativo Azure Active Directory.

    Depois que os valores forem especificados, o exemplo de código deverá retornar um token semelhante ao exemplo a seguir:

    ![Certificado de][api-management-arm-token]

    > [!NOTE]
    > O token pode expirar após um determinado período. Execute o exemplo de código novamente para gerar um novo token.

## <a name="calling-the-backup-and-restore-operations"></a>Chamando as operações de backup e restauração

As APIs REST são [serviço de gerenciamento de API-backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) e [serviço de gerenciamento de API-restauração](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Antes de chamar as operações de "backup e restauração" descritas nas seções a seguir, defina o cabeçalho de solicitação de autorização para sua chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Fazer backup de um serviço de gerenciamento de API

Para fazer backup de um serviço de gerenciamento de API, emita a seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

em que:

-   `subscriptionId`-ID da assinatura que contém o serviço de gerenciamento de API que você está tentando fazer backup
-   `resourceGroupName`-nome do grupo de recursos do serviço de gerenciamento de API do Azure
-   `serviceName`-o nome do serviço de gerenciamento de API no qual você está fazendo um backup especificado no momento da sua criação
-   `api-version`-substituir por `2018-06-01-preview`

No corpo da solicitação, especifique o nome da conta de armazenamento do Azure de destino, a chave de acesso, o nome do contêiner de BLOB e o nome do backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Defina o valor do cabeçalho de solicitação `Content-Type` como `application/json`.

Backup é uma operação de longa execução que pode levar mais de um minuto para ser concluída. Se a solicitação tiver êxito e o processo de backup tiver iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho de `Location`. Faça as solicitações ' GET ' para a URL no cabeçalho `Location` para descobrir o status da operação. Enquanto o backup estiver em andamento, você continuará a receber um código de status ' 202 aceito '. Um código de resposta de `200 OK` indica a conclusão bem-sucedida da operação de backup.

Observe as seguintes restrições ao fazer uma solicitação de backup:

-   O **contêiner** especificado no corpo da solicitação **deve existir**.
-   Enquanto o backup está em andamento, **Evite alterações no gerenciamento de serviços** , como atualização de SKU ou downgrade, alteração no nome de domínio e muito mais.
-   A restauração de um **backup é garantida somente por 30 dias** desde o momento de sua criação.
-   Os **dados de uso** usados para a criação de relatórios de análise não estão **incluídos** no backup. Use a [API REST de gerenciamento de API do Azure][azure api management rest api] para recuperar periodicamente relatórios de análise para fins de proteção.
-   Além disso, os seguintes itens não fazem parte dos dados de backup: certificados SSL de domínio personalizados e quaisquer certificados intermediários ou raiz carregados pelo cliente, pelo conteúdo do portal do desenvolvedor e pelas configurações de integração de rede virtual.
-   A frequência com a qual você realiza backups de serviço afeta seu objetivo de ponto de recuperação. Para minimizá-lo, é recomendável implementar backups regulares e executar backups sob demanda depois de fazer alterações no serviço de gerenciamento de API.
-   **As alterações** feitas na configuração do serviço (por exemplo, APIs, políticas e aparência do portal do desenvolvedor) enquanto a operação de backup está em processo **podem ser excluídas do backup e serão perdidas**.
-   **Permitir** o acesso do plano de controle à conta de armazenamento do Azure. O cliente deve abrir o seguinte conjunto de IPs de entrada em sua conta de armazenamento para backup. 
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32
### <a name="step2"> </a>Restaurar um serviço de gerenciamento de API

Para restaurar um serviço de gerenciamento de API de um backup criado anteriormente, faça a seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

em que:

-   `subscriptionId`-ID da assinatura que contém o serviço de gerenciamento de API no qual você está restaurando um backup
-   `resourceGroupName`-nome do grupo de recursos que contém o serviço de gerenciamento de API do Azure no qual você está restaurando um backup
-   `serviceName`-o nome do serviço de gerenciamento de API que está sendo restaurado no especificado no momento da criação
-   `api-version`-substituir por `2018-06-01-preview`

No corpo da solicitação, especifique o local do arquivo de backup. Ou seja, adicione o nome da conta de armazenamento do Azure, a chave de acesso, o nome do contêiner de BLOB e o nome do backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Defina o valor do cabeçalho de solicitação `Content-Type` como `application/json`.

Restore é uma operação de execução demorada que pode levar até 30 ou mais minutos para ser concluída. Se a solicitação for bem-sucedida e o processo de restauração tiver iniciado, você receberá um código de status de resposta `202 Accepted` com um cabeçalho de `Location`. Faça as solicitações ' GET ' para a URL no cabeçalho `Location` para descobrir o status da operação. Enquanto a restauração estiver em andamento, você continuará recebendo o código de status ' 202 aceito '. Um código de resposta de `200 OK` indica a conclusão bem-sucedida da operação de restauração.

> [!IMPORTANT]
> **A SKU** do serviço que está sendo restaurado **deve corresponder** à SKU do serviço de backup que está sendo restaurado.
>
> **As alterações** feitas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto a operação de restauração está em andamento **podem ser substituídas**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> As operações de backup e restauração também podem ser executadas com os comandos [_backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) e [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) do PowerShell, respectivamente.

## <a name="next-steps"></a>Passos seguintes

Confira os seguintes recursos para obter orientações diferentes sobre o processo de backup/restauração.

-   [Replicar contas de gerenciamento de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizar a Cópia de Segurança da Gestão de API e restaurar com o Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Gerenciamento de API do Azure: fazer backup e restaurar a configuração](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _a abordagem detalhada por Stuart não corresponde à orientação oficial, mas é interessante._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
