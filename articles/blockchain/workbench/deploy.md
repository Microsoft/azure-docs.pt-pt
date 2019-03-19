---
title: Implementar o Azure Blockchain Workbench
description: Como implementar o Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 7fead05e7404e042d923631f4ba745553085943a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098115"
---
# <a name="deploy-azure-blockchain-workbench"></a>Implementar o Azure Blockchain Workbench

O Azure Blockchain Workbench é implementado com um modelo de solução no Azure Marketplace. O modelo simplifica a implementação de componentes necessários para criar aplicações de blockchain. Uma vez implantado, o Blockchain Workbench fornece acesso a aplicações de cliente para criar e gerir utilizadores e aplicações de blockchain.

Para obter mais informações sobre os componentes de Blockchain Workbench, consulte [arquitetura de Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Preparar para a implementação

Blockchain Workbench permite-lhe implementar um livro razão de blockchain, juntamente com um conjunto de serviços do Azure relevantes mais frequentemente utilizada para criar uma aplicação baseada em blockchain. Implantar o Blockchain Workbench resulta nos seguintes serviços do Azure a ser aprovisionados num grupo de recursos na sua subscrição do Azure.

* Tópico do Event Grid 1 evento
* O espaço de nomes do 1 do Service Bus
* 1 application Insights
* 1 banco de dados do SQL (Standard S0)
* 2 serviços de aplicações (Standard)
* 2 cofres de chaves do azure
* 2 contas de armazenamento do azure (Standard LRS)
* 2 conjuntos de dimensionamento de máquinas virtuais (para nós de trabalho e a validação do)
* 2 redes virtuais (incluindo o Balanceador de carga, o grupo de segurança de rede e o endereço IP público para cada rede virtual)
* Opcional: Azure Monitor

Segue-se um exemplo de implementação criado num **myblockchain** grupo de recursos.

![Exemplo de implementação](media/deploy/example-deployment.png)

O custo de Blockchain Workbench é um agregado do custo dos serviços do Azure subjacentes. Informações sobre preços do serviços do Azure podem ser calculados utilizando a [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

> [!IMPORTANT]
> Se estiver a utilizar uma subscrição com limites de serviço baixa, como uma subscrição do escalão gratuito do Azure, a implementação poderá falhar devido a insuficiente quota de núcleos VM. Antes da implantação, verifique a sua quota com a documentação de orientação do [quotas de vCPU de máquina virtual](../../virtual-machines/windows/quotas.md) artigo. A seleção de VM padrão exige 6 núcleos VM. Como a alteração para um tamanho mais pequeno de VM *Standard DS1 v2* reduz o número de núcleos para 4.

## <a name="prerequisites"></a>Pré-requisitos

O Azure Blockchain Workbench requer registos de configuração e a aplicação do Azure AD. Pode optar por fazer o Azure AD [configurações manualmente](#azure-ad-configuration) antes da implantação ou executar um script após a implementação. Se estiver a Reimplementar o Blockchain Workbench, veja [configuração do Azure AD](#azure-ad-configuration) para verificar a configuração do Azure AD.

> [!IMPORTANT]
> Bancada de trabalho não tem de ser implementado no mesmo inquilino que está a utilizar para registar uma aplicação do Azure AD. Bancada de trabalho tem de ser implementada num inquilino em que tem permissões suficientes para implementar recursos. Para obter mais informações sobre os inquilinos do Azure AD, consulte [como obter um inquilino do Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [integrar aplicações com o Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).



## <a name="deploy-blockchain-workbench"></a>Implementar o Blockchain Workbench

Depois dos passos de pré-requisitos foram concluídos, está pronto para implementar o Blockchain Workbench. As secções seguintes descrevem como implantar o framework.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a sua conta no canto superior direito e mude para o desejado inquilino do Azure AD onde pretende implementar o Azure Blockchain Workbench.
3. No painel esquerdo, selecione **criar um recurso**. Procure `Azure Blockchain Workbench` no **pesquisar no Marketplace** barra de pesquisa. 

    ![Barra de pesquisa do Marketplace](media/deploy/marketplace-search-bar.png)

4. Selecione **o Azure Blockchain Workbench**.

    ![Resultados da pesquisa de mercado](media/deploy/marketplace-search-results.png)

5. Selecione **Criar**.
6. Conclua as definições básicas.

    ![Criar o Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Definição | Descrição  |
    |---------|--------------|
    | Prefixo de recursos | Identificador exclusivo curto para a sua implementação. Este valor é utilizado como base para atribuir nomes a recursos. |
    | Nome de utilizador VM | O nome de utilizador é utilizado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação | Selecione se pretende utilizar uma palavra-passe ou a chave para ligar a VMs. |
    | Palavra-passe | A palavra-passe é utilizada para ligar a VMs. |
    | SSH | Utilizar uma chave pública RSA no início com o formato de linha única **ssh-rsa** ou utilize o formato PEM com várias linha. Pode gerar chaves SSH com `ssh-keygen` em Linux e OS X ou PuTTYGen no Windows. Obter mais informações sobre chaves SSH, veja [chaves de como utilizar SSH com Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Palavra-passe de base de dados / Confirmar palavra-passe de base de dados | Especifique a palavra-passe a utilizar para acessar o banco de dados criado como parte da implementação. |
    | Região de implementação | Especifique onde pretende implementar recursos de Blockchain Workbench. Para uma melhor disponibilidade, isto deve corresponder a **localização** definição. |
    | Subscrição | Especifique a subscrição do Azure que pretende utilizar para a sua implementação. |
    | Grupos de recursos | Criar um novo grupo de recursos, selecionando **criar novo** e especifique um nome de grupo de recursos exclusivo. |
    | Localização | Especifica a região que pretende implementar a estrutura. |

7. Selecione **OK** para concluir a seção de configuração da definição básico.

8. Na **definições avançadas**, escolha se pretende criar uma nova rede de blockchain ou utilizar uma rede de uma prova da autoridade blockchain existente.

    Para **criar novo**:

    O *criar um novo* opção cria um conjunto de nós de prova de Ethereum autoridade (PoA) numa subscrição de um único membro. 

    ![Definições avançadas para a nova rede de blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Definição | Descrição  |
    |---------|--------------|
    | Monitorização | Escolha se pretende ativar o Azure Monitor para monitorizar a sua rede de blockchain |
    | Definições do Active Directory do Azure | Escolher **adicionar posteriormente**.</br>Nota: Se optar por [pré-configurar o Azure AD](#azure-ad-configuration) ou estiver a Reimplementar, optar por *adicionar agora*. |
    | Seleção de VM | Escolha o tamanho da VM preferencial para a sua rede de blockchain. Escolha um tamanho VM mais pequeno, tal como *Standard DS1 v2* se estiver a utilizar uma subscrição com limites de serviço baixa, como o escalão gratuito do Azure. |

    Para **utilizar existente**:

    O *utilizar existente* opção permite-lhe especificar uma rede de blockchain Ethereum prova-of-Authority (PoA). Pontos finais têm os seguintes requisitos.

   * O ponto final tem de ser uma rede de blockchain Ethereum prova-of-Authority (PoA).
   * O ponto final tem de ser publicamente acessível através da rede.
   * A rede de blockchain PoA deve ser configurada para ter o preço de gás definida como zero.

     > [!NOTE]
     > Não, porque são financiadas contas de Blockchain Workbench. Se os fundos forem necessários, as transações falharem.

     ![Definições avançadas para a rede de blockchain existente](media/deploy/advanced-blockchain-settings-existing.png)

     | Definição | Descrição  |
     |---------|--------------|
     | Ponto de extremidade Ethereum RPC | Forneça o ponto de extremidade RPC de uma rede de blockchain PoA existente. O ponto final começa com https:// ou http:// e termina com um número de porta. Por exemplo, `http<s>://<network-url>:<port>` |
     | Definições do Active Directory do Azure | Escolher **adicionar posteriormente**.</br>Nota: Se optar por [pré-configurar o Azure AD](#azure-ad-configuration) ou estiver a Reimplementar, optar por *adicionar agora*. |
     | Seleção de VM | Escolha o tamanho da VM preferencial para a sua rede de blockchain. |

9. Selecione **OK** para concluir as definições avançadas.

10. Reveja o resumo para verificar que os parâmetros são precisos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

11. Selecione **criar** para aceitar os termos e implementar o Azure Blockchain Workbench.

A implementação pode demorar até 90 minutos. Pode utilizar o portal do Azure para monitorizar o progresso. No grupo de recursos criado recentemente, selecione **implementações > Descrição geral** para ver o estado dos artefatos implementados.

> [!IMPORTANT]
> Post implantação, precisa para concluir as definições do Active Directory. Se escolheu **adicionar mais tarde**, tem de executar o [script de configuração do Azure AD](#azure-ad-configuration-script).  Se escolheu **adicionar agora**, precisa [configurar o URL de resposta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>URL de Web de Blockchain Workbench

Depois de concluída a implementação da bancada de trabalho de Blockchain, um novo grupo de recursos contém recursos do Blockchain Workbench. Serviços de Blockchain Workbench são acedidos através de um URL de web. Os passos seguintes mostram como obter o URL de web do framework implantado.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. No painel de navegação do lado esquerdo, selecione **grupos de recursos**
3. Escolha o nome do grupo de recursos que especificou ao implementar o Blockchain Workbench.
4. Selecione o **tipo** cabeçalho de coluna para ordenar a lista por ordem alfabética por tipo.
5. Existem dois recursos com tipo **serviço de aplicações**. Selecione o tipo de recurso **serviço de aplicações** *sem* o "-api" sufixo.

    ![Lista de aplicações do serviço](media/deploy/resource-group-list.png)

6. No serviço de aplicações **Essentials** secção, copie a **URL** valor, que representa o URL da web para o seu Blockchain Workbench implementado.

    ![Princípios básicos do serviço de aplicações](media/deploy/app-service.png)

Para associar um nome de domínio personalizado com o Blockchain Workbench, veja [configurar um nome de domínio personalizado para uma aplicação web no serviço de aplicações do Azure utilizando o Gestor de tráfego](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Script de configuração do Azure AD

O Azure AD tem de ser configurado para concluir a implementação de Blockchain Workbench. Usará um script do PowerShell para fazer a configuração.

1. Num browser, navegue para o [URL de Web do Blockchain Workbench](#blockchain-workbench-web-url).
2. Verá instruções para configurar o Azure AD através do Cloud Shell. Copie o comando e iniciar o Cloud Shell.

    ![Iniciar o script AAD](media/deploy/launch-aad-script.png)

3. Escolha o inquilino do Azure AD onde implementou o Blockchain Workbench.
4. No Cloud Shell, colar e execute o comando.
5. Quando lhe for pedido, introduza o inquilino do Azure AD que pretende utilizar para o Blockchain Workbench. Este será o inquilino que contém os utilizadores para o Blockchain Workbench.

    > [!IMPORTANT]
    > O utilizador autenticado requer permissões para criar registos de aplicações do Azure AD e conceder permissões de delegado de aplicação no inquilino. Terá de pedir a um administrador do inquilino para executar o script de configuração do Azure AD ou criar um novo inquilino.

    ![Introduza o inquilino do Azure AD](media/deploy/choose-tenant.png)

6. Será solicitado para autenticar para o inquilino do Azure AD através de um browser. Abrir o URL do web num browser e introduza o código para autenticar.

    ![Autenticar com o código](media/deploy/authenticate.png)

7. O script emite várias mensagens de estado. Obterá uma **êxito** mensagem de estado se o inquilino foi aprovisionado com êxito.
8. Navegue para o URL de Blockchain Workbench. É-lhe perguntado para dar consentimento para conceder as permissões de leitura para o diretório. Isso permite que o Blockchain Workbench acesso a aplicações web para os utilizadores no inquilino. Se for administrador de inquilinos, pode optar por dar consentimento para toda a organização. Esta opção aceita consentimento para todos os utilizadores no inquilino. Caso contrário, cada utilizador é solicitado consentimento na primeira utilização da aplicação web Blockchain Workbench.
9. Selecione **Accept** para dar consentimento.

     ![Consentimento para ler os perfis de utilizadores](media/deploy/graph-permission-consent.png)

10. Depois de consentimento, pode ser utilizada a aplicação web de Blockchain Workbench.

## <a name="azure-ad-configuration"></a>Configuração do AD do Azure

Se optar por configurar ou verificar as definições do Azure AD antes da implantação manualmente, execute todos os passos nesta secção. Se preferir configurar automaticamente as definições do Azure AD, utilize [um script de configuração do Azure AD](#azure-ad-configuration-script) depois de implementar o Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registo de aplicações API do Blockchain Workbench

Implementação de Blockchain Workbench requer o registo de uma aplicação do Azure AD. Precisa de um inquilino do Azure Active Directory (Azure AD) para registar a aplicação. Pode utilizar um inquilino existente ou criar um novo inquilino. Se estiver a utilizar um inquilino do Azure AD existente, tem permissões suficientes para registar aplicações, conceder permissões da Graph API e permitir o acesso de convidado dentro de um inquilino do Azure AD. Se não tiver permissões suficientes no inquilino do Azure AD existente, crie um novo inquilino.


1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a sua conta no canto superior direito e mude para o Azure AD pretendido do inquilino. O inquilino deve ser o inquilino do administrador de subscrição da subscrição em que a Bancada de trabalho é implementada e tem permissões suficientes para registar aplicações.
3. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **registos de aplicações** > **novo registo de aplicação**.

    ![Registo de aplicações](media/deploy/app-registration.png)

4. Fornecer um **Name** e **URL de início de sessão** para a aplicação. Pode utilizar valores de marcador de posição, uma vez que os valores são alterados durante a implantação. 

    ![Criar registo de aplicações](media/deploy/app-registration-create.png)

    |Definição  | Valor  |
    |---------|---------|
    |Name | `Blockchain API` |
    |Tipo de aplicação |Aplicação/API Web|
    |URL de início de sessão | `https://blockchainapi` |

5. Selecione **criar** para registar a aplicação do Azure AD.

### <a name="modify-manifest"></a>Modificar o manifesto

Em seguida, terá de modificar o manifesto para utilizar funções de aplicação no Azure AD para especificar administradores do Blockchain Workbench.  Para obter mais informações sobre manifestos da aplicação, consulte [manifesto de aplicação do Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Para a aplicação que registou, selecione **manifesto** no painel de detalhes de aplicação registada.
2. Gere um GUID. Pode gerar um GUID usando o comando do PowerShell [guid]:: Novo_guid (-) ou o cmdlet New-GUID. Outra opção consiste em utilizar um Web site de gerador GUID.
3. Pretender atualizar o **appRoles** secção do manifesto. No painel de manifesto de edição, selecione **edite** e substitua `"appRoles": []` com o JSON fornecido. Certifique-se de que substitua o valor para o **id** campo com o GUID é gerado. 

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
    > O valor **administrador** é necessário para identificar os administradores de Blockchain Workbench.

4. No manifesto, também alterar o **Oauth2AllowImplicitFlow** valor **verdadeiro**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Selecione **guardar** para guardar as alterações de manifestos.

### <a name="add-graph-api-required-permissions"></a>Adicionar permissões da Graph API necessárias

A aplicação de API tem de pedir permissão ao utilizador para aceder ao diretório. Defina a seguinte permissão necessária para a aplicação de API:

1. No registo de aplicações API do Blockchain, selecione **definições > permissões obrigatórias > selecionar uma API > Microsoft Graph**.

    ![Selecionar uma API](media/deploy/client-app-select-api.png)

    Clique em **Selecionar**.

2. Na **ativar o acesso ao** sob **permissões delegadas**, escolha **ler perfis básicos de todos os utilizadores**.

    ![Ativar o acesso](media/deploy/client-app-read-perms.png)

    Selecione **salvar** , em seguida, selecione **feito**.

3. Na **permissões obrigatórias**, selecione **conceder permissões** , em seguida, selecione **Sim** para o pedido de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   Concessão de permissão permite a Bancada de trabalho de Blockchain aceder aos utilizadores no diretório. A permissão de leitura é necessária para procurar e adicionar membros a Bancada de trabalho de Blockchain.

### <a name="get-application-id"></a>Obter ID da aplicação

As informações de inquilino e ID de aplicação são necessários para a implementação. Recolher e armazenar as informações para utilização durante a implementação.

1. Para a aplicação que registou, selecione **configurações** > **propriedades**.
2. Na **propriedades** painel, cópia e arquivo, os seguintes valores para utilizar mais tarde durante a implementação.

    ![Propriedades da aplicação API](media/deploy/app-properties.png)

    | Definição para armazenar  | Utilizar na implementação |
    |------------------|-------------------|
    | ID da aplicação | A configuração do Azure Active Directory > ID de aplicação |

### <a name="get-tenant-domain-name"></a>Obter nome de domínio de inquilino

Recolher e armazenar o nome de domínio de inquilino do Active Directory onde as aplicações estão registadas. 

No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Nomes de domínio personalizados**. Copie e armazene o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Definições do utilizador convidado

Se tiver utilizadores convidados no seu inquilino do Azure AD, siga os passos adicionais para garantir que a Bancada de trabalho de Blockchain atribuição de utilizador e gestão funciona corretamente.

1. Mudar de inquilino do Azure AD e selecione **do Azure Active Directory > definições de utilizador > Gerir definições de colaboração externa**.
2. Definir **permissões de utilizador convidado são limitadas** ao **não**.
    ![Definições de colaboração externa](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configurar o URL de resposta

Depois de ter sido implementado o Azure Blockchain Workbench, tem de configurar a aplicação de cliente do Azure Active Directory (Azure AD) **URL de resposta** da bancada de trabalho de Blockchain implementado URL da web.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Certifique-se de que está no inquilino onde que registou a aplicação de cliente do Azure AD.
3. No painel de navegação do lado esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Registos das aplicações**.
4. Selecione a aplicação de cliente do Azure AD que registou na secção de pré-requisitos.
5. Selecione **definições > URLs de resposta**.
6. Especifique o URL da web principal da implementação do Azure Blockchain Workbench, que obteve na **obter o URL de Web do Azure Blockchain Workbench** secção. O URL de resposta é o prefixo `https://`. Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de Resposta](media/deploy/configure-reply-url.png)

7. Selecione **guardar** para atualizar o registo de cliente.

## <a name="remove-a-deployment"></a>Remover uma implementação

Quando já não é necessária uma implementação, pode remover uma implementação ao eliminar o grupo de recursos de Blockchain Workbench.

1. No portal do Azure, navegue até **grupo de recursos** no painel de navegação esquerda e selecione o grupo de recursos que pretende eliminar. 
2. Selecione **Eliminar grupo de recursos**. Verifique a eliminação ao introduzir o nome do grupo de recursos e selecione **eliminar**.

    ![Eliminar grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo de procedimentos, implementou o Azure Blockchain Workbench. Para saber como criar uma aplicação de blockchain, avance para o próximo artigo de procedimento.

> [!div class="nextstepaction"]
> [Criar uma aplicação de blockchain no Azure Blockchain Workbench](create-app.md)
