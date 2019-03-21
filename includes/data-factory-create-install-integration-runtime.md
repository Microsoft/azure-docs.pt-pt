---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1b0289f5771663f3800ff4e58ba5c0528eb89cb0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124444"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

Nesta secção, vai criar um integration runtime autoalojado e vai associá-lo a um computador no local com a base de dados do SQL Server. O runtime de integração autoalojado é o componente que copia os dados do SQL Server no seu computador para o armazenamento de Blobs do Azure. 

1. Crie uma variável para o nome do integration runtime. Utilize um nome exclusivo e tome nota do mesmo. Vai utilizá-lo mais tarde no tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Criar um integration runtime autoalojado. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Segue-se o resultado do exemplo:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
  
3. Para obter o estado do integration runtime criado, execute o comando seguinte. Confirme que o valor da propriedade **Estado** está definido como **NeedRegistration**. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Segue-se o resultado do exemplo:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

4. Para obter as chaves de autenticação utilizadas para registar o integration runtime autoalojado no serviço Azure Data Factory na cloud, execute o comando seguinte: 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Segue-se o resultado do exemplo:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Copie uma das chaves (exclua as aspas) utilizadas para registar o integration runtime autoalojado que instalar no computador nos passos seguintes.  

## <a name="install-the-integration-runtime"></a>Instalar o integration runtime
1. Se já tiver o integration runtime no seu computador, desinstale-o utilizando **Adicionar ou Remover Programas**. 

2. [Transfira](https://www.microsoft.com/download/details.aspx?id=39717) o integration runtime autoalojado num computador windows local. Execute a instalação.

3. Na página **Bem-vindo à Configuração do Microsoft Integration Runtime** , selecione**Seguinte**.

4. Na página **Contrato de Licença do Utilizador Final**, aceite os termos e o contrato de licença e selecione **Seguinte**.

5. Na página **Pasta de Destino**, selecione **Seguinte**.

6. Na página **Pronto para instalar o Microsoft Integration Runtime**, selecione **Instalar**.

7. Se vir uma mensagem de erro sobre a configuração do computador para entrar no modo de suspensão ou hibernação quando não estiver a ser utilizado, selecione **OK**.

8. Se vir a página **Opções de Energia**, feche-a e vá para a página de configuração.

9. Na página **Configuração do Microsoft Integration Runtime Concluída**, selecione **Concluir**.

10. Na página **Registar o Integration Runtime (Autoalojado)**, cole a chave que guardou na secção anterior e selecione **Registar**. 

    ![Registar o integration runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

11. Quando o integration runtime autoalojado for registado com êxito, verá a mensagem seguinte:

    ![Registado com êxito](media/data-factory-create-install-integration-runtime/registered-successfully.png)

12. Na página **Novo Nó de Integration Runtime (Autoalojado)**, selecione **Seguinte**. 

    ![Página Novo Nó de Integration Runtime](media/data-factory-create-install-integration-runtime/new-integration-runtime-node-page.png)

13. Na página **Canal de Comunicação da Intranet**, selecione **Ignorar**. Selecione a certificação TLS/SSL para proteger a comunicação intra-nós num ambiente de integration runtime de vários nós. 

    ![Página de Cala de Comunicação da Intranet](media/data-factory-create-install-integration-runtime/intranet-communication-channel-page.png)

14. Na página **Registar Integration Runtime (Autoalojado)**, selecione **Configuration Manager**.

15. Quando o nó for ligado ao serviço cloud, verá a página seguinte:

    ![Página o nó está ligado](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Agora, teste a conectividade à base de dados do SQL Server.

    ![Separador Diagnóstico](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Na página do **Configuration Manager**, vá para o separador **Diagnósticos**.

    b. Selecione **SqlServer** para o tipo de origem de dados.

    c. Introduza o nome do servidor.

    d. Introduza o nome da base de dados.

    e. Selecione o modo de autenticação.

    f. Introduza o nome de utilizador.

    g. Introduza a palavra-passe do nome de utilizador.

    h. Selecione **Testar** para confirmar que o integration runtime se consegue ligar ao SQL Server. Se a ligação for bem-sucedida, verá uma marca de verificação verde. Se a ligação não for bem-sucedida, verá uma mensagem de erro. Corrija os problemas e confirme que o runtime de integração se consegue ligar ao SQL Server.    

    > [!NOTE]
    > Tome nota dos valores para o tipo de autenticação, servidor, base de dados, utilizador e palavra-passe. Vai utilizá-los mais tarde no tutorial. 
    
