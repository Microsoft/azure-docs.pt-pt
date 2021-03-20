---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: c0a0b44b3de088539a070f1182248c4e6db7303f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97510282"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Criar um integration runtime autoalojado

Nesta secção, vai criar um integration runtime autoalojado e vai associá-lo a um computador no local com a base de dados do SQL Server. O tempo de execução de integração auto-hospedado é o componente que copia dados do SQL Server na sua máquina para a Base de Dados Azure SQL. 

1. Crie uma variável para o nome do integration runtime. Utilize um nome exclusivo e tome nota do mesmo. Vai utilizá-lo mais tarde no tutorial. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Criar um integration runtime autoalojado. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Segue-se o resultado do exemplo:

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Para obter o estado do integration runtime criado, execute o comando seguinte. Confirme que o valor da propriedade **Estado** está definido como **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Segue-se o resultado do exemplo:

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Para obter as chaves de autenticação utilizadas para registar o integration runtime autoalojado no serviço Azure Data Factory na cloud, execute o comando seguinte: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Segue-se o resultado do exemplo:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Copie uma das chaves (exclua as aspas) utilizadas para registar o integration runtime autoalojado que instalar no computador nos passos seguintes.  

## <a name="install-the-integration-runtime-tool"></a>Instale a ferramenta de execução de integração

1. Se já tiver o integration runtime no seu computador, desinstale-o utilizando **Adicionar ou Remover Programas**. 

2. [Transfira](https://www.microsoft.com/download/details.aspx?id=39717) o integration runtime autoalojado num computador windows local. Execute a instalação.

3. Na página **Bem-vindo à Configuração do Microsoft Integration Runtime**, selecione **Seguinte**.

4. Na página **Contrato de Licença do Utilizador Final**, aceite os termos e o contrato de licença e selecione **Seguinte**.

5. Na página **Pasta de Destino**, selecione **Seguinte**.

6. Na página **Pronto para instalar o Microsoft Integration Runtime**, selecione **Instalar**.

7. Na página **Configuração do Microsoft Integration Runtime Concluída**, selecione **Concluir**.

8. Na página **Registar o Integration Runtime (Autoalojado)**, cole a chave que guardou na secção anterior e selecione **Registar**. 

    ![Registar o integration runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. Na página de nó de novo número **de integração (auto-hospedada),** selecione **Finish**. 

10. Quando o integration runtime autoalojado for registado com êxito, verá a mensagem seguinte:

    ![Registado com êxito](media/data-factory-create-install-integration-runtime/registered-successfully.png)

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

    exemplo, Introduza a palavra-passe que está associada ao nome de utilizador.

    h. Selecione **Testar** para confirmar que o integration runtime se consegue ligar ao SQL Server. Se a ligação for bem-sucedida, verá uma marca de verificação verde. Se a ligação não for bem-sucedida, verá uma mensagem de erro. Corrija os problemas e confirme que o runtime de integração se consegue ligar ao SQL Server.    

    > [!NOTE]
    > Tome nota dos valores para o tipo de autenticação, servidor, base de dados, utilizador e palavra-passe. Vai utilizá-los mais tarde no tutorial.
