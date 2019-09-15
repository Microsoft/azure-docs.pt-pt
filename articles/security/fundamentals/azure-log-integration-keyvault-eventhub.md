---
title: Integrar logs de Azure Key Vault usando os hubs de eventos | Microsoft Docs
description: Tutorial que fornece as etapas necessárias para disponibilizar logs de Key Vault para um SIEM usando a integração de log do Azure
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004116"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Tutorial de integração do log do Azure: Processar eventos de Azure Key Vault usando os hubs de eventos

>[!IMPORTANT]
> O recurso integração de logs do Azure será preterido por 06/15/2019. Os downloads do AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer ao mover para frente examine o [Azure monitor de envio para integração com ferramentas Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Você pode usar a integração de log do Azure para recuperar eventos registrados e disponibilizá-los para o sistema SIEM (gerenciamento de eventos e informações de segurança). Este tutorial mostra um exemplo de como a integração de log do Azure pode ser usada para processar logs que são adquiridos por meio dos hubs de eventos do Azure.

O método preferencial para integrar os logs do Azure é usando o conector de Azure Monitor do seu fornecedor SIEM e seguindo estas [instruções](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). No entanto, se o fornecedor do SIEM não fornecer um conector para Azure Monitor, você poderá usar a integração de log do Azure como uma solução temporária (se o SIEM tiver suporte da integração de log do Azure) até que esse conector esteja disponível.

 
Use este tutorial para familiarizar-se com a forma como a integração de log do Azure e os hubs de eventos funcionam juntos, seguindo as etapas de exemplo e compreendendo como cada etapa dá suporte à solução. Em seguida, você pode pegar o que aprendeu aqui para criar suas próprias etapas para dar suporte aos requisitos exclusivos da sua empresa.

> [!WARNING]
> As etapas e os comandos deste tutorial não devem ser copiados e colados. Eles são apenas exemplos. Não use os comandos do PowerShell "no estado em que se encontram" em seu ambiente em tempo real. Você deve personalizá-los com base em seu ambiente exclusivo.


Este tutorial orienta você pelo processo de colocar Azure Key Vault atividade registrada em um hub de eventos e disponibilizá-lo como arquivos JSON para seu sistema SIEM. Você pode configurar seu sistema SIEM para processar os arquivos JSON.

>[!NOTE]
>A maioria das etapas neste tutorial envolve a configuração de cofres de chaves, contas de armazenamento e hubs de eventos. As etapas específicas de integração de log do Azure estão no final deste tutorial. Não execute essas etapas em um ambiente de produção. Elas são destinadas apenas a um ambiente de laboratório. Você deve personalizar as etapas antes de usá-las em produção.

As informações fornecidas ao longo do caminho ajudam a entender os motivos por trás de cada etapa. Links para outros artigos fornecem mais detalhes sobre determinados tópicos.

Para obter mais informações sobre os serviços que este tutorial menciona, consulte: 

- [Cofre de Chaves do Azure](/azure/key-vault/key-vault-overview)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Integração de log do Azure](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Configuração inicial

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para poder concluir as etapas neste artigo, você precisará do seguinte:

* Uma assinatura do Azure e uma conta nessa assinatura com direitos de administrador. Se você não tiver uma assinatura, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/).
 
* Um sistema com acesso à Internet que atende aos requisitos para instalar a integração de log do Azure. O sistema pode estar em um serviço de nuvem ou hospedado localmente.

* Integração do log do Azure instalada. Para instalá-lo:

   a. Use Área de Trabalho Remota para se conectar ao sistema mencionado na etapa 2.   
   b. Copie o instalador de integração de logs do Azure para o sistema. c. Inicie o instalador e aceite os termos de licença para software Microsoft.

* Se você fornecer informações de telemetria, deixe a caixa de seleção marcada. Se você preferir não enviar informações de uso à Microsoft, desmarque a caixa de seleção.

   Para obter mais informações sobre a integração de log do Azure e como instalá-la, consulte [integração de log do Azure com log de diagnóstico do Azure e encaminhamento de eventos do Windows](azure-log-integration-get-started.md).

* A versão mais recente do PowerShell.

   Se você tiver o Windows Server 2016 instalado, terá pelo menos o PowerShell 5,0. Se você estiver usando qualquer outra versão do Windows Server, talvez tenha uma versão anterior do PowerShell instalada. Você pode verificar a versão inserindo ```get-host``` em uma janela do PowerShell. Se você não tiver o PowerShell 5,0 instalado, você poderá [baixá-lo](https://www.microsoft.com/download/details.aspx?id=50395).

   Depois de ter pelo menos o PowerShell 5,0, você pode prosseguir para instalar a versão mais recente seguindo as instruções em [instalar o Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Criar elementos de infraestrutura de suporte

1. Abra uma janela do PowerShell com privilégios elevados e vá para **C:\Program Files\Microsoft Azure log Integration**.
1. Importe os cmdlets AzLog executando o script LoadAzLogModule. ps1. Insira o `.\LoadAzLogModule.ps1` comando. (Observe o ". \" nesse comando.) Deverá ver algo semelhante ao seguinte:</br>

   ![Lista de módulos carregados](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Insira o `Connect-AzAccount` comando. Na janela de logon, insira as informações de credencial para a assinatura que você usará para este tutorial.

   >[!NOTE]
   >Se esta for a primeira vez que você está fazendo logon no Azure por meio deste computador, você verá uma mensagem sobre como permitir que a Microsoft colete dados de uso do PowerShell. Recomendamos que você habilite essa coleta de dados porque ela será usada para melhorar Azure PowerShell.

1. Após a autenticação bem-sucedida, você estará conectado. Anote a ID da assinatura e o nome da assinatura, pois você precisará delas para concluir as etapas posteriores.

1. Crie variáveis para armazenar valores que serão usados posteriormente. Insira cada uma das seguintes linhas do PowerShell. Talvez seja necessário ajustar os valores para que correspondam ao seu ambiente.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(O nome da sua assinatura pode ser diferente. Você pode vê-lo como parte da saída do comando anterior.)
    - ```$location = 'West US'```(Essa variável será usada para passar o local onde os recursos devem ser criados. Você pode alterar essa variável para qualquer local de sua escolha.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(O nome pode ser qualquer coisa, mas deve incluir apenas letras minúsculas e números.)
    - ```$storageName = $name```(Essa variável será usada para o nome da conta de armazenamento.)
    - ```$rgname = $name```(Essa variável será usada para o nome do grupo de recursos.)
    - ```$eventHubNameSpaceName = $name```(Esse é o nome do namespace do hub de eventos.)
1. Especifique a assinatura com a qual você trabalhará:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Criar um grupo de recursos:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Se você digitar `$rg` neste ponto, deverá ver uma saída semelhante a esta captura de tela:

   ![Saída após a criação de um grupo de recursos](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Crie uma conta de armazenamento que será usada para manter o controle das informações de Estado:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Crie o namespace do hub de eventos. Isso é necessário para criar um hub de eventos.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Obtenha a ID da regra que será usada com o provedor de informações:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Obtenha todos os locais do Azure possíveis e adicione os nomes a uma variável que pode ser usada em uma etapa posterior:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Se você inserir `$locations` nesse ponto, verá os nomes de local sem as informações adicionais retornadas por Get-AzLocation.
1. Criar um perfil de log de Azure Resource Manager: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Para obter mais informações sobre o perfil de log do Azure, consulte [visão geral do log de atividades do Azure](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Você pode receber uma mensagem de erro ao tentar criar um perfil de log. Em seguida, você pode examinar a documentação para Get-AzLogProfile e remove-AzLogProfile. Se você executar Get-AzLogProfile, verá informações sobre o perfil de log. Você pode excluir o perfil de log existente inserindo ```Remove-AzLogProfile -name 'Log Profile Name'``` o comando.
>
>![Erro de perfil do Gerenciador de recursos](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

1. Crie o cofre de chaves:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Configurar o registro em log para o cofre de chaves:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Gerar atividade de log

As solicitações precisam ser enviadas para Key Vault para gerar a atividade de log. Ações como geração de chave, armazenamento de segredos ou leitura de segredos de Key Vault criarão entradas de log.

1. Exibir as chaves de armazenamento atuais:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Gerar um novo **Key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Exiba as chaves novamente e veja que **Key2** contém um valor diferente:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Defina e leia um segredo para gerar entradas de log adicionais:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Segredo retornado](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Configurar a integração de log do Azure

Agora que você configurou todos os elementos necessários para que Key Vault fazer logon em um hub de eventos, precisará configurar a integração de log do Azure:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Execute o comando AzLog para cada Hub de eventos:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Depois de um minuto ou de execução dos dois últimos comandos, você deverá ver os arquivos JSON sendo gerados. Você pode confirmar isso monitorando o diretório **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Passos Seguintes

- [Perguntas frequentes sobre integração de log do Azure](azure-log-integration-faq.md)
- [Introdução à integração de log do Azure](azure-log-integration-get-started.md)
- [Integre logs de recursos do Azure em seus sistemas SIEM](azure-log-integration-overview.md)
