---
title: Implantar FarmBeats do Azure
description: Descreve como implantar o FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797728"
---
# <a name="deploy-farmbeats"></a>Implantar FarmBeats

Este artigo descreve como configurar o Azure FarmBeats.

O Azure FarmBeats é uma solução extensível e específica do setor para o pecuária controlado por dados que permite o provisionamento contínuo e a conectividade de dispositivos de sensor para a nuvem do Azure, a coleta de dados de telemetria e a agregação. O Azure FarmBeats tem vários sensores, como câmeras, drones, sensores de solo e gerenciamento de dispositivos da nuvem, o que inclui infraestrutura e serviços no Azure para dispositivos preparados para IoT (Internet das Coisas) para um extensíveis Web e aplicativo móvel para fornecer visualização, alertas e ideias.

> [!NOTE]
> O Azure FarmBeats tem suporte apenas em ambientes de nuvem pública. Para obter mais informações sobre o ambiente de nuvem, consulte [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

O Azure FarmBeats tem os dois componentes a seguir:

- **Hub de dados** -o Hub de dados é a camada de plataforma do Azure FarmBeats que permite criar, armazenar, processar dados e extrair informações de pipelines de dados novos ou existentes. Essa camada de plataforma é útil para executar e criar seus modelos e pipelines de dados de agricultura.

- **Accelerator** -Accelerator é a camada de solução do Azure FarmBeats que tem um aplicativo interno para ilustrar os recursos do Azure FarmBeats usando os modelos de agricultura pré-criados. Essa camada de solução permite criar limites de farm e extrair informações dos dados de agricultura dentro do contexto do limite do farm.

Uma implantação rápida do Azure FarmBeats deve levar menos de uma hora. Os custos para o Hub de dados e acelerador variam de acordo com o uso.

## <a name="deployed-resources"></a>Recursos implantados

A implantação do Azure FarmBeats cria os recursos listados abaixo em sua assinatura:

|S.No  |Nome do Recurso  |Componente FarmBeats do Azure  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Hub de dados       |
|2  |    Application Insights      |     Hub de dados/acelerador     |
|3  |Cache do Azure para Redis   |Hub de dados   |
|4  |       Cofre de chaves do Azure    |  Hub de dados/acelerador        |
|5  |    Time Series Insights       |     Hub de dados      |
|6 |      Namespace do EventHub    |  Hub de dados       |
|7  |    Azure Data Factory V2       |     Hub de dados/acelerador      |
|8  |Conta do Batch    |Hub de dados   |
|9  |       Conta de armazenamento     |  Hub de dados/acelerador        |
|10  |    Aplicação lógica        |     Hub de dados      |
|11  |    Conexão de API        |     Hub de dados      |
|12|      Serviço de aplicativo      |  Hub de dados/acelerador       |
|13 |    plano do serviço de aplicativo        |     Hub de dados/acelerador      |
|14 |Conta do Azure Maps     |Accelerator    |
|15 |       Time Series Insights      |  Hub de dados     |

O Azure FarmBeats está disponível para download no Azure Marketplace. Você pode acessá-lo diretamente do portal do Azure.  

## <a name="prepare"></a>Preparação

Você precisa das seguintes permissões para implantar o Azure FarmBeats:

- Locatário: acesso de leitura
- Assinatura: colaborador do proprietário
- Grupo de recursos: proprietário

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a implantação, verifique se você tem o seguinte:

- Conta do Sentinel
- Azure Active Directory (registro do aplicativo)
- FarmBeats do Azure

## <a name="create-a-sentinel-account"></a>Criar uma conta do Sentinel    

Uma conta com o Sentinel ajuda você a baixar as imagens de satélite do Sentinela de seu site oficial para seu dispositivo. Siga estas etapas para criar uma conta gratuita:

1. Aceda a https://scihub.copernicus.eu/dhus/#/self-registration. Na página de registro, forneça um nome, sobrenome, nome de usuário, senha e email.
2. No menu suspenso **selecionar domínio** , selecione a opção **Land**.
3. No menu suspenso **selecionar uso** , selecione a opção **educação**.
4. No menu suspenso **Selecionar país** , selecione seu país.
5. Selecione **registrar** para concluir o processo de registro.

Um email de verificação será enviado para o endereço de email registrado para confirmação. Selecione o link e confirme. Seu processo de registro foi concluído.

## <a name="create-azure-ad-app-registration"></a>Criar registro de aplicativo do Azure AD

Para autenticação e autorização no Azure FarmBeats, você deve ter um registro de aplicativo do Azure Active Directory que:

- Caso 1: o instalador pode criar automaticamente (desde que você tenha as permissões de acesso de locatário, assinatura e grupo de recursos necessárias).
- Caso 2: você pode criar e configurar o antes de implantar o Azure FarmBeats (requer etapas manuais).

**Caso 1**: o instalador assume que você tem os direitos para criar um registro de aplicativo do Azure Active Directory dentro da assinatura desejada. Para se registrar, entre no portal, vá para **Azure active directory** > **registro de aplicativo** > **novo registro**.

Se você já tiver uma assinatura, poderá mover diretamente para o próximo procedimento.

**Caso 2**: esse método é a etapa preferida quando você não tem direitos suficientes para criar e configurar um registro de aplicativo do Azure AD em sua assinatura. Solicite que seu administrador use o [script personalizado](https://aka.ms/FarmBeatsAADScript), o que ajudará o administrador de ti a gerar e configurar automaticamente o registro de aplicativo do Azure AD no portal do Azure. Como uma saída para executar esse script personalizado usando o ambiente do PowerShell, o administrador de ti precisa compartilhar uma ID de cliente de aplicativo Azure Active Directory e um segredo de senha com você. Anote esses valores.

Use as seguintes etapas para executar o script de registro de aplicativo do Azure AD:

1. Obtenha o [script](https://aka.ms/FarmBeatsAADScript)de registro.
2. Entre no portal do Azure e selecione sua assinatura e locatário do AD.
3. Inicie o Cloud Shell a partir do menu de navegação superior do portal do Azure.

    ![Batidas no farm de projetos](./media/prepare-for-deployment/navigation-bar-1.png)


4. Os usuários de primeira hora serão solicitados a selecionar uma assinatura para criar uma conta de armazenamento e Microsoft Azure compartilhamento de arquivos. Selecione **Create storage** (Criar armazenamento).
5. Na primeira vez, os usuários serão solicitados a escolher a experiência de shell preferencial ou o PowerShell. Escolha PowerShell.
6. Em Cloud Shell Insira os comandos abaixo para executar o script.

    ```powershell
    ./create_aad_script.ps1
    ```
7. Anote a ID do aplicativo do Azure AD e o segredo do cliente para compartilhar com a pessoa que está implantando o Azure FarmBeats.

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Criar uma oferta do Azure FarmBeats no Marketplace

Use estas etapas para criar uma oferta do Azure FarmBeats no Marketplace:

1. Entre no **portal do Azure** e selecione sua conta no canto superior direito e alterne para o locatário do Azure ad onde você deseja implantar Microsoft Azure FarmBeats.
2. Selecione **Pesquisar/Marketplace** ou **criar recursos**. Digite **FarmBeats** para exibir os detalhes da oferta. Baixe os guias disponíveis por meio de hiperlinks do aka.ms nesta página antes de prosseguir para as próximas etapas.
3. Selecione **criar** e insira as seguintes informações:

   - Insira o nome da assinatura.
   - Insira um nome de grupo de recursos existente (somente grupo de recursos vazio) ou crie um novo grupo de recursos para implantar o Azure FarmBeats. Anote esse grupo de recursos para inserir dentro do arquivo Input. JSON em um estágio posterior.
   - Insira a região na qual você deseja instalar o Azure FarmBeats. O FarmBeats pode ser instalado atualmente nessas regiões: EUA Central, Europa Ocidental, leste dos EUA 2, Europa Setentrional, oeste dos EUA, Sudeste Asiático, leste dos EUA, leste da Austrália, oeste dos EUA 2.
4. Selecione **OK**, que redireciona você para a página de termos de uso. Examine os termos do Marketplace padrão ou selecione o hiperlink para revisar os termos de uso.
5. Selecione **fechar**e, em seguida, a caixa de seleção "concordo" e, em seguida, selecione **criar**.
6. Agora você assinou com êxito o contrato de licença de usuário final (EULA) do Azure FarmBeats no Marketplace. Para continuar com a implantação, siga as próximas etapas deste guia.

### <a name="prepare-inputjson-file"></a>Preparar o arquivo Input. JSON

Esse é o arquivo de entrada para Azure Cloud Shell e parâmetros cujos valores são especificados nesse arquivo antes que o carregamento não seja solicitado durante a implantação, portanto, economizando algum tempo.  

> [!NOTE]
> Esse arquivo insere valores para Azure Cloud Shell.  Para economizar tempo, durante a implantação, não serão solicitados os parâmetros que você adicionar a esse arquivo. Você será solicitado a fornecer parâmetros ausentes.

Examine os parâmetros antes de preparar o arquivo.

|Comando | Descrição|
|--- | ---|
|SKU  | Fornece uma opção para baixar um ou ambos os componentes do Azure FarmBeats. Especifica quais componentes baixar. Para instalar apenas o Hub de dados, use "onlydatabhub". Para instalar o Hub de dados e o acelerador, use "ambos".|
|SubscriptionId  | Especifica a assinatura para instalar o FarmBeats|
|"datahubResourceGroup"  | Nome do grupo de recursos para recursos do hub de dados.|
|"datahubLocation" | Local onde você gostaria de armazenar os recursos do hub de dados.|
|"acceleratorWebsiteName"  |Prefixo de URL exclusivo para nomear o Hub de dados
Site do Swagger. O padrão é o nome do grupo de recursos do hub de dados. Pressione Enter para continuar com o valor padrão.|
|"acceleratorResourceGroup"  | Nome do grupo de recursos para recursos do hub de dados. |
|"acceleratorLocation"  | Local para armazenar os recursos do hub de dados
"acceleratorWebsiteName"  | Prefixo de URL exclusivo para nomear o site do acelerador. O padrão é acelerador. Pressione Enter para continuar com o valor padrão.|
|''sentinelUsername'' | nome de usuário para entrar: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"sentinelPassword"  | Senha para entrar: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"farmbeatsAppId"  | Valores a serem compartilhados pelo Team Azure FarmBeats.  |
|"farmbeatsPassword"  | Valores a serem compartilhados pelo Team Azure FarmBeats.|
|"notificationEmailAddress"  | Endereço de email para receber as notificações de todos os alertas que você configurar no Hub de dados.|
|"atualizaç" aadAppClientId ""  |[**Opcional**] Parâmetro a ser incluído no Input. JSON somente se o aplicativo do Azure AD já existir.  -True/false. False para uma instalação inicial e verdadeiro para o cenário de atualização.|
|"aadAppClientId"  | [**Opcional**] Parâmetro a ser incluído no Input. JSON somente se o aplicativo do Azure AD já existir.  |
|"aadAppClientSecret"   | [**Opcional**] Parâmetro a ser incluído no Input. JSON somente se o aplicativo do Azure AD já existir.|


Exemplo de entrada JSON:

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Implantar dentro Cloud Shell linha de comando baseada em navegador

Como parte do fluxo de trabalho do Marketplace, você criou um grupo de recursos e assinou o contrato de licença de usuário final, que pode ser revisado mais uma vez como parte da implantação real. A implantação será feita por meio de Azure Cloud Shell (linha de comando baseada em navegador) usando o ambiente bash.  

> [!NOTE]
> As sessões inativas de Cloud Shell expiram após 20 minutos. Tente concluir a implantação neste momento.

1. Entre no portal **do Azure** e selecione a assinatura e o locatário do AD desejados.
2. Inicie o **Cloud Shell** na parte superior da navegação do portal **do Azure** .

   ![Batidas no farm de projetos](./media/prepare-for-deployment/navigation-bar-1.png)

3. Selecione uma assinatura para criar uma conta de armazenamento e Microsoft Azure compartilhamento de arquivos.
4. Selecione **Create storage** (Criar armazenamento).
5. Selecione o menu suspenso ambiente no lado esquerdo da janela do Shell (bash).

   ![Batidas no farm de projetos](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>Cenário de implantação 1

O instalador cria o Azure AD (você tem permissões de leitura de locatário do AD).  

1. Baixe o modelo [Input. JSON](https://aka.ms/PPInputJsonTemplate) . Inclua a ID do cliente Aplicativo Azure e a senha no arquivo Input. JSON e salve-o.
2. Abra o arquivo baixado em um bloco de notas e preencha o arquivo inserindo os valores.

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
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > Você pode ignorar esta etapa e as entradas serão solicitadas em tempo de execução.

3. Salve o arquivo e anote o caminho (no computador local).  
4. Vá para Azure Cloud Shell e depois da autenticação bem-sucedida, selecione o carregamento (consulte o ícone realçado na imagem abaixo) e carregue o arquivo Input. JSON no armazenamento Cloud Shell. Você não precisa passar o parâmetro do Azure AD dentro do JSON porque o instalador criará e configurará o registro do aplicativo do Azure AD para você.

   ![Batidas no farm de projetos](./media/prepare-for-deployment/bash-2-1.png)

5. Digite ou cole o "comando de implantação" no Cloud Shell. Certifique-se de modificar o caminho para entrada. Arquivo JSON e pressione Enter.  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   O script baixa automaticamente todas as dependências e cria o implantador. Em seguida, você será solicitado a concordar com o contrato de licença de usuário final (EULA) do Azure FarmBeats.

   - Digite ' Y ' se você concordar e continuar para a próxima etapa.
   - Digite ' N ' se você não concordar com os termos e a implantação será encerrada.

   Em seguida, será solicitado que você insira um token de acesso para a implantação. Copie o código gerado e faça logon para https://microsoft.com/devicelogin com suas credenciais do Azure.

   > [!NOTE]
   > O código expira após 60 minutos. Quando ele expirar, você poderá reiniciar digitando o comando de implantação novamente.

6. No próximo prompt, insira a senha da conta do Sentinel.
7. O instalador agora valida e inicia a implantação, o que pode levar cerca de 20 minutos.
8. Depois que a implantação for bem-sucedida, você receberá os links de saída abaixo:
    - **URL do hub de dados**: link do Swagger para experimentar as APIs FarmBeats do Azure.
    - **URL do acelerador**: interface do usuário para explorar o acelerador de farm inteligente do Azure FarmBeats.
    - **Arquivo de log do implantador**-arquivo de log criado durante a implantação. Ele pode ser usado para solução de problemas.

    - Digite ' Y ' se você concordar e continuará na próxima etapa.
    - Digite ' n' se você não concordar com os termos e a implantação será encerrada.

   Em seguida, será solicitado que você insira um token de acesso para a implantação. Copie o código gerado e entre no https://microsoft.com/devicelogin com suas credenciais do Azure.

   > [!NOTE]
    > Anote isso e mantenha o caminho do arquivo de log de implantação útil para uso futuro.


### <a name="deployment-scenario-2"></a>Cenário de implantação 2

O registro de aplicativo Azure Active Directory existente é usado para implantar.

1. Baixar [JSON de entrada](https://aka.ms/PPInputJsonTemplate) inclua a ID do cliente aplicativo Azure e a senha no Input. JSON, salve-o.

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

     Siga o restante das etapas:

2. Anote o caminho para o arquivo Input. JSON (no computador local).
3. Vá para Azure Cloud Shell mais uma vez e você será autenticado com êxito, selecione o botão carregar (consulte o ícone realçado na imagem abaixo) e carregue o arquivo Input. JSON no armazenamento Cloud Shell.

    ![Batidas no farm de projetos](./media/prepare-for-deployment/bash-2-1.png)

4. Digite ou cole o *comando de implantação* no Cloud Shell. Certifique-se de modificar o caminho para entrada. Arquivo JSON e pressione Enter.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Siga o restante das etapas:

5. O script baixa automaticamente todas as dependências e cria o implantador.
6. Você será solicitado a ler e concordar com o contrato de licença de usuário final (EULA) do Azure FarmBeats.

   - Digite ' Y ' se você concordar e continuará na próxima etapa.
   - Digite ' n' se você não concordar com os termos e a implantação será encerrada.

7. Você será solicitado a inserir um token de acesso para a implantação. Copie o código gerado e entre no https://microsoft.com/devicelogin com suas credenciais do Azure.
8. O instalador agora irá validar e começar a criar os recursos, o que pode levar cerca de 20 minutos. Mantenha a sessão ativa em Cloud Shell durante esse tempo.
9. Depois que a implantação passar com êxito, você receberá os links de saída abaixo:
   - **URL do hub de dados**: link do Swagger para testar APIs do FarmBeats.
   - **URL do acelerador**: interface do usuário para explorar o acelerador do Smart farm do FarmBeats.
   - **Arquivo de log do implantador**: salva os logs durante a implantação e pode ser usado para solução de problemas.

Se você encontrar problemas, examine [solucionar problemas](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Validar implantação

### <a name="data-hub"></a>Hub de dados

Depois que a instalação do hub de dados for concluída, você receberá a URL para acessar as APIs do Azure FarmBeats por meio da interface Swagger no formato: https://\<yourdatahub-site-Name >. azurewebsites. net

1. Para entrar por meio do Swagger, copie e cole a URL no navegador.
2. Entre com portal do Azure credenciais.
3. Teste de sanidade (opcional)

     - É possível entrar com êxito no portal do Swagger usando o link do hub de dados, que você recebeu como uma saída para uma implantação bem-sucedida.
     - Obter API de tipos estendidos – selecione "Experimente o/execute"
     - Você deve receber o código de resposta do servidor 200 e não uma exceção como 403 "usuário não autorizado".

### <a name="accelerator"></a>Accelerator

Depois que a instalação do acelerador for concluída, você receberá a URL para acessar o FarmBeats user-interface no formato: https://\<Accelerator-site-Name >. azurewebsites. net

1. Para entrar do acelerador, copie e cole a URL no navegador.
2. Entre com portal do Azure credenciais.
3. Execute um teste de sanidade opcional.

    - Verifique se é possível entrar com êxito no portal do acelerador usando o link do acelerador que você recebeu como uma saída para uma implantação bem-sucedida.
    - Selecione **criar farm**.
    - Sob o ícone "?", abra os guias de FarmBeats usando o botão de **introdução** .

## <a name="upgrade"></a>Atualizar

As etapas para atualização são semelhantes à primeira instalação. Siga estes passos.

1. Entre no portal do Azure e selecione a assinatura desejada e o locatário do AD.
2. Inicie o Cloud Shell a partir do menu de navegação superior do portal do Azure.

   ![Batidas no farm de projetos](./media/prepare-for-deployment/navigation-bar-1.png)

3. Selecione uma assinatura para criar uma conta de armazenamento e um compartilhamento de arquivos do Azure files.
4. Selecione **Create storage** (Criar armazenamento).
5. Selecione o ambiente na lista suspensa à esquerda do Shell (bash).
6. Faça alterações no arquivo Input. JSON somente se necessário e carregue para o Azure Cloud Shell. Por exemplo, você pode atualizar seu endereço de email para a notificação que deseja receber.
7. Anote o caminho do arquivo Input. JSON (no computador local).
8. Vá para Azure Cloud Shell. Depois que você for autenticado com êxito, selecione o botão carregar (consulte o ícone realçado na imagem abaixo) e carregue o arquivo Input. JSON no armazenamento Cloud Shell.

   ![Batidas no farm de projetos](./media/prepare-for-deployment/bash-2-1.png)

9. Digite ou cole o **comando de implantação** no Cloud Shell. Certifique-se de modificar o caminho para entrada. Arquivo JSON e pressione Enter.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    Siga o restante das etapas:

10. O instalador solicita automaticamente as entradas necessárias em tempo de execução:

    - Insira um token de acesso para implantação. Copie o código gerado e entre no https://microsoft.com/devicelogin com suas credenciais do Azure.

     > [!NOTE]
     > O código expira após 60 minutos. Quando ele expirar, você poderá reiniciar digitando o comando de implantação novamente.

     - Senha do Sentinela

11. Agora, o instalador valida e inicia a criação dos recursos, o que pode levar cerca de 20 minutos.
12. Depois que a implantação for bem-sucedida, você receberá os links de saída abaixo:

    - **URL do hub de dados**: link do Swagger para testar APIs do FarmBeats.
    - **URL do acelerador**: interface do usuário para explorar o acelerador do Smart farm do FarmBeats.
    - **Arquivo de log do implantador**: salva os logs durante a implantação. Ele pode ser usado para solução de problemas.

    > [!NOTE]
    > Anote os links acima e mantenha o caminho do arquivo de log de implantação útil para uso futuro.

## <a name="uninstall"></a>Desinstalar

No momento, não há suporte para a desinstalação automatizada do FarmBeats usando o instalador. Para remover o Hub de dados ou acelerador, no portal do Azure, exclua o grupo de recursos no qual esses componentes estão instalados ou exclua os recursos manualmente.

Por exemplo, se você implantou o Hub de dados e acelerador em dois grupos de recursos diferentes, exclua esses grupos de recursos da seguinte maneira:

1. Entre no portal do Azure.
2. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD desejado no qual você deseja implantar o Microsoft FarmBeats.

   > [!NOTE]
   > O Hub de dados é necessário para que o acelerador funcione corretamente. Não recomendamos a desinstalação do hub de dados sem a desinstalação do acelerador.

3. Selecione grupos de recursos e digite o nome do hub de dados ou do grupo de recursos do acelerador que você deseja excluir.
4. Selecione o nome do grupo de recursos. Digite o nome novamente para verificar novamente e clique em Excluir para remover o grupo de recursos e todos os seus recursos subjacentes.
5. Como alternativa, você pode excluir cada recurso manualmente, o que não é recomendado.
7. Para excluir/desinstalar o Hub de dados, vá para o grupo de recursos diretamente no Azure e exclua o grupo de recursos desse local.

## <a name="next-steps"></a>Passos seguintes

Você implantou o Azure FarmBeats. Agora, saiba como [criar farms](manage-farms.md#create-farms).
