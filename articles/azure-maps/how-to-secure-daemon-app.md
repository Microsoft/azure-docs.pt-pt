---
title: Como proteger uma aplicação daemon
titleSuffix: Azure Maps
description: Utilize o portal Azure para gerir a autenticação para configurar uma aplicação de confiança.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895652"
---
# <a name="secure-a-daemon-application"></a>Garantir uma aplicação daemon

O seguinte guia é para processos de fundo, temporizadores e empregos que são hospedados num ambiente confiável e seguro. Exemplos incluem Azure Web Jobs, Azure Function Apps, Windows Services e qualquer outro serviço de fundo confiável.

> [!Tip]
> A Microsoft recomenda a implementação do Azure Ative Directory (Azure AD) e do controlo de acesso baseado em funções Azure (Azure RBAC) para aplicações de produção. Para uma visão geral dos conceitos, consulte [a Autenticação Azure Maps.](./azure-maps-authentication.md)

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Cenário: Autenticação de chave partilhada

Depois de criar uma conta Azure Maps, as teclas primárias e secundárias são geradas. Recomendamos que utilize a chave primária como chave de subscrição quando [utilizar a autenticação de chave partilhada para ligar para Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Pode utilizar uma chave secundária em cenários como alterações de teclas rolantes. Para mais informações, consulte [autenticação no Azure Maps.](./azure-maps-authentication.md)

### <a name="securely-store-shared-key"></a>Armazenar de forma segura a chave partilhada

A chave primária e secundária permite a autorização a todas as APIs para a conta Maps. As aplicações devem armazenar as chaves numa loja segura, como o Azure Key Vault. A aplicação deve recuperar a chave partilhada como um segredo do Azure Key Vault para evitar armazenar a chave partilhada em texto simples na configuração da aplicação. Para entender como configurar um Cofre de Chaves Azure, consulte o [guia de desenvolvimento do Azure Key Vault](../key-vault/general/developers-guide.md).

Os seguintes passos descrevem este processo:

1. Crie um cofre de chaves Azure.
2. Criar um principal de serviço Azure AD criando um registo de App ou identidade gerida, o principal criado é responsável por aceder ao Azure Key Vault.
3. Atribua o acesso principal do serviço à permissão de segredos da Chave `Get` Azure.
4. Atribua temporariamente acesso a `Set` segredos permissão para si como desenvolvedor.
5. Desenr considere a chave partilhada nos segredos do Cofre de Chaves e refira o ID secreto como configuração para a aplicação Daemon e remova a permissão dos seus `Set` segredos.
6. Implemente a autenticação Azure AD na aplicação Daemon para recuperar o segredo de chave compartilhado do Cofre da Chave Azure.
7. Crie pedido de API AZure Maps REST com chave partilhada.

> [!Tip]
> Se a aplicação estiver hospedada em ambiente Azure, deverá implementar uma Identidade Gerida para reduzir o custo e a complexidade de gerir um segredo para autenticar para a Azure Key Vault. Consulte o seguinte tutorial do Azure Key Vault [para ligar através de identidade gerida](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

A aplicação Daemon é responsável por recuperar a chave partilhada de um armazenamento seguro. A implementação com o Azure Key Vault requer autenticação através da Azure AD para aceder ao segredo. Em vez disso, encorajamos a autenticação direta do AZure AD ao Azure Maps como resultado da complexidade adicional e requisitos operacionais da utilização da autenticação de chaves partilhadas.

> [!IMPORTANT]
> Para simplificar a regeneração das chaves, recomendamos que as aplicações utilizem uma chave de cada vez. As aplicações podem então regenerar a chave não utilizada e implantar a nova chave regenerada para uma loja secreta segura, como o Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Cenário: Controlo de acesso baseado em funções da AZure AD

Uma vez criada uma conta Azure Maps, o valor Azure Maps `x-ms-client-id` está presente na página de detalhes de autenticação do portal Azure. Este valor representa a conta que será utilizada para pedidos de API REST. Este valor deve ser armazenado na configuração da aplicação e recuperado antes de estoitados https. O objetivo do cenário é permitir que a aplicação da daemon autente para Azure AD e chamar Azure Maps REST APIs.

> [!Tip]
> Recomendamos hospedar em Máquinas Virtuais Azure, Conjuntos de Escala de Máquinas Virtuais ou Serviços de Aplicação para permitir benefícios de componentes de identidade gerida.

### <a name="daemon-hosted-on-azure-resources"></a>Daemon acolheu os recursos da Azure

Ao executar os recursos Azure, configurar identidades geridas pela Azure para permitir um esforço de gestão de credencial de baixo custo e mínimo. 

Consulte [a visão geral das identidades geridas](../active-directory/managed-identities-azure-resources/overview.md) para permitir o acesso da aplicação a uma Identidade Gerida.

Benefícios de identidade gerida:

* O sistema Azure geriu a autenticação da criptografia de chaves públicas X509.
* Segurança Azure AD com certificados X509 em vez de segredos de cliente.
* A Azure gere e renova todos os certificados associados ao recurso Identidade Gerida.
* Gestão operacional de credenciais simplificada removendo qualquer necessidade de um serviço de loja secreta seguro como Azure Key Vault. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Daemon hospedado em recursos não-Azure

Quando funciona num ambiente não-Azure As identidades geridas não estão disponíveis. Por isso, deve configurar um comitado através de um registo de pedido azure AD para o pedido da Daemon.

1. No portal Azure, na lista de serviços da Azure, selecione **Azure Ative Directory**  >  **App registra**  >  **Novas inscrições** .  

    > [!div class="mx-imgBorder"]
    > ![Registo da aplicação](./media/how-to-manage-authentication/app-registration.png)

2. Se já registou a sua aplicação, então continue para o próximo passo. Se ainda não registou a sua aplicação, insira um **Nome,** escolha um **tipo de conta de Suporte** e, em seguida, selecione **Registar.»**  

    > [!div class="mx-imgBorder"]
    > ![Detalhes do registo da aplicação](./media/how-to-manage-authentication/app-create.png)

3. Para atribuir permissões delegadas da API ao Azure Maps, aceda à aplicação. Em seguida, nos **registos da App,** selecione **permissões API**  >  **Adicione uma permissão** . Nos **APIs a minha organização utiliza,** procure e selecione **Azure Maps.**

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

4. Selecione a caixa de verificação ao lado **do Access Azure Maps** e, em seguida, selecione **Permissões de adicionar** .

    > [!div class="mx-imgBorder"]
    > ![Selecione permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

5. Complete os seguintes passos para criar um certificado secreto ou de configuração do cliente.

    * Se a sua aplicação utilizar a autenticação do servidor ou da aplicação, então na página de registo da sua aplicação, aceda aos **segredos & certificados.** Em seguida, faça o upload de um certificado de chave pública ou crie uma palavra-passe selecionando **o novo segredo do cliente** .

        > [!div class="mx-imgBorder"]
        > ![Criar um segredo de cliente](./media/how-to-manage-authentication/app-keys.png)

    * Depois de selecionar **Adicionar,** copie o segredo e guarde-o de forma segura num serviço como o Azure Key Vault. Reveja [o Guia de Desenvolvimento do Cofre de Chaves Azure](../key-vault/general/developers-guide.md) para armazenar o certificado ou segredo de forma segura. Vais usar este segredo para obter fichas do Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Adicione um segredo de cliente](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Conceder acesso baseado em funções para a aplicação da Daemon ao Azure Maps

Você concede *ao Azure um controlo de acesso baseado em funções (Azure RBAC)* atribuindo a identidade gerida criada ou o principal de serviço a uma ou mais definições de funções Azure Maps. Para ver as definições de função Azure que estão disponíveis para O Azure Maps, vá ao **Controlo de Acesso (IAM)** . Selecione **Roles** , e, em seguida, procure por papéis que comecem com *Azure Maps* . Estas funções do Azure Maps são as funções a que podes conceder acesso.

> [!div class="mx-imgBorder"]
> ![Ver funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Aceda à sua **Conta Azure Maps.** Selecione **Controlo de acesso (IAM)** > **Atribuições de funções** .

    > [!div class="mx-imgBorder"]
    > ![Conceder acesso usando Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. No **separador role assignments,** **Adicione** uma tarefa de função. 

    > [!div class="mx-imgBorder"]
    > ![A screenshot mostra as atribuições de rolo com Add selecionado.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Selecione uma definição de função Azure Maps incorporada, como O Leitor de **Dados do Azure Maps** ou **Colaborador de Dados do Azure Maps.** No **acesso de Atribuição a** , selecione **Azure AD utilizador, grupo ou principal de serviço** ou identidade gerida com o Sistema de identidade gerido atribuído pelo **Utilizador.**  /  **System assigned Managed identity** Selecione o principal. Em seguida, selecione **Guardar** .

    > [!div class="mx-imgBorder"]
    > ![Como adicionar atribuição de funções](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Pode confirmar que a atribuição de funções foi aplicada no separador de atribuição de funções.

## <a name="request-token-with-managed-identity"></a>Pedido de token com identidade gerida

Uma vez configurada uma identidade gerida para o recurso de hospedagem, utilize a Azure SDK ou a REST API para adquirir um símbolo para o Azure Maps, consulte detalhes sobre [adquirir um token de acesso](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md). Seguindo o guia, a expectativa é que seja devolvido um token de acesso que pode ser usado em pedidos de API REST.

## <a name="request-token-with-application-registration"></a>Pedido de token com registo de pedido

Depois de registar a sua aplicação e associá-la ao Azure Maps, pode solicitar fichas de acesso.

* ID de recurso Azure `https://atlas.microsoft.com/`
* Azure AD App ID
* ID de Inquilino do Azure AD
* Segredo de cliente de registo da App Azure AD

Pedido:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Resposta:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Consulte [os cenários de autenticação para Azure AD,](../active-directory/develop/authentication-vs-authorization.md)para obter exemplos mais detalhados.

## <a name="next-steps"></a>Passos seguintes

Encontre as métricas de utilização da API para a sua conta Azure Maps:
> [!div class="nextstepaction"]
> [Ver métricas de utilização](how-to-view-api-usage.md)

Explore amostras que mostram como integrar a Azure AD com Azure Maps:
> [!div class="nextstepaction"]
> [Amostras de Azure Maps](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)