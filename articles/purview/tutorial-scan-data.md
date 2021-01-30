---
title: 'Tutorial: Digitalizar dados com Azure Purview (pré-visualização)'
description: Neste tutorial, você executou um kit de iniciação para configurar uma propriedade de dados e, em seguida, digitalizar dados de fontes de dados para o seu catálogo Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: e373556d189e9fffc0626d19250e79c09962a004
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062108"
---
# <a name="tutorial-scan-data-with-azure-purview-preview"></a>Tutorial: Digitalizar dados com Azure Purview (Pré-visualização)

> [!IMPORTANT]
> A Azure Purview está atualmente em PREVIEW. Os [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluem termos legais adicionais que se aplicam às funcionalidades do Azure que estão em versão beta, pré-visualização ou ainda não lançadas para a disponibilidade geral.

Nesta *série tutorial de cinco partes,* você aprenderá os fundamentos de como gerir a governação de dados através de uma propriedade de dados usando Azure Purview (Preview). A propriedade de dados que você cria nesta parte do tutorial é usada para o resto da série.

Na primeira parte desta série tutorial, você:

> [!div class="checklist"]
>
> * Criar uma propriedade de dados com vários recursos de dados Azure.
> * Digitalize os dados num catálogo.

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* Uma [conta Azure Purview](create-catalog-portal.md).
* [O kit de arranque](https://github.com/Azure/Purview-Samples/blob/master/PurviewStarterKitV4.zip) que irá implantar o seu espólio de dados.

> [!NOTE]
> O kit de arranque só está disponível para o Windows.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-a-data-estate"></a>Criar uma propriedade de dados

Nesta secção, você executou os scripts do kit de arranque para criar uma propriedade de dados simulada. Uma propriedade de dados é um portfólio de todos os dados que uma empresa detém. O script do kit de arranque executa as seguintes ações:

* Cria uma conta de armazenamento Azure Blob e povoa a conta com dados.
* Cria uma conta Azure Data Lake Storage Gen2.
* Cria uma instância Azure Data Factory e associa a instância ao Azure Purview.
* Configura e aciona um oleoduto de atividade de cópia entre o armazenamento de Azure Blob e as contas Azure Data Lake Storage Gen2.
* Empurra a linhagem associada da Azure Data Factory para a Azure Purview.

### <a name="prepare-to-run-the-starter-kit"></a>Prepare-se para executar o kit de arranque

Siga estes passos para configurar o software do cliente do kit de arranque na sua máquina Windows:

1. [Descarregue o kit de arranque](https://download.microsoft.com/download/9/7/9/979db3b1-0916-4997-a7fb-24e3d8f83174/PurviewStarterKitV4.zip)e extraia o seu conteúdo para o local à sua escolha.


1. No seu computador, introduza o **PowerShell** na caixa de pesquisa na barra de tarefas do Windows. Na lista de pesquisa, clique com o botão direito **Do Windows PowerShell** e, em seguida, selecione **Executar como administrador**.

1. Na janela PowerShell, introduza o seguinte comando, `<path-to-starter-kit>` substituindo-o pelo caminho da pasta dos ficheiros do kit de arranque extraído.

   ```powershell
   dir -Path <path-to-starter-kit> -Recurse | Unblock-File
   ```

1. Introduza o seguinte comando para instalar os cmdlets Azure.

   ```powershell
   Install-Module -Name Az -AllowClobber -Scope CurrentUser
   ```

1. Se vir o aviso, o *fornecedor NuGet é obrigado a continuar,* insira **Y** e, em seguida, prima Enter.

1. Se vir o aviso, *repositório não fidehido,* insira **A** e, em seguida, prima Enter.

Pode levar até um minuto para o PowerShell instalar os módulos necessários.

### <a name="collect-data-needed-to-run-the-scripts"></a>Recolher dados necessários para executar os scripts

Antes de executar os scripts PowerShell para arrancar o catálogo, obtenha os valores dos seguintes argumentos para usar nos scripts:

* TenantID
   1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
   1. Na secção **Gerir** o painel de navegação esquerdo, selecione **Propriedades**. Em seguida, selecione o ícone de cópia para **O ID do inquilino** para guardar o valor para a sua área de transferência. Cole o valor num editor de texto para utilização posterior.

* SubscriçãoID
   1. No portal Azure, procure e selecione o nome da instância Azure Purview que criou como pré-requisito.
   1. Selecione a secção **'Vista Geral'** e guarde o GUIADO para o **ID de subscrição.**

   > [!NOTE]
   > Certifique-se de que está a utilizar a mesma subscrição que aquela em que criou a Conta Azure Purview. Esta é a mesma subscrição que foi colocada na lista de autorizações.

* CatálogoName: O nome da conta Azure Purview que criou na [Create a Azure Purview .](create-catalog-portal.md)

* CatálogoResourceGroupName: O nome do grupo de recursos no qual criou a sua conta Azure Purview.

### <a name="verify-permissions"></a>Verificar permissões

Siga estes passos para adicionar o utilizador que executa o script à conta Azure Purview que foi criada como pré-requisito. Os utilizadores precisam tanto de *Funções de Curador de Dados de Purview* como de Administrador de Fonte de Dados de *Purview.* 

Se você mesmo criou a conta Azure Purview, você tem acesso automaticamente e pode saltar esta secção.

1. Vá à página de contas do Tesouro no portal Azure e selecione a conta Azure Purview que pretende modificar.

1. Na página da conta, selecione o separador **Access control (IAM)** e **+ Adicionar**.

1. Selecione **Adicionar atribuição de função**.

1. **Insira o papel de curador de dados de visão para** o *papel.*
 
1. Utilize o valor predefinido para *atribuir acesso a*. O valor predefinido deve ser **o utilizador, o grupo ou o principal de serviço.**

1. Introduza o nome do utilizador que executa o script em **Select**.

1. Selecione **Guardar**.

1. Repita os passos anteriores com o definido *função* para **a Função de Administrador de Fonte de Dados de Purga**.

Para mais informações, consulte [permissões de catálogo.](catalog-permissions.md)

### <a name="run-the-client-side-setup-scripts"></a>Executar os scripts de configuração do lado do cliente

Depois de concluída a configuração do catálogo, execute os seguintes scripts na janela PowerShell para criar os ativos, substituindo os espaços reservados pelos valores previamente recolhidos.

1. Utilize o seguinte comando para navegar para o diretório do kit de arranque. `path-to-starter-kit`Substitua-a pelo caminho da pasta da lima extraída.

   ```powershell
   cd <path-to-starter-kit>
   ```

1. O comando seguinte define a política de execução para o computador local. Insira **A** para *Sim a Todos* quando for solicitado a alterar a política de execução.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted
   ```

1. Ligue-se ao Azure utilizando o seguinte comando. Substitua os marcadores de posição `TenantID` e `SubscriptionID`.

   ```powershell
   .\RunStarterKit.ps1 -ConnectToAzure -TenantId <TenantID> `
   -SubscriptionId <SubscriptionID>
   ```

   Quando executar o comando, poderá aparecer uma janela pop-up para que possa iniciar sação utilizando as suas credenciais do Azure Ative Directory.


1. Utilize o seguinte comando para executar o kit de arranque. Substitua os `CatalogName` espaços `TenantID` `SubscriptionID` `NewResourceGroupName` reservados e `CatalogResourceGroupName` reservados. Para `NewResourceGroupName` , usar um nome único (apenas com caracteres alfanuméricos minúsculos) para o grupo de recursos que conterá a propriedade de dados.

   > [!IMPORTANT]
   > O **nome newresourcegroup** usa apenas números e letras minúsculas e deve ter menos de 17 caracteres. **Não são permitidos alfabetos e caracteres especiais.** Esta restrição provém das regras de nomeação de conta de armazenamento.

   ```powershell
   .\RunStarterKit.ps1 -CatalogName <CatalogName> -TenantId <TenantID>`
   -ResourceGroup <newresourcegroupname> `
   -SubscriptionId <SubscriptionID> `
   -CatalogResourceGroup <CatalogResourceGroupName>
   ```

Pode levar até 10 minutos para o ambiente ser configurado. Durante este tempo, poderá ver várias janelas pop-up, que pode ignorar. Não feche a janela **BlobDataCreator.exe;** fecha-se automaticamente quando termina.

Quando vir a `Executing Copy pipeline xxxxxxxxxx-487e-4fc4-9628-92dd8c2c732b` mensagem, aguarde por outro exemplo de **BlobDataCreator.exe** para começar e terminar de correr.

> [!IMPORTANT]
> Caso o "Número de tarefas ativas" deixe de diminuir, então pode sair da janela do criador do blob e bater na janela da powershell

Após o fim do processo, é criado um grupo de recursos com o nome fornecido. As contas Azure Data Factory, Azure Blob e Azure Data Lake Storage Gen2 estão todas contidas neste grupo de recursos. O grupo de recursos está contido na subscrição especificada.

## <a name="scan-data-into-the-catalog"></a>Digitalizar dados no catálogo

A digitalização é um processo pelo qual o catálogo se conecta diretamente a uma fonte de dados num horário especificado pelo utilizador. O catálogo reflete o espólio de dados de uma empresa através da digitalização, linhagem, portal e API. Os objetivos incluem examinar o que está lá dentro, extrair esquemas e tentar compreender a semântica. Nesta secção, configura-se uma verificação das fontes de dados geradas com o kit de arranque.

O script do kit de arranque que executou criou duas fontes de dados, o armazenamento Azure Blob e o Azure Data Lake Storage Gen2. Pode digitalizar estas fontes de dados no catálogo uma de cada vez.

### <a name="authenticate-to-your-storage-with-managed-identity"></a>Autenticar para o seu armazenamento com Identidade Gerida

Uma Identidade Gerida com o mesmo nome que a sua conta Azure Purview é criada automaticamente quando a sua conta é criada. Antes de poder digitalizar os seus dados, tem de dar permissões de funções a Azure Purview nas suas contas de armazenamento.

1. Navegue para o grupo de recursos que foi criado pelo kit de arranque e selecione a sua conta de armazenamento blob.

1. Selecione **Access Control (IAM)** do menu de navegação à esquerda. Em seguida, selecione **+ Adicionar**.

1. Descreva a função para **o leitor de dados de blocos** de armazenamento e introduza o nome da conta Azure Purview para **Select**. Em seguida, **selecione Guardar** para dar esta atribuição de função à sua conta Purview.

   :::image type="content" source="media/tutorial-scan-data/add-role-assignment.png" alt-text="Adicionar atribuição de função":::

1. Repita os passos anteriores para Azure Data Lake Storage Gen2.

### <a name="scan-your-data-sources"></a>Digitalize as suas fontes de dados

1. Navegue para o seu recurso Azure Purview no [portal Azure](https://portal.azure.com) e selecione *Open Purview Studio*. É automaticamente levado para a página inicial do seu Estúdio de Purview.

1. Selecione **Fontes** na página do seu catálogo e selecione **Registar-se**. Em seguida, selecione **Azure Blob Storage** e **Continue**.

   :::image type="content" source="media/tutorial-scan-data/add-blob-storage.png" alt-text="Registar recurso de armazenamento de bolhas":::

1. Na página **'Registar fontes',** insira um **Nome**. Escolha o nome da **conta de Armazenamento** da conta de armazenamento Azure Blob que criou anteriormente com o kit de arranque. A conta tem o `<YourResourceGroupName>adcblob` nome. Selecione **Concluir**.

   :::image type="content" source="./media/tutorial-scan-data/register-azure-blob-storage.png" alt-text="Screenshot mostrando as definições para registar uma fonte de dados de armazenamento Azure Blob." border="true":::

1. Na vista do mapa **de fontes de dados,** selecione **Nova digitalização** no azulejo de armazenamento Azure Blob.

   :::image type="content" source="./media/tutorial-scan-data/select-setup-scan.png" alt-text="Screenshot mostrando como selecionar uma configuração de digitalização a partir de uma fonte de dados." border="true":::

1. Na página **Scan,** introduza um nome de digitalização, selecione a sua Identidade Gerida a partir do dropdown **credencial** e **Continue**.

   :::image type="content" source="media/tutorial-scan-data/scan-blob.png" alt-text="Scan blob armazenamento em Azure Purview":::

1. Pode verificar a sua verificação para bolhas individuais. Neste tutorial, mantenha todos os ativos verificados para digitalizar tudo e **Continue.**

   :::image type="content" source="media/tutorial-scan-data/scope-your-scan.png" alt-text="Coloque o seu armazenamento":::

1. Selecione o conjunto de regras de verificação predefinido e **Continue**.

1. Pode configurar um gatilho para exames recorrentes. Para este tutorial, selecione **Uma vez** e **continue**.

1. **Selecione Guardar e correr** para completar a verificação.

1. Repita os passos anteriores para digitalizar a sua conta Azure Data Lake Storage Gen2.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:
> [!div class="checklist"]
>
> * Execute o script do kit de arranque para configurar um ambiente de propriedade de dados.
> * Digitalize os dados num catálogo Azure Purview.

Avance para o próximo tutorial para aprender a navegar na página inicial e procurar um ativo.

> [!div class="nextstepaction"]
> [Navegue na página inicial e procure um ativo](tutorial-asset-search.md)
