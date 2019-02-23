---
title: Através da recuperação de desastres de implementar a cópia de segurança e restaurar na gestão de API do Azure | Documentos da Microsoft
description: Saiba como utilizar a cópia de segurança e restaurar para efetuar a recuperação após desastre na gestão de API do Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 56d0b8ced4a0eed3c2bf215ed0e5fc77c343f7fd
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56728646"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Como implementar a recuperação após desastre com o serviço backup e restaurar na gestão de API do Azure

Ao publicar e gerir as suas APIs através da gestão de API do Azure, está aproveitando a tolerância a falhas e os recursos de infraestrutura que tinha o design, caso contrário, implementar e gerir manualmente. A plataforma do Azure reduz uma grande parte de potenciais falhas por uma fração do custo.

Para recuperar os problemas de disponibilidade que afetam a região que aloja o seu serviço de gestão de API, esteja pronto para reconstituir seu serviço noutra região a qualquer momento. Dependendo da sua disponibilidade e os objetivos de tempo de recuperação, pode querer reservar um serviço de cópia de segurança num ou mais regiões. Também pode tentar manter a respetiva configuração e conteúdo em sincronia com o serviço do Active Directory. A funcionalidade de serviço "cópia de segurança e restaurar" fornece o bloco de construção necessário para implementar a sua estratégia de recuperação após desastre.

Este guia mostra como autenticar os pedidos do Azure Resource Manager. Ela também mostra como criar cópias de segurança e restaurar as instâncias de serviço de gestão de API.

> [!NOTE]
> O processo para criar cópias de segurança e restaurar uma instância de serviço de gestão de API para recuperação após desastre também pode ser utilizado para replicar as instâncias do serviço de gestão de API para cenários como a transição.
>
> Cada cópia de segurança expira após 30 dias. Se tentar restaurar uma cópia de segurança após a expiração do período de expiração de 30 dias, o restauro falhará com uma `Cannot restore: backup expired` mensagem.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Pedidos de autenticação do Azure Resource Manager

> [!IMPORTANT]
> A API REST para cópia de segurança e restauro utiliza o Azure Resource Manager e tem um mecanismo de autenticação diferentes que as APIs REST para gerir as entidades de gestão de API. Os passos nesta secção descrevem como autenticar os pedidos do Azure Resource Manager. Para obter mais informações, consulte [pedidos de autenticação do Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790557.aspx).

Todas as tarefas que fazer em recursos com o Azure Resource Manager tem de ser autenticadas com o Azure Active Directory com os seguintes passos:

* Adicione uma aplicação para o inquilino do Azure Active Directory.
* Definir permissões para a aplicação que foi adicionado.
* Obter o token para autenticar pedidos para o Azure Resource Manager.

### <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Com a subscrição que contém a sua instância do serviço de gestão de API, navegue para o **registos de aplicações** separador **Azure Active Directory** (Azure Active Directory > registos de aplicações/gerir).

    > [!NOTE]
    > Se o diretório predefinido do Azure Active Directory não estiver visível à sua conta, contacte o administrador da subscrição do Azure para conceder as permissões necessárias para a sua conta.
3. Clique em **Novo registo de aplicação**.

    O **criar** janela aparece à direita. Que é onde introduz as informações relevantes da aplicação AAD.
4. Introduza um nome para a aplicação.
5. Para o tipo de aplicação, selecione **nativo**.
6. Introduza um URL de marcador de posição, como `http://resources` para o **URI de redirecionamento**, conforme é um campo obrigatório, mas não é utilizado o valor mais tarde. Clique na caixa de verificação para guardar a aplicação.
7. Clique em **Criar**.

### <a name="add-an-application"></a>Adicionar uma aplicação

1. Assim que o aplicativo for criado, clique em **definições**.
2. Clique em **permissões obrigatórias**.
3. Clique em **+ adicionar**.
4. Prima **selecionar uma API**.
5. Escolher **Windows** **API de gestão de serviço do Azure**.
6. Prima **selecione**.

    ![Adicionar permissões](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Clique em **permissões delegadas** junto a aplicação recentemente adicionada, marque a caixa **gestão de serviço de acesso do Azure (pré-visualização)**.
8. Prima **selecione**.
9. Clique em **conceder permissões**.

### <a name="configuring-your-app"></a>Configurar a sua aplicação

Antes de chamar as APIs que gerar a cópia de segurança e restaurá-lo, terá de obter um token. O exemplo seguinte utiliza a [ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) pacote NuGet para obter o token.

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

Substitua `{tenant id}`, `{application id}`, e `{redirect uri}` com as instruções seguintes:

1. Substitua `{tenant id}` com o ID de inquilino da aplicação do Azure Active Directory que criou. Pode acessar o ID de clicando **registos de aplicações** -> **pontos finais**.

    ![Pontos Finais][api-management-endpoint]
2. Substitua `{application id}` com o valor que obtém ao navegar para o **definições** página.
3. Substitua a `{redirect uri}` com o valor a partir do **URIs de redirecionamento** separador da sua aplicação do Azure Active Directory.

    Depois dos valores são especificados, o exemplo de código deverá devolver um token semelhante ao seguinte exemplo:

    ![Certificado de][api-management-arm-token]

    > [!NOTE]
    > O token poderão expirar após um determinado período. Execute o exemplo de código novamente para gerar um novo token.

## <a name="calling-the-backup-and-restore-operations"></a>Chamar as operações de cópia de segurança e restauro

As APIs REST são [serviço de gestão de Api - cópia de segurança](/rest/api/apimanagement/apimanagementservice/backup) e [serviço de gestão de Api - restauro](/rest/api/apimanagement/apimanagementservice/restore).

Antes de chamar as operações de "cópia de segurança e restauro" descritas nas seções a seguir, defina o cabeçalho do pedido de autorização para a chamada REST.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>Fazer cópias de segurança de um serviço de gestão de API

Para fazer o backup de um problema do serviço de gestão de API da seguinte solicitação HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Em que:

* `subscriptionId` -ID da subscrição que contém o serviço de gestão de API que está a tentar criar cópias de segurança
* `resourceGroupName` -nome do grupo de recursos do seu serviço de gestão de API do Azure
* `serviceName` -o nome do serviço de gestão de API que faz uma cópia de segurança especificado no momento da criação
* `api-version` -Substituir por `2018-06-01-preview`

No corpo do pedido, especifique o nome de conta de armazenamento do Azure de destino, a chave de acesso, o nome do contentor de blob e o nome de cópia de segurança:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Defina o valor do `Content-Type` cabeçalho do pedido para `application/json`.

Cópia de segurança é uma operação de longa execução que pode demorar mais de um minuto para concluir.  Se o pedido foi concluída com êxito e o processo de cópia de segurança começou, irá receber uma `202 Accepted` código de estado de resposta com um `Location` cabeçalho.  Tornar "GET" pedidos para o URL no `Location` cabeçalho para saber o estado da operação. Enquanto a cópia de segurança está em curso, continua a receber um código de estado de "202 aceite". Um código de resposta de `200 OK` indica a conclusão com êxito da operação de cópia de segurança.

Quando efetua um pedido de cópia de segurança, tenha em atenção as seguintes restrições:

* **Contentor** especificada no corpo do pedido **tem de existir**.
* Enquanto a cópia de segurança está em curso **evitar alterações na gestão de serviço** como atualização SKU ou mudar, alterar em nome de domínio e muito mais.
* Restauro de uma **cópia de segurança é garantida apenas durante 30 dias** desde o momento da criação.
* **Dados de utilização** utilizado para a criação de relatórios de análise **não está incluído** na cópia de segurança. Uso [API de REST de gestão de API do Azure] [ Azure API Management REST API] periodicamente obter relatórios de análise para salvaguarda.
* A frequência com que realizar cópias de segurança do serviço afetam o objetivo de ponto de recuperação. Para minimizar ele, é recomendável implementar cópias de segurança regulares e efetuar cópias de segurança a pedido depois de efetuar alterações ao seu serviço de gestão de API.
* **As alterações** feitas à configuração do serviço, (por exemplo, APIs, políticas e aspeto do portal do programador) durante a cópia de segurança operação está em curso **podem ser excluídos da cópia de segurança e serão perdidas**.

### <a name="step2"> </a>Restaurar um serviço de gestão de API

Para restaurar um serviço de gestão de API de uma cópia de segurança criada anteriormente, fazer o seguinte pedido HTTP:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Em que:

* `subscriptionId` -ID da subscrição que contém o serviço de gestão de API que está a restaurar uma cópia de segurança em
* `resourceGroupName` -nome do grupo de recursos que contém o serviço de gestão de API do Azure que está a restaurar uma cópia de segurança em
* `serviceName` -o nome da gestão de API de serviço que está a ser restaurado na especificado em seu tempo de criação
* `api-version` -Substituir por `2018-06-01-preview`

No corpo do pedido, especifique a localização do ficheiro de cópia de segurança. Ou seja, adicione o nome da conta de armazenamento do Azure, a chave de acesso, o nome do contentor de blob e o nome de cópia de segurança:

```json
{
  "storageAccount": "{storage account name for the backup}",
  "accessKey": "{access key for the account}",
  "containerName": "{backup container name}",
  "backupName": "{backup blob name}"
}
```

Defina o valor do `Content-Type` cabeçalho do pedido para `application/json`.

Restauro é uma operação de longa execução que poderá demorar até 30 ou mais minutos a concluir. Se o pedido foi concluída com êxito e o processo de restauração começou, irá receber uma `202 Accepted` código de estado de resposta com um `Location` cabeçalho. Tornar "GET" pedidos para o URL no `Location` cabeçalho para saber o estado da operação. Enquanto o restauro estiver em curso, continua a receber "aceite 202" código de estado. Um código de resposta de `200 OK` indica a conclusão com êxito da operação de restauro.

> [!IMPORTANT]
> **O SKU** do serviço que está a ser restaurado na **tem de corresponder ao** o SKU do serviço de cópia de segurança a ser restaurado.
>
> **As alterações** efetuadas à configuração do serviço (por exemplo, APIs, as políticas, o aspeto do portal do programador) ao restauro operação está em curso **podem ser substituídos**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Operações de backup e restauração também podem ser realizadas com o PowerShell *AzApiManagement de cópia de segurança* e *restauro AzApiManagement* comandos, respetivamente.

## <a name="next-steps"></a>Passos Seguintes

Confira os seguintes recursos para diferente orientações passo a passo do processo de cópia de segurança/restauro.

* [Replicar contas de gestão de API do Azure](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
* [Automatizando cópia de segurança de gestão de API e restauro com o Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
* [Gestão de API do Azure: Efetuar e restaurar Configuration](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  *a abordagem mais, Stuart não coincide com as diretrizes oficiais, mas é interessante.*

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2

[Azure API Management REST API]: https://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
