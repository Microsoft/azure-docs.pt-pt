---
title: Extensão de VM de diagnóstico de desempenho do Azure para Windows | Microsoft Docs
description: Apresenta a extensão de VM de diagnóstico de desempenho do Azure para Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: genlin
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f9a50b0e5dd4e96c9235348bbfaae1d8a6e54d53
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846614"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensão de VM de diagnóstico de desempenho do Azure para Windows

A extensão de VM de diagnóstico de desempenho do Azure ajuda a coletar dados de diagnóstico de desempenho de VMs do Windows. A extensão executa a análise e fornece um relatório de conclusões e recomendações para identificar e resolver problemas de desempenho na máquina virtual. Essa extensão instala uma ferramenta de solução de problemas chamada [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Se você quiser executar o diagnóstico em sua VM do portal do Azure para VMs não clássicas, é recomendável usar a nova experiência. Para obter mais informações, consulte [diagnóstico de desempenho para máquinas virtuais do Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Pré-requisitos

Essa extensão pode ser instalada no Windows Server 2008 R2, no Windows Server 2012, no Windows Server 2012 R2 e no Windows Server 2016. Ele também pode ser instalado em Windows 8.1 e no Windows 10.

## <a name="extension-schema"></a>Esquema de extensão
O JSON a seguir mostra o esquema para a extensão de VM de diagnóstico de desempenho do Azure. Essa extensão requer o nome e a chave de uma conta de armazenamento para armazenar a saída e o relatório de diagnóstico. Esses valores são confidenciais. A chave da conta de armazenamento deve ser armazenada dentro de uma configuração de configuração protegida. Os dados de configuração protegidos da extensão de VM do Azure são criptografados e só são descriptografados na máquina virtual de destino. Observe que **storageAccountName** e **storageAccountKey** diferenciam maiúsculas de minúsculas. Outros parâmetros necessários estão listados na seção a seguir.

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"        
        }
      }
    }
```

### <a name="property-values"></a>Valores de propriedade

|   **Name**   |**Valor/exemplo**|       **Descrição**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|A versão da API.
|publisher|Microsoft.Azure.Performance.Diagnostics|O namespace do Publicador para a extensão.
|type|AzurePerformanceDiagnostics|O tipo da extensão de VM.
|typeHandlerVersion|1.0|A versão do manipulador de extensão.
|performanceScenario|básica|O cenário de desempenho para o qual capturar dados. Os valores válidos são: **Basic**, **vmslow**, **azurefiles**e **Custom**.
|traceDurationInSeconds|300|A duração dos rastreamentos, se qualquer uma das opções de rastreamento estiver selecionada.
|perfCounterTrace|p|Opção para habilitar o rastreamento do contador de desempenho. Os valores válidos são **p** ou valor vazio. Se você não quiser capturar esse rastreamento, deixe o valor como vazio.
|networkTrace|D|Opção para habilitar o rastreamento de rede. Os valores válidos são **n** ou um valor vazio. Se você não quiser capturar esse rastreamento, deixe o valor como vazio.
|xperfTrace|x|Opção para habilitar o rastreamento XPerf. Os valores válidos são **x** ou valor vazio. Se você não quiser capturar esse rastreamento, deixe o valor como vazio.
|storPortTrace|s|Opção para habilitar o rastreamento StorPort. Os valores válidos são **s** ou valor vazio. Se você não quiser capturar esse rastreamento, deixe o valor como vazio.
|srNumber|123452016365929|O número do tíquete de suporte, se disponível. Deixe o valor como vazio se você não o tiver.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Data e hora atuais em UTC. Se você estiver usando o portal para instalar essa extensão, não será necessário fornecer esse valor.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|O identificador exclusivo de uma VM.
|storageAccountName|mystorageaccount|O nome da conta de armazenamento para armazenar os logs de diagnóstico e os resultados.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|A chave para a conta de armazenamento.

## <a name="install-the-extension"></a>Instalar a extensão

Siga estas instruções para instalar a extensão em máquinas virtuais do Windows:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a máquina virtual onde você deseja instalar esta extensão.

    ![Captura de tela de portal do Azure, com máquinas virtuais realçadas](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecione a folha **extensões** e selecione **Adicionar**.

    ![Captura de tela da folha de extensões, com adicionar realçado](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecione **diagnóstico de desempenho do Azure**, examine os termos e condições e selecione **criar**.

    ![Captura de tela de novo recurso, com o diagnóstico de desempenho do Azure realçado](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Forneça os valores de parâmetro para a instalação e selecione **OK** para instalar a extensão. Para obter mais informações sobre os cenários com suporte, consulte [como usar o PerfInsights](how-to-use-perfinsights.md#supported-troubleshooting-scenarios). 

    ![Captura de tela da caixa de diálogo instalar extensão](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Quando a instalação for bem-sucedida, você verá uma mensagem indicando esse status.

    ![Captura de tela da mensagem de provisionamento bem-sucedido](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A extensão é executada quando o provisionamento foi bem-sucedido. Leva dois minutos ou menos para ser concluído para o cenário básico. Para outros cenários, ele é executado durante a duração especificada durante a instalação.

## <a name="remove-the-extension"></a>Remover a extensão
Para remover a extensão de uma máquina virtual, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com), selecione a máquina virtual da qual você deseja remover essa extensão e, em seguida, selecione a folha **extensões** . 
2. Selecione o ( **..** .) para a entrada de extensão de diagnóstico de desempenho na lista e selecione **desinstalar**.

    ![Captura de tela da folha de extensões, com desinstalação realçada](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Você também pode selecionar a entrada de extensão e selecionar a opção **desinstalar** .

## <a name="template-deployment"></a>Implementação de modelos
As extensões de máquina virtual do Azure podem ser implantadas com modelos de Azure Resource Manager. O esquema JSON detalhado na seção anterior pode ser usado em um modelo de Azure Resource Manager. Isso executa a extensão de VM de diagnóstico de desempenho do Azure durante uma implantação de modelo de Azure Resource Manager. Aqui está um modelo de exemplo:

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "performanceScenario": "[parameters('performanceScenario')]",
            "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
            "perfCounterTrace": "[parameters('perfCounterTrace')]",
            "networkTrace": "[parameters('networkTrace')]",
            "xperfTrace": "[parameters('xperfTrace')]",
            "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]",
            "resourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('vmName'))]"
        },
        "protectedSettings": {
            "storageAccountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

## <a name="powershell-deployment"></a>Implantação do PowerShell
O `Set-AzVMExtension` comando pode ser usado para implantar a extensão de VM de diagnóstico de desempenho do Azure em uma máquina virtual existente.

PowerShell

```
$PublicSettings = @{ "storageAccountName"="mystorageaccount";"performanceScenario"="basic";"traceDurationInSeconds"=300;"perfCounterTrace"="p";"networkTrace"="";"xperfTrace"="";"storPortTrace"="";"srNumber"="";"requestTimeUtc"="2017-09-28T22:08:53.736Z";"resourceId"="VMResourceId" }
$ProtectedSettings = @{"storageAccountKey"="mystoragekey" }

Set-AzVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS
```

## <a name="information-on-the-data-captured"></a>Informações sobre os dados capturados
A ferramenta PerfInsights coleta vários logs, configuração e dados de diagnóstico, dependendo do cenário selecionado. Para obter mais informações, consulte a [documentação do PerfInsights](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Exibir e compartilhar os resultados

A saída da extensão pode ser encontrada em um arquivo zip que foi carregado para a conta de armazenamento especificada durante a instalação e é compartilhado por 30 dias usando [SAS (assinaturas de acesso compartilhado)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Esse arquivo zip contém logs de diagnóstico e um relatório com conclusões e recomendações. Um link SAS para o arquivo zip de saída pode ser encontrado dentro de um arquivo de texto chamado *zipfilename*_saslink. txt na pasta **C:\Packages\Plugins\Microsoft.Azure.performance.Diagnostics.AzurePerformanceDiagnostics\\ \< versão >** . Qualquer pessoa que tenha esse link é capaz de baixar o arquivo zip.

Para auxiliar o engenheiro de suporte a trabalhar em seu tíquete de suporte, a Microsoft poderá usar esse link SAS para baixar os dados de diagnóstico.

Para exibir o relatório, extraia o arquivo zip e abra o arquivo **PerfInsights Report. html** .

Você também deve ser capaz de baixar o arquivo zip diretamente do portal selecionando a extensão.

![Captura de tela do status detalhado do diagnóstico de desempenho](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> O link SAS exibido no portal pode não funcionar às vezes. Isso pode ser causado por uma URL malformada durante as operações de codificação e decodificação. Em vez disso, você pode obter o link diretamente do arquivo * _saslink. txt da VM.

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

- Status de implantação de extensão (na área de notificação) pode mostrar "implantação em andamento" mesmo que a extensão seja provisionada com êxito.

    Esse problema pode ser ignorado com segurança, desde que o status da extensão indique que a extensão foi provisionada com êxito.
- Você pode resolver alguns problemas durante a instalação usando os logs de extensão. A saída de execução de extensão é registrada em arquivos encontrados no seguinte diretório:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).
