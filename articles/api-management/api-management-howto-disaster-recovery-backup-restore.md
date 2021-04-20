---
title: Implementar a recuperação de desastres utilizando backup e restauro na Gestão da API
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
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: ad0936fddacf8f5b2e4917441f5feaa41aad9de4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739805"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação após desastre através do serviço de cópia de segurança e restauro na Gestão de API do Azure

Ao publicar e gerir as suas APIs através da Azure API Management, está a aproveitar a tolerância a falhas e as capacidades de infraestrutura que de outra forma conceberia, implementaria e geriria manualmente. A plataforma Azure atenua uma grande fração de potenciais falhas a uma fração do custo.

Para recuperar de problemas de disponibilidade que afetam a região que acolhe o seu serviço de Gestão de API, esteja pronto para reconstituir o seu serviço noutra região a qualquer momento. Dependendo do seu objetivo de tempo de recuperação, talvez queira manter um serviço de espera em uma ou mais regiões. Também poderá tentar manter a sua configuração e conteúdo sincronizados com o serviço ativo de acordo com o seu objetivo de ponto de recuperação. As funcionalidades de backup e restauro do serviço fornecem os blocos de construção necessários para implementar a estratégia de recuperação de desastres.

As operações de backup e restauro também podem ser usadas para replicar a configuração do serviço de gestão da API entre ambientes operacionais, por exemplo, desenvolvimento e encenação. Tenha em atenção que os dados de tempo de execução, como utilizadores e subscrições, também serão copiados, o que pode nem sempre ser desejável.

Este guia mostra como automatizar operações de backup e restauro e como garantir a autenticação bem sucedida de backup e restaurar pedidos por Azure Resource Manager.

> [!IMPORTANT]
> A operação de restauro não altera a configuração personalizada do nome de anfitrião do serviço alvo. Recomendamos a utilização do mesmo nome de anfitrião personalizado e certificado TLS para serviços ativos e de standby, de modo a que, após a operação de restauro, o tráfego possa ser redirecionado para a instância de espera através de uma simples alteração DNS CNAME.
>
> A operação de backup não captura dados de registo pré-agregados utilizados em relatórios mostrados na lâmina Analytics no portal Azure.

> [!WARNING]
> Cada cópia de segurança expira após 30 dias. Se tentar restaurar uma cópia de segurança após o prazo de validade de 30 dias, a restauração falhará com uma `Cannot restore: backup expired` mensagem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Autenticação de pedidos do Gestor de Recursos Azure

> [!IMPORTANT]
> A API REST para backup e restauro utiliza o Azure Resource Manager e tem um mecanismo de autenticação diferente dos APIs REST para gerir as suas entidades de Gestão de API. Os passos desta secção descrevem como autenticar os pedidos do Gestor de Recursos Azure. Para mais informações, consulte [pedidos autenticadores do Gestor de Recursos Azure.](/rest/api/index)

Todas as tarefas que faz sobre recursos utilizando o Gestor de Recursos Azure devem ser autenticadas com o Azure Ative Directory utilizando os seguintes passos:

-   Adicione uma aplicação ao inquilino do Azure Ative Directory.
-   Desa estada permissões para a aplicação que adicionou.
-   Obtenha o símbolo para autenticar pedidos ao Gestor de Recursos Azure.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação de Diretório Ativo Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Utilizando a subscrição que contém a sua instância de serviço de Gestão API, navegue para o separador **de registos da App** em **Azure Ative Directy** (Azure Ative Directory > Registos De Gestão/Aplicação).

    > [!NOTE]
    > Se o diretório de incumprimento do Azure Ative Não estiver visível na sua conta, contacte o administrador da subscrição Azure para conceder as permissões necessárias à sua conta.

3. Clique em **Novo registo de aplicação**.

    A janela **Create** aparece à direita. É aí que insira a aplicação AAD relevante.

4. Introduza um nome para a aplicação.
5. Para o tipo de aplicação, selecione **Native**.
6. Introduza um URL de espaço reservado, como `http://resources` para o **Redirect URI,** uma vez que é um campo obrigatório, mas o valor não é usado mais tarde. Clique na caixa de verificação para guardar a aplicação.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar uma aplicação

1. Assim que a aplicação for criada, clique nas **permissões de API**.
2. Clique **+ Adicione uma permissão.**
4. Prima **Select Microsoft APIs**.
5. Escolha **a gestão de serviços Azure.**
6. **Seleção de imprensa**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/add-app-permission.png" alt-text="Screenshot que mostra como adicionar permissões de aplicações."::: 

7. Clique em **Permissões Delegadas** ao lado da aplicação recém-adicionada, consulte a caixa para **Access Azure Service Management (pré-visualização)**.

    :::image type="content" source="./media/api-management-howto-disaster-recovery-backup-restore/delegated-app-permission.png" alt-text="Screenshot que mostra a adição de permissões de aplicações delegadas.":::

8. **Seleção de imprensa**.
9. Clique **em Adicionar Permissões**.

### <a name="configuring-your-app"></a>Configurar a aplicação

Antes de ligar para as APIs que geram a cópia de segurança e restaurá-la, precisa de obter um token. O exemplo a seguir utiliza o pacote [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet para recuperar o token.

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

Substituir `{tenant id}` `{application id}` , e utilizando as `{redirect uri}` seguintes instruções:

1. `{tenant id}`Substitua-se pelo ID do inquilino da aplicação Azure Ative Directory que criou. Pode aceder ao ID clicando nos **registos de aplicações**  ->  **Endpoints.**

    ![Pontos Finais][api-management-endpoint]

2. `{application id}`Substitua-o pelo valor que obtém navegando na página **Definições.**
3. Substitua-o `{redirect uri}` pelo valor do **separador Redirecionar URIs** da sua aplicação Azure Ative Directory.

    Uma vez especificados os valores, o exemplo de código deve devolver um símbolo semelhante ao seguinte exemplo:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > O token pode expirar após um determinado período. Execute novamente a amostra de código para gerar um novo símbolo.

## <a name="calling-the-backup-and-restore-operations"></a>Chamando as operações de backup e restauro

As APIs REST são [Api Management Service - Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) and [Api Management Service - Restore](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Antes de ligar para as operações de "backup e restauro" descritas nas seguintes secções, decreva o cabeçalho de pedido de autorização para a sua chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Apoiar um serviço de Gestão API

Para fazer o suporte de um serviço de gestão da API, o seguinte pedido HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

em que:

-   `subscriptionId` - ID da subscrição que detém o serviço de Gestão API que está a tentar apoiar
-   `resourceGroupName` - nome do grupo de recursos do seu serviço de Gestão API da Azure
-   `serviceName` - o nome do serviço de Gestão da API que está a fazer uma cópia de segurança especificada no momento da sua criação
-   `api-version` - substituir por `2019-12-01`

No corpo do pedido, especifique o nome da conta de armazenamento target Azure, a chave de acesso, o nome do recipiente blob e o nome de reserva:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Desa estada o valor do cabeçalho do `Content-Type` pedido para `application/json` .

A cópia de segurança é uma operação de longa duração que pode demorar mais de um minuto a ser concluída. Se o pedido tiver sido bem sucedido e o processo de backup tiver começado, receberá um `202 Accepted` código de estado de resposta com um `Location` cabeçalho. Faça pedidos 'GET' à URL no `Location` cabeçalho para saber o estado da operação. Enquanto a cópia de segurança está em andamento, continua a receber um código de estado '202 Aceitos'. Um código de resposta `200 OK` indica a conclusão bem sucedida da operação de backup.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Restaurar um serviço de Gestão API

Para restaurar um serviço de Gestão API a partir de uma cópia de segurança previamente criada, faça o seguinte pedido HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

em que:

-   `subscriptionId` - ID da subscrição que detém o serviço de Gestão API em que está a restaurar uma cópia de segurança
-   `resourceGroupName` - nome do grupo de recursos que detém o serviço de Gestão API da Azure em que está a restaurar uma cópia de segurança
-   `serviceName` - o nome do serviço de Gestão da API que está a ser restaurado no momento da sua criação
-   `api-version` - substituir por `api-version=2019-12-01`

No corpo do pedido, especifique a localização do ficheiro de reserva. Ou seja, adicione o nome da conta de armazenamento Azure, a chave de acesso, o nome do recipiente blob e o nome de reserva:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Desa estada o valor do cabeçalho do `Content-Type` pedido para `application/json` .

Restaurar é uma operação de longa duração que pode demorar até 30 minutos a ser concluída. Se o pedido tiver sido bem sucedido e o processo de restauro tiver começado, receberá um `202 Accepted` código de estado de resposta com um `Location` cabeçalho. Faça pedidos 'GET' à URL no `Location` cabeçalho para saber o estado da operação. Enquanto a restauração está em curso, você continua a receber o código de estado '202 Aceitos'. Um código de resposta `200 OK` indica a conclusão bem sucedida da operação de restauro.

> [!IMPORTANT]
> **O SKU** do serviço que está a ser restaurado **deve coincidir com** o SKU do serviço de apoio que está a ser restaurado.
>
> **As alterações efetuadas** na configuração do serviço (por exemplo, APIs, políticas, aparência do portal do programador) enquanto a operação de restauro está em curso **podem ser substituídas**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> As operações de backup e restauro também podem ser realizadas com comandos powerShell [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) e [_Restore-AzApiManagement,_](/powershell/module/az.apimanagement/restore-azapimanagement) respectivamente.

## <a name="constraints-when-making-backup-or-restore-request"></a>Constrangimentos ao fazer backup ou restaurar o pedido

-   **Deve** **existir** o recipiente especificado no organismo de pedido .
-   Enquanto a cópia de segurança está em andamento, **evite alterações de gestão no serviço,** tais como upgrade ou downgrade SKU, mudança no nome de domínio, e muito mais.
-   A recuperação de um **backup só é garantida por 30 dias** desde o momento da sua criação.
-   **As alterações efetuadas** na configuração do serviço (por exemplo, APIs, políticas e aparecimento do portal do programador) enquanto a operação de backup está em processo **podem ser excluídas da cópia de segurança e serão perdidas**.
-   Se a Conta de Armazenamento Azure [estiver][azure-storage-ip-firewall] ativada, o cliente deve **permitir** o conjunto de endereços IP do Avião ip [do Azure API Management Control][control-plane-ip-address] Na sua Conta de Armazenamento para Cópia de Segurança ou Restauro do trabalho. A Conta de Armazenamento Azure pode estar em qualquer Região de Azure, exceto aquela onde está localizado o serviço de Gestão da API. Por exemplo, se o serviço de Gestão API estiver no Oeste dos EUA, então a Conta de Armazenamento Azure pode estar no West US 2 e o cliente precisa de abrir o plano de controlo IP 13.64.39.16 (API Management Control Plane IP of West US) na firewall. Isto porque os pedidos para o Azure Storage não são SNATed para um IP público da Compute (Azure Api Management Control Plane) na mesma Região de Azure. O pedido de armazenamento da Região Cruzada será SNATED para o Endereço IP Público.
-   [A partilha de recursos de origem cruzada (CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) **não** deve ser ativada no Serviço Blob na Conta de Armazenamento Azure.
-   **O SKU** do serviço que está a ser restaurado **deve coincidir com** o SKU do serviço de apoio que está a ser restaurado.

## <a name="what-is-not-backed-up"></a>O que não é apoiado
-   **Os dados de utilização utilizados** para criar relatórios de análise **não estão incluídos** na cópia de segurança. Utilize [a Azure API Management REST API][azure api management rest api] para recuperar periodicamente relatórios de análise para segurança.
-   [Certificados TLS/SSL de domínio personalizado.](configure-custom-domain.md)
-   [Certificado AC personalizado,](api-management-howto-ca-certificates.md)que inclui certificados intermédios ou de raiz carregados pelo cliente.
-   [Definições de](api-management-using-with-vnet.md) integração de rede virtual.
-   [Configuração de identidade gerida.](api-management-howto-use-managed-service-identity.md)
-   [Diagnóstico do Monitor Azure](api-management-howto-use-azure-monitor.md) A configuração.
-   Protocolos e definições [de Cipher.](api-management-howto-manage-protocols-ciphers.md)
-   [Conteúdo do portal do desenvolvedor.](developer-portal-faq.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management)

A frequência com que executa cópias de segurança de serviço afeta o seu objetivo de ponto de recuperação. Para minimizá-lo, recomendamos a implementação de backups regulares e a realização de backups a pedido depois de efetuar alterações no seu serviço de Gestão API.

## <a name="next-steps"></a>Passos seguintes

Confira os seguintes recursos para diferentes passos do processo de backup/restauro.

-   [Replicar Contas de Gestão da API da AZure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automatizar a Cópia de Segurança da Gestão de API e restaurar com o Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Gestão AZURE API: Configuração](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration) 
     de backup e restauro _A abordagem detalhada por Stuart não corresponde à orientação oficial, mas é interessante._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
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
