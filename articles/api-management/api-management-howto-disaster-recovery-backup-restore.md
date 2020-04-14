---
title: Implementar recuperação de desastres usando backup e restaurar na Gestão aPI
titleSuffix: Azure API Management
description: Aprenda a usar backup e restaurar para realizar a recuperação de desastres na Azure API Management.
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
ms.openlocfilehash: e74d7dcf8764d167e0080c9d7cca5573bd69ef1d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261011"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure

Ao publicar e gerir as suas APIs através da Azure API Management, está a aproveitar-se da tolerância a falhas e das capacidades de infraestrutura que de outra forma desenharia, implementaria e geriria manualmente. A plataforma Azure atenua uma grande fração de potenciais falhas a uma fração do custo.

Para recuperar de problemas de disponibilidade que afetam a região que acolhe o seu serviço de Gestão API, esteja pronto para reconstituir o seu serviço noutra região a qualquer momento. Dependendo do seu objetivo de tempo de recuperação, você pode querer manter um serviço de espera em uma ou mais regiões. Também pode tentar manter a sua configuração e conteúdo sincronizados com o serviço ativo de acordo com o objetivo do seu ponto de recuperação. As funcionalidades de backup e restauro de serviço fornecem os blocos de construção necessários para implementar a estratégia de recuperação de desastres.

As operações de backup e restauro também podem ser utilizadas para replicar a configuração do serviço de gestão da API entre ambientes operacionais, por exemplo, desenvolvimento e encenação. Tenha em atenção que os dados de tempo de execução, como utilizadores e subscrições, também serão copiados, o que pode nem sempre ser desejável.

Este guia mostra como automatizar as operações de backup e restaurar as operações e como garantir a autenticação bem sucedida de backup e restaurar pedidos por parte do Gestor de Recursos Azure.

> [!IMPORTANT]
> Restaurar a operação não altera a configuração personalizada do nome de anfitrião do serviço alvo. Recomendamos que utilize o mesmo nome de anfitrião personalizado e certificado TLS para serviços ativos e de espera, para que, após a restauração da operação, o tráfego possa ser redirecionado para a instância de espera por uma simples alteração DNS CNAME.
>
> A operação de backup não captura dados de registo pré-agregados utilizados em relatórios mostrados na lâmina Analytics no portal Azure.

> [!WARNING]
> Cada cópia de segurança expira após 30 dias. Se tentar restaurar uma cópia de segurança após o termo do prazo de `Cannot restore: backup expired` validade de 30 dias, o restauro falhará com uma mensagem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticação de pedidos de Gestor de Recursos Azure

> [!IMPORTANT]
> A REST API para backup e restauro utiliza o Gestor de Recursos Azure e tem um mecanismo de autenticação diferente do REST APIs para gerir as suas entidades de Gestão de API. Os passos nesta secção descrevem como autenticar pedidos do Gestor de Recursos Azure. Para mais informações, consulte [pedidos autenticados do Gestor](/rest/api/index)de Recursos Azure .

Todas as tarefas que faz sobre recursos utilizando o Gestor de Recursos Azure devem ser autenticadas com o Diretório Ativo Azure utilizando os seguintes passos:

-   Adicione uma candidatura ao inquilino do Diretório Ativo Azure.
-   Detete permissões para a aplicação que adicionou.
-   Obtenha o símbolo para autenticar pedidos ao Gestor de Recursos Azure.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação azure ative diretório

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Utilizando a subscrição que contém a sua instância de serviço de Gestão API, navegue para o separador de **registos** da App em **Diretório Ativo Azure (Diretório Ativo** Azure > registos de Gestão/App).

    > [!NOTE]
    > Se o diretório padrão do Diretório Ativo Azure não estiver visível na sua conta, contacte o administrador da subscrição Azure para conceder as permissões necessárias à sua conta.

3. Clique em **Novo registo de aplicação**.

    A janela **Criar** aparece à direita. É aí que se entra na aplicação AAD informações relevantes.

4. Introduza um nome para a aplicação.
5. Para o tipo de aplicação, selecione **Native**.
6. Introduza um URL `http://resources` de espaço reservado, como para o **Redirect URI,** pois é um campo necessário, mas o valor não é usado mais tarde. Clique na caixa de verificação para guardar a aplicação.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar uma aplicação

1. Assim que a aplicação for criada, clique em **permissões API**.
2. Clique **+ Adicione uma permissão**.
4. Prima **selecione Microsoft APIs**.
5. Escolha **a Gestão de Serviços Azure.**
6. Pressione **Selecione**.

    ![Adicionar permissões](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Clique em **Permissões Delegadas** ao lado da aplicação recém-adicionada, verifique a caixa para **Access Azure Service Management (pré-visualização)**.
8. Pressione **Selecione**.
9. Clique em **Permissões de Concessão**.

### <a name="configuring-your-app"></a>Configurar a aplicação

Antes de ligar para as APIs que geram a cópia de segurança e restaurá-la, precisa de um símbolo. O exemplo seguinte utiliza o pacote [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet para recuperar o token.

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

Substitua, `{tenant id}` `{application id}` `{redirect uri}` e use as seguintes instruções:

1. Substitua-a `{tenant id}` pela identificação do inquilino da aplicação Azure Ative Directory que criou. Pode aceder ao ID clicando em pontos**finais**de registos de **aplicações** -> .

    ![Pontos Finais][api-management-endpoint]

2. Substitua-o `{application id}` pelo valor que obtém navegando para a página **Definições.**
3. Substitua `{redirect uri}` o valor com o valor do separador **URIs Redirecionamento** da sua aplicação Azure Ative Directory.

    Uma vez especificados os valores, o exemplo do código deve devolver um token semelhante ao seguinte exemplo:

    ![Certificado de][api-management-arm-token]

    > [!NOTE]
    > O símbolo pode expirar após um determinado período. Execute novamente a amostra de código para gerar um novo símbolo.

## <a name="calling-the-backup-and-restore-operations"></a>Chamar as operações de backup e restaurar

As APIs rest são O Serviço de [Gestão Api - Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) e Serviço de [Gestão Api - Restaurar](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Antes de ligar para as operações de "backup e restauro" descritas nas seguintes secções, detete tede o cabeçalho do pedido de autorização para a sua chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Apoiar um serviço de Gestão API

Para fazer um apoio a um serviço de gestão da API, o seguinte pedido http:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

em que:

-   `subscriptionId`- Identificação da subscrição que detém o serviço de Gestão API que está a tentar fazer back-up
-   `resourceGroupName`- nome do grupo de recursos do seu serviço de Gestão API Azure
-   `serviceName`- o nome do serviço de Gestão API que está a fazer uma cópia de segurança especificada no momento da sua criação
-   `api-version`- substituir por`2018-06-01-preview`

No corpo do pedido, especifique o nome da conta de armazenamento-alvo Azure, chave de acesso, nome do recipiente blob e nome de cópia de segurança:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Desloque `Content-Type` o valor `application/json`do cabeçalho de pedido para .

Backup é uma operação de longa duração que pode demorar mais de um minuto a ser concluída. Se o pedido tiver sido bem sucedido `202 Accepted` e o processo `Location` de backup ter começado, receberá um código de estado de resposta com um cabeçalho. Faça pedidos 'GET' ao URL `Location` no cabeçalho para saber o estado da operação. Enquanto o backup está em andamento, você continua a receber um código de estado '202 Aceito'. Um código `200 OK` de resposta indica a conclusão bem sucedida da operação de cópia de segurança.

Note os seguintes constrangimentos ao efeteu um pedido de cópia de segurança ou restauro:

-   **Deve** **existir**um recipiente especificado no organismo de pedido.
-   Enquanto o backup está em andamento, **evite alterações de gestão no serviço,** como upgrade ou downgrade SKU, alteração no nome de domínio e muito mais.
-   A restauração de um **backup só é garantida por 30 dias** desde o momento da sua criação.
-   **Os dados de utilização** utilizados para a criação de relatórios de análise **não estão incluídos** na cópia de segurança. Utilize a [API de Gestão de API azure][azure api management rest api] para recuperar periodicamente relatórios de análise para a segurança.
-   Além disso, os seguintes itens não fazem parte dos dados de backup: certificados de domínio personalizado TLS/SSL e quaisquer certificados intermédios ou de raiz enviados por conteúdo de portal de cliente, desenvolvedor e definições de integração de rede virtual.
-   A frequência com que executa cópias de serviço afeta o objetivo do ponto de recuperação. Para minimizá-lo, recomendamos a implementação de backups regulares e a realização de backups a pedido após a sua alteração ao seu serviço de Gestão API.
-   **As alterações** introduzidas na configuração do serviço (por exemplo, APIs, políticas e aparência do portal do desenvolvedor) enquanto a operação de backup está em processo **pode ser excluída da cópia de segurança e será perdida**.
-   **Permita o** acesso do plano de controlo à Conta de Armazenamento Azure, se tiver [firewall][azure-storage-ip-firewall] ativado. O cliente deve abrir o conjunto de endereços IP IP do plano de controlo de [gestão da API Azure][control-plane-ip-address] na sua conta de armazenamento para backup de ou restaurar. 

> [!NOTE]
> Se tentar fazer backup/restaurar de/para um serviço de Gestão API utilizando uma conta de armazenamento que tenha [firewall][azure-storage-ip-firewall] ativada, na mesma Região Azure, então isso não funcionará. Isto porque os pedidos ao Armazenamento Azure não são SNATed para um IP público da Compute > (Azure Api Management Control Plane). O pedido de armazenamento da Região Transversal será sNATed.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Restaurar um serviço de Gestão API

Para restaurar um serviço de Gestão API a partir de uma cópia de segurança previamente criada, faça o seguinte pedido http:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

em que:

-   `subscriptionId`- Identificação da subscrição que detém o serviço de Gestão API em que está a restaurar uma cópia de segurança
-   `resourceGroupName`- nome do grupo de recursos que detém o serviço de Gestão API Azure que está a restaurar uma cópia de segurança em
-   `serviceName`- o nome do serviço de gestão da API que está a ser restabelecido no seu tempo de criação
-   `api-version`- substituir por`2018-06-01-preview`

No corpo do pedido, especifique a localização do ficheiro de reserva. Ou seja, adicione o nome da conta de armazenamento Azure, chave de acesso, nome do recipiente blob e nome de backup:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Desloque `Content-Type` o valor `application/json`do cabeçalho de pedido para .

Restaurar é uma operação de longo curso que pode demorar até 30 ou mais minutos para ser concluída. Se o pedido tiver sido bem sucedido `202 Accepted` e o processo `Location` de restauro tiver começado, receberá um código de estado de resposta com um cabeçalho. Faça pedidos 'GET' ao URL `Location` no cabeçalho para saber o estado da operação. Enquanto o restauro está em andamento, você continua a receber o código de estado '202 Aceito'. Um código `200 OK` de resposta indica a conclusão bem sucedida da operação de restauro.

> [!IMPORTANT]
> **O SKU** do serviço em que está a ser restaurado **deve corresponder** ao SKU do serviço de apoio que está a ser restaurado.
>
> **As alterações** introduzidas na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do desenvolvedor) enquanto a operação de restauro está em curso **podem ser substituídas**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> As operações de backup e restauro também podem ser realizadas com comandos PowerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) e [_Restore-AzApiManagement,_](/powershell/module/az.apimanagement/restore-azapimanagement) respectivamente.

## <a name="next-steps"></a>Passos seguintes

Confira os seguintes recursos para diferentes saídas do processo de backup/restauro.

-   [Replicar contas de gestão da API Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizar a Cópia de Segurança da Gestão de API e restaurar com o Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management: Backing Up and Restoring Configuration](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _A abordagem detalhada por Stuart não corresponde à orientação oficial, mas é interessante._

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
