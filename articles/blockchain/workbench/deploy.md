---
title: Implementar pré-visualização da bancada azure blockchain
description: Como implementar a Pré-visualização da bancada azure blockchain
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943193"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Implementar pré-visualização da bancada azure blockchain

A Pré-visualização da bancada azure Blockchain é implementada utilizando um modelo de solução no Mercado Azure. O modelo simplifica a implementação de componentes necessários para criar aplicações blockchain. Uma vez implementado, o Blockchain Workbench fornece acesso a aplicações de clientes para criar e gerir utilizadores e aplicações blockchain.

Para obter mais informações sobre os componentes da Bancada de Trabalho blockchain, consulte a [arquitetura azure blockchain workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Blockchain Workbench permite-lhe implementar um livro de blockchain juntamente com um conjunto de serviços Relevantes Azure mais utilizados para construir uma aplicação baseada em blockchain. A implantação da bancada blockchain resulta na prestação dos seguintes serviços Azure dentro de um grupo de recursos na sua subscrição Azure.

* Plano de Serviço de Aplicações (Standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* Base de Dados SQL (Standard S0) + Servidor Lógico SQL
* Conta de Armazenamento Azure (LRS Standard)
* Conjunto de escala de máquina virtual com capacidade de 1
* Grupo de recursos de rede virtual (com Balancer de Carga, Grupo de Segurança de Rede, Endereço IP Público, Rede Virtual)
* Serviço Azure Blockchain. Se estiver a utilizar uma implementação anterior da bancada blockchain, considere recolocar a bancada azure blockchain para utilizar o Serviço Blockchain Azure.

Segue-se um exemplo de implantação criado no grupo de recursos **myblockchain.**

![Implementação de exemplo](media/deploy/example-deployment.png)

O custo da Bancada de Trabalho blockchain é um agregado do custo dos serviços Azure subjacentes. As informações sobre preços dos serviços Azure podem ser calculadas utilizando a [calculadora](https://azure.microsoft.com/pricing/calculator/)de preços .

## <a name="prerequisites"></a>Pré-requisitos

A bancada azure Blockchain requer a configuração da AD Azure e os registos de aplicações. Pode optar por fazer as configurações do AD [Azure manualmente](#azure-ad-configuration) antes de ser implantado ou executar uma implementação de um post de script. Se estiver a reimplantar a bancada blockchain Workbench, consulte a [configuração do Azure AD](#azure-ad-configuration) para verificar a sua configuração de AD Azure.

> [!IMPORTANT]
> A bancada de trabalho não tem de ser implantada no mesmo inquilino que a que está a usar para registar uma aplicação Azure AD. A bancada deve ser implantada num inquilino onde você tem permissões suficientes para mobilizar recursos. Para obter mais informações sobre os inquilinos da AD Azure, consulte Como obter um inquilino de [Diretório Ativo](../../active-directory/develop/quickstart-create-new-tenant.md) e [integrar aplicações com o Azure Ative Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Implementar bancada de trabalho blockchain

Uma vez concluídos os passos pré-requisitos, está pronto para implantar a bancada de trabalho blockchain. As seguintes secções descrevem como implementar o quadro.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione a sua conta no canto superior direito e mude para o inquilino AD Azure desejado onde pretende implementar a Bancada de Trabalho Azure Blockchain.
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > Bancada de **Trabalho Azure Blockchain (pré-visualização)** .

    ![Criar bancada de trabalho Azure Blockchain](media/deploy/blockchain-workbench-settings-basic.png)

    | Definição | Descrição  |
    |---------|--------------|
    | Prefixo de recurso | Identificador único curto para a sua implantação. Este valor é usado como base para nomear recursos. |
    | Nome de utilizador VM | O nome do utilizador é utilizado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação | Selecione se pretender utilizar uma palavra-passe ou chave para ligar aos VMs. |
    | Palavra-passe | A palavra-passe é usada para ligar a VMs. |
    | SSH | Utilize uma chave pública RSA no formato de linha única a partir de **ssh-rsa** ou utilize o formato PEM multi-line. Pode gerar chaves SSH utilizando `ssh-keygen` no Linux e NO OS X, ou utilizando o PuTTYGen no Windows. Mais informações sobre as teclas SSH, consulte [como utilizar as teclas SSH com windows on Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Base de dados e senha blockchain | Especifique a palavra-passe a utilizar para acesso à base de dados criada como parte da implementação. A palavra-passe deve satisfazer três dos seguintes quatro requisitos: comprimento deve ser entre 12 e 72 caracteres, 1 caracteres minúsculos, 1 personagem superior, 1 número e 1 personagem especial que não seja sinal de número(#), por cento (%), vírem(,), estrela(*), citação de volta(\`), citação dupla('), traço (-) e semicoluna(;) |
    | Região de implantação | Especifique onde implementar recursos da bancada blockchain. Para uma melhor disponibilidade, isto deve corresponder à definição de **Localização.** |
    | Subscrição | Especifique a Subscrição Azure que deseja utilizar para a sua implementação. |
    | Grupos de recursos | Crie um novo grupo de Recursos selecionando **Criar novo** e especificar um nome único de grupo de recursos. |
    | Localização | Especifique a região que pretende implementar o quadro. |

1. Selecione **OK** para terminar a secção de configuração básica.

1. Em **Definições Avançadas,** escolha se pretende criar uma nova rede blockchain ou utilizar uma rede blockchain de prova de autoridade existente.

    Para **criar novos:**

    A nova opção *cria* um livro de contabilidade de Quorum do Serviço Blockchain Azure com o sku básico padrão.

    ![Configurações avançadas para nova rede blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Definição | Descrição  |
    |---------|--------------|
    | Nível de preços do Serviço Azure Blockchain | Escolha o nível de serviço **Basic** ou **Standard** Azure Blockchain que é usado para blockchain Workbench |
    | Definições de Diretório Ativo Azure | Escolha **adicionar mais tarde**.</br>Nota: Se escolheu [pré-configurar o Azure AD](#azure-ad-configuration) ou está a recolocar-se, opte por *Adicionar Agora*. |
    | Seleção VM | Selecione o desempenho de armazenamento preferido e o tamanho vm para a sua rede blockchain. Escolha um tamanho VM menor, como o *Standard DS1 v2,* se estiver numa subscrição com limites de serviço baixos, como o nível livre de Azure. |

    Para **utilização existente:**

    A opção *de utilização existente* permite especificar uma rede blockchain Ethereum Proof-of-Authority (PoA). Os pontos finais têm os seguintes requisitos.

   * O ponto final deve ser uma rede blockchain Ethereum Proof-of-Authority (PoA).
   * O ponto final deve ser acessível ao público por toda a rede.
   * A rede de blockchain PoA deve ser configurada para ter o preço do gás definido para zero.

     > [!NOTE]
     > As contas da bancada blockchain não são financiadas. Se forem necessários fundos, as transações falham.

     ![Configurações avançadas para a rede blockchain existente](media/deploy/advanced-blockchain-settings-existing.png)

     | Definição | Descrição  |
     |---------|--------------|
     | Ethereum RPC Endpoint | Forneça o ponto final rPC de uma rede de blockchain PoA existente. O ponto final começa com https:// ou http:// e termina com um número de porta. Por exemplo, `http<s>://<network-url>:<port>` |
     | Definições de Diretório Ativo Azure | Escolha **adicionar mais tarde**.</br>Nota: Se escolheu [pré-configurar o Azure AD](#azure-ad-configuration) ou está a recolocar-se, opte por *Adicionar Agora*. |
     | Seleção VM | Selecione o desempenho de armazenamento preferido e o tamanho vm para a sua rede blockchain. Escolha um tamanho VM menor, como o *Standard DS1 v2,* se estiver numa subscrição com limites de serviço baixos, como o nível livre de Azure. |

1. Selecione **OK** para terminar Definições Avançadas.

1. Reveja o resumo para verificar se os seus parâmetros estão corretos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

1. Selecione **Criar** para concordar com os termos e implementar a sua bancada de trabalho Azure Blockchain.

O destacamento pode demorar até 90 minutos. Pode utilizar o portal Azure para monitorizar o progresso. No grupo de recursos recém-criado, selecione **Deployments > Visão geral** para ver o estado dos artefactos implantados.

> [!IMPORTANT]
> Pós-implantação, é necessário completar as definições de Diretório Ativo. Se escolheu **Add Later,** tem de executar o script de [configuração Azure AD](#azure-ad-configuration-script).  Se escolheu **Adicionar agora,** precisa [configurar o URL de resposta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench web URL

Uma vez concluída a implantação da bancada blockchain, um novo grupo de recursos contém os seus recursos blockchain Workbench. Os serviços blockchain Workbench são acedidos através de um URL web. Os seguintes passos mostram-lhe como recuperar o URL web da estrutura implantada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, selecione **Grupos de Recursos**.
1. Escolha o nome do grupo de recursos especificado ao implementar a bancada blockchain Workbench.
1. Selecione a direção da coluna **TYPE** para classificar a lista alfabeticamente por tipo.
1. Existem dois recursos com serviço de **aplicações**tipo. Selecione o recurso do serviço de **aplicações** tipo *sem* o sufixo "-api".

    ![Lista de serviços de aplicativos](media/deploy/resource-group-list.png)

1. Na visão **geral**do serviço de aplicações, copie o valor **URL,** que representa o URL web para a sua bancada de trabalho blockchain implantada.

    ![Essenciais do serviço de aplicações](media/deploy/app-service.png)

Para associar um nome de domínio personalizado com blockchain Workbench, consulte configurar um nome de [domínio personalizado para uma aplicação web no Azure App Service utilizando o Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Script de configuração azure AD

O Azure AD deve ser configurado para completar a sua implementação da bancada blockchain. Usará um script PowerShell para fazer a configuração.

1. Num browser, navegue para o URL web da [bancada blockchain Workbench](#blockchain-workbench-web-url).
1. Verá instruções para configurar o Azure AD usando cloud shell. Copie o comando e lance a Cloud Shell.

    ![Script AAD de lançamento](media/deploy/launch-aad-script.png)

1. Escolha o inquilino da AD Azure onde implementou a bancada blockchain Workbench.
1. No ambiente Cloud Shell PowerShell, cola e executa o comando.
1. Quando solicitado, entre no inquilino Azure AD que pretende utilizar para a Bancada de Trabalho blockchain. Este será o inquilino que contém os utilizadores da Bancada de Trabalho blockchain.

    > [!IMPORTANT]
    > O utilizador autenticado requer permissões para criar registos de candidaturas da Azure AD e conceder permissões de candidatura delegadas no inquilino. Você pode precisar pedir a um administrador do inquilino para executar o script de configuração Azure AD ou criar um novo inquilino.

    ![Insira o inquilino da AD Azure](media/deploy/choose-tenant.png)

1. Você será solicitado a autenticar ao inquilino da AD Azure usando um navegador. Abra o URL web num browser, insira o código e autenticar.

    ![Autenticar com código](media/deploy/authenticate.png)

1. O script produz várias mensagens de estado. Recebeuma mensagem de estado de **sucesso** se o inquilino foi aprovisionado com sucesso.
1. Navegue para o URL da bancada de trabalho blockchain. É-lhe pedido que consinta em conceder permissões de leitura para o diretório. Isto permite o acesso da aplicação web Blockchain Workbench aos utilizadores do inquilino. Se você é o administrador do inquilino, você pode optar por consentir para toda a organização. Esta opção aceita o consentimento para todos os utilizadores do inquilino. Caso contrário, cada utilizador é solicitado para o consentimento na primeira utilização da aplicação web blockchain Workbench.
1. Selecione **Aceitar** o consentimento.

     ![Consentimento para ler perfis de utilizadores](media/deploy/graph-permission-consent.png)

1. Após o consentimento, a aplicação web blockchain Workbench pode ser usada.

Completou a sua implantação na bancada Azure Blockchain. Consulte [os próximos passos](#next-steps) para que as sugestões possam começar a utilizar a sua implantação.

## <a name="azure-ad-configuration"></a>Configuração da AD Azure

Se optar por configurar manualmente ou verificar as definições de AD Azure antes da implementação, complete todos os passos nesta secção. Se preferir configurar automaticamente as definições de AD Azure, utilize o script de [configuração do Azure AD](#azure-ad-configuration-script) depois de implementar a bancada blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registo de aplicativos Blockchain Workbench API

A implementação da bancada blockchain requer o registo de uma aplicação Azure AD. Você precisa de um inquilino azure Ative Directory (Azure AD) para registar a app. Você pode usar um inquilino existente ou criar um novo inquilino. Se estiver a utilizar um inquilino Azure AD existente, precisa de permissões suficientes para registar candidaturas, conceder permissões de API graph e permitir o acesso dos hóspedes dentro de um inquilino da AD Azure. Se não tiver permissões suficientes num inquilino da AD Azure existente, crie um novo inquilino.


1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione a sua conta no canto superior direito e mude para o inquilino AD Azure desejado. O inquilino deve ser o inquilino da subscrição da subscrição onde a Bancada de Trabalho azure Blockchain está implantada e você tem permissões suficientes para registar candidaturas.
1. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **registos** de aplicações > **Novo registo.**

    ![Registo da aplicação](media/deploy/app-registration.png)

1. Forneça um **nome** de exibição e escolha **contas apenas neste diretório organizacional**.

    ![Criar registo de aplicativos](media/deploy/app-registration-create.png)

1. Selecione **Registar** para registar a aplicação Azure AD.

### <a name="modify-manifest"></a>Modificar manifesto

Em seguida, é necessário modificar o manifesto para utilizar funções de aplicação dentro da AD Azure para especificar os administradores da bancada de trabalho blockchain.  Para obter mais informações sobre os manifestos de candidatura, consulte o manifesto de candidatura do [Azure Ative Directory](../../active-directory/develop/reference-app-manifest.md).


1. É necessário um GUID para o manifesto. Pode gerar um GUID utilizando o comando PowerShell `[guid]::NewGuid()` ou `New-GUID` cmdlet. Outra opção é usar um site de gerador GUIA.
1. Para a aplicação que registou, selecione **Manifesto** na secção **Gerir.**
1. Em seguida, atualize a secção **appRoles** do manifesto. Substitua `"appRoles": []` pelo JSON fornecido. Certifique-se de substituir o valor do campo **id** pelo GUID gerado. 

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
    > O **Administrador** de Valor é necessário para identificar os administradores da bancada de trabalho blockchain.

1. No manifesto, também altere o valor **Oauth2AllowImplicitFlow** para **verdadeiro**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Selecione **Guardar** para guardar as alterações manifestas.

### <a name="add-graph-api-required-permissions"></a>Adicionar permissões necessárias ao Gráfico API

A aplicação API necessita de solicitar autorização ao utilizador para aceder ao diretório. Detete a seguinte permissão necessária para o pedido de API:

1. No registo da aplicação *Blockchain API,* selecione **permissões API**. Por predefinição, é adicionada a permissão de utilizador da API **graph.Read.**
1. A aplicação Workbench requer o acesso à informação básica do perfil dos utilizadores. Nas *permissões configuradas,* selecione **Adicionar uma permissão**. Na **Microsoft APIs,** selecione **Microsoft Graph**.
1. Uma vez que a aplicação Workbench utiliza as credenciais autenticadas de utilizador, selecione **permissões delegadas**.
1. Na categoria *Utilizador,* escolha **User.ReadBasic.All** permission.

    ![Configuração de registo de aplicações Azure AD mostrando adicionar o Utilizador do Microsoft Graph.ReadBasic.Todas as permissões delegadas](media/deploy/add-graph-user-permission.png)

    **Selecione Adicionar permissões**.

1. Nas *permissões configuradas,* selecione o consentimento do **administrador da Concessão** para o domínio e, em seguida, selecione **Sim** para o pedido de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   A concessão de permissão permite que a Blockchain Workbench aceda aos utilizadores no diretório. A permissão de leitura é necessária para pesquisar e adicionar membros à Bancada de Trabalho blockchain.

### <a name="get-application-id"></a>Obter ID de aplicação

A identificação da aplicação e a informação do inquilino são necessárias para implantação. Recolher e armazenar as informações para utilização durante a implementação.

1. Para a aplicação que registou, selecione **Visão Geral**.
1. Copiar e armazenar o valor de ID da **aplicação** para posterior utilização durante a implementação.

    ![Propriedades de aplicativos API](media/deploy/app-properties.png)

    | Definição para armazenar  | Utilização na implantação |
    |------------------|-------------------|
    | Id de aplicação (cliente) | Configuração de Diretório Ativo Azure > Id de aplicação |

### <a name="get-tenant-domain-name"></a>Obtenha o nome do domínio do inquilino

Colete e guarde o nome de domínio do inquilino do Diretório Ativo onde as candidaturas estão registadas. 

No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Nomes de domínio personalizados**. Copie e guarde o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Configurações de utilizador convidado

Se tiver utilizadores convidados no seu inquilino Azure AD, siga os passos adicionais para garantir que a atribuição e gestão do utilizador da Bancada de Trabalho blockchain funcione corretamente.

1. Troque-lhe o seu inquilino Azure AD e selecione **Azure Ative Directory > Configurações de utilizador > Gerencie as definições**de colaboração externa .
1. Definir As **permissões do utilizador do Hóspede estão limitadas** a **Nº**.
    ![Definições de colaboração externa](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configurar o URL de resposta

Uma vez implantada a bancada de trabalho Azure Blockchain, tem de configurar a aplicação de cliente Azure Ative Directory (Azure AD) **Resposta URL** do URL web da bancada de trabalho blockchain implantado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se está no inquilino onde registou o pedido de cliente da Azure AD.
1. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registos das aplicações**.
1. Selecione a aplicação do cliente Azure AD que registou na secção pré-requisito.
1. **Selecione Autenticação**.
1. Especifique o URL web principal da implementação da bancada azure blockchain que recuperou na secção URL web blockchain [Workbench.](#blockchain-workbench-web-url) O URL de resposta é pré-fixado com `https://`. Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de resposta à autenticação](media/deploy/configure-reply-url.png)

1. Na secção **de definição Avançada,** verifique **fichas** de acesso e **fichas de identificação**.

    ![Definições avançadas de autenticação](media/deploy/authentication-advanced-settings.png)

1. Selecione **Guardar** para atualizar o registo do cliente.

## <a name="remove-a-deployment"></a>Remover uma implantação

Quando uma implementação já não é necessária, pode remover uma implementação eliminando o grupo de recursos blockchain Workbench.

1. No portal Azure, navegue para o **grupo Recurso** no painel de navegação à esquerda e selecione o grupo de recursos que pretende eliminar. 
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação inserindo o nome do grupo de recursos e selecione **Delete**.

    ![Eliminar grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de "como fazer", implantou a bancada azure blockchain. Para aprender a criar uma aplicação blockchain, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Criar uma aplicação blockchain na bancada azure blockchain](create-app.md)
