---
title: Instalar e configurar extensão de diagnóstico do Windows Azure (WAD)
description: Saiba como recolher dados de diagnóstico do Azure numa conta de Armazenamento Azure para que possa vê-lo com uma das várias ferramentas disponíveis.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: 5b3cc4cbaa663b7932609e85c544378a7cca69ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472690"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalar e configurar extensão de diagnóstico do Windows Azure (WAD)
A extensão de diagnóstico do Azure é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos computacionais. Este artigo fornece detalhes sobre a instalação e configuração da extensão de diagnóstico do Windows e uma descrição de como os dados são armazenados na conta de Armazenamento Azure.

A extensão de diagnóstico é implementada como uma [extensão virtual](/virtual-machines/extensions/overview) da máquina em Azure, por isso suporta as mesmas opções de instalação usando modelos de Gestor de Recursos, PowerShell e CLI. Consulte [extensões e funcionalidades da máquina Virtual para](/virtual-machines/extensions/features-windows) obter detalhes sobre a instalação e manutenção de extensões de máquinas virtuais.

## <a name="install-with-azure-portal"></a>Instalar com portal Azure
Pode instalar e configurar a extensão de diagnóstico numa máquina virtual individual no portal Azure, que lhe fornece uma interface em oposição a trabalhar diretamente com a configuração. Quando ativa a extensão de diagnóstico, utilizará automaticamente uma configuração predefinida com os contadores e eventos de desempenho mais comuns. Pode modificar esta configuração predefinida de acordo com os seus requisitos específicos.

> [!NOTE]
> Existem definições de extensão de diagnóstico que não pode configurar usando o portal Azure, incluindo o envio de dados para o Azure Event Hubs. Deve utilizar um dos outros métodos de configuração para estas definições.

1. Abra o menu para uma máquina virtual no portal Azure.
2. Clique nas **definições de diagnóstico** na secção **de monitorização** do menu VM.
3. Clique em **ativar a monitorização ao nível do hóspede** se a extensão de diagnóstico ainda não estiver ativada.
4. Será criada uma nova conta de Armazenamento Azure para o VM com o nome baseado no nome do grupo de recursos para o VM. Pode anexar o VM a outra conta de armazenamento selecionando o separador **Agente.**

![Definições de diagnóstico](media/diagnostics-extension-windows-install/diagnostic-settings.png)


Pode modificar a configuração predefinida uma vez ativada a extensão de diagnóstico. A tabela seguinte descreve as opções que pode modificar nos diferentes separadores. Algumas opções têm um comando **Personalizado** que lhe permite especificar configuração mais detalhada; consulte o esquema de extensão de diagnóstico do Windows para obter detalhes sobre [diferentes](diagnostics-extension-schema-windows.md) definições.

| Guia | Descrição |
|:---|:---|
| Descrição geral | Apresenta a configuração atual com ligações aos outros separadores. |
| Contadores de desempenho | Selecione os contadores de desempenho para recolher e a taxa de amostra para cada um.  |
| Registos | Selecione os dados de registo para recolher. Isto inclui registos do Windows Event, registos IIS, registos de aplicações .NET e eventos ETW.  |
| Informações de falha de sistema | Ativar o depósito de colisão para diferentes processos. |
| Pias | Ativar os sumidouros de dados para enviar dados para destinos além do Armazenamento Azure.<br>Azure Monitor - Envia dados de desempenho para as Métricas do Monitor Azure.<br>Informações sobre aplicações - Envie dados para uma aplicação De insights de aplicação. |
| Agente | Modificar a seguinte configuração para o agente:<br>- Mude a conta de armazenamento.<br>- Especificar o disco local máximo utilizado para o agente.<br>- Configure os registos para a saúde do próprio agente.|


> [!NOTE]
> Embora a configuração para a extensão de diagnóstico possa ser formatada em JSON ou XML, qualquer configuração feita no portal Azure será sempre armazenada como JSON. Se utilizar o XML com outro método de configuração e, em seguida, alterar a sua configuração com o portal Azure, as definições serão alteradas para JSON.

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Consulte a [monitorização e diagnóstico de utilização com um Modelo de VM windows e gestor de recursos Azure](../../virtual-machines/extensions/diagnostics-template.md) na implementação da extensão de diagnóstico com modelos do Gestor de Recursos Azure. 

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure
O Azure CLI pode ser utilizado para implantar a extensão De diagnóstico azure a uma máquina virtual existente utilizando [extensão az vm definida](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set) como no exemplo seguinte. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

As definições protegidas são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) do esquema de configuração. Segue-se um exemplo mínimo de um ficheiro de definições protegida que define a conta de armazenamento. Consulte a [configuração exemplo](diagnostics-extension-schema-windows.md#privateconfig-element) para obter detalhes completos das definições privadas.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```
As configurações públicas são definidas no [elemento público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Segue-se um exemplo mínimo de um ficheiro de definições públicas que permite a recolha de registos de infraestruturas de diagnóstico, um único contador de desempenho e um único registo de eventos. Consulte a [configuração exemplo](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das definições públicas.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Implementação powerShell
A PowerShell pode ser utilizada para implantar a extensão de Diagnóstico Azure a uma máquina virtual existente utilizando a [Definição de AzVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmdiagnosticsextension) como no exemplo seguinte. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

As configurações privadas são definidas no [elemento PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)enquanto as configurações públicas são definidas no [elemento público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Também pode optar por especificar os detalhes da conta de armazenamento como parâmetros do Cmdlet set-AzVMDiagnosticsExtension em vez de incluí-los nas definições privadas.

Segue-se um exemplo mínimo de um ficheiro de configuração que permite a recolha de registos de infraestruturas de diagnóstico, um único contador de desempenho e um único registo de eventos. Consulte a [configuração exemplo](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações privadas e públicas. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000
            },
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M",
                        "unit": "percent"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                    {
                        "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                    }
                ]
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Consulte também [o PowerShell para ativar o Azure Diagnostics numa máquina virtual que executa o Windows](../../virtual-machines/extensions/diagnostics-windows.md).

## <a name="data-storage"></a>Armazenamento de dados
A tabela seguinte lista os diferentes tipos de dados recolhidos da extensão de diagnóstico e se são armazenados como uma mesa ou uma bolha. Os dados armazenados em tabelas também podem ser armazenados em bolhas dependendo da [definição do StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) na sua configuração pública.


| Dados | Tipo de armazenamento | Descrição |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Alterações no monitor de diagnóstico e configuração. |
| MESA DE Diretores DE WAD | Tabela | Diretórios que o monitor de diagnóstico está a monitorizar.  Isto inclui registos IIS, registos de pedidos falhados do IIS e diretórios personalizados.  A localização do ficheiro de registo blob é especificada no campo do contentor e o nome da bolha está no campo RelativePath.  O campo AbsolutePath indica a localização e o nome do ficheiro tal como existia na máquina virtual Azure. |
| WadLogsTable | Tabela | Registos escritos em código utilizando o ouvinte de traços. |
| WADPerformanceCountersTable | Tabela | Contadores de desempenho. |
| WADWindowsEventLogsTable | Tabela | Registos do Evento Windows. |
| wad-iis-failedreqlogfiles | Blobs | Contém informações dos registos de Pedidos Falhados do IIS. |
| wad-iis-logfiles | Blobs | Contém informações sobre os registos do IIS. |
| "personalizado" | Blobs | Um recipiente personalizado baseado na configuração de diretórios que são monitorizados pelo monitor de diagnóstico.  O nome deste recipiente blob será especificado no WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para visualizar dados de diagnóstico
Várias ferramentas estão disponíveis para visualizar os dados depois de transferidos para o armazenamento. Por exemplo:

* Server Explorer em Estúdio Visual - Se tiver instalado as Ferramentas Azure para o Microsoft Visual Studio, pode utilizar o nó de Armazenamento Azure no Server Explorer para visualizar os dados de blob e tabela apenas de leitura das suas contas de armazenamento Azure. Pode apresentar dados da sua conta de emulador de armazenamento local e também das contas de armazenamento que criou para o Azure. Para mais informações, consulte [Navegação e Gestão de Recursos](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)de Armazenamento com o Server Explorer .
* O [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento Azure no Windows, OSX e Linux.
* O [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Azure Diagnostics Manager que lhe permite visualizar, descarregar e gerir os dados de diagnóstico recolhidos pelas aplicações em execução no Azure.

## <a name="next-steps"></a>Passos Seguintes
- Consulte [os dados do Windows Azure](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre o reencaminhamento de dados de monitorização para os Hubs de Eventos Do Azure.
