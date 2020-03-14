---
title: Autenticar Azure Stream Analytics para Azure Data Lake Storage Gen1
description: Este artigo descreve como utilizar identidades geridas para autenticar a sua tarefa do Azure Stream Analytics para a saída de geração 1 de armazenamento do Azure Data Lake.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79254381"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autenticar Stream Analytics para Azure Data Lake Storage Gen1 usando identidades geridas

O Azure Stream Analytics suporta a autenticação de identidade gerida com a saída de geração 1 do Azure Data Lake Storage (ADLS). A identidade é um aplicativo gerenciado registado no Azure Active Directory que representa uma determinada tarefa do Stream Analytics e pode ser utilizada para autenticar a um recurso de destino. Identidades geridas eliminam as limitações dos métodos de autenticação baseada no utilizador, como a necessidade de autenticar devido a alterações de palavra-passe ou as Expirações de token de utilizador que ocorrem a cada 90 dias. Além disso, ajudam a identidades geridas com a automatização de implementações de tarefa do Stream Analytics essa saída a geração 1 de armazenamento do Azure Data Lake.

Este artigo mostra-lhe três formas de ativar a identidade gerida para um trabalho de Azure Stream Analytics que produz para um Azure Data Lake Storage Gen1 através do portal Azure, implantação do modelo Azure Resource Manager e ferramentas Azure Stream Analytics para O Estúdio Visual.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portal do Azure

1. Comece por criar uma nova tarefa de Stream Analytics ou ao abrir uma tarefa existente no portal do Azure. A partir da barra de menu seletiva do lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**.

   ![Identidade gerida por Configure Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecione Utilize a **Identidade Gerida atribuída** pelo Sistema a partir da janela que aparece à direita. Clique em **Guardar** para um principal de serviço para obter a identidade do trabalho de Stream Analytics no Diretório Ativo Azure. O ciclo de vida da identidade criado recentemente será gerido pelo Azure. Quando a tarefa do Stream Analytics é eliminada, a identidade associada (ou seja, o principal de serviço) é eliminada automaticamente pelo Azure.

   Quando a configuração é guardada, o ID de objeto (OID) de principal de serviço está listado como ID de Principal, conforme mostrado abaixo:

   ![ID de principal de serviço do Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   O principal de serviço tem o mesmo nome que a tarefa do Stream Analytics. Por exemplo, se o nome do seu trabalho é **MyASAJob**, o nome do diretor de serviço criado também é **MyASAJob**.

3. Na janela de propriedades de saída do afundatório de saída ADLS Gen1, clique no modo de autenticação drop-down e selecione **Identidade Gerida **.

4. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída ADLS, consulte Criar uma saída data lake Store com análise de [fluxo.](../data-lake-store/data-lake-store-stream-analytics.md) Quando terminar, clique em **Guardar**.

   ![Configurar o armazenamento do Azure Data Lake](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navegue para a página de visão geral do seu ADLS Gen1 e clique no explorador de **dados**.

   ![Configurar a descrição geral do Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. No painel do explorador de dados, selecione **Access** e clique em **Adicionar** no painel de acesso.

   ![Configurar o acesso do Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Na caixa de texto no painel de **utilizador ou grupo Select,** digite o nome do diretor de serviço. Lembre-se de que o nome do principal de serviço também é o nome da tarefa de Stream Analytics correspondente. Quando começar a escrever o nome do principal, ele será exibido abaixo da caixa de texto. Escolha o nome principal do serviço desejado e clique **em Selecionar**.

   ![Selecione um nome principal de serviço](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. No painel **de Permissões,** verifique as permissões **de Escrita** e **Execução** e atribua-as a **Esta Pasta e a todas as crianças**. Em seguida, clique em **Ok**.

   ![Selecione a escrita e permissões de execução](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. O diretor de serviço está listado em **Permissões Atribuídas** no painel de **acesso,** como mostrado abaixo. Agora pode voltar atrás e iniciar a tarefa de Stream Analytics.

   ![Lista no portal de acesso do Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Para saber mais sobre as permissões do sistema de ficheiros Data Lake Storage Gen1, consulte [o Access Control em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Ferramentas Stream Analytics para Estúdio Visual

1. Em JobConfig.json, deset Use a identidade atribuída ao **sistema para** **true**.

   ![Stream Analytics trabalho config identidades geridas](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Na janela de propriedades de saída do afundatório de saída ADLS Gen1, clique no modo de autenticação drop-down e selecione **Identidade Gerida **.

   ![Identidades geridas pela saída aDLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Preencha o resto das propriedades e clique em **Guardar**.

4. Clique em **Submeter ao Azure** no editor de consulta.

   Quando submeteo trabalho, as ferramentas fazem duas coisas:

   * Cria automaticamente um diretor de serviço para a identidade do trabalho da Stream Analytics no Diretório Ativo Azure. O ciclo de vida da identidade criado recentemente será gerido pelo Azure. Quando a tarefa do Stream Analytics é eliminada, a identidade associada (ou seja, o principal de serviço) é eliminada automaticamente pelo Azure.

   * Definir automaticamente as permissões **de Escrita** e **Execução** para o caminho prefixo ADLS Gen1 utilizado no trabalho e atribuí-lo a esta pasta e a todas as crianças.

5. Você pode gerar os modelos de Gestor de Recursos com a seguinte propriedade usando [O Stream Analytics CI. CD Nuget versão](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 ou superior numa máquina de construção (fora do Estúdio Visual). Siga os passos de implementação do modelo do Gestor de Recursos na secção seguinte para obter o diretor de serviço e conceder acesso ao diretor de serviço via PowerShell.

## <a name="resource-manager-template-deployment"></a>Implementação de modelo do Resource Manager

1. Pode criar um recurso *Microsoft.StreamAnalytics/streamingjobs* com uma identidade gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para a sua tarefa do Azure Stream Analytics.

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

   Tome nota do ID Principal da resposta de tarefa para conceder acesso ao recurso do ADLS necessário.

   O ID do **Inquilino** é a identificação do inquilino do Diretório Ativo Azure onde o diretor de serviço é criado. O principal de serviço é criado no inquilino do Azure que seja considerada fidedigna pela subscrição.

   O **Tipo** indica o tipo de identidade gerida, tal como explicado em tipos de identidades geridas. Apenas o tipo de sistema atribuído é suportado.

2. Fornece acesso ao principal de serviço com o PowerShell. Para dar acesso ao serviço principal através do PowerShell, execute o seguinte comando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   O **PrincipalId** é o Id do objeto do principal de serviço e está listado no ecrã do portal assim que o diretor de serviço é criado. Se criou a tarefa através de uma implementação de modelo do Resource Manager, o ID de objeto é listado na propriedade de identidade da resposta de tarefa.

   **Exemplo**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Para saber mais sobre o comando powerShell acima, consulte a documentação [Set-AzDataLakeStoreItemAclEntry.](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)

## <a name="limitations"></a>Limitações
Esta funcionalidade não suporta o seguinte:

1. **Acesso multi-inquilino**: O principal de serviço criado para um determinado trabalho stream analytics residirá no inquilino do Azure Ative Directory em que o trabalho foi criado, e não pode ser usado contra um recurso que reside num inquilino azure Ative Directory diferente. Portanto, você só pode usar MSI em recursos ADLS Gen 1 que estão dentro do mesmo inquilino azure ative diretório que o seu trabalho azure Stream Analytics. 

2. **[Identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/overview.md)** : não é suportado. Isto significa que o utilizador não pode entrar no seu próprio serviço principal para ser utilizado pelo seu trabalho stream analytics. O diretor de serviço é gerado pelo Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma saída data lake Store com análise de fluxo](../data-lake-store/data-lake-store-stream-analytics.md)
* [Test Stream Analytics consultas localmente com Estúdio Visual](stream-analytics-vs-tools-local-run.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-live-data-local-testing.md) 
