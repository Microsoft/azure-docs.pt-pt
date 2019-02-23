---
title: Integrar registos do Azure Key Vault ao utilizar os Hubs de eventos | Documentos da Microsoft
description: Tutorial que fornece os passos necessários para que os registos do Cofre de chaves disponíveis para um SIEM com o Azure Log Integration
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 22d4a18ad1c6e80baa6e798be399ab2cd4836fbc
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733412"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial de integração de registos do Azure: Processar os eventos do Azure Key Vault através dos Hubs de eventos

>[!IMPORTANT]
> A funcionalidade de integração de registo do Azure vai ser preterida até 06/01/2019. Downloads de AzLog foram desativados a 27 de Junho de 2018. Para obter orientações sobre o que fazer a postagem de revisão de encaminhamento de movimentação [monitor do Azure de utilização para integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Pode utilizar o Azure Log Integration para obter eventos registrados e disponibilizá-los ao seu sistema de gestão (SIEM) de informações e eventos da segurança. Este tutorial mostra um exemplo de como a integração de registos do Azure pode ser usada para processar registos que são adquiridos através do Event Hubs do Azure.

O método preferido para a integração de registos do Azure é utilizando o conector do Azure Monitor do seu fornecedor SIEM e seguir estas [instruções](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). No entanto, se o seu fornecedor SIEM não fornecer um conector para o Azure Monitor, poderá conseguir utilizar a integração de registo do Azure como uma solução temporária (se a sua SIEM é suportado pelo Azure Log Integration) até que tal um conector esteja disponível.

 
Utilize este tutorial para se familiarizar com como o Azure Log Integration e os Hubs de eventos funcionam em conjunto ao seguir os passos de exemplo e compreender como cada passo suporta a solução. Em seguida, pode tirar o que aprendeu aqui para criar suas próprias etapas para suportar requisitos exclusivos da sua empresa.

>[!WARNING]
As etapas e os comandos neste tutorial não se destina a ser copiado e colado. Eles são apenas exemplos. Não utilize os comandos do PowerShell "como estão" no seu ambiente ao vivo. Será necessário personalizá-los com base no seu ambiente exclusivo.


Este tutorial explica-lhe o processo de criar atividade do Azure Key Vault com sessão iniciada para um hub de eventos e disponibilizá-lo como ficheiros JSON para o seu sistema SIEM. Em seguida, pode configurar o seu sistema SIEM para processar os ficheiros JSON.

>[!NOTE]
>A maioria dos passos neste tutorial envolve configurar cofres de chaves, contas de armazenamento e os hubs de eventos. Os passos de integração de registos do Azure específicos estão no final deste tutorial. Não efetue estes passos num ambiente de produção. Eles destinam-se de um ambiente de laboratório apenas. Tem de personalizar os passos antes de usá-los em produção.

Informações fornecidas ao longo do processo ajudam-o a compreender as razões por trás de cada passo. Links para outros artigos dar-lhe mais detalhes sobre certos tópicos.

Para obter mais informações sobre os serviços que menciona neste tutorial, veja: 

- [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integração de registos do Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuração inicial

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Antes de concluir os passos neste artigo, precisa do seguinte:

* Uma subscrição do Azure e conta com direitos de administrador da subscrição. Se não tiver uma subscrição, pode criar uma [conta gratuita](https://azure.microsoft.com/free/).
 
* Um sistema com acesso à internet que cumpra os requisitos para instalar o Azure Log Integration. O sistema pode estar num serviço em nuvem ou alojadas no local.

* Integração de registos do Azure instalado. Para instalá-lo:

   a. Utilize o ambiente de trabalho remoto para ligar ao sistema mencionado no passo 2.   
   b. Copie o instalador de integração de registos do Azure para o sistema. c. Iniciar o instalador e aceite os termos de licença de Software da Microsoft.

* Se for fornecer informações de telemetria, deixe a caixa de verificação selecionada. Se preferir não enviar informações de utilização à Microsoft, desmarque a caixa de verificação.

   Para obter mais informações sobre a integração de registos do Azure e como instalá-lo, consulte [integração de registos do Azure com o registo de diagnósticos do Azure e o reencaminhamento de eventos do Windows](security-azure-log-integration-get-started.md).

* A versão mais recente do PowerShell.

   Se tiver instalado o Windows Server 2016, então tem, pelo menos, PowerShell 5.0. Se estiver a utilizar qualquer outra versão do Windows Server, poderá ter uma versão anterior do PowerShell instalado. Pode verificar a versão introduzindo ```get-host``` numa janela do PowerShell. Se não tiver PowerShell 5.0 instalado, pode [baixá-lo](https://www.microsoft.com/download/details.aspx?id=50395).

   Depois de ter, pelo menos, PowerShell 5.0, pode avançar para instalar a versão mais recente ao seguir as instruções em [instalar o Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Criar elementos de infraestrutura com suporte

1. Abra uma janela do PowerShell elevada e aceda à **C:\Program Files\Microsoft Azure Log Integration**.
1. Importe os cmdlets de AzLog ao executar o script LoadAzLogModule.ps1. Introduza o `.\LoadAzLogModule.ps1` comando. (Observe que o ". \" nesse comando.) Deverá ver algo semelhante ao seguinte:</br>

   ![Lista de módulos carregados](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Introduza o `Connect-AzAccount` comando. Na janela de início de sessão, introduza as informações de credencial para a subscrição que irá utilizar para este tutorial.

   >[!NOTE]
   >Se esta for a primeira vez que está a iniciar sessão no Azure desta máquina, verá uma mensagem sobre a permissão da Microsoft para recolher dados de utilização do PowerShell. Recomendamos que ative a recolha de dados como vai ser utilizada para melhorar o Azure PowerShell.

1. Após a autenticação com êxito, tem sessão iniciada. Tome nota do ID de subscrição e o nome de subscrição, pois precisará-los para concluir os passos posteriores.

1. Crie variáveis para armazenar os valores que serão utilizados posteriormente. Introduza cada uma das linhas do PowerShell seguintes. Poderá ter de ajustar os valores para corresponder ao seu ambiente.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (O nome da sua subscrição pode ser diferente. Para ver isso como parte da saída do comando anterior.)
    - ```$location = 'West US'``` (Essa variável será usada para passar a localização onde os recursos devem ser criados. Pode alterar essa variável para ser de qualquer local de sua escolha.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (O nome pode ser qualquer coisa, mas deve incluir apenas letras minúsculas e números.)
    - ``` $storageName = $name``` (Essa variável será utilizada para o nome da conta de armazenamento).
    - ```$rgname = $name ``` (Essa variável será utilizada para o nome do grupo de recursos).
    - ``` $eventHubNameSpaceName = $name``` (Este é o nome do espaço de nomes do hub de eventos.)
1. Especifique a subscrição que trabalhar com:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Criar um grupo de recursos:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Se introduzir `$rg` neste momento, deverá ver um resultado semelhante a esta captura de ecrã:

   ![Saída depois de criar um grupo de recursos](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Crie uma conta de armazenamento que será utilizada para controlar as informações de estado:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Crie o espaço de nomes do hub de eventos. Isto é necessário para criar um hub de eventos.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Obtenha o ID da regra que será utilizado com o fornecedor de informações:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
1. Obter todas as localizações do Azure possíveis e adicione os nomes a uma variável que pode ser utilizada num passo posterior:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Se introduzir `$locations` neste momento, ver os nomes de localização sem informações adicionais, devolvidas pelo Get-AzLocation.
1. Crie um perfil de registo do Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Para obter mais informações sobre o perfil de registo do Azure, consulte [descrição geral do registo de atividades do Azure](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Poderá receber uma mensagem de erro quando tenta criar um perfil de registo. Em seguida, pode rever a documentação para Get-AzLogProfile e Remove-AzLogProfile. Se executar Get-AzLogProfile, verá informações sobre o perfil de registo. Pode eliminar o perfil de registo existente ao introduzir o ```Remove-AzLogProfile -name 'Log Profile Name' ``` comando.
>
>![Erro de perfil do Gestor de recursos](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

1. Crie o Cofre de chaves:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

1. Configure o registo para o Cofre de chaves:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Gerar registos de atividade

Pedidos têm de ser enviados para o Key Vault para gerar atividade de registo. Ações, como a geração de chaves, armazenar segredos, ou ler segredos do Key Vault, irá criar entradas de registo.

1. Exiba as chaves de armazenamento atual:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Gerar um novo **chave2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Exibir as chaves novamente e ver que **chave2** mantém um valor diferente:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Definir e ler um segredo para gerar entradas de registo adicionais:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Devolvido secreta](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurar a integração de registos do Azure

Agora que configurou todos os elementos necessários para que o Cofre de chaves de registo para um hub de eventos, terá de configurar a integração de registos do Azure:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ``` $storagekey = $storagekeys[0].Value```

Execute o comando AzLog para cada hub de eventos:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Depois de um minuto, ou seja, executar os comandos de dois últimos, deverá ver ficheiros JSON que está a ser gerados. Pode confirmar que o diretório de monitorização **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Passos Seguintes

- [Integração de registos do Azure FAQ](security-azure-log-integration-faq.md)
- [Introdução ao Azure Log Integration](security-azure-log-integration-get-started.md)
- [Integrar registos de recursos do Azure nos sistemas SIEM](security-azure-log-integration-overview.md)
