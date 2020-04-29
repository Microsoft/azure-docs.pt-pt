---
title: Autenticar Azure Stream Analytics para Azure Data Lake Storage Gen1
description: Este artigo descreve como usar identidades geridas para autenticar o seu trabalho de Azure Stream Analytics à saída do Azure Data Lake Storage Gen1.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79254381"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autenticar Stream Analytics para Azure Data Lake Storage Gen1 usando identidades geridas

O Azure Stream Analytics suporta a autenticação de identidade gerida com a saída da Gen1 de Armazenamento de Lagos de Dados Azure (ADLS). A identidade é uma aplicação gerida registada no Azure Ative Directory que representa um determinado trabalho de Stream Analytics, e pode ser usada para autenticar um recurso direcionado. Identidades geridas eliminam as limitações dos métodos de autenticação baseados no utilizador, como a necessidade de reautenticar devido a alterações de palavra-passe ou expirações de token do utilizador que ocorrem a cada 90 dias. Além disso, identidades geridas ajudam na automatização de implementações de trabalho stream analytics que são saídapara Azure Data Lake Storage Gen1.

Este artigo mostra-lhe três formas de ativar a identidade gerida para um trabalho de Azure Stream Analytics que produz para um Azure Data Lake Storage Gen1 através do portal Azure, implantação do modelo Azure Resource Manager e ferramentas Azure Stream Analytics para O Estúdio Visual.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portal do Azure

1. Comece por criar um novo trabalho stream analytics ou abrindo um emprego existente no portal Azure. A partir da barra de menu seletiva do lado esquerdo do ecrã, selecione **Identidade Gerida** localizada em **Configuração**.

   ![Identidade gerida por Configure Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Selecione Utilize a **Identidade Gerida atribuída** pelo Sistema a partir da janela que aparece à direita. Clique em **Guardar** para um principal de serviço para obter a identidade do trabalho de Stream Analytics no Diretório Ativo Azure. O ciclo de vida da identidade recém-criada será gerido pelo Azure. Quando o trabalho stream analytics é eliminado, a identidade associada (isto é, o diretor de serviço) é automaticamente eliminada pelo Azure.

   Quando a configuração é guardada, o ID do objeto (OID) do diretor de serviço é listado como o ID principal, como mostrado abaixo:

   ![Id principal do serviço Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   O diretor de serviço tem o mesmo nome que o trabalho da Stream Analytics. Por exemplo, se o nome do seu trabalho é **MyASAJob**, o nome do diretor de serviço criado também é **MyASAJob**.

3. Na janela de propriedades de saída do afundatório de saída ADLS Gen1, clique no modo de autenticação drop-down e selecione **Identidade Gerida **.

4. Preencha o resto das propriedades. Para saber mais sobre a criação de uma saída ADLS, consulte Criar uma saída data lake Store com análise de [fluxo.](../data-lake-store/data-lake-store-stream-analytics.md) Quando terminar, clique em **Guardar**.

   ![Configure Armazenamento de data lake](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navegue para a página de visão geral do seu ADLS Gen1 e clique no explorador de **dados**.

   ![Configure Data Lake Storage Overview](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. No painel do explorador de dados, selecione **Access** e clique em **Adicionar** no painel de acesso.

   ![Configure Data Lake Storage Access](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. Na caixa de texto no painel de **utilizador ou grupo Select,** digite o nome do diretor de serviço. Lembre-se que o nome do diretor de serviço é também o nome do trabalho correspondente do Stream Analytics. Quando começar a escrever o nome principal, aparecerá por baixo da caixa de texto. Escolha o nome principal do serviço desejado e clique **em Selecionar**.

   ![Selecione um nome principal de serviço](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. No painel **de Permissões,** verifique as permissões **de Escrita** e **Execução** e atribua-as a **Esta Pasta e a todas as crianças**. Em seguida, clique em **Ok**.

   ![Selecione escrever e executar permissões](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. O diretor de serviço está listado em **Permissões Atribuídas** no painel de **acesso,** como mostrado abaixo. Agora pode voltar e começar o seu trabalho de Stream Analytics.

   ![Lista de acesso ao Stream Analytics no portal](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Para saber mais sobre as permissões do sistema de ficheiros Data Lake Storage Gen1, consulte [o Access Control em Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Ferramentas Stream Analytics para Estúdio Visual

1. Em JobConfig.json, deset Use a identidade atribuída ao **sistema para** **true**.

   ![Stream Analytics trabalho config identidades geridas](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. Na janela de propriedades de saída do afundatório de saída ADLS Gen1, clique no modo de autenticação drop-down e selecione **Identidade Gerida **.

   ![Identidades geridas pela saída aDLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Preencha o resto das propriedades e clique em **Guardar**.

4. Clique em **Submeter ao Azure** no editor de consulta.

   Quando submeteo trabalho, as ferramentas fazem duas coisas:

   * Cria automaticamente um diretor de serviço para a identidade do trabalho da Stream Analytics no Diretório Ativo Azure. O ciclo de vida da identidade recém-criada será gerido pelo Azure. Quando o trabalho stream analytics é eliminado, a identidade associada (isto é, o diretor de serviço) é automaticamente eliminada pelo Azure.

   * Definir automaticamente as permissões **de Escrita** e **Execução** para o caminho prefixo ADLS Gen1 utilizado no trabalho e atribuí-lo a esta pasta e a todas as crianças.

5. Você pode gerar os modelos de Gestor de Recursos com a seguinte propriedade usando [O Stream Analytics CI. CD Nuget versão](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) 1.5.0 ou superior numa máquina de construção (fora do Estúdio Visual). Siga os passos de implementação do modelo do Gestor de Recursos na secção seguinte para obter o diretor de serviço e conceder acesso ao diretor de serviço via PowerShell.

## <a name="resource-manager-template-deployment"></a>Implementação do modelo do Gestor de Recursos

1. Pode criar um recurso *Microsoft.StreamAnalytics/streamingjobs* com uma identidade gerida, incluindo a seguinte propriedade na secção de recursos do seu modelo de Gestor de Recursos:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Esta propriedade diz ao Azure Resource Manager para criar e gerir a identidade para o seu trabalho de Azure Stream Analytics.

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

   Tome nota do ID principal da resposta ao trabalho para conceder acesso ao recurso ADLS necessário.

   O ID do **Inquilino** é a identificação do inquilino do Diretório Ativo Azure onde o diretor de serviço é criado. O diretor de serviço é criado no inquilino Azure que é confiável pela subscrição.

   O **Tipo** indica o tipo de identidade gerida, tal como explicado em tipos de identidades geridas. Apenas o tipo designado do sistema é suportado.

2. Fornecer acesso ao diretor de serviço utilizando o PowerShell. Para dar acesso ao diretor de serviço via PowerShell, execute o seguinte comando:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   O **PrincipalId** é o Id do objeto do principal de serviço e está listado no ecrã do portal assim que o diretor de serviço é criado. Se criou o trabalho utilizando um modelo de gestor de recursos, o ID do Objeto está listado na propriedade identidade da resposta ao trabalho.

   **Exemplo**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Para saber mais sobre o comando powerShell acima, consulte a documentação [Set-AzDataLakeStoreItemAclEntry.](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)

## <a name="limitations"></a>Limitações
Esta funcionalidade não suporta o seguinte:

1. **Acesso multi-inquilino**: O principal de serviço criado para um determinado trabalho stream analytics residirá no inquilino do Azure Ative Directory em que o trabalho foi criado, e não pode ser usado contra um recurso que reside num inquilino azure Ative Directory diferente. Portanto, você só pode usar MSI em recursos ADLS Gen 1 que estão dentro do mesmo inquilino azure ative diretório que o seu trabalho azure Stream Analytics. 

2. **[Identidade atribuída ao utilizador](../active-directory/managed-identities-azure-resources/overview.md)**: não é suportado. Isto significa que o utilizador não pode entrar no seu próprio serviço principal para ser utilizado pelo seu trabalho stream analytics. O diretor de serviço é gerado pelo Azure Stream Analytics.

## <a name="next-steps"></a>Passos seguintes

* [Criar uma saída data lake Store com análise de fluxo](../data-lake-store/data-lake-store-stream-analytics.md)
* [Test Stream Analytics consultas localmente com Estúdio Visual](stream-analytics-vs-tools-local-run.md)
* [Teste dados ao vivo localmente usando ferramentas Azure Stream Analytics para Estúdio Visual](stream-analytics-live-data-local-testing.md) 
