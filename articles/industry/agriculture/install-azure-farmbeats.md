---
title: Instalar o Azure FarmBeats
description: Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482455"
---
# <a name="install-azure-farmbeats"></a>Instalar o Azure FarmBeats

Este artigo descreve como instalar o Azure FarmBeats em sua assinatura do Azure.

O Azure FarmBeats é uma oferta entre empresas disponível no Azure Marketplace. Ele permite a agregação de conjuntos de dados de agricultura entre provedores e geração de informações acionáveis. O Azure FarmBeats faz isso permitindo que você crie modelos de ia (inteligência artificial) ou de ML (aprendizado de máquina) com base em conjuntos de dados com fusível. Os dois principais componentes do Azure FarmBeats são:

- **Datahub**: uma camada de API que habilita a agregação, a normalização e a condefinição de vários conjuntos de dados de agricultura em diferentes provedores.

- **Acelerador**: um aplicativo Web de exemplo criado com base em Datahub. Ele inicia o desenvolvimento e a visualização do modelo. O acelerador usa as APIs FarmBeats do Azure para demonstrar a visualização de dados de sensor ingeridos como gráficos e visualização de saída de modelo como mapas.

## <a name="before-you-start"></a>Antes de começar
### <a name="components-installed"></a>Componentes instalados

Quando você instala o Azure FarmBeats, os seguintes recursos são provisionados em sua assinatura do Azure:

| Recursos do Azure instalados  | Componente FarmBeats do Azure  |
|---------|---------|
| Estatísticas das Aplicações   |      Acelerador de & Datahub      |
| Serviço de Aplicações     |     Acelerador de & Datahub     |
| Plano do Serviço de Aplicações   | Acelerador de & Datahub  |
| Conexão de API    |  Datahub       |
| Cache do Azure para Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acelerador de & Datahub      |
| Conta do Azure Batch    | Datahub   |
| Azure Key Vault |  Acelerador de & Datahub        |
| Conta do Azure Maps       |     Accelerator    |
| Namespace do hub de eventos    |     Datahub      |
| Aplicação Lógica      |  Datahub       |
| Conta de Armazenamento      |     Acelerador de & Datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Custos incorridos

O custo da FarmBeats do Azure é uma agregação do custo dos serviços do Azure subjacentes. As informações de preço para os serviços do Azure podem ser calculadas usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator). O custo real da instalação total irá variar com base no uso. O custo de estado estacionário para os dois componentes é:

* Datahub-menos de $10 por dia
* Acelerador-menos de $2 por dia

### <a name="regions-supported"></a>Regiões com suporte

Atualmente, o Azure FarmBeats tem suporte em ambientes de nuvem pública nas seguintes regiões:
* Leste da Austrália
* Centro dos E.U.A.
* Este dos E.U.A.
* Este dos E.U.A. 2
* Oeste dos E.U.A.
* E.U.A. Oeste 2
* Europa do Norte
* Europa Ocidental
* Sudeste Asiático

### <a name="time-taken"></a>Tempo decorrido

Toda a configuração do Azure FarmBeats, incluindo a preparação e a instalação, levará menos de uma hora.

## <a name="prerequisites"></a>Pré-requisitos    

Antes de iniciar a instalação real do Azure FarmBeats, você precisará concluir as seguintes etapas:

### <a name="create-sentinel-account"></a>Criar conta do Sentinel
A configuração do Azure FarmBeats permite que você obtenha imagens de satélite gratuitas da missão satélite [Sentinel-2](https://scihub.copernicus.eu/) da Agência de espaço europeu para seu farm. Para configurar essa instalação, você precisa de uma conta do Sentinel.

Siga as etapas para criar uma conta gratuita com o sentinela:

1. Acesse a página de [inscrição](https://scihub.copernicus.eu/dhus/#/self-registration) oficial.
2. Forneça os detalhes necessários (nome, sobrenome, nome de usuário, senha e ID de email) e preencha o formulário.
3. Um link de verificação será enviado para a ID de email registrado. Selecione o link fornecido no email e conclua a verificação.

O processo de registro é concluído após a conclusão da verificação. Anote seu **nome de usuário Sentinel** e a **senha Sentinel**.

### <a name="decide-subscription-and-region"></a>Decidir assinatura e região

Você precisará da ID de assinatura do Azure e da região em que deseja instalar o Azure FarmBeats. Escolha uma das regiões listadas na seção [regiões com suporte](#regions-supported) .

Anote a **ID da assinatura do Azure** e a **região do Azure**.

### <a name="verify-permissions"></a>Verificar permissões

Você precisará verificar se tem privilégios e permissões suficientes no locatário do Azure que você está procurando para instalar o Azure FarmBeats.

Você pode verificar suas permissões de acesso no portal do Azure seguindo as instruções em [controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Para instalar o Azure FarmBeats, você precisa das seguintes permissões:
- Locatário-acesso de leitura
- Assinatura-colaborador ou proprietário
- Grupo de recursos-proprietário.

Além disso, o Azure FarmBeats requer Azure Active Directory registros de aplicativo. Há duas maneiras de concluir a instalação necessária do Azure AD:

**Caso 1**: você tem permissões de **gravação** no locatário do Azure para o qual está instalando. Esse caso significa que você tem as permissões necessárias para criar o registro do aplicativo do AAD dinamicamente durante a instalação.

Você pode prosseguir diretamente para a seção [concluir a inscrição no Marketplace](#complete-azure-marketplace-sign-up) .


**Caso 2**: você não tem permissões de **gravação** no locatário do Azure. Esse caso é comum quando você está tentando instalar o Azure FarmBeats na assinatura do Azure da sua empresa e seu acesso de **gravação** é restrito apenas ao grupo de recursos que você possui.
Nesse caso, solicite ao seu administrador de ti que siga as etapas abaixo para gerar e concluir automaticamente o registro de aplicativo do Azure AD no portal do Azure.

1. Baixe e extraia o [script do gerador de aplicativo do AAD](https://aka.ms/FarmBeatsAADScript) para seu computador local.
2. Entre no portal do Azure e selecione sua assinatura e o locatário do Azure AD.
3. Inicie o Cloud Shell na barra de ferramentas na parte superior da portal do Azure.

    ![FarmBeats do projeto](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Escolha o PowerShell como a experiência de shell preferencial. Na primeira vez, os usuários serão solicitados a selecionar uma assinatura e criar uma conta de armazenamento. Conclua a configuração conforme instruído.
5. Carregue o script (da etapa 1) para Cloud Shell e anote o local do arquivo carregado.

    > [!NOTE]
    > Por padrão, o arquivo é carregado em seu diretório base.

6. Vá para o diretório base usando o comando ' CD ' e execute o seguinte script:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Insira o nome do **site Datahub** e o nome do **site do acelerador** . Anote a saída do script e compartilhe-o com a pessoa que está instalando o Azure FarmBeats.

Depois que o administrador de ti fornecer os detalhes necessários, anote a **ID do cliente do AAD, o segredo do cliente do AAD, o nome do site Datahub & nome do site do acelerador**.

   > [!NOTE]
   > Se você estiver seguindo as instruções do caso 2, não se esqueça de adicionar a ID do cliente do AAD & o segredo do cliente do AAD como parâmetros separados no [arquivo de parâmetros](#prepare-parameters-file)

Agora, você tem todas as informações necessárias para prosseguir para a próxima seção.

### <a name="complete-azure-marketplace-sign-up"></a>Concluir a inscrição no Azure Marketplace

Você precisa concluir a inscrição para a oferta do Azure FarmBeats no Azure Marketplace antes de poder iniciar o processo de instalação baseado em Cloud-Shell. Siga as etapas abaixo para concluir a inscrição:

1.  Inicie sessão no Portal do Azure. Selecione sua conta no canto superior direito e alterne para o locatário do Azure AD no qual você deseja instalar o Azure FarmBeats.

2.  Vá para o Azure Marketplace no portal e pesquise **FarmBeats do Azure** no Marketplace

3.  Uma nova janela com uma visão geral do Azure FarmBeats é exibida. Selecione **criar**.

4.  Aparece uma nova janela. Conclua o processo de inscrição escolhendo a assinatura, o grupo de recursos e o local corretos para os quais você deseja instalar o Azure FarmBeats.

5.  Depois que os detalhes inseridos forem validados, selecione **OK**. A página Termos de uso é exibida. Examine os termos e selecione **criar** para concluir o processo de inscrição.

Esta etapa conclui o processo de inscrição no Azure Marketplace. Agora você está pronto para iniciar a preparação do arquivo de parâmetros.

### <a name="prepare-parameters-file"></a>Preparar arquivo de parâmetros
A etapa final da fase de pré-requisitos é criar um arquivo JSON que servirá como entrada durante a instalação do Cloud Shell. Os parâmetros no arquivo JSON precisarão ser substituídos por valores apropriados.

Um arquivo JSON de exemplo é fornecido abaixo. Baixe o exemplo e atualize os detalhes necessários.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Você pode consultar a tabela de parâmetros abaixo para entender mais sobre cada um dos parâmetros.

| Parâmetro | Valor|
|--- | ---|
|SKU  | Fornece ao usuário uma opção para instalar o Datahub e o Accelerator ou apenas o Datahub. Para instalar apenas Datahub, use "Datahub". Para instalar o Datahub e o Accelerator, use "both"|
|subscriptionId | Especifica a assinatura do Azure para instalar o Azure FarmBeats|
|datahubResourceGroup| Especifica o nome do grupo de recursos para seus recursos do Datahub. Insira o nome do grupo de recursos que você criou no Azure Marketplace aqui|
|localização |O local/região do Azure onde você deseja instalar o Azure FarmBeats|
|datahubWebsiteName  | O prefixo de URL exclusivo para seu aplicativo Web Datahub |
|acceleratorResourceGroup  | Especifica o nome do grupo de recursos para seus recursos de acelerador|
|acceleratorWebsiteName |O prefixo de URL exclusivo para o aplicativo Web do acelerador|
|sentinelUsername | O nome de usuário para obter imagens satélite de sentinela|
|notificationEmailAddress  | O endereço de email para receber as notificações de todos os alertas que você configurar em seu Datahub|
|updateIfExists| Adicional Um parâmetro a ser incluído no arquivo de parâmetros somente se você quiser atualizar uma instância existente do Azure FarmBeats. Para uma atualização, outros detalhes, como os nomes e locais do grupo de recursos, precisam ser iguais|
|aadAppClientId | Adicional Um parâmetro a ser incluído no arquivo de parâmetros somente se você estiver usando um aplicativo AAD criado previamente. Consulte o caso 2 na seção [verificar permissões](#verify-permissions) para obter mais detalhes |
|aadAppClientSecret  | Adicional Adicional Um parâmetro a ser incluído no arquivo de parâmetros somente se você estiver usando um aplicativo AAD criado previamente. Consulte o caso 2 na seção [verificar permissões](#verify-permissions) para obter mais detalhes|

Com base na tabela acima e no arquivo JSON de exemplo, crie seu arquivo de parâmetros JSON e salve-o em seu computador local.

## <a name="install"></a>Instalação

A instalação real dos recursos do Azure FarmBeats acontece em Cloud Shell interface de linha de comando baseada em navegador usando o ambiente bash. Siga as instruções abaixo para instalar o Azure FarmBeats:

1. Inicie sessão no Portal do Azure. Selecione a assinatura e o locatário do Azure que você deseja instalar o Azure FarmBeats.
2. Inicie o **Cloud Shell** na barra de ferramentas no canto superior direito da portal do Azure.
3. Escolha bash como a experiência de shell preferencial. Selecione o botão **carregar** (realçado na imagem abaixo) e carregue o arquivo JSON de parâmetros preparados.

      ![FarmBeats do projeto](./media/install-azure-farmbeats/bash-2-1.png)

4. **Copie** o comando abaixo e **substitua o \<nome de usuário >** pelo valor correto para que o comando aponte para o caminho correto do arquivo carregado.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Execute o comando modificado para iniciar o processo de instalação. Você será solicitado a:
 - Concorde com os termos de **licença do Azure FarmBeats** . Digite "Y" para prosseguir para a próxima etapa se você concordar com o Termos de uso. Digite "N" para encerrar a instalação, se você não concordar com os termos de uso.

 - Em seguida, você será solicitado a inserir um token de acesso para a instalação. Copie o código gerado e faça logon na [página de logon do dispositivo](https://microsoft.com/devicelogin) com suas **credenciais do Azure**.

 - Depois que a entrada for concluída com êxito, o instalador solicitará a senha da conta do Sentinel. Insira a **senha da conta do Sentinel**.

6. O arquivo de parâmetros é validado e a instalação dos recursos do Azure é iniciada. A instalação leva cerca de **25 minutos** para ser concluída.    
> [!NOTE]
> As sessões inativas de Cloud Shell expiram após **20 minutos**. Mantenha a sessão de Cloud Shell ativa enquanto o instalador estiver implantando os recursos do Azure. Se a sessão atingir o tempo limite, você precisará reiniciar o processo de instalação.

Quando a instalação for concluída, você receberá os seguintes links de saída:
* **URL do Datahub**: o link do Swagger para acessar as APIs do Datahub.
* **URL do acelerador**: o aplicativo Web para explorar o acelerador de FarmBeats do Azure.
* **Arquivo de log do instalador**: o arquivo de log que contém detalhes da instalação. Esse arquivo de log pode ser usado para solucionar problemas de instalação, se necessário.

Você pode verificar a conclusão de sua instalação do FarmBeats do Azure fazendo as seguintes verificações:

**Datahub**
1. Faça logon na URL do acelerador fornecida (no formato **https://\<yourdatahub-site-name >. azurewebsites. net/Swagger**) com suas credenciais do Azure.
2. Você deve ser capaz de ver os diferentes objetos da API FarmBeats e executar operações REST nas APIs.

**Accelerator**
1. Faça logon na URL do acelerador fornecida (no formato **https://\<youraccelerator-site-name >. azurewebsites. net/Swagger**) com suas credenciais do Azure.
2. Você deve ser capaz de ver a interface do usuário do acelerador com uma opção para criar farms em seu navegador.

Sua capacidade de executar as operações acima indica uma instalação bem-sucedida do Azure FarmBeats.

## <a name="upgrade"></a>Atualização
Na versão de visualização pública, para atualizar uma instalação existente do Azure FarmBeats, você precisará executar o comando de instalação em Cloud Shell novamente, com um parâmetro "**updateIfExists**" adicional no arquivo de parâmetros definido como "**true**". Consulte a última linha do exemplo de JSON abaixo para o parâmetro de atualização.

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
O comando atualiza a instalação existente do Azure FarmBeats para a versão mais recente e fornece os links de saída.

## <a name="uninstall"></a>Desinstalar

Para desinstalar o FarmBeats Datahub ou acelerador do Azure, conclua as seguintes etapas:

1.  Faça logon no portal do Azure e **exclua os grupos de recursos** nos quais esses componentes estão instalados.

2.  Vá para Azure Active Directory & **excluir o aplicativo do Azure ad** vinculado à instalação do FarmBeats do Azure. Isso removerá a instalação do Azure FarmBeats da sua assinatura do Azure.

## <a name="next-steps"></a>Passos seguintes
Você aprendeu a instalar o Azure FarmBeats em sua assinatura do Azure. Agora, saiba como [Adicionar usuários](manage-users-in-azure-farmbeats.md#manage-users) à instância do FarmBeats do Azure.
