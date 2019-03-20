---
title: Extensão de VM de diagnóstico de desempenho do Azure para Windows | Documentos da Microsoft
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
ms.openlocfilehash: 769305cc3d838832f8f445ac9623a1724603f968
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002728"
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Extensão de VM de diagnóstico de desempenho do Azure para Windows

Extensão de VM de diagnóstico de desempenho do Azure ajuda a recolher dados de diagnóstico de desempenho de VMs do Windows. A extensão executa análise e fornece um relatório de descobertas e recomendações para identificar e resolver problemas de desempenho na máquina virtual. Esta extensão instala uma ferramenta de resolução de problemas chamada [PerfInsights](https://aka.ms/perfinsights).

> [!NOTE]
> Se quiser executar diagnósticos na sua VM a partir do portal do Azure para VMs não clássica, recomenda-se para utilizar a nova experiência. Para obter mais informações, consulte [diagnóstico de desempenho para máquinas virtuais do Azure](performance-diagnostics.md) 

## <a name="prerequisites"></a>Pré-requisitos

Esta extensão pode ser instalada no Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016. Também pode ser instalado no Windows 8.1 e Windows 10.

## <a name="extension-schema"></a>Esquema de extensão
O JSON seguinte mostra o esquema da extensão de VM de diagnóstico de desempenho do Azure. Esta extensão requer o nome e chave para uma conta de armazenamento armazenar a saída de diagnóstico e o relatório. Estes valores são confidenciais. Chave da conta de armazenamento deve ser armazenado dentro de uma configuração de definição protegido. Dados de configuração de extensão protegido de VM do Azure são encriptados e só é desencriptado na máquina de virtual de destino. Tenha em atenção que **storageAccountName** e **storageAccountKey** diferenciam maiúsculas de minúsculas. Outros parâmetros necessários estão listados na secção seguinte.

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

|   **Nome**   |**Valor / exemplo**|       **Descrição**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|A versão da API.
|publicador|Microsoft.Azure.Performance.Diagnostics|O espaço de nomes de publicador para a extensão.
|tipo|AzurePerformanceDiagnostics|O tipo de extensão de VM.
|typeHandlerVersion|1.0|A versão do manipulador de extensão.
|performanceScenario|básica|O cenário de desempenho para o qual capturar os dados. Os valores válidos são: **básica**, **vmslow**, **azurefiles**, e **personalizado**.
|traceDurationInSeconds|300|A duração dos rastreios, se qualquer uma das opções de rastreio estão selecionadas.
|perfCounterTrace|p|Opção para ativar o rastreio de contador de desempenho. Os valores válidos são **p** ou valor de vazio. Se não pretender capturar esse rastreamento, deixe o valor como vazio.
|networkTrace|D|Opção para ativar o rastreio de rede. Os valores válidos são **n** ou valor de vazio. Se não pretender capturar esse rastreamento, deixe o valor como vazio.
|xperfTrace|x|Opção para ativar o rastreio de XPerf. Os valores válidos são **x** ou valor de vazio. Se não pretender capturar esse rastreamento, deixe o valor como vazio.
|storPortTrace|s|Opção para ativar o rastreio de StorPort. Os valores válidos são **s** ou valor de vazio. Se não pretender capturar esse rastreamento, deixe o valor como vazio.
|srNumber|123452016365929|O pedido de suporte número de suporte, se disponível. Deixe o valor como vazio se não o tiver.
|requestTimeUtc|2017-09-28T22:08:53.736Z|Data e hora atuais em Utc. Se estiver a utilizar o portal para instalar esta extensão, não é necessário fornecer este valor.
|resourceId|/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}|O identificador exclusivo de uma VM.
|storageAccountName|mystorageaccount|O nome da conta do storage para armazenar os registos de diagnóstico e os resultados.
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|A chave para a conta de armazenamento.

## <a name="install-the-extension"></a>Instalar a extensão

Siga estas instruções para instalar a extensão em máquinas de virtuais do Windows:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione a máquina virtual onde pretende instalar esta extensão.

    ![Captura de ecrã do portal do Azure, com as máquinas virtuais realçado](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. Selecione o **extensões** painel e selecione **Add**.

    ![Painel de captura de ecrã de extensões, com adicionar realçado](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Selecione **diagnóstico de desempenho do Azure**, reveja os termos e condições e selecione **criar**.

    ![Captura de ecrã do novo ecrã de recursos, com o diagnóstico de desempenho do Azure realçadas](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. Forneça os valores de parâmetro para a instalação e selecione **OK** para instalar a extensão. Para obter mais informações sobre cenários suportados, consulte [como utilizar o PerfInsights](how-to-use-perfInsights.md#supported-troubleshooting-scenarios). 

    ![Caixa de diálogo de extensão de captura de ecrã de instalar](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. Quando a instalação foi bem-sucedida, verá uma mensagem a indicar este estado.

    ![Mensagem de captura de ecrã de aprovisionamento com êxito](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > A extensão é executada quando o aprovisionamento foi concluída com êxito. Demora dois minutos ou menos para concluir o cenário básico. Para outros cenários, ele percorre a duração especificada durante a instalação.

## <a name="remove-the-extension"></a>Remover a extensão
Para remover a extensão de uma máquina virtual, siga estes passos:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com), selecione a máquina virtual a partir do qual pretende remover esta extensão e, em seguida, selecione a **extensões** painel. 
2. Selecione o (**...** ) para a entrada de extensão de diagnóstico de desempenho na lista e selecione **desinstalar**.

    ![Painel de captura de ecrã de extensões, com a desinstalação realçado](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > Também pode selecionar a entrada de extensão e selecione o **desinstalação** opção.

## <a name="template-deployment"></a>Implementação de modelos
Extensões de máquina virtual do Azure podem ser implementadas com modelos Azure Resource Manager. O esquema JSON detalhado na secção anterior pode ser utilizado num modelo do Azure Resource Manager. Esta solução é executada a extensão de VM de diagnóstico de desempenho do Azure durante uma implementação de modelo do Azure Resource Manager. Este é um modelo de exemplo:

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

## <a name="powershell-deployment"></a>Implementação do PowerShell
O `Set-AzVMExtension` comando pode ser utilizado para implementar a extensão de VM de diagnóstico de desempenho do Azure para uma máquina virtual existente.

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
A ferramenta de PerfInsights recolhe vários registos de configuração e dados de diagnóstico, dependendo do cenário selecionado. Para obter mais informações, consulte a [PerfInsights documentação](https://aka.ms/perfinsights).

## <a name="view-and-share-the-results"></a>Ver e partilhar os resultados

Saída da extensão pode ser encontrada num arquivo zip que carregou para a conta de armazenamento especificada durante a instalação e é partilhado por 30 dias, utilizando [assinaturas de acesso partilhado (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md). Este ficheiro zip contém os registos de diagnóstico e de um relatório com descobertas e recomendações. Uma ligação SAS para o ficheiro zip de saída pode ser encontrada num arquivo de texto chamado *zipfilename*_saslink.txt sob a pasta **C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics \\ \<versão >**. Qualquer pessoa que tenha esta ligação é capaz de transferir o ficheiro zip.

Para ajudar o engenheiro de suporte a trabalhar no seu pedido de suporte, Microsoft poderá utilizar esta ligação SAS para transferir os dados de diagnóstico.

Para ver o relatório, extraia o ficheiro zip e abra o **PerfInsights Report.html** ficheiro.

Deve também conseguir transferir o ficheiro zip diretamente no portal, selecionando a extensão.

![Estado detalhado da captura de ecrã do diagnóstico de desempenho](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> A ligação SAS apresentada no portal do poderá não funcionar, às vezes. Isto pode ser causado por um URL com formato incorreto durante as operações de codificação e decodificação. Em vez disso, pode obter a ligação diretamente a partir do ficheiro de *_saslink.txt da VM.

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

- Estado de implementação de extensão (na área de notificação) poderá mostrar "Implementação em curso", apesar da extensão é aprovisionada com êxito.

    Este problema pode ser ignorado com segurança, desde que o estado da extensão indica que a extensão é aprovisionada com êxito.
- Pode solucionar alguns problemas durante a instalação utilizando os registos de extensão. Resultado da execução de extensão é registado para arquivos encontrados no diretório seguinte:

        C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics\<version>

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
