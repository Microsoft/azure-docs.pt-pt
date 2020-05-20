---
title: Instalar e configurar extensão de diagnóstico do Windows Azure (WAD)
description: Saiba como recolher dados de diagnóstico do Azure numa conta de Armazenamento Azure para que possa vê-lo com uma das várias ferramentas disponíveis.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.openlocfilehash: a964a28b728a2b1741fb555f47fe6e329bc9902a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655665"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalar e configurar extensão de diagnóstico do Windows Azure (WAD)
[A extensão](diagnostics-extension-overview.md) de diagnóstico do Azure é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos computacionais. Este artigo fornece detalhes sobre a instalação e configuração da extensão de diagnóstico do Windows e uma descrição de como os dados são armazenados na conta de Armazenamento Azure.

A extensão de diagnóstico é implementada como uma [extensão virtual](../../virtual-machines/extensions/overview.md) da máquina em Azure, por isso suporta as mesmas opções de instalação usando modelos de Gestor de Recursos, PowerShell e CLI. Consulte [extensões e funcionalidades da máquina Virtual para](../../virtual-machines/extensions/features-windows.md) obter detalhes sobre a instalação e manutenção de extensões de máquinas virtuais.

## <a name="overview"></a>Descrição geral
Quando configurar a extensão de diagnóstico do Windows Azure, deve especificar uma conta de armazenamento onde todos os dados especificados serão enviados. Pode opcionalmente adicionar um para mais *afundados* de dados para enviar os dados para diferentes locais.

- Pia Azure Monitor - Envie os dados de desempenho dos hóspedes para as Métricas do Monitor Azure.
- Afundatório de hub de eventos - Envie os dados de desempenho dos hóspedes e de registo para os centros de eventos do Azure para a frente fora do Azure. Esta pia não pode ser configurada no portal Azure.


## <a name="install-with-azure-portal"></a>Instalar com portal Azure
Pode instalar e configurar a extensão de diagnóstico numa máquina virtual individual no portal Azure, que lhe fornece uma interface em oposição a trabalhar diretamente com a configuração. Quando ativa a extensão de diagnóstico, utilizará automaticamente uma configuração predefinida com os contadores e eventos de desempenho mais comuns. Pode modificar esta configuração predefinida de acordo com os seus requisitos específicos.

> [!NOTE]
> Os seguintes descrevem as definições mais comuns para a extensão de diagnóstico. Para mais detalhes sobre todas as opções de configuração, consulte o esquema de [extensão](diagnostics-extension-schema-windows.md)de diagnóstico do Windows .

1. Abra o menu para uma máquina virtual no portal Azure.

2. Clique nas **definições de diagnóstico** na secção **de monitorização** do menu VM.

3. Clique em **ativar a monitorização ao nível do hóspede** se a extensão de diagnóstico ainda não estiver ativada.

   ![Ativar monitorização](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Será criada uma nova conta de Armazenamento Azure para o VM com o nome baseado no nome do grupo de recursos para o VM, e será selecionado um conjunto padrão de contadores e registos de desempenho dos hóspedes.

   ![Definições de diagnóstico](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. No separador de **contadores performance,** selecione as métricas de hóspedes que gostaria de recolher desta máquina virtual. Utilize a definição **Personalizada** para uma seleção mais avançada.

   ![Contadores de desempenho](media/diagnostics-extension-windows-install/performance-counters.png)

6. No separador **Logs,** selecione os registos para recolher a partir da máquina virtual. Os registos podem ser enviados para centros de armazenamento ou eventos, mas não para o Monitor Azure. Utilize o [agente Log Analytics](log-analytics-agent.md) para recolher registos de hóspedes no Monitor Azure.

   ![Registos](media/diagnostics-extension-windows-install/logs.png)

7. No separador **de despejos crash,** especifique quaisquer processos para recolher depósitos de memória após um acidente. Os dados serão escritos na conta de armazenamento para a definição de diagnóstico, e pode especificar opcionalmente um recipiente de bolha.

   ![Informações de falha de sistema](media/diagnostics-extension-windows-install/crash-dumps.png)

8. No **separador Sinks,** especifique se deve enviar os dados para outros locais que não o armazenamento do Azure. Se selecionar **o Monitor Azure,** os dados de desempenho dos hóspedes serão enviados para as Métricas do Monitor Do Azure. Não é possível configurar os centros de eventos utilizando o portal Azure.

   ![Pias](media/diagnostics-extension-windows-install/sinks.png)
   
   Se não tiver ativado uma Identidade Atribuída ao Sistema configurada para a sua máquina virtual, poderá ver o aviso abaixo quando guardar uma configuração com a pia do Monitor Azure. Clique no banner para ativar a identidade atribuída ao sistema.
   
   ![Entidade gerida](media/diagnostics-extension-windows-install/managed-entity.png)

9. No **Agente,** pode alterar a conta de armazenamento, definir a quota do disco e especificar se deve recolher registos de infraestruturas de diagnóstico.  

   ![Agente](media/diagnostics-extension-windows-install/agent.png)

10. Clique em **Guardar** para salvar a configuração. 

> [!NOTE]
> Embora a configuração para a extensão de diagnóstico possa ser formatada em JSON ou XML, qualquer configuração feita no portal Azure será sempre armazenada como JSON. Se utilizar o XML com outro método de configuração e, em seguida, alterar a sua configuração com o portal Azure, as definições serão alteradas para JSON.

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Consulte a [monitorização e diagnóstico de utilização com um Modelo de VM windows e gestor de recursos Azure](../../virtual-machines/extensions/diagnostics-template.md) na implementação da extensão de diagnóstico com modelos do Gestor de Recursos Azure. 

## <a name="azure-cli-deployment"></a>Implantação Azure CLI
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
                "overallQuotaInMB": 10000,
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

## <a name="next-steps"></a>Passos seguintes
- Consulte [os dados do Windows Azure](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre o reencaminhamento de dados de monitorização para os Hubs de Eventos Do Azure.
