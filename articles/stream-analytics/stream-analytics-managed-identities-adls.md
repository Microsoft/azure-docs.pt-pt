---
title: Autenticar Azure Stream Analytics para Azure Data Lake Storage Gen1
description: Este artigo descreve como usar identidades gerenciadas para autenticar seu trabalho de Azure Stream Analytics para Azure Data Lake Storage Gen1 saída.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934986"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autenticar Stream Analytics para Azure Data Lake Storage Gen1 usando identidades gerenciadas

O Azure Stream Analytics dá suporte à autenticação Azure Data Lake Storage de identidade gerenciada com a saída de Gen1 (ADLS). A identidade é uma aplicação gerida registada no Azure Active Directory que representa uma determinada tarefa do Stream Analytics e que pode ser utilizada para autenticar em relação a um recurso visado. As identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautenticação devido às alterações de palavra-passe ou às expirações de token de utilizador que ocorrem a cada 90 dias. Além disso, as identidades gerenciadas ajudam com a automação de implantações Stream Analytics trabalho que geram uma saída para Azure Data Lake Storage Gen1.

Este artigo mostra três maneiras de habilitar a identidade gerenciada para um trabalho Azure Stream Analytics que gera um Azure Data Lake Storage Gen1 por meio do portal do Azure, Azure Resource Manager implantação de modelo e ferramentas de Azure Stream Analytics para o Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portal do Azure

1. Comece criando um novo trabalho de Stream Analytics ou abrindo um trabalho existente no portal do Azure. Na barra de menus localizada no lado esquerdo da tela, selecione **identidade gerenciada** localizada em **Configurar**.

   ![Configurar Stream Analytics identidade gerenciada](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecione **usar identidade gerenciada atribuída pelo sistema** na janela que aparece à direita. Clique em **salvar** em uma entidade de serviço para obter a identidade do trabalho de Stream Analytics no Azure Active Directory. O ciclo de vida da identidade recém-criada será gerenciado pelo Azure. Quando o trabalho de Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure.

   Quando a configuração é salva, a ID de objeto (OID) da entidade de serviço é listada como a ID da entidade de segurança, conforme mostrado abaixo:

   ![ID da entidade de serviço Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   A entidade de serviço tem o mesmo nome que o trabalho de Stream Analytics. Por exemplo, se o nome do seu trabalho for **MyASAJob**, o nome da entidade de serviço criada também será **MyASAJob**.

3. Na janela Propriedades de saída da ADLS Gen1 coletor de saída, clique na lista suspensa modo de autenticação e selecione * * identidade gerenciada * *.

4. Preencha o restante das propriedades. Para saber mais sobre como criar uma saída de ADLS, confira [criar uma saída do data Lake Store com o Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). Quando tiver terminado, clique em **salvar**.

   ![Configurar Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navegue até a página Visão geral do seu ADLS Gen1 e clique em **Data Explorer**.

   ![Configurar Data Lake Storage visão geral](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. No painel data Explorer, selecione **acesso** e clique em **Adicionar** no painel de acesso.

   ![Configurar o acesso ao Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Na caixa de texto do painel **Selecionar usuário ou grupo** , digite o nome da entidade de serviço. Lembre-se de que o nome da entidade de serviço também é o nome do trabalho de Stream Analytics correspondente. Quando você começar a digitar o nome principal, ele será exibido abaixo da caixa de texto. Escolha o nome da entidade de serviço desejada e clique em **selecionar**.

   ![Selecione um nome da entidade de serviço](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. No painel **permissões** , verifique as permissões de **gravação** e **execução** e atribua-as a **esta pasta e a todos os filhos**. Em seguida, clique em **OK**.

   ![Selecionar permissões de gravação e execução](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. A entidade de serviço está listada sob **permissões atribuídas** no painel de **acesso** , conforme mostrado abaixo. Agora você pode voltar e iniciar seu trabalho de Stream Analytics.

   ![Lista de acesso Stream Analytics no portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Para saber mais sobre Data Lake Storage Gen1 permissões do sistema de arquivos, consulte [controle de acesso em Azure data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Ferramentas de Stream Analytics para o Visual Studio

1. Em JobConfig. JSON, defina **usar identidade atribuída pelo sistema** como **true**.

   ![Stream Analytics identidades gerenciadas da configuração de trabalho](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Na janela Propriedades de saída da ADLS Gen1 coletor de saída, clique na lista suspensa modo de autenticação e selecione * * identidade gerenciada * *.

   ![Identidades gerenciadas de saída do ADLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Preencha o restante das propriedades e clique em **salvar**.

4. Clique em **Enviar para o Azure** no editor de consultas.

   Quando você envia o trabalho, as ferramentas fazem duas coisas:

   * Cria automaticamente uma entidade de serviço para a identidade do trabalho de Stream Analytics no Azure Active Directory. O ciclo de vida da identidade recém-criada será gerenciado pelo Azure. Quando o trabalho de Stream Analytics é excluído, a identidade associada (ou seja, a entidade de serviço) é excluída automaticamente pelo Azure.

   * Definir automaticamente as permissões de **gravação** e **execução** para o caminho de prefixo de ADLS Gen1 usado no trabalho e atribuí-lo a essa pasta e a todos os filhos.

5. Você pode gerar os modelos do Resource Manager com a seguinte propriedade usando [Stream Analytics CI. CD NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) versão 1.5.0 ou superior em um computador de compilação (fora do Visual Studio). Siga as etapas de implantação do modelo do Resource Manager na próxima seção para obter a entidade de serviço e conceder acesso à entidade de serviço por meio do PowerShell.

## <a name="resource-manager-template-deployment"></a>Implantação do modelo do Resource Manager

1. Você pode criar um recurso *Microsoft. StreamAnalytics/streamingjobs* com uma identidade gerenciada, incluindo a seguinte propriedade na seção de recursos do seu modelo do Resource Manager:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Essa propriedade informa Azure Resource Manager para criar e gerenciar a identidade de seu trabalho de Azure Stream Analytics.

   **Trabalho de exemplo**
   
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
  
   **Exemplo de resposta de trabalho**

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

   Anote a ID da entidade de segurança da resposta do trabalho para conceder acesso ao recurso ADLS necessário.

   A **ID do locatário** é a ID do locatário de Azure Active Directory em que a entidade de serviço é criada. A entidade de serviço é criada no locatário do Azure que é confiável para a assinatura.

   O **tipo** indica o tipo de identidade gerenciada, conforme explicado em tipos de identidades gerenciadas. Somente o tipo atribuído ao sistema tem suporte.

2. Forneça acesso à entidade de serviço usando o PowerShell. Para conceder acesso à entidade de serviço por meio do PowerShell, execute o seguinte comando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   O **PrincipalId** é a ID de objeto da entidade de serviço e é listado na tela do portal assim que a entidade de serviço é criada. Se você criou o trabalho usando uma implantação de modelo do Resource Manager, a ID do objeto será listada na propriedade Identity da resposta do trabalho.

   **Exemplo**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Para saber mais sobre o comando do PowerShell acima, consulte a documentação do [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="limitations"></a>Limitações
Este recurso não oferece suporte ao seguinte:

1. **Acesso multilocatário**: a entidade de serviço criada para um determinado trabalho de Stream Analytics residirá no locatário Azure Active Directory no qual o trabalho foi criado e não pode ser usada em um recurso que resida em um Azure Active Directory diferente vários. Portanto, você só pode usar o MSI em recursos do ADLS Gen 1 que estão dentro do mesmo locatário Azure Active Directory que o seu trabalho de Azure Stream Analytics. 

2. **[Identidade atribuída ao usuário](../active-directory/managed-identities-azure-resources/overview.md)** : não tem suporte. Isso significa que o usuário não é capaz de inserir sua própria entidade de serviço a ser usada por seus Stream Analytics trabalho. A entidade de serviço é gerada pelo Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma saída do data Lake Store com o Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testar Stream Analytics consultas localmente com o Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testar dados dinâmicos localmente usando o Azure Stream Analytics Tools para Visual Studio](stream-analytics-live-data-local-testing.md) 
