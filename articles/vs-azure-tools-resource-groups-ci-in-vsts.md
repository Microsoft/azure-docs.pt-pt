---
title: Integração contínua nos serviços de DevOps do Azure com projetos de grupo de recursos do Azure | Documentos da Microsoft
description: Descreve como configurar a integração contínua nos serviços de DevOps do Azure com projetos de implantação do grupo de recursos do Azure no Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: d7859572b090913db13fe9bb7f3ed67619fe5521
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456351"
---
# <a name="continuous-integration-in-azure-devops-services-using-azure-resource-group-deployment-projects"></a>Integração contínua nos serviços de DevOps do Azure com projetos de implantação do grupo de recursos do Azure
Para implementar um modelo do Azure, executar tarefas em várias etapas: Cópia de compilação, teste, para o Azure (também denominado "Teste") e para implementar o modelo. Existem duas formas diferentes de implementar modelos de serviços de DevOps do Azure. Ambos os métodos fornece os mesmos resultados, pelo que deve escolher aquele que melhor se adequa a seu fluxo de trabalho.

1. Adicione um único passo para o seu pipeline de compilação que executa o script do PowerShell que está incluído no projeto de implantação de grupo de recursos do Azure (Deploy-AzureResourceGroup.ps1). O script copia artefactos e, em seguida, implementa o modelo.
2. Adicione que vários serviços do Azure DevOps criar passos, cada um, executar uma tarefa de fase.

Este artigo demonstra as duas opções. A primeira opção tem a vantagem de utilizar o mesmo script usado por desenvolvedores no Visual Studio e fornecer consistência em todo o ciclo de vida. A segunda opção oferece uma alternativa conveniente para o script interno. Ambos os procedimentos partem do princípio de que já tiver um projeto de implantação do Visual Studio marcado para os serviços de DevOps do Azure.

## <a name="copy-artifacts-to-azure"></a>Copiar artefactos para o Azure
Independentemente do cenário, se tiver quaisquer artefactos necessários para a implementação de modelo, tem de dar acesso do Azure Resource Manager para eles. Estes artefactos podem incluir arquivos, como:

* Modelos aninhados
* Scripts de configuração e scripts de DSC
* Binários de aplicativo

### <a name="nested-templates-and-configuration-scripts"></a>Modelos aninhados e Scripts de configuração
Ao utilizar os modelos fornecidos pelo Visual Studio (ou criados com trechos de código do Visual Studio), o script do PowerShell não só prepara os artefactos, ele também parametriza o URI para os recursos para implementações diferentes. O script, em seguida, copia os artefactos para um contentor seguro no Azure, cria um token SaS para esse contentor e, em seguida, transfere a informação para a implementação do modelo. Ver [criar uma implementação de modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) para saber mais sobre modelos aninhados.  Quando utilizar as tarefas nos serviços de DevOps do Azure, tem de selecionar as tarefas adequadas para a sua implementação do modelo e se for necessário, passar valores de parâmetros do passo de preparação para a implementação do modelo.

## <a name="set-up-continuous-deployment-in-azure-pipelines"></a>Configurar a implementação contínua nos Pipelines do Azure
Para chamar o script do PowerShell nos Pipelines do Azure, terá de atualizar o seu pipeline de compilação. Em resumo, as etapas são: 

1. Edite o pipeline de compilação.
2. Configure a autorização do Azure nos Pipelines do Azure.
3. Adicione um passo de compilação do Azure PowerShell que referencia o script do PowerShell no projeto de implantação de grupo de recursos do Azure.
4. Defina o valor do *- ArtifactsStagingDirectory* parâmetro para trabalhar com um projeto criado nos Pipelines do Azure.

### <a name="detailed-walkthrough-for-option-1"></a>Instruções detalhadas sobre a opção 1
Os procedimentos seguintes explicam as etapas necessárias para configurar a implementação contínua nos serviços de DevOps do Azure com uma única tarefa que executa o script do PowerShell no seu projeto. 

1. Edite o seu pipeline de compilação de serviços de DevOps do Azure e adicione um passo de compilação do Azure PowerShell. Escolher o pipeline de compilação sob o **Criar pipelines** categoria e, em seguida, escolha o **editar** ligação.
   
   ![Editar o pipeline de compilação][0]
2. Adicionar um novo **do Azure PowerShell** passo para o pipeline de compilação de compilação e, em seguida, escolha o **Adicionar passo de compilação...** Editar...
   
   ![Adicionar passo de compilação][1]
3. Escolha o **implementar tarefas** categoria, selecione a **Azure PowerShell** de tarefas e, em seguida, selecione seu **Add** botão.
   
   ![Adicionar tarefas][2]
4. Escolha o **do Azure PowerShell** passo de compilação e, em seguida, preencha os valores.
   
   1. Se já tiver um ponto de extremidade de serviço do Azure adicionado aos serviços de DevOps do Azure, escolha a subscrição na **subscrição do Azure** caixa de lista pendente e, em seguida, avance para a secção seguinte. 
      
      Se não tiver um ponto de extremidade de serviço do Azure nos serviços de DevOps do Azure, terá de adicionar um. Esta subsecção leva-o ao longo do processo. Se a sua conta do Azure utiliza uma conta Microsoft (por exemplo, Hotmail), tem de seguir passos seguintes para obter uma autenticação do Principal de serviço.

   2. Escolha o **gerir** junto a ligar a **subscrição do Azure** caixa de lista pendente.
      
      ![Gerir subscrições do Azure][3]
   3. Escolher **Azure** no **ponto final de serviço novo** caixa de lista pendente.
      
      ![Novo ponto final de serviço][4]
   4. Na **adicionar subscrição do Azure** caixa de diálogo, selecione a **Principal de serviço** opção.
      
      ![Opção de principal de serviço][5]
   5. Adicione as suas informações de subscrição do Azure para o **adicionar subscrição do Azure** caixa de diálogo. Tem de fornecer os seguintes itens:
      
      * ID da subscrição
      * Nome da Subscrição
      * ID do Principal de Serviço
      * Chave de Principal de serviço
      * Id de Inquilino
   6. Adicionar um nome à sua escolha para o **subscrição** caixa nome. Este valor aparece mais adiante no **subscrição do Azure** na lista pendente nos serviços de DevOps do Azure. 

   7. Se não souber o ID de subscrição do Azure, pode utilizar um dos seguintes comandos para recuperá-la.
      
      Para scripts do PowerShell, utilize:
      
      `Get-AzureRmSubscription`
      
      Para a CLI do Azure, utilize:
      
      `azure account show`
   8. Para obter um ID de Principal de serviço, a chave de Principal de serviço e o ID de inquilino, siga o procedimento num [aplicação criar do Active Directory e o principal de serviço com o portal](active-directory/develop/howto-create-service-principal-portal.md) ou [autenticar um principal de serviço com o Azure Gestor de recursos](active-directory/develop/howto-authenticate-service-principal-powershell.md).
   9. Adicione os valores de ID de Principal de serviço, a chave de Principal de serviço e o ID de inquilino para o **adicionar subscrição do Azure** caixa de diálogo caixa e, em seguida, escolha a **OK** botão.
      
      Agora tem um Principal de serviço válido a utilizar para executar o script do PowerShell do Azure.
5. Editar o pipeline de compilação e escolha o **do Azure PowerShell** passo de compilação. Selecione a subscrição na **subscrição do Azure** caixa de lista pendente. (Se a subscrição não aparecer, escolha o **Atualize** botão junto a **gerir** link.) 
   
   ![Configurar tarefas de compilação do Azure PowerShell][8]
6. Forneça um caminho para o script do PowerShell AzureResourceGroup.ps1 de implementar. Para fazer isso, escolha o botão de reticências (...) junto aos **caminho do Script** caixa, navegue para o script do PowerShell de implementar AzureResourceGroup.ps1 no **Scripts** pasta do seu projeto, selecioná-la e, em seguida, Escolha o **OK** botão.    
   
   ![Selecione o caminho para script][9]
7. Depois de selecionar o script, atualize o caminho para o script para que é executado a partir do Build.StagingDirectory (no mesmo diretório que *ArtifactsLocation* está definido como). Pode fazê-lo adicionando "$(Build.StagingDirectory)/"para o início do caminho do script.
   
    ![Editar o caminho para script][10]
8. Na **argumentos de Script** , introduza os seguintes parâmetros (numa única linha). Ao executar o script no Visual Studio, pode ver como o VS utiliza os parâmetros nos **saída** janela. Pode usar isso como um ponto de partida para definir os valores dos parâmetros do passo de compilação.
   
   | Parâmetro | Descrição |
   | --- | --- |
   | -ResourceGroupLocation |O valor de localização geográfica onde está localizado, por exemplo, o grupo de recursos **eastus** ou **"Este dos E.U.A."**. (Adicionar aspas se houver um espaço no nome.) Ver [regiões do Azure](https://azure.microsoft.com/regions/) para obter mais informações. |
   | -ResourceGroupName |O nome do grupo de recursos utilizado para esta implementação. |
   | -UploadArtifacts |Este parâmetro, quando estiver presente, especifica que os artefatos que precisam de ser carregados para o Azure, do sistema local. Apenas terá de definir esse comutador se a implementação de modelo requer artefactos Extras que deseja testar com o script do PowerShell (como scripts de configuração ou modelos aninhados). |
   | -StorageAccountName |O nome da conta de armazenamento utilizada nos artefatos do estágio para esta implementação. Este parâmetro só é utilizado se escalonar artefactos para implementação. Se este parâmetro for fornecido, é criada uma nova conta de armazenamento se o script não tiver criado um durante uma implantação anterior. Se o parâmetro for especificado, a conta de armazenamento tem de existir. |
   | -StorageAccountResourceGroupName |O nome do grupo de recursos associado com a conta de armazenamento. Este parâmetro é necessário apenas se fornecer um valor para o parâmetro StorageAccountName. |
   | -TemplateFile |O caminho para o ficheiro de modelo no projeto de implantação de grupo de recursos do Azure. Para melhorar a flexibilidade, utilize um caminho para este parâmetro que é relativo à localização do script do PowerShell, em vez de um caminho absoluto. |
   | -TemplateParametersFile |O caminho para o ficheiro de parâmetros no projeto de implantação de grupo de recursos do Azure. Para melhorar a flexibilidade, utilize um caminho para este parâmetro que é relativo à localização do script do PowerShell, em vez de um caminho absoluto. |
   | -ArtifactStagingDirectory |Este parâmetro permite que o PowerShell script saber a pasta de onde os ficheiros binários do projeto devem ser copiados. Este valor substitui o valor predefinido utilizado pelo script do PowerShell. Para utilizar serviços de DevOps do Azure, defina o valor como: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Eis um exemplo de argumentos de script (danificado para facilitar a leitura de linha):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Quando tiver terminado, o **argumentos de Script** caixa deve assemelhar-se a lista seguinte:
   
   ![Argumentos do script][11]
9. Depois de adicionar todos os itens necessários para o passo de compilação do Azure PowerShell, escolha o **fila** criar botão para compilar o projeto. O **criar** ecrã mostra a saída do script do PowerShell.

### <a name="detailed-walkthrough-for-option-2"></a>Instruções detalhadas sobre a opção 2
Os procedimentos seguintes explicam as etapas necessárias para configurar a implementação contínua nos serviços de DevOps do Azure com as tarefas incorporadas.

1. Edite o seu pipeline de compilação de serviços de DevOps do Azure para adicionar que duas novas etapas de compilação. Escolher o pipeline de compilação sob o **definições de compilação** categoria e, em seguida, escolha a **editar** ligação.
   
   ![Editar definição de compilação][12]
2. Adicionar novos passos de compilação para o pipeline de compilação com o **Adicionar passo de compilação...** Editar...
   
   ![Adicionar passo de compilação][13]
3. Escolha o **Deploy** categoria de tarefa, selecione o **cópia de ficheiros do Azure** de tarefas e, em seguida, escolha seu **Add** botão.
   
   ![Adicionar tarefa de cópia de ficheiros do Azure][14]
4. Escolha o **implementação de grupo de recursos do Azure** de tarefas, em seguida, escolha seu **Add** botão e, em seguida **fechar** o **catálogo de tarefa**.
   
   ![Adicionar tarefas de implementação de grupo de recursos do Azure][15]
5. Escolha o **cópia de ficheiros do Azure** de tarefas e preencher seus valores.
   
   Se já tiver um ponto de extremidade de serviço do Azure adicionado aos serviços de DevOps do Azure, escolha a subscrição na **subscrição do Azure** caixa de lista pendente. Se não tiver uma subscrição, veja [opção 1](#detailed-walkthrough-for-option-1) para obter instruções sobre como configurar um nos serviços de DevOps do Azure.
   
   * Origem – introduza **$(Build.StagingDirectory)**
   * Tipo de ligação do Azure - selecione **do Azure Resource Manager**
   * Subscrição do Azure RM - selecione a subscrição para a conta de armazenamento que pretende utilizar na **subscrição do Azure** caixa de lista pendente. Se a subscrição não aparecer, escolha o **Atualize** botão junto a **gerir** ligação.
   * Tipo de destino - selecione **BLOBs do Azure**
   * Conta de armazenamento ao RM - selecione a conta de armazenamento que gostaria de utilizar para artefactos de teste
   * Nome do contentor - introduza o nome do contentor que pretende utilizar para teste; ele pode ser qualquer nome de contentor válido, mas usar um dedicado para o pipeline de compilação
   
   Para os valores de saída:
   
   * Introduza o contentor de armazenamento de URI - **artifactsLocation**
   * Token de SAS do contentor de armazenamento - introduza **artifactsLocationSasToken**
   
   ![Configurar tarefas de cópia de ficheiros do Azure][16]
6. Escolha o **implementação de grupo de recursos do Azure** passo de compilação e, em seguida, preencha os valores.
   
   * Tipo de ligação do Azure - selecione **do Azure Resource Manager**
   * Subscrição do Azure RM - selecione a subscrição para a implementação no **subscrição do Azure** caixa de lista pendente. Normalmente, será a mesma subscrição utilizada no passo anterior
   * Ação - selecione **criar ou grupo de recursos de atualização**
   * Grupo de recursos – selecione um grupo de recursos ou introduza o nome de um novo grupo de recursos para a implementação
   * Localização – selecione a localização para o grupo de recursos
   * Modelo - introduza o caminho e nome do modelo devem ser implantados de prefixação **$(Build.StagingDirectory)**, por exemplo: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Parâmetros do modelo - introduza o caminho e o nome dos parâmetros a utilizar prefixação **$(Build.StagingDirectory)**, por exemplo: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Substitua os parâmetros de modelo - introduza ou copie e cole o seguinte código:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Configurar tarefas de implementação do grupo de recursos do Azure][17]
7. Depois de adicionar todos os itens necessários, guardar o pipeline de compilação e escolha **Enfileirar nova compilação** na parte superior.

## <a name="next-steps"></a>Passos Seguintes
Leia [descrição geral do Azure Resource Manager](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Azure Resource Manager e os grupos de recursos do Azure.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
