---
title: Autenticado Azure Stream Analytics para Azure Data Lake Storage Gen1
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho Azure Stream Analytics para a saída da Azure Data Lake Storage Gen1.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18
ms.openlocfilehash: d5b406d260b1c6518d9227c51d84ab7eb3754329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598155"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Authenticate Stream Analytics para Azure Data Lake Storage Gen1 usando identidades geridas

O Azure Stream Analytics suporta a autenticação de identidade gerida com a saída da Azure Data Lake Storage (ADLS) Gen1. A identidade é uma aplicação gerida registada no Azure Ative Directory que representa um determinado trabalho de Stream Analytics, e pode ser usada para autenticar a um recurso direcionado. As identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautorização devido a alterações de palavra-passe ou expirações simbólicas do utilizador que ocorrem a cada 90 dias. Adicionalmente, identidades geridas ajudam com a automatização de implementações de trabalho stream Analytics que produção para Azure Data Lake Storage Gen1.

Este artigo mostra-lhe três maneiras de ativar a identidade gerida para um trabalho Azure Stream Analytics que produz para um Azure Data Lake Storage Gen1 através do portal Azure, implementação de modelo de Azure Resource Manager e ferramentas Azure Stream Analytics para Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portal do Azure

1. Comece por criar um novo trabalho stream Analytics ou abrindo um emprego existente no portal Azure. A partir da barra de menu localizada no lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**.

   ![Configurar a identidade gerida stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecione **Utilizar identidade gerida atribuída pelo sistema** a partir da janela que aparece à direita. Clique em **Guardar** para um responsável de serviço para a identidade do trabalho stream Analytics no Azure Ative Directory. O ciclo de vida da identidade recém-criada será gerido pelo Azure. Quando o trabalho stream Analytics é eliminado, a identidade associada (isto é, o principal de serviço) é automaticamente eliminada pela Azure.

   Quando a configuração é guardada, o ID do objeto (OID) do principal de serviço é listado como o ID principal como mostrado abaixo:

   ![ID principal do serviço Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   O diretor de serviço tem o mesmo nome que o trabalho da Stream Analytics. Por exemplo, se o nome do seu trabalho for **MyASAJob,** o nome do chefe de serviço criado também é **MyASAJob**.

3. Na janela de propriedades de saída do lavatório de saída ADLS Gen1, clique no modo de autenticação e selecione **Identidade Gerida**.

4. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída ADLS, consulte [Criar uma saída da Data lake Store com análise de fluxo.](../data-lake-store/data-lake-store-stream-analytics.md) Quando terminar, clique em **Guardar**.

   ![Configurar armazenamento do lago de dados Azure](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navegue para a página geral da sua ADLS Gen1 e clique no **explorador de dados.**

   ![Configure a visão geral do armazenamento do lago de dados](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. No painel de explorador de dados, selecione **Access** e clique em **Adicionar** no painel de acesso.

   ![Configure Data Lake Storage Access](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Na caixa de texto no painel **de utilizador ou grupo Select,** digite o nome do principal de serviço. Lembre-se que o nome do diretor de serviço é também o nome do trabalho correspondente stream analytics. Quando começar a escrever o nome principal, aparecerá abaixo da caixa de texto. Escolha o nome principal do serviço pretendido e clique em **Select**.

   ![Selecione um nome principal de serviço](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. No painel **permissões,** verifique as permissões **de escrever** e **executar** e atribua-as a esta Pasta e a todas **as crianças**. Em seguida, clique **em Ok**.

   ![Selecione escrever e executar permissões](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. O principal do serviço está listado em **Permissões Atribuídas** no painel **de acesso,** conforme mostrado abaixo. Agora pode voltar e começar o seu trabalho stream Analytics.

   ![Lista de acesso do Stream Analytics no portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Para saber mais sobre as permissões do sistema de ficheiros Data Lake Storage Gen1, consulte [o Access Control in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Ferramentas stream analytics para Estúdio Visual

1. Em JobConfig.js, desemote a identidade atribuída ao Sistema de **Utilização** para **Verdadeiro**.

   ![Stream Analytics trabalho config identidades geridas](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Na janela de propriedades de saída do lavatório de saída ADLS Gen1, clique no modo de autenticação e selecione **Identidade Gerida**.

   ![Identidades geridas pela saída ADLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Preencha o resto das propriedades e clique em **Guardar.**

4. Clique em **Submeter a Azure** no editor de consulta.

   Quando submetes o trabalho, as ferramentas fazem duas coisas:

   * Cria automaticamente um principal de serviço para a identidade do trabalho stream Analytics no Azure Ative Directory. O ciclo de vida da identidade recém-criada será gerido pelo Azure. Quando o trabalho stream Analytics é eliminado, a identidade associada (isto é, o principal de serviço) é automaticamente eliminada pela Azure.

   * Descreva automaticamente permissões **de escrever** e **executar** para o prefixo ADLS Gen1 utilizado no trabalho e atribua-o a esta pasta e a todas as crianças.

5. Pode gerar os modelos de Gestor de Recursos com a seguinte propriedade utilizando stream Analytics CI.CD versão [de pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 ou superior numa máquina de construção (fora do Visual Studio). Siga os passos de implementação do modelo do Gestor de Recursos na secção seguinte para obter o principal de serviço e conceder acesso ao principal de serviço via PowerShell.

## <a name="resource-manager-template-deployment"></a>Implementação do modelo do gestor de recursos

1. Pode criar um recurso *Microsoft.StreamAnalytics/streamingjobs* com uma identidade gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para o seu trabalho Azure Stream Analytics.

   **Trabalho de amostra**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Resposta ao trabalho da amostra**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Tome nota da identificação principal da resposta ao trabalho para conceder acesso ao recurso ADLS necessário.

   O **ID do Inquilino** é a identificação do inquilino do Diretório Ativo Azure onde o diretor de serviço é criado. O diretor de serviço é criado no inquilino Azure que é confiado pela subscrição.

   O **Tipo** indica o tipo de identidade gerida como explicado em tipos de identidades geridas. Apenas o tipo de Sistema Atribuído é suportado.

2. Fornecer acesso ao principal de serviço utilizando o PowerShell. Para dar acesso ao principal de serviço via PowerShell, execute o seguinte comando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   O **PrincipalId** é o ID de objeto do principal de serviço e está listado no ecrã do portal assim que o principal do serviço é criado. Se criou o trabalho usando uma implementação de modelo de Gestor de Recursos, o ID do objeto está listado na propriedade identidade da resposta do trabalho.

   **Exemplo**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Para saber mais sobre o comando PowerShell acima, consulte a documentação [Set-AzLakeStoreItemAclEntry.](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)

## <a name="remove-managed-identity"></a>Remover Identidade Gerida

A Identidade Gerida criada para um trabalho stream Analytics só é eliminada quando o trabalho é eliminado. Não há como apagar a Identidade Gerida sem apagar o trabalho. Se já não pretender utilizar a Identidade Gerida, pode alterar o método de autenticação para a saída. A Identidade Gerida continuará a existir até que o trabalho seja eliminado, e será usado se decidir utilizar novamente a autenticação de Identidade Gerida.

## <a name="limitations"></a>Limitações
Esta funcionalidade não suporta o seguinte:

1. **Acesso multi-inquilino**: O diretor de serviço criado para um determinado trabalho stream Analytics residirá no inquilino do Azure Ative Directory em que o trabalho foi criado, e não pode ser usado contra um recurso que reside em um inquilino azure Ative Directory. Portanto, você só pode usar MSI em recursos ADLS Gen 1 que estão dentro do mesmo inquilino Azure Ative Directory que o seu trabalho Azure Stream Analytics. 

2. **[Identidade atribuída ao utilizador:](../active-directory/managed-identities-azure-resources/overview.md)** não é suportado. Isto significa que o utilizador não pode introduzir o seu próprio principal de serviço para ser utilizado pelo seu trabalho stream Analytics. O principal do serviço é gerado pela Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma saída da Data lake Store com análise de fluxo](../data-lake-store/data-lake-store-stream-analytics.md)
* [Teste Stream Analytics consultas localmente com Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-live-data-local-testing.md) 
