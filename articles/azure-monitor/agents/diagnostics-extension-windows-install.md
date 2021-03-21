---
title: Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)
description: Saiba como instalar e configurar a extensão de diagnóstico do Windows. Além disso, saiba como é que os dados são armazenados e a conta de Armazenamento Azure.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 174f372f9dbe8dc0449c7f9b9f5b34c6206f92de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708565"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalar e configurar a extensão de diagnóstico do Windows Azure (WAD)
[A extensão de diagnóstico Azure](diagnostics-extension-overview.md) é um agente no Azure Monitor que recolhe dados de monitorização do sistema operativo convidado e cargas de trabalho de máquinas virtuais Azure e outros recursos compute. Este artigo fornece detalhes sobre a instalação e configuração da extensão de diagnóstico do Windows e uma descrição de como os dados são armazenados e conta de Armazenamento Azure.

A extensão de diagnóstico é implementada como uma [extensão de máquina virtual](../../virtual-machines/extensions/overview.md) em Azure, por isso suporta as mesmas opções de instalação usando modelos de Gestor de Recursos, PowerShell e CLI. Consulte [as extensões e funcionalidades da máquina virtual para windows](../../virtual-machines/extensions/features-windows.md) para obter detalhes sobre a instalação e manutenção de extensões de máquinas virtuais.

## <a name="overview"></a>Descrição geral
Quando configurar o Windows Azure a extensão de diagnóstico, deve especificar uma conta de armazenamento onde todos os dados especificados serão enviados. Pode opcionalmente adicionar um ou mais *sumidouros de dados* para enviar os dados para diferentes locais.

- Pia Azure Monitor - Envie dados de desempenho dos hóspedes para as Métricas do Monitor Azure.
- Afundamento do centro de eventos - Envie o desempenho do hóspede e faça login nos centros de eventos da Azure para encaminhar para fora de Azure. Esta pia não pode ser configurada no portal Azure.


## <a name="install-with-azure-portal"></a>Instalar com portal Azure
Pode instalar e configurar a extensão de diagnóstico numa máquina virtual individual no portal Azure, que lhe fornece uma interface em vez de trabalhar diretamente com a configuração. Quando ativar a extensão de diagnóstico, utilizará automaticamente uma configuração predefinida com os contadores de desempenho e eventos mais comuns. Pode modificar esta configuração predefinida de acordo com os seus requisitos específicos.

> [!NOTE]
> Descrevem as definições mais comuns para a extensão de diagnóstico. Para obter mais detalhes sobre todas as opções de configuração, consulte [o esquema de extensão de diagnóstico do Windows](diagnostics-extension-schema-windows.md).

1. Abra o menu para uma máquina virtual no portal Azure.

2. Clique nas **definições de Diagnóstico** na secção **de Monitorização** do menu VM.

3. Clique **Em Ativar a monitorização ao nível** do hóspede se a extensão de diagnóstico ainda não tiver sido ativada.

   ![Ativar monitorização](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Será criada uma nova conta de Armazenamento Azure para o VM com o nome baseado no nome do grupo de recursos para o VM, e será selecionado um conjunto padrão de contadores e registos de desempenho dos hóspedes.

   ![Definições de diagnóstico](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. No **separador 'Contadores' Performance,** selecione as métricas do hóspede que pretende recolher desta máquina virtual. Utilize a definição **Personalizada** para uma seleção mais avançada.

   ![Contadores de desempenho](media/diagnostics-extension-windows-install/performance-counters.png)

6. No **separador 'Registares',** selecione os registos para recolher a partir da máquina virtual. Os registos podem ser enviados para centros de armazenamento ou evento, mas não para o Azure Monitor. Utilize o [agente Log Analytics](../agents/log-analytics-agent.md) para recolher registos de convidados no Azure Monitor.

   ![A screenshot mostra o separador Logs com diferentes registos selecionados para uma máquina virtual.](media/diagnostics-extension-windows-install/logs.png)

7. No **separador Crash, especifique** quaisquer processos para recolher os depósitos de memória após um acidente. Os dados serão escritos na conta de armazenamento para a definição de diagnóstico, e pode especificar opcionalmente um recipiente blob.

   ![Informações de falha de sistema](media/diagnostics-extension-windows-install/crash-dumps.png)

8. No **separador Sinks, especifique** se deve enviar os dados para outros locais que não o armazenamento Azure. Se selecionar **o Azure Monitor,** os dados de desempenho dos hóspedes serão enviados para as Métricas do Monitor Azure. Não é possível configurar os centros de eventos que se afundam utilizando o portal Azure.

   ![A screenshot mostra o separador Sinks com a opção Enviar dados de diagnóstico para a opção Azure Monitor Ativado.](media/diagnostics-extension-windows-install/sinks.png)
   
   Se não tiver ativado uma Identidade Atribuída do Sistema configurada para a sua máquina virtual, poderá ver o aviso abaixo quando guardar uma configuração com a pia do Monitor Azure. Clique no banner para ativar a identidade atribuída ao sistema.
   
   ![Entidade gerida](media/diagnostics-extension-windows-install/managed-entity.png)

9. No **Agente,** pode alterar a conta de armazenamento, definir a quota do disco e especificar se deve recolher registos de infraestruturas de diagnóstico.  

   ![A screenshot mostra o separador Agente com a opção de definir a conta de armazenamento.](media/diagnostics-extension-windows-install/agent.png)

10. Clique **em Guardar** para guardar a configuração. 

> [!NOTE]
> Embora a configuração para a extensão de diagnóstico possa ser formatada em JSON ou XML, qualquer configuração feita no portal Azure será sempre armazenada como JSON. Se utilizar o XML com outro método de configuração e, em seguida, alterar a sua configuração com o portal Azure, as definições serão alteradas para JSON.

## <a name="resource-manager-template"></a>Modelo do Resource Manager
Consulte [a monitorização e diagnósticos de utilização com modelos de Gestor de Recursos Windows VM e Azure](../../virtual-machines/extensions/diagnostics-template.md) na implementação da extensão de diagnóstico com modelos do Gestor de Recursos Azure. 

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI
O CLI Azure pode ser utilizado para implantar a extensão Azure Diagnostics a uma máquina virtual existente utilizando [o conjunto de extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) como no exemplo seguinte. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

As definições protegidas são definidas no [elemento PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) do esquema de configuração. Segue-se um exemplo mínimo de um ficheiro de definições protegidas que define a conta de armazenamento. Consulte [a configuração de exemplo](diagnostics-extension-schema-windows.md#privateconfig-element) para obter detalhes completos das definições privadas.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

As configurações públicas são definidas no [elemento público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Segue-se um exemplo mínimo de um ficheiro de definições públicas que permite a recolha de registos de infraestruturas de diagnóstico, um balcão de desempenho único e um único registo de eventos. Consulte [a configuração de exemplo](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das definições do público.

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



## <a name="powershell-deployment"></a>Implementação do PowerShell
O PowerShell pode ser utilizado para implantar a extensão Azure Diagnostics a uma máquina virtual existente utilizando [o Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) como no exemplo seguinte. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

As definições privadas são definidas no [elemento PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)enquanto as configurações públicas são definidas no [elemento Público](diagnostics-extension-schema-windows.md#publicconfig-element) do esquema de configuração. Também pode optar por especificar os detalhes da conta de armazenamento como parâmetros do Set-AzVMDiagnosticsExtension cmdlet em vez de incluí-los nas definições privadas.

Segue-se um exemplo mínimo de um ficheiro de configuração que permite a recolha de registos de infraestruturas de diagnóstico, um balcão de desempenho único e um único registo de eventos. Consulte [a configuração exemplo](diagnostics-extension-schema-windows.md#publicconfig-element) para obter detalhes completos das configurações privadas e públicas. 

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
A tabela que se segue lista os diferentes tipos de dados recolhidos a partir da extensão de diagnóstico e se são armazenados como uma mesa ou uma bolha. Os dados armazenados em tabelas também podem ser armazenados em bolhas dependendo da [definição de StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) na sua configuração pública.


| Dados | Tipo de armazenamento | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Alterações no monitor de diagnóstico e na configuração. |
| WADDirectoriesTable | Tabela | Diretórios que o monitor de diagnóstico está a monitorizar.  Isto inclui registos IIS, registos de pedidos falhados do IIS e diretórios personalizados.  A localização do ficheiro de registo de bolhas é especificada no campo de Contentores e o nome da bolha está no campo RelativePath.  O campo AbsolutePath indica a localização e o nome do ficheiro tal como existia na máquina virtual Azure. |
| WadLogsTable | Tabela | Registos escritos em código utilizando o ouvinte de traços. |
| WADPerformanceCountersTable | Tabela | Contadores de desempenho. |
| WADWindowsEventLogsTable | Tabela | Registos do Evento windows. |
| wad-iis-failedreqlogfiles | Blobs | Contém informações de registos de pedidos falhados do IIS. |
| wad-iis-logfiles | Blobs | Contém informações sobre registos IIS. |
| "costume" | Blobs | Um recipiente personalizado baseado em diretórios configurados que são monitorizados pelo monitor de diagnóstico.  O nome deste recipiente blob será especificado no WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para visualizar dados de diagnóstico
Várias ferramentas estão disponíveis para visualizar os dados depois de serem transferidos para armazenamento. Por exemplo:

* Server Explorer in Visual Studio - Se instalou as Ferramentas Azure para o Microsoft Visual Studio, pode utilizar o nó de armazenamento Azure no Server Explorer para ver apenas os dados de bolhas e tabelas de leitura a partir das suas contas de armazenamento Azure. Pode apresentar dados a partir da sua conta de emulador de armazenamento local e também a partir de contas de armazenamento que criou para o Azure. Para obter mais informações, consulte [procurar e gerir recursos de armazenamento com o Explorador do Servidor.](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)
* [O Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento Azure no Windows, OSX e Linux.
* [O Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Azure Diagnostics Manager que lhe permite visualizar, transferir e gerir os dados de diagnóstico recolhidos pelas aplicações em execução no Azure.

## <a name="next-steps"></a>Passos seguintes
- Consulte [Enviar dados da extensão de diagnóstico do Windows Azure aos Centros de Eventos](diagnostics-extension-stream-event-hubs.md) para obter mais informações sobre o encaminhamento de dados de monitorização para Azure Event Hubs.
