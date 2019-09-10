---
title: Implantar a visualização do Azure Blockchain Workbench
description: Como implantar a visualização do Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 2ea18c784c6b5cf61013c131360d20349e67b1e5
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845274"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Implantar a visualização do Azure Blockchain Workbench

A versão prévia do Azure Blockchain Workbench é implantada usando um modelo de solução no Azure Marketplace. O modelo simplifica a implantação de componentes necessários para criar aplicativos blockchain. Depois de implantado, o Blockchain Workbench fornece acesso a aplicativos cliente para criar e gerenciar usuários e aplicativos Blockchain.

Para obter mais informações sobre os componentes do Blockchain Workbench, consulte [arquitetura do Azure Blockchain Workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Preparar para a implementação

O Blockchain Workbench permite que você implante um Blockchain Ledger junto com um conjunto de serviços relevantes do Azure mais frequentemente usado para criar um aplicativo baseado em Blockchain. A implantação do Blockchain Workbench resulta nos seguintes serviços do Azure sendo provisionados em um grupo de recursos em sua assinatura do Azure.

* Plano do serviço de aplicativo (padrão)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* Banco de dados SQL (Standard S0) + servidor lógico do SQL
* Conta de armazenamento do Azure (Standard LRS)
* Conjunto de dimensionamento de máquinas virtuais com capacidade de 1
* Grupo de recursos de rede virtual (com Load Balancer, grupo de segurança de rede, endereço IP público, rede virtual)
* Serviço Blockchain do Azure. Se você estiver usando uma implantação anterior do Blockchain Workbench, considere reimplantar o Azure Blockchain Workbench para usar o serviço Blockchain do Azure.

Veja a seguir um exemplo de implantação criada no grupo de recursos **myblockchain** .

![Exemplo de implantação](media/deploy/example-deployment.png)

O custo do Blockchain Workbench é uma agregação do custo dos serviços do Azure subjacentes. As informações de preço para os serviços do Azure podem ser calculadas usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Pré-requisitos

O Azure Blockchain Workbench requer a configuração do Azure AD e os registros do aplicativo. Você pode optar por fazer as configurações do Azure AD [manualmente](#azure-ad-configuration) antes da implantação ou executar um script após a implantação. Se você estiver Reimplantando o Blockchain Workbench, consulte [configuração do Azure ad](#azure-ad-configuration) para verificar sua configuração do Azure AD.

> [!IMPORTANT]
> O Workbench não precisa ser implantado no mesmo locatário que aquele que você está usando para registrar um aplicativo do Azure AD. O Workbench deve ser implantado em um locatário em que você tenha permissões suficientes para implantar recursos. Para obter mais informações sobre locatários do Azure AD, consulte [como obter um locatário Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [integrar aplicativos com Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Implantar o Blockchain Workbench

Depois que as etapas de pré-requisito forem concluídas, você estará pronto para implantar o Blockchain Workbench. As seções a seguir descrevem como implantar a estrutura.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD desejado no qual você deseja implantar o Azure Blockchain Workbench.
3. No painel esquerdo, selecione **criar um recurso**. Pesquise na barra de pesquisa **do Marketplace.** `Azure Blockchain Workbench` 

    ![Barra de pesquisa do Marketplace](media/deploy/marketplace-search-bar.png)

4. Selecione **Azure Blockchain Workbench**.

    ![Resultados da pesquisa do Marketplace](media/deploy/marketplace-search-results.png)

5. Selecione **Criar**.
6. Conclua as configurações básicas.

    ![Criar o Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Definição | Descrição  |
    |---------|--------------|
    | Prefixo de recurso | Identificador exclusivo curto para sua implantação. Esse valor é usado como base para a nomenclatura de recursos. |
    | Nome de usuário da VM | O nome de usuário é usado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação | Selecione se você deseja usar uma senha ou chave para se conectar a VMs. |
    | Palavra-passe | A senha é usada para conectar-se às VMs. |
    | SSH | Use uma chave pública RSA no formato de linha única começando com **SSH-RSA** ou use o formato PEM de várias linhas. Você pode gerar chaves SSH usando `ssh-keygen` o no Linux e o os X, ou usando PuTTYGen no Windows. Para obter mais informações sobre chaves SSH, consulte [como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Banco de dados e senha de Blockchain | Especifique a senha a ser usada para acessar o banco de dados criado como parte da implantação. A senha deve atender a três dos quatro requisitos a seguir: o comprimento precisa ter entre 12 & 72 caracteres, um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial que não seja um sinal numérico (#), porcentagem (%), vírgula (,), estrela (*), aspas de fundo (\`), aspas duplas ("), aspas simples ('), Dash (-) e semicolumn (;) |
    | Região de implantação | Especifique onde implantar os recursos do Blockchain Workbench. Para obter a melhor disponibilidade, isso deve corresponder à configuração de **local** . |
    | Subscription | Especifique a assinatura do Azure que você deseja usar para a implantação. |
    | Grupos de recursos | Crie um novo grupo de recursos selecionando **criar novo** e especifique um nome de grupo de recursos exclusivo. |
    | Location | Especifique a região em que você deseja implantar a estrutura. |

7. Selecione **OK** para concluir a seção configuração de configuração básica.

8. Em **Configurações avançadas**, escolha se deseja criar uma nova rede blockchain ou usar uma rede blockchain de prova de autoridade existente.

    Para **criar novo**:

    A opção *criar nova* implanta um razão de quorum do serviço Blockchain do Azure com o SKU básico padrão.

    ![Configurações avançadas para nova rede blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Definição | Descrição  |
    |---------|--------------|
    | Tipo de preço do serviço Blockchain do Azure | Escolha a camada de serviço do Azure Blockchain **básica** ou **padrão** que é usada para o Blockchain Workbench |
    | Configurações de Azure Active Directory | Escolha **adicionar mais tarde**.</br>Nota: Se você optar por [pré-configurar o Azure ad](#azure-ad-configuration) ou se estiver reimplantando, opte por *Adicionar agora*. |
    | Seleção de VM | Selecione o desempenho de armazenamento preferencial e o tamanho da VM para sua rede blockchain. Escolha um tamanho de VM menor, como o *DS1 v2 padrão* se você estiver em uma assinatura com limites de serviço baixos, como a camada gratuita do Azure. |

    Para **uso existente**:

    A opção *usar existente* permite que você especifique uma rede Blockchain de Ethereum (prova de autoridade) de POA. Os pontos de extremidade têm os seguintes requisitos.

   * O ponto de extremidade deve ser uma rede blockchain PoA (prova de autoridade Ethereum).
   * O ponto de extremidade deve ser publicamente acessível pela rede.
   * A rede PoA blockchain deve ser configurada para ter o preço de gás definido como zero.

     > [!NOTE]
     > As contas do Blockchain Workbench não são financiadas. Se forem necessários fundos, as transações falharão.

     ![Configurações avançadas para a rede blockchain existente](media/deploy/advanced-blockchain-settings-existing.png)

     | Definição | Descrição  |
     |---------|--------------|
     | Ponto de extremidade RPC Ethereum | Forneça o ponto de extremidade RPC de uma rede PoA blockchain existente. O ponto de extremidade começa com https://ou http://e termina com um número de porta. Por exemplo, `http<s>://<network-url>:<port>` |
     | Configurações de Azure Active Directory | Escolha **adicionar mais tarde**.</br>Nota: Se você optar por [pré-configurar o Azure ad](#azure-ad-configuration) ou se estiver reimplantando, opte por *Adicionar agora*. |
     | Seleção de VM | Selecione o desempenho de armazenamento preferencial e o tamanho da VM para sua rede blockchain. Escolha um tamanho de VM menor, como o *DS1 v2 padrão* se você estiver em uma assinatura com limites de serviço baixos, como a camada gratuita do Azure. |

9. Selecione **OK** para concluir as configurações avançadas.

10. Examine o resumo para verificar se os parâmetros são precisos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

11. Selecione **criar** para concordar com os termos e implantar o Azure Blockchain Workbench.

A implantação pode levar até 90 minutos. Você pode usar o portal do Azure para monitorar o progresso. No grupo de recursos recém-criado, selecione **Implantações > visão geral** para ver o status dos artefatos implantados.

> [!IMPORTANT]
> Após a implantação, você precisa concluir Active Directory configurações. Se você escolheu **adicionar mais tarde**, precisará executar o [script de configuração do Azure ad](#azure-ad-configuration-script).  Se você escolheu **Adicionar agora**, precisará [Configurar a URL de resposta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>URL da Web do Blockchain Workbench

Depois que a implantação do Blockchain Workbench for concluída, um novo grupo de recursos conterá os recursos do Blockchain Workbench. Os serviços do Blockchain Workbench são acessados por meio de uma URL da Web. As etapas a seguir mostram como recuperar a URL da Web da estrutura implantada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel de navegação à esquerda, selecione **grupos de recursos**
3. Escolha o nome do grupo de recursos que você especificou ao implantar o Blockchain Workbench.
4. Selecione o título da coluna **tipo** para classificar a lista alfabeticamente por tipo.
5. Há dois recursos com o tipo **serviço de aplicativo**. Selecione o recurso do tipo **serviço de aplicativo** *sem* o sufixo "-API".

    ![Lista de serviço de aplicativo](media/deploy/resource-group-list.png)

6. Na seção **Essentials** do serviço de aplicativo, copie o valor da **URL** , que representa a URL da Web para o Blockchain Workbench implantado.

    ![Conceitos básicos do serviço de aplicativo](media/deploy/app-service.png)

Para associar um nome de domínio personalizado ao Blockchain Workbench, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no serviço de Azure App usando o Gerenciador de tráfego](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Script de configuração do Azure AD

O Azure AD deve ser configurado para concluir a implantação do Blockchain Workbench. Você usará um script do PowerShell para fazer a configuração.

1. Em um navegador, navegue até a [URL da Web do Blockchain Workbench](#blockchain-workbench-web-url).
2. Você verá instruções para configurar o Azure AD usando o Cloud Shell. Copie o comando e inicie o Cloud Shell.

    ![Iniciar script do AAD](media/deploy/launch-aad-script.png)

3. Escolha o locatário do Azure AD no qual você implantou o Blockchain Workbench.
4. Em Cloud Shell, Cole e execute o comando.
5. Quando solicitado, insira o locatário do Azure AD que você deseja usar para o Blockchain Workbench. Esse será o locatário que contém os usuários do Blockchain Workbench.

    > [!IMPORTANT]
    > O usuário autenticado requer permissões para criar registros de aplicativo do Azure AD e conceder permissões de aplicativo delegado no locatário. Talvez seja necessário solicitar que um administrador do locatário execute o script de configuração do Azure AD ou crie um novo locatário.

    ![Inserir locatário do Azure AD](media/deploy/choose-tenant.png)

6. Você será solicitado a autenticar o locatário do Azure AD usando um navegador. Abra a URL da Web em um navegador, insira o código e autentique-o.

    ![Autenticar com código](media/deploy/authenticate.png)

7. O script gera várias mensagens de status. Você receberá uma mensagem de status de **êxito** se o locatário tiver sido provisionado com êxito.
8. Navegue até a URL do Blockchain Workbench. Você será solicitado a consentir para conceder permissões de leitura ao diretório. Isso permite que o aplicativo Web do Blockchain Workbench acesse os usuários no locatário. Se você for o administrador de locatários, poderá optar por consentir para toda a organização. Essa opção aceita o consentimento para todos os usuários no locatário. Caso contrário, cada usuário receberá uma solicitação de consentimento no primeiro uso do aplicativo Web do Blockchain Workbench.
9. Selecione **aceitar** para consentir.

     ![Consentimento para ler perfis de usuários](media/deploy/graph-permission-consent.png)

10. Após o consentimento, o aplicativo Web Blockchain Workbench pode ser usado.

## <a name="azure-ad-configuration"></a>Configuração do Azure AD

Se você optar por configurar manualmente ou verificar as configurações do Azure AD antes da implantação, conclua todas as etapas nesta seção. Se você preferir definir automaticamente as configurações do Azure AD, use o [script de configuração do Azure ad](#azure-ad-configuration-script) depois de implantar o Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registo de aplicações API do Blockchain Workbench

A implantação do Blockchain Workbench requer o registro de um aplicativo do Azure AD. Você precisa de um locatário Azure Active Directory (Azure AD) para registrar o aplicativo. Você pode usar um locatário existente ou criar um novo locatário. Se você estiver usando um locatário existente do Azure AD, precisará de permissões suficientes para registrar aplicativos, conceder API do Graph permissões e permitir o acesso de convidado dentro de um locatário do Azure AD. Se você não tiver permissões suficientes em um locatário existente do Azure AD, crie um novo locatário.


1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD desejado. O locatário deve ser o locatário do administrador da assinatura em que o Workbench é implantado e você tem permissões suficientes para registrar aplicativos.
3. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **registros de aplicativo** > **novo registro de aplicativo**.

    ![Registo de aplicações](media/deploy/app-registration.png)

4. Forneça um **nome** e uma **URL de logon** para o aplicativo. Você pode usar valores de espaço reservado, pois os valores são alterados durante a implantação. 

    ![Criar registro de aplicativo](media/deploy/app-registration-create.png)

    |Definição  | Valor  |
    |---------|---------|
    |Name | `Blockchain API` |
    |Tipo de aplicação |Aplicativo Web/API|
    |URL de início de sessão | `https://blockchainapi` |

5. Selecione **criar** para registrar o aplicativo do Azure AD.

### <a name="modify-manifest"></a>Modificar manifesto

Em seguida, você precisa modificar o manifesto para usar funções de aplicativo no Azure AD para especificar os administradores do Blockchain Workbench.  Para obter mais informações sobre manifestos do aplicativo, consulte [Azure Active Directory manifesto do aplicativo](../../active-directory/develop/reference-app-manifest.md).

1. Para o aplicativo que você registrou, selecione **manifesto** no painel detalhes do aplicativo registrado.
2. Gerar um GUID. Você pode gerar um GUID usando o comando [GUID] do PowerShell: Cmdlet NewGuid () ou New-GUID. Outra opção é usar um site de gerador de GUID.
3. Você vai atualizar a seção **appRoles** do manifesto. No painel Editar manifesto, selecione **Editar** e substituir `"appRoles": []` pelo JSON fornecido. Certifique-se de substituir o valor do campo **ID** pelo GUID gerado. 

    ![Editar manifesto](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > O **administrador** de valor é necessário para identificar os administradores do Blockchain Workbench.

4. No manifesto, altere também o valor de **Oauth2AllowImplicitFlow** para **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Selecione **salvar** para salvar as alterações de manifesto.

### <a name="add-graph-api-required-permissions"></a>Adicionar API do Graph permissões necessárias

O aplicativo de API precisa solicitar permissão do usuário para acessar o diretório. Defina a seguinte permissão necessária para o aplicativo de API:

1. No registro do aplicativo de API do Blockchain, selecione **configurações > permissões necessárias > selecione uma > de API Microsoft Graph**.

    ![Selecionar uma API](media/deploy/client-app-select-api.png)

    Clique em **Selecionar**.

2. Em **habilitar acesso** em **permissões delegadas**, escolha **ler perfis básicos de todos os usuários**.

    ![Ativar o acesso](media/deploy/client-app-read-perms.png)

    Selecione **salvar** e selecione **concluído**.

3. Em **permissões necessárias**, selecione **conceder permissões** e, em seguida, selecione **Sim** para o prompt de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   Conceder permissão permite que o Blockchain Workbench acesse usuários no diretório. A permissão de leitura é necessária para pesquisar e adicionar membros ao Blockchain Workbench.

### <a name="get-application-id"></a>Obter ID do aplicativo

A ID do aplicativo e as informações do locatário são necessárias para a implantação. Colete e armazene as informações para uso durante a implantação.

1. Para o aplicativo que você registrou, selecione **configurações** > **Propriedades**.
2. No painel **Propriedades** , copie e armazene os valores a seguir para uso posterior durante a implantação.

    ![Propriedades do aplicativo de API](media/deploy/app-properties.png)

    | Configuração para armazenar  | Usar na implantação |
    |------------------|-------------------|
    | ID da aplicação | ID do aplicativo de > de Azure Active Directory instalação |

### <a name="get-tenant-domain-name"></a>Obter nome de domínio do locatário

Colete e armazene o nome de domínio do locatário Active Directory em que os aplicativos são registrados. 

No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Nomes de domínio personalizados**. Copie e armazene o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Configurações do usuário convidado

Se você tiver usuários convidados em seu locatário do Azure AD, siga as etapas adicionais para garantir que a atribuição e o gerenciamento de usuários do Blockchain Workbench funcionem corretamente.

1. Alterne seu locatário do Azure AD e selecione **Azure Active Directory > configurações de usuário > gerenciar configurações de colaboração externas**.
2. Definir **permissões de usuário convidado é limitado** a **não**.
    ![Configurações de colaboração externas](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configurando a URL de resposta

Depois que o Azure Blockchain Workbench tiver sido implantado, você precisará configurar a **URL de resposta** do aplicativo cliente Azure Active Directory (Azure AD) da URL da Web do Blockchain Workbench implantado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Verifique se você está no locatário em que você registrou o aplicativo cliente do Azure AD.
3. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registos das aplicações**.
4. Selecione o aplicativo cliente do Azure AD que você registrou na seção pré-requisito.
5. Selecione **configurações > URLs de resposta**.
6. Especifique a URL da Web principal da implantação do Azure Blockchain Workbench que você recuperou na seção **obter a URL da Web do Azure Blockchain Workbench** . A URL de resposta é prefixada com `https://`. Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de Resposta](media/deploy/configure-reply-url.png)

7. Selecione **salvar** para atualizar o registro do cliente.

## <a name="remove-a-deployment"></a>Remover uma implantação

Quando uma implantação não for mais necessária, você poderá remover uma implantação excluindo o grupo de recursos do Blockchain Workbench.

1. No portal do Azure, navegue até o **grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que deseja excluir. 
2. Selecione **Eliminar grupo de recursos**. Verifique a exclusão inserindo o nome do grupo de recursos e selecione **excluir**.

    ![Eliminar grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de instruções, você implantou o Azure Blockchain Workbench. Para saber como criar um aplicativo blockchain, vá para o próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)
