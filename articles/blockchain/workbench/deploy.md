---
title: Implementar pré-visualização da bancada de trabalho Azure Blockchain
description: Como implementar a pré-visualização da bancada de trabalho do Azure Blockchain
ms.date: 07/16/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: b46a35b45a51d0cc76942c4ca142c4c7792a28b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077024"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Implementar pré-visualização da bancada de trabalho Azure Blockchain

A pré-visualização da bancada de trabalho Azure Blockchain é implementada usando um modelo de solução no Mercado Azure. O modelo simplifica a implementação de componentes necessários para criar aplicações blockchain. Uma vez implementado, o Blockchain Workbench fornece acesso a aplicações de clientes para criar e gerir utilizadores e aplicações blockchain.

Para obter mais informações sobre os componentes da Bancada Blockchain, consulte [a arquitetura Azure Blockchain Workbench.](architecture.md)

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Blockchain Workbench permite-lhe implementar um livro de contabilidade blockchain juntamente com um conjunto de serviços Azure relevantes mais frequentemente usados para construir uma aplicação baseada em blockchain. A implementação da Blockchain Workbench resulta na prestação dos seguintes serviços Azure dentro de um grupo de recursos na sua assinatura Azure.

* Plano de Serviço de Aplicações (Standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* Base de Dados SQL (Standard S0)
* Conta de Armazenamento Azure (LRS Standard)
* Escala de máquina virtual definida com capacidade de 1
* Grupo de recursos de rede virtual (com Balanceador de Carga, Grupo de Segurança de Rede, Endereço IP Público, Rede Virtual)
* Serviço Azure Blockchain. Se estiver a utilizar uma implementação anterior da Blockchain Workbench, considere recolocar a Azure Blockchain Workbench para utilizar o Serviço Azure Blockchain.

Segue-se um exemplo de implantação criado no grupo de recursos **myblockchain.**

![Implantação de exemplo](media/deploy/example-deployment.png)

O custo da Blockchain Workbench é um agregado do custo dos serviços Azure subjacentes. As informações sobre os preços dos serviços Azure podem ser calculadas utilizando [a calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Pré-requisitos

A azure Blockchain Workbench requer configuração AD AZure e registos de candidaturas. Pode optar por fazer as configurações AD do Azure [manualmente](#azure-ad-configuration) antes de implantar ou executar uma implementação de post de script. Se estiver a recolocar a bancada blockchain Workbench, consulte a [configuração AD do Azure](#azure-ad-configuration) para verificar a configuração AD do Azure.

> [!IMPORTANT]
> Workbench não precisa ser implantado no mesmo inquilino que você está usando para registar um pedido de AD Azure. A bancada de trabalho deve ser implantada num inquilino onde tenha permissões suficientes para mobilizar recursos. Para obter mais informações sobre os inquilinos da AZure AD, consulte [Como obter um inquilino de Diretório Ativo](../../active-directory/develop/quickstart-create-new-tenant.md) e Integrar [aplicações com o Azure Ative Directory](../../active-directory/develop/quickstart-register-app.md).

## <a name="deploy-blockchain-workbench"></a>Implementar bancada de trabalho blockchain

Uma vez concluídos os passos pré-requisitos, está pronto para implantar a bancada blockchain Workbench. As secções seguintes descrevem como implantar o quadro.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione a sua conta no canto superior direito e mude para o inquilino AD Azure desejado onde pretende implantar a Azure Blockchain Workbench.
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **blockchain**  >  **Azure Blockchain Workbench (pré-visualização)**.

    ![Criar bancada de blockchain Azure](media/deploy/blockchain-workbench-settings-basic.png)

    | Definições | Descrição  |
    |---------|--------------|
    | Prefixo de recursos | Curto identificador único para a sua implantação. Este valor é usado como base para nomear recursos. |
    | Nome de utilizador VM | O nome de utilizador é utilizado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação | Selecione se pretender utilizar uma palavra-passe ou chave para ligar aos VMs. |
    | Palavra-passe | A palavra-passe é utilizada para a ligação aos VMs. |
    | SSH | Utilize uma chave pública RSA no formato de linha única a começar por **ssh-rsa** ou utilize o formato PEM multi-line. Pode gerar teclas SSH utilizando `ssh-keygen` o Linux e o OS X, ou utilizando o PuTTYGen no Windows. Mais informações sobre as teclas SSH, consulte [como utilizar as teclas SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Base de dados e senha blockchain | Especifique a palavra-passe a utilizar para acesso à base de dados criada como parte da implementação. A palavra-passe deve satisfazer três dos quatro requisitos seguintes: o comprimento deve estar entre 12 & 72 caracteres, 1 caracteres maiúsculos, 1 caracteres maiúsculos, 1 número e 1 caracteres especiais que não seja sinal de número (#), por cento(,), vírgula(,), citação \` traseira(, citação dupla(, citação única('), traço(-) e semicolumn (;) |
    | Região de implantação | Especificar onde implantar os recursos da Bancada Blockchain. Para uma melhor disponibilidade, isto deve coincidir com a definição de localização da **Região.** Nem todas as regiões estão disponíveis durante a pré-visualização. As funcionalidades podem não estar disponíveis em algumas regiões. O Azure Blockchain Data Manager está disponível nas seguintes regiões de Azure: Leste dos EUA e Europa Ocidental.|
    | Subscrição | Especifique a Subscrição Azure que deseja utilizar para a sua implementação. |
    | Grupos de recursos | Crie um novo grupo de Recursos selecionando **Criar novo** e especificar um nome único de grupo de recursos. |
    | Localização | Especifique a região que deseja implantar o quadro. |

1. Selecione **OK** para terminar a secção de configuração básica.

1. Em **Definições Avançadas,** escolha se pretende criar uma nova rede blockchain ou utilizar uma rede blockchain de prova de autoridade existente.

    Para **criar novo**:

    A nova opção *de criar* implementa um livro-razão do Azure Blockchain Service Quorum com o sku básico padrão.

    ![Definições avançadas para nova rede blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Definições | Descrição  |
    |---------|--------------|
    | Nível de preços do Serviço Azure Blockchain | Escolha o nível **básico** ou **standard** do serviço blockchain Azure que é usado para blockchain Workbench |
    | Definições do Azure Active Directory | Escolha **Adicionar Mais tarde**.</br>Nota: Se optou por [pré-configurar a Ad Azure](#azure-ad-configuration) ou se está a recolocar, opte por *Add Now*. |
    | Seleção VM | Selecione o desempenho de armazenamento preferido e o tamanho VM para a sua rede blockchain. Escolha um tamanho VM menor, como *o Standard DS1 v2* se estiver numa subscrição com limites de serviço baixos como o nível livre do Azure. |

    Para **utilização existente:**

    A opção *de utilização existente* permite especificar uma rede blockchain Ethereum Proof of Authority (PoA). Os pontos finais têm os seguintes requisitos.

   * O ponto final deve ser uma rede blockchain Ethereum Proof-of-Authority (PoA).
   * O ponto final deve ser acessível ao público pela rede.
   * A rede de blockchain PoA deve ser configurada para ter o preço do gás definido para zero.

     > [!NOTE]
     > As contas da Blockchain Workbench não são financiadas. Se forem necessários fundos, as transações falham.

     ![Definições avançadas para a rede blockchain existente](media/deploy/advanced-blockchain-settings-existing.png)

     | Definições | Descrição  |
     |---------|--------------|
     | Ethereum RPC Endpoint | Fornecer o ponto final RPC de uma rede de blockchain PoA existente. O ponto final começa com https:// ou http:// e termina com um número de porta. Por exemplo, `http<s>://<network-url>:<port>` |
     | Definições do Azure Active Directory | Escolha **Adicionar Mais tarde**.</br>Nota: Se optou por [pré-configurar a Ad Azure](#azure-ad-configuration) ou se está a recolocar, opte por *Add Now*. |
     | Seleção VM | Selecione o desempenho de armazenamento preferido e o tamanho VM para a sua rede blockchain. Escolha um tamanho VM menor, como *o Standard DS1 v2* se estiver numa subscrição com limites de serviço baixos como o nível livre do Azure. |

1. Selecione **Review + criar** para terminar Definições Avançadas.

1. Reveja o resumo para verificar se os seus parâmetros estão corretos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

1. Selecione **Criar** para concordar com os termos e implementar a sua bancada de trabalho Azure Blockchain.

A colocação pode demorar até 90 minutos. Pode utilizar o portal Azure para monitorizar o progresso. No grupo de recursos recém-criado, selecione **Implementações > Visão Geral** para ver o estado dos artefactos implantados.

> [!IMPORTANT]
> Após a implementação, tem de completar as definições do Ative Directory. Se escolheu **Add Later**, tem de executar o script de [configuração AD Azure](#azure-ad-configuration-script).  Se escolheu **Adicionar agora,** tem de [configurar o URL de resposta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench web URL

Uma vez concluída a implantação da Bancada Blockchain, um novo grupo de recursos contém os seus recursos blockchain Workbench. Os serviços blockchain Workbench são acedidos através de um URL web. Os passos seguintes mostram-lhe como recuperar o URL web da estrutura implantada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, selecione **grupos de recursos**.
1. Escolha o nome do grupo de recursos especificado ao implementar a bancada blockchain Workbench.
1. Selecione o título da coluna **TYPE** para classificar a lista alfabeticamente por tipo.
1. Existem dois recursos com o serviço **de aplicações** tipo. Selecione o recurso do serviço de **aplicações** tipo *sem* o sufixo "-api".

    ![Lista de serviços de aplicações](media/deploy/resource-group-list.png)

1. No Resumo do **Serviço** de Aplicações, copie o valor **URL,** que representa o URL web para a sua bancada de trabalho blockchain implantada.

    ![Essencial do serviço de aplicações](media/deploy/app-service.png)

Para associar um nome de domínio personalizado ao Blockchain Workbench, consulte [configurar um nome de domínio personalizado para uma aplicação web no Azure App Service usando o Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Script de configuração AD AZure

O Azure AD tem de ser configurado para completar a sua implementação blockchain Workbench. Utilizará um script PowerShell para fazer a configuração.

1. Num browser, navegue para o [URL web blockchain Workbench.](#blockchain-workbench-web-url)
1. Verá instruções para configurar o Azure AD usando a Cloud Shell. Copie o comando e lance a Cloud Shell.

    ![Lançar script AAD](media/deploy/launch-aad-script.png)

1. Escolha o inquilino AZure AD onde implantou a Blockchain Workbench.
1. No ambiente Cloud Shell PowerShell, cole e execute o comando.
1. Quando solicitado, insira o inquilino AZure AD que pretende usar para blockchain Workbench. Este será o inquilino que contém os utilizadores da Blockchain Workbench.

    > [!IMPORTANT]
    > O utilizador autenticado requer permissões para criar registos de pedidos Azure AD e conceder permissões de candidatura delegadas no arrendatário. Você pode precisar de pedir a um administrador do inquilino para executar o script de configuração AD Azure ou criar um novo inquilino.

    ![Insira inquilino da AZure AD](media/deploy/choose-tenant.png)

1. Você será solicitado a autenticar para o inquilino AZURE AD usando um navegador. Abra o URL web num browser, introduza o código e autentica.

    ![Autenticar com código](media/deploy/authenticate.png)

1. O script produz várias mensagens de estado. Obtém-se uma mensagem de estado **de sucesso** se o inquilino tiver sido a provisionado com sucesso.
1. Navegue para o URL blockchain Workbench. É-lhe pedido que consinta em conceder permissões de leitura ao diretório. Isto permite ao blockchain Workbench acesso à aplicação web aos utilizadores do inquilino. Se você é o administrador do inquilino, você pode escolher o consentimento para toda a organização. Esta opção aceita o consentimento de todos os utilizadores do arrendatário. Caso contrário, cada utilizador é solicitado a consentimento na primeira utilização da aplicação web Blockchain Workbench.
1. Selecione **Aceite** o consentimento.

     ![Consentimento para ler perfis dos utilizadores](media/deploy/graph-permission-consent.png)

1. Após o consentimento, a aplicação web Blockchain Workbench pode ser utilizada.

Completou a sua implantação da Azure Blockchain Workbench. Consulte [os próximos passos](#next-steps) para que as sugestões se iniciem utilizando a sua implantação.

## <a name="azure-ad-configuration"></a>Configuração AD Azure

Se optar por configurar manualmente ou verificar as definições AD do Azure antes da sua implantação, preencha todos os passos desta secção. Se preferir configurar automaticamente as definições de Azure AD, utilize [o script de configuração AD Azure](#azure-ad-configuration-script) depois de implementar a Bancada Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registo de aplicativos API blockchain Workbench

A implementação da Blockchain Workbench requer o registo de uma aplicação AD Azure. Você precisa de um inquilino Azure Ative (Azure AD) para registar a app. Você pode usar um inquilino existente ou criar um novo inquilino. Se você estiver usando um inquilino AZure AD existente, você precisa de permissões suficientes para registar pedidos, conceder permissões de API gráfico e permitir o acesso do hóspede dentro de um inquilino AD AZure. Se não tiver permissões suficientes num inquilino existente da Azure AD, crie um novo inquilino.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione a sua conta no canto superior direito e mude para o inquilino AD Azure desejado. O inquilino deve ser o inquilino da subscrição da subscrição onde a Azure Blockchain Workbench está implantada e você tem permissões suficientes para registar pedidos.
1. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **registos de Aplicações**  >  **Novo registo**.

    ![Registo de aplicações](media/deploy/app-registration.png)

1. Forneça um **nome** de exibição e escolha **contas apenas neste diretório organizacional.**

    ![Criar um registo de aplicações](media/deploy/app-registration-create.png)

1. **Selecione Registar-se** para registar a aplicação Azure AD.

### <a name="modify-manifest"></a>Modificar manifesto

Em seguida, precisa modificar o manifesto para utilizar funções de aplicação dentro da Azure AD para especificar administradores da Blockchain Workbench.  Para obter mais informações sobre manifestos de aplicação, consulte [o manifesto de aplicação do Diretório Ativo Azure.](../../active-directory/develop/reference-app-manifest.md)

1. É necessário um GUID para o manifesto. Pode gerar um GUID utilizando o comando PowerShell `[guid]::NewGuid()` ou `New-GUID` cmdlet. Outra opção é utilizar um site de geradorES GUID.
1. Para a aplicação que registou, selecione **Manifesto** na secção **Gerir.**
1. Em seguida, atualize a secção **appRoles** do manifesto. `"appRoles": []`Substitua-a pelo JSON fornecido. Certifique-se de que substitui o valor do `id` campo pelo GUID gerado.
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
    > O **administrador de** valor é necessário para identificar administradores da Blockchain Workbench.

1. No manifesto, altere também o valor **Oauth2AllowImplicitFlow** para **verdadeiro**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. **Selecione Guardar** para guardar as alterações do manifesto.

### <a name="add-graph-api-required-permissions"></a>Adicionar API gráfico necessário permissões

A aplicação API precisa de solicitar autorização do utilizador para aceder ao diretório. Desa estatudo a seguinte permissão necessária para o pedido da API:

1. No registo da aplicação *Blockchain API,* selecione **permissões API**. Por predefinição, é adicionada a permissão do Utilizador API **do Gráfico.Read.**
1. A aplicação Workbench requer acesso lido às informações básicas do perfil dos utilizadores. Nas *permissões configuradas,* **selecione Adicionar uma permissão**. Nas **APIs da Microsoft**, selecione Microsoft **Graph**.
1. Uma vez que a aplicação Workbench utiliza as credenciais de utilizador autenticadas, selecione **permissões delegadas**.
1. Na categoria *Utilizador,* escolha **User.ReadBasic.All** permission.

    ![Configuração de registo de aplicações AD AZure mostrando a adição do Utilizador do Gráfico Microsoft.ReadBasic.Todas as permissões delegadas](media/deploy/add-graph-user-permission.png)

    **Selecione Permissões de adicionar**.

1. Nas *permissões configuradas,* selecione **o consentimento administrativo de Grant** para o domínio e, em seguida, selecione **Sim** para a solicitação de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   A concessão de permissão permite que blockchain Workbench aceda aos utilizadores no diretório. A permissão de leitura é necessária para pesquisar e adicionar membros à Blockchain Workbench.

### <a name="get-application-id"></a>Obtenha iD de aplicação

A identificação do pedido e a informação do inquilino são necessárias para a implantação. Recolher e armazenar as informações para utilização durante a implantação.

1. Para a aplicação que registou, selecione **Overview**.
1. Copie e guarde o valor **de ID da aplicação** para utilização posterior durante a implementação.

    ![Propriedades de aplicativos API](media/deploy/app-properties.png)

    | Definição para armazenar  | Utilização na implantação |
    |------------------|-------------------|
    | ID da Aplicação (cliente) | Azure Ative Directy configuração > ID de aplicação |

### <a name="get-tenant-domain-name"></a>Obtenha o nome de domínio do inquilino

Recolher e armazenar o nome de domínio do inquilino ative onde as aplicações estão registadas.

No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Nomes de domínio personalizados**. Copiar e armazenar o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Configurações do utilizador do hóspede

Se tiver utilizadores convidados no seu inquilino Azure AD, siga os passos adicionais para garantir que a atribuição e gestão do utilizador blockchain Workbench funcione corretamente.

1. Mude-lhe o seu inquilino AZure AD e selecione **Azure Ative Directory > As definições do Utilizador > Gerir as definições de colaboração externa**.
1. As **permissões de utilizador do Utilizador do set Guest estão limitadas** ao **Nº.**
    ![Definições de colaboração externa](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configuração do URL de resposta

Uma vez implementada a Azure Blockchain Workbench, tem de configurar o **Azure** Ative Directory (Azure AD) aplicação de resposta ao url de resposta da web web blockchain Workbench implementada.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Verifique se está no inquilino onde registou o pedido de cliente Azure AD.
1. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registos de aplicações**.
1. Selecione a aplicação de cliente Azure AD que registou na secção pré-requisito.
1. Selecione **Autenticação**.
1. Especifique o URL web principal da implementação da bancada de workbench Azure Blockchain que recuperou na secção [de URL web blockchain Workbench.](#blockchain-workbench-web-url) O URL de resposta está pré-fixado com `https://` . Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de resposta à autenticação](media/deploy/configure-reply-url.png)

1. Na secção **de definição Avançada,** verifique **fichas de acesso** e **fichas de identificação**.

    ![Configurações avançadas de autenticação](media/deploy/authentication-advanced-settings.png)

1. **Selecione Guardar** para atualizar o registo do cliente.

## <a name="remove-a-deployment"></a>Remover uma implantação

Quando uma implementação já não é necessária, pode remover uma implementação eliminando o grupo de recursos blockchain Workbench.

1. No portal Azure, navegue para o **grupo De recursos** no painel de navegação esquerdo e selecione o grupo de recursos que pretende eliminar.
1. Selecione **Eliminar grupo de recursos**. Verifique a eliminação introduzindo o nome do grupo de recursos e selecione **Delete**.

    ![Eliminar grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, lançou a Azure Blockchain Workbench. Para aprender a criar uma aplicação blockchain, continue para o próximo artigo como fazer.

> [!div class="nextstepaction"]
> [Criar uma aplicação blockchain em Azure Blockchain Workbench](create-app.md)
