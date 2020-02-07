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
ms.date: 02/03/2020
ms.author: apimpm
ms.openlocfilehash: 8f748764d0f61e4932b2d4710f5a6805a5eddf0e
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2020
ms.locfileid: "77047476"
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
> Cada backup expira após 30 dias. Se tentar restaurar uma cópia de segurança após o termo do prazo de validade de 30 dias, o restauro falhará com uma mensagem `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticando solicitações de Azure Resource Manager

> [!IMPORTANT]
> A API REST para backup e restauração usa Azure Resource Manager e tem um mecanismo de autenticação diferente das APIs REST para gerenciar suas entidades de gerenciamento de API. As etapas nesta seção descrevem como autenticar Azure Resource Manager solicitações. Para mais informações, consulte [pedidos autenticados do Gestor](/rest/api/index)de Recursos Azure .

Todas as tarefas que você faz em recursos usando o Azure Resource Manager devem ser autenticadas com Azure Active Directory usando as seguintes etapas:

-   Adicione um aplicativo ao locatário Azure Active Directory.
-   Defina permissões para o aplicativo que você adicionou.
-   Obtenha o token para autenticar solicitações para Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo Azure Active Directory

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Utilizando a subscrição que contém a sua instância de serviço de Gestão API, navegue para o separador de **registos** da App em **Diretório Ativo Azure** (Registos azure Ative Directory > Manage/App).

    > [!NOTE]
    > Se o Azure Active Directory diretório padrão não estiver visível para sua conta, entre em contato com o administrador da assinatura do Azure para conceder as permissões necessárias para sua conta.

3. Clique em **Novo registo de aplicação**.

    A janela **Criar** aparece à direita. É aí que você insere as informações relevantes do aplicativo do AAD.

4. Insira um nome para a aplicação.
5. Para o tipo de aplicação, selecione **Native**.
6. Introduza um URL de espaço reservado, como `http://resources` para o **Redirect URI,** pois é um campo necessário, mas o valor não é usado mais tarde. Clique na caixa de seleção para salvar o aplicativo.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar uma aplicação

1. Assim que a aplicação for criada, clique em **permissões API**.
2. Clique **+ Adicione uma permissão**.
4. Prima **selecione Microsoft APIs**.
5. Escolha **a Gestão de Serviços Azure.**
6. Pressione **Selecione**.

    ![Adicionar permissões](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Clique em **Permissões Delegadas** ao lado da aplicação recém-adicionada, verifique a caixa para **Access Azure Service Management (pré-visualização)** .
8. Pressione **Selecione**.
9. Clique em **Permissões de Concessão**.

### <a name="configuring-your-app"></a>Configurar a sua aplicação

Antes de chamar as APIs que geram o backup e restaurá-la, você precisa obter um token. O exemplo seguinte utiliza o pacote [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet para recuperar o token.

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

Substitua `{tenant id}`, `{application id}`e `{redirect uri}` utilizando as seguintes instruções:

1. Substitua `{tenant id}` com a identificação do inquilino da aplicação Azure Ative Directory que criou. Pode aceder ao ID clicando nos **registos da App** -> **Pontos Finais**.

    ![Pontos Finais][api-management-endpoint]

2. Substitua `{application id}` pelo valor que obtém navegando na página **Definições.**
3. Substitua o `{redirect uri}` pelo valor do separador **URIs redirecionado** da sua aplicação Azure Ative Directory.

    Depois que os valores forem especificados, o exemplo de código deverá retornar um token semelhante ao exemplo a seguir:

    ![Certificado de][api-management-arm-token]

    > [!NOTE]
    > O token pode expirar após um determinado período. Execute o exemplo de código novamente para gerar um novo token.

## <a name="calling-the-backup-and-restore-operations"></a>Chamando as operações de backup e restauração

As APIs rest são O Serviço de [Gestão Api - Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) e Serviço de [Gestão Api - Restaurar](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore).

Antes de chamar as operações de "backup e restauração" descritas nas seções a seguir, defina o cabeçalho de solicitação de autorização para sua chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Apoiar um serviço de Gestão API

Para fazer backup de um serviço de gerenciamento de API, emita a seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Quando:

-   `subscriptionId` - IDENTIFICAÇÃO da subscrição que detém o serviço de Gestão API que está a tentar fazer back-up
-   `resourceGroupName` - nome do grupo de recursos do seu serviço de Gestão API Azure
-   `serviceName` - o nome do serviço de Gestão API que está a fazer uma cópia de segurança especificada no momento da sua criação
-   `api-version` - substitua-a por `2018-06-01-preview`

No corpo da solicitação, especifique o nome da conta de armazenamento do Azure de destino, a chave de acesso, o nome do contêiner de BLOB e o nome do backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Detete o valor do cabeçalho de pedido de `Content-Type` para `application/json`.

Backup é uma operação de longa execução que pode levar mais de um minuto para ser concluída. Se o pedido tiver sido bem sucedido e o processo de backup tiver começado, receberá um código de estado de resposta `202 Accepted` com um cabeçalho `Location`. Faça pedidos 'GET' ao URL no cabeçalho `Location` para saber o estado da operação. Enquanto o backup estiver em andamento, você continuará a receber um código de status ' 202 aceito '. Um código de resposta de `200 OK` indica a conclusão bem sucedida da operação de backup.

Note os seguintes constrangimentos ao efeteu um pedido de cópia de segurança ou restauro:

-   **Deve** **existir**um recipiente especificado no organismo de pedido.
-   Enquanto o backup está em andamento, **evite alterações de gestão no serviço,** como upgrade ou downgrade SKU, alteração no nome de domínio e muito mais.
-   A restauração de um **backup só é garantida por 30 dias** desde o momento da sua criação.
-   **Os dados de utilização** utilizados para a criação de relatórios de análise **não estão incluídos** na cópia de segurança. Utilize a [API de Gestão de API azure][azure api management rest api] para recuperar periodicamente relatórios de análise para a segurança.
-   Além disso, os seguintes itens não fazem parte dos dados de backup: certificados SSL de domínio personalizados e quaisquer certificados intermediários ou raiz carregados pelo cliente, pelo conteúdo do portal do desenvolvedor e pelas configurações de integração de rede virtual.
-   A frequência com a qual você realiza backups de serviço afeta seu objetivo de ponto de recuperação. Para minimizá-lo, é recomendável implementar backups regulares e executar backups sob demanda depois de fazer alterações no serviço de gerenciamento de API.
-   **As alterações** introduzidas na configuração do serviço (por exemplo, APIs, políticas e aparência do portal do desenvolvedor) enquanto a operação de backup está em processo **pode ser excluída da cópia de segurança e será perdida**.
-   **Permita o** acesso do plano de controlo à Conta de Armazenamento Azure, se tiver [firewall][azure-storage-ip-firewall] ativado. O cliente deve abrir o conjunto de endereços IP IP do plano de controlo de [gestão da API Azure][control-plane-ip-address] na sua conta de armazenamento para backup de ou restaurar. 

> [!NOTE]
> Se tentar fazer backup/restaurar de/para um serviço de Gestão API utilizando uma conta de armazenamento que tenha [firewall][azure-storage-ip-firewall] ativada, na mesma Região Azure, então isso não funcionará. Isto porque os pedidos ao Armazenamento Azure não são SNATed para um IP público da Compute > (Azure Api Management Control Plane). O pedido de armazenamento da Região Transversal será sNATed.

### <a name="step2"> </a>Restaurar um serviço de Gestão API

Para restaurar um serviço de gerenciamento de API de um backup criado anteriormente, faça a seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Quando:

-   `subscriptionId` - ID da subscrição que detém o serviço de Gestão API em que está a restaurar uma cópia de segurança
-   `resourceGroupName` - nome do grupo de recursos que detém o serviço de Gestão API Azure em que está a restaurar uma cópia de segurança
-   `serviceName` - o nome do serviço de gestão da API sendo restaurado em especificado no seu tempo de criação
-   `api-version` - substitua-a por `2018-06-01-preview`

No corpo da solicitação, especifique o local do arquivo de backup. Ou seja, adicione o nome da conta de armazenamento do Azure, a chave de acesso, o nome do contêiner de BLOB e o nome do backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Detete o valor do cabeçalho de pedido de `Content-Type` para `application/json`.

Restore é uma operação de execução demorada que pode levar até 30 ou mais minutos para ser concluída. Se o pedido tiver sido bem sucedido e o processo de restauro tiver começado, receberá um código de estado de resposta `202 Accepted` com um cabeçalho `Location`. Faça pedidos 'GET' ao URL no cabeçalho `Location` para saber o estado da operação. Enquanto a restauração estiver em andamento, você continuará recebendo o código de status ' 202 aceito '. Um código de resposta de `200 OK` indica a conclusão bem sucedida da operação de restauro.

> [!IMPORTANT]
> **O SKU** do serviço em que está a ser restaurado **deve corresponder** ao SKU do serviço de apoio que está a ser restaurado.
>
> **As alterações** introduzidas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto a operação de restauro está em curso **podem ser substituídas**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> As operações de backup e restauro também podem ser realizadas com comandos PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) e [_Restore-AzApiManagement,_ ](/powershell/module/az.apimanagement/restore-azapimanagement) respectivamente.

## <a name="next-steps"></a>Passos seguintes

Confira os seguintes recursos para obter orientações diferentes sobre o processo de backup/restauração.

-   [Replicar contas de gestão da API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizar a Cópia de Segurança da Gestão de API e restaurar com o Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Backing Up and Restoring Configuration](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    A _abordagem detalhada por Stuart não corresponde à orientação oficial, mas é interessante._

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
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
