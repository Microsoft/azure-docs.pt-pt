---
title: Implantar FarmBeats do Azure
description: Este artigo descreve como implantar o Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941581"
---
# <a name="deploy-azure-farmbeats"></a>Implantar FarmBeats do Azure

Este artigo descreve como configurar o Azure FarmBeats.

O Azure FarmBeats é uma solução extensível e específica do setor para o pecuária controlado por dados que permite o provisionamento contínuo e a conectividade do dispositivo de sensor para a nuvem do Azure, a coleta de dados de telemetria e a agregação. O Azure FarmBeats inclui uma variedade de sensores, como câmeras, drones e sensores de solo. Com o Azure FarmBeats, você pode gerenciar dispositivos da nuvem, o que inclui o gerenciamento de infraestrutura e serviços no Azure para dispositivos preparados para IoT e o uso de um aplicativo Web e móvel extensível para fornecer visualização, alertas e informações.

> [!NOTE]
> O Azure FarmBeats tem suporte apenas em ambientes de nuvem pública. Para obter mais informações, consulte [o que é uma nuvem pública?](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

O Azure FarmBeats tem os dois componentes a seguir:

- **Datahub**: a camada de plataforma do FarmBeats do Azure, que permite criar, armazenar, processar dados e desenhar informações de pipelines de dados novos ou existentes. Essa camada de plataforma é útil para executar e criar seus modelos e pipelines de dados de agricultura.

- **Acelerador**: a camada de solução do Azure FarmBeats, que tem um aplicativo interno para ilustrar os recursos do Azure FarmBeats usando os modelos de agricultura pré-criados. Essa camada de solução permite criar limites de farm e extrair informações dos dados de agricultura dentro do contexto do limite do farm.

Uma implantação rápida do Azure FarmBeats deve levar menos de uma hora. Os custos de Datahub e aceleração variam, dependendo do uso.

## <a name="deployed-resources"></a>Recursos implantados

A implantação do Azure FarmBeats cria os seguintes recursos em sua assinatura:

| Série. n.º  | Nome do recurso  | Componente FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Estatísticas das Aplicações      |     Datahub/acelerador     |
|3  |Cache do Azure para Redis   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/acelerador        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Namespace do hub de eventos do Azure    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/acelerador      |
|8  |Conta do Batch    |Datahub   |
|9  |       Conta de armazenamento     |  Datahub/acelerador        |
|10  |    Aplicação lógica        |     Datahub      |
|11  |    Conexão de API        |     Datahub      |
|12|      Serviço de Aplicações do Azure      |  Datahub/acelerador       |
|13 |    Plano do serviço de aplicações        |     Datahub/acelerador      |
|14 |Conta do Azure Maps     |Accelerator    |

O Azure FarmBeats está disponível para download no Azure Marketplace, que você pode acessar diretamente do portal do Azure.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Criar uma oferta do Azure FarmBeats no Azure Marketplace

Para criar uma oferta do Azure FarmBeats no Azure Marketplace, faça o seguinte:

1. Entre no portal do Azure, selecione sua conta no canto superior direito e, em seguida, alterne para o locatário do Azure Active Directory (Azure AD) em que você deseja implantar o Azure FarmBeats.
1. O Azure FarmBeats está disponível no Azure Marketplace. Na página **do Azure Marketplace** , selecione **obter agora**.
1. Selecione **Criar**.

> [!NOTE]
> A conclusão da oferta no Azure Marketplace é apenas uma parte da instalação. Para concluir a implantação do Azure FarmBeats em sua assinatura do Azure, siga as instruções nas próximas seções.

## <a name="prepare"></a>Preparar

Para poder implantar o Azure FarmBeats, você precisará das seguintes permissões:

- **Locatário**: acesso de leitura
- **Assinatura**: colaborador ou proprietário
- **Grupo de recursos**: proprietário

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a implantação, verifique se os seguintes pré-requisitos estão em vigor:

- Uma conta do Sentinel
- Um registro de aplicativo do Azure AD

## <a name="create-a-sentinel-account"></a>Criar uma conta do Sentinel    

Uma conta com o Sentinel ajuda você a baixar as imagens de satélite do Sentinela de seu site oficial para seu dispositivo. Para criar uma conta gratuita, faça o seguinte:

1. Vá para a [página de registro da conta do Sentinel](https://scihub.copernicus.eu/dhus/#/self-registration).
1. No formulário de registro, forneça seu nome, sobrenome, nome de usuário, senha e endereço de email.

Um email de verificação será enviado para o endereço de email registrado para confirmação. Selecione o link para confirmar seu endereço de email. Seu processo de registro foi concluído.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registro de aplicativo do Azure AD

Para autenticação e autorização no Azure FarmBeats, você deve ter um registro de aplicativo do Azure AD. Você pode criá-lo de uma das duas maneiras:

* **Opção 1**: o instalador pode criar o registro automaticamente, desde que você tenha as permissões necessárias de acesso ao locatário, à assinatura e ao grupo de recursos. Se essas permissões estiverem em vigor, continue na seção ["preparar o arquivo de entrada. JSON"](#prepare-the-inputjson-file) .

* **Opção 2**: você pode criar e configurar o registro manualmente antes de implantar o Azure FarmBeats. Recomendamos esse método quando você não tiver as permissões necessárias para criar e configurar um registro de aplicativo do Azure AD em sua assinatura. Peça ao administrador para usar o [script personalizado](https://aka.ms/FarmBeatsAADScript), o que ajudará o administrador de ti a gerar e configurar automaticamente o registro de aplicativo do Azure AD no portal do Azure. Como uma saída para executar esse script personalizado usando o ambiente do PowerShell, o administrador de ti precisa compartilhar uma ID de cliente de aplicativo do Azure AD e um segredo de senha com você. Anote esses valores.

    Para executar o script de registro de aplicativo do Azure AD, faça o seguinte:

    1. Baixe o [script](https://aka.ms/FarmBeatsAADScript).
    1. Entre no portal do Azure e selecione sua assinatura e o locatário do Azure AD.
    1. Inicie o Cloud Shell na barra de ferramentas na parte superior da portal do Azure.

        ![FarmBeats do projeto](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Se você for um usuário de primeira vez, será solicitado a selecionar uma assinatura para criar uma conta de armazenamento e Microsoft Azure compartilhamento de arquivos. Selecione **Create storage** (Criar armazenamento).
    1. Os usuários de primeira hora também oferecem uma variedade de experiências de Shell, bash ou PowerShell. Escolha PowerShell.
    1. Carregue o script (da etapa 1) para Cloud Shell e anote o local do arquivo carregado.

        > [!NOTE]
        > Por padrão, o arquivo é carregado em seu diretório base.

        Use o seguinte script:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Anote a ID do aplicativo do Azure AD e o segredo do cliente para compartilhar com a pessoa que está implantando o Azure FarmBeats.

### <a name="prepare-the-inputjson-file"></a>Preparar o arquivo Input. JSON

Como parte da instalação, crie um arquivo *Input. JSON* , como mostrado aqui:

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Esse é o arquivo de entrada para Azure Cloud Shell. Ele contém os parâmetros cujos valores são usados durante a instalação. Todos os parâmetros no arquivo JSON precisam ser substituídos por valores apropriados ou removidos. Ele é removido, o instalador o solicitará durante a instalação.

Antes de preparar o arquivo, examine os parâmetros na tabela a seguir:

|Comando | Descrição|
|--- | ---|
|SKU  | Fornece uma opção para baixar um ou ambos os componentes do Azure FarmBeats. Especifica quais componentes baixar. Para instalar apenas Datahub, use "onlydatabhub". Para instalar o Datahub e o Accelerator, use "both".|
|subscriptionId | Especifica a assinatura para instalar o Azure FarmBeats.|
|datahubResourceGroup| O nome do grupo de recursos para seus recursos do Datahub.|
|localização |O local onde você deseja criar os recursos.|
|acceleratorWebsiteName |O prefixo de URL exclusivo para nomear sua instância de Datahub.|
|acceleratorResourceGroup  | O prefixo de URL exclusivo para nomear o site do acelerador.|
|datahubWebsiteName  | O prefixo de URL exclusivo para nomear seu site Datahub. |
|sentinelUsername | O nome de usuário com o qual entrar (por exemplo, `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | O endereço de email para receber as notificações de todos os alertas que você configurar em sua instância do Datahub.|
|updateIfExists| Adicional Um parâmetro a ser incluído no arquivo *Input. JSON* somente se você quiser atualizar uma instância existente do Azure FarmBeats. Para uma atualização, outros detalhes, como os nomes e locais do grupo de recursos, precisam ser os mesmos.|
|aadAppClientId | Adicional Um parâmetro a ser incluído no arquivo *Input. JSON* somente se o aplicativo do Azure ad já existir.  |
|aadAppClientSecret  | Adicional Um parâmetro a ser incluído no arquivo *Input. JSON* somente se o aplicativo do Azure ad já existir.|

## <a name="deploy-the-app-within-cloud-shell"></a>Implantar o aplicativo no Cloud Shell

Como parte do fluxo de trabalho do Marketplace abordado anteriormente, você deve ter criado um grupo de recursos e assinado o contrato de "termos de licença", que pode ser revisado novamente como parte da implantação real. Você pode implantar o aplicativo por meio do Cloud Shell interface de linha de comando baseada em navegador usando o ambiente bash. Para implantar via Cloud Shell, continue para as próximas seções.

> [!NOTE]
> As sessões inativas de Cloud Shell expiram após 20 minutos. Tente concluir a implantação neste momento.

1. Entre no portal do Azure e selecione a assinatura e o locatário do Azure que você deseja usar.
1. Inicie o Cloud Shell na barra de ferramentas na parte superior da portal do Azure.
1. Se estiver usando Cloud Shell pela primeira vez, você será solicitado a selecionar uma assinatura para criar uma conta de armazenamento e um compartilhamento de arquivos do Azure.
1. Selecione **criar armazenamento**.  
1. Para o ambiente, selecione **bash** (não PowerShell).

## <a name="deployment-scenario-1"></a>Cenário de implantação 1

Nesse cenário, que é descrito anteriormente na "opção 1," o instalador cria o registro de aplicativo do Azure AD automaticamente. Você implanta o FarmBeats fazendo o seguinte:

1. Copie o modelo JSON de exemplo a seguir e salve-o como *Input. JSON*. Lembre-se de anotar o caminho do arquivo no computador local.

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. Abra Azure Cloud Shell. Após a autenticação bem-sucedida, selecione o botão **carregar** (realçado na imagem a seguir) e, em seguida, carregue o arquivo *Input. json* no armazenamento Cloud Shell.  

    ![FarmBeats do projeto](./media/prepare-for-deployment/bash-2-1.png)

1. Vá para o diretório base em Cloud Shell. Por padrão, o nome do diretório é */home/\<nome de usuário >* .
1. Em Cloud Shell, digite o comando a seguir. Certifique-se de modificar o caminho para o arquivo *Input. JSON* e, em seguida, selecione a tecla Enter.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     O instalador baixa automaticamente todas as dependências e cria o implantador. Você será solicitado a concordar com os termos de licença do Azure FarmBeats.

     - Se você concordar, digite **Y** para prosseguir para a próxima etapa.
     - Se você não concordar, insira **N**e a implantação será encerrada.

1. No prompt, insira um token de acesso para a implantação. Copie o código gerado e faça logon na página de [logon do dispositivo](https://microsoft.com/devicelogin) com suas credenciais do Azure.

    > [!NOTE]
    > O token expira após 60 minutos. Se ele expirar, você poderá reiniciar redigitando o comando de implantação.

1. No prompt, insira a senha da conta do Sentinel.

   O instalador valida e inicia a implantação, o que pode levar cerca de 20 minutos.

   Depois que a implantação for concluída com êxito, você receberá os seguintes links de saída:

    - **URL do Datahub**: o link do Swagger para experimentar as APIs do FarmBeats do Azure.
    - **URL do acelerador**: a interface do usuário para explorar o acelerador do Azure FarmBeats Smart farm.
    - **Arquivo de log do implantador**: o arquivo de log que é criado durante a implantação. Você pode usá-lo para solução de problemas, se necessário.

## <a name="deployment-scenario-2"></a>Cenário de implantação 2

Nesse cenário, que é descrito anteriormente na "opção 2", você usa o registro de aplicativo Azure Active Directory existente para implantar o FarmBeats fazendo o seguinte:

1. Copie o seguinte arquivo JSON, que inclui o Aplicativo Azure ID do cliente e a senha no arquivo *Input. JSON* , e salve-o. Lembre-se de anotar o caminho do arquivo no computador local.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. Vá para Azure Cloud Shell novamente e você será autenticado com êxito.
1. Selecione o botão **carregar** (realçado na imagem a seguir) e, em seguida, carregue o arquivo *Input. json* no armazenamento Cloud Shell.

    ![FarmBeats do projeto](./media/prepare-for-deployment/bash-2-1.png)

1. Vá para o diretório base em Cloud Shell. Por padrão, o nome do diretório é */home/\<nome de usuário >* .
1. Em Cloud Shell, digite o comando a seguir. Certifique-se de modificar o caminho para o arquivo *Input. JSON* e, em seguida, selecione a tecla Enter.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     O instalador baixa automaticamente todas as dependências e cria o implantador. Você será solicitado a concordar com os termos de licença do Azure FarmBeats.

     - Se você concordar, digite **Y** para prosseguir para a próxima etapa.
     - Se você não concordar, insira **N**e a implantação será encerrada.

1. No prompt, insira um token de acesso para a implantação. Copie o código gerado e faça logon na página de [logon do dispositivo](https://microsoft.com/devicelogin) com suas credenciais do Azure.

   O instalador valida e inicia a implantação, o que pode levar cerca de 20 minutos.

   Depois que a implantação for concluída com êxito, você receberá os seguintes links de saída:

    - **URL do Datahub**: o link do Swagger para experimentar as APIs do FarmBeats do Azure.
    - **URL do acelerador**: a interface do usuário para explorar o acelerador do Azure FarmBeats Smart farm.
    - **Arquivo de log do implantador**: o arquivo de log que é criado durante a implantação. Você pode usá-lo para solução de problemas, se necessário.

Se você encontrar problemas, examine [solucionar problemas](troubleshoot-project-farmbeats.md).


## <a name="validate-the-deployment"></a>Validar a implementação

### <a name="datahub"></a>Datahub

Depois que a instalação do Datahub for concluída, você receberá a URL para acessar as APIs do FarmBeats do Azure por meio da interface do Swagger no formato https://\<yourdatahub-site-Name >. azurewebsites. net/Swagger.

1. Para entrar por meio do Swagger, copie e cole a URL em seu navegador.
2. Entre com suas credenciais do portal do Azure.
3. Você pode ver o Swagger e executar todas as operações REST nas APIs do Azure FarmBeats. Isso indica uma implantação bem-sucedida do Azure FarmBeats.

### <a name="accelerator"></a>Accelerator

Depois que a instalação do acelerador for concluída, você receberá a URL para acessar a interface do usuário FarmBeats do Azure no formato https://\<Accelerator-site-Name >. azurewebsites. net.

1. Para entrar do acelerador, copie e cole a URL no navegador.
1. Entre com suas credenciais do portal do Azure.

## <a name="upgrade"></a>Atualização

As instruções para atualização são semelhantes às da primeira instalação. Faça o seguinte:

1. Entre no portal do Azure e, em seguida, selecione a assinatura desejada e o locatário do Azure.
1. Abra Cloud Shell na parte superior da navegação do portal do Azure.

   ![FarmBeats do projeto](./media/prepare-for-deployment/navigation-bar-1.png)

1. Na lista suspensa **ambiente** à esquerda, selecione **bash**.
1. Se necessário, faça alterações no arquivo *Input. JSON* e, em seguida, carregue-o no Cloud Shell. Por exemplo, você pode atualizar seu endereço de email para a notificação que deseja receber.
1. Digite ou cole os dois comandos a seguir em Cloud Shell. Certifique-se de modificar o caminho do arquivo *Input. JSON* e, em seguida, selecione a tecla Enter.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   O instalador solicita automaticamente as entradas necessárias em tempo de execução.

1. Insira um token de acesso para implantação. Copie o código gerado e entre na [página de logon do dispositivo](https://microsoft.com/devicelogin) com suas credenciais do Azure.
1. Insira sua senha do Sentinel.

   Agora, o instalador valida e inicia a criação dos recursos, o que pode levar cerca de 20 minutos.

   Depois que a implantação for concluída com êxito, você receberá os seguintes links de saída:
    - **URL do Datahub**: o link do Swagger para experimentar as APIs do FarmBeats do Azure.
    - **URL do acelerador**: a interface do usuário para explorar o acelerador do Azure FarmBeats Smart farm.
    - **Arquivo de log do implantador**: o arquivo de log que é criado durante a implantação. Você pode usá-lo para solução de problemas, se necessário.

> [!NOTE]
> Anote os valores anteriores para uso futuro.


## <a name="uninstall"></a>Desinstalar

Atualmente, não há suporte para a desinstalação automatizada do Azure FarmBeats usando o instalador. Para remover o Datahub ou acelerador, no portal do Azure, exclua o grupo de recursos no qual esses componentes estão instalados ou exclua os recursos manualmente.

Por exemplo, se você implantou o Datahub e o acelerador em dois grupos de recursos diferentes, você pode excluir esses grupos de recursos da seguinte maneira:

1. Inicie sessão no Portal do Azure.
1. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD no qual você deseja implantar o Azure FarmBeats.

   > [!NOTE]
   > Datahub é necessário para que o acelerador funcione corretamente. Não recomendamos a desinstalação do Datahub sem a desinstalação do acelerador.

1. Selecione **grupos de recursos**e digite o nome do grupo de recursos Datahub ou acelerador que você deseja excluir.
1. Selecione o nome do grupo de recursos. Digite novamente o nome para confirmá-lo e, em seguida, selecione **excluir** para remover o grupo de recursos e todos os seus recursos subjacentes.
   Como alternativa, você pode excluir cada recurso manualmente, um método que não recomendamos.
1. Para excluir ou desinstalar o Datahub, vá para o grupo de recursos diretamente no Azure e, em seguida, exclua o grupo de recursos desse local.

## <a name="next-steps"></a>Passos seguintes

Você aprendeu a implantar o Azure FarmBeats. Em seguida, saiba como [criar farms do FarmBeats](manage-farms.md#create-farms).
