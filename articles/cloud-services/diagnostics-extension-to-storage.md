---
title: Armazenar e Ver Dados de Diagnósticos no Armazenamento do Azure
description: Saiba como recolher dados de diagnóstico do Azure numa conta de Armazenamento Azure para que possa vê-lo com uma das várias ferramentas disponíveis.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740079"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e ver dados de diagnósticos no Armazenamento do Azure

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Os dados de diagnóstico não são armazenados permanentemente a menos que os transfira para o Emulador de Armazenamento Microsoft Azure ou para o Azure Storage. Uma vez armazenado, pode ser visto com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especificar uma conta de armazenamento
Especifica a conta de armazenamento que pretende utilizar no ficheiro ServiceConfiguration.cscfg. A informação da conta é definida como uma cadeia de ligação numa definição de configuração. O exemplo a seguir mostra a cadeia de ligação padrão criada para um novo projeto de Cloud Service no Estúdio Visual:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Pode alterar este fio de ligação para fornecer informações de conta para uma conta de armazenamento Azure.

Dependendo do tipo de dados de diagnóstico que estão a ser recolhidos, o Azure Diagnostics utiliza o serviço Blob ou o serviço Table. A tabela seguinte mostra as fontes de dados que são persistidos e o seu formato.

| Origem de dados | Formato de armazenamento |
| --- | --- |
| Troncos de Azure |Tabela |
| IIS 7.0 registos |Blobs |
| Registos da infraestrutura do Diagnóstico do Azure |Tabela |
| Registos de rastreio de pedidos falhados |Blobs |
| Registos do Evento Windows |Tabela |
| Contadores de desempenho |Tabela |
| Informações de falha de sistema |Blobs |
| Registos de erros personalizados |Blobs |

## <a name="transfer-diagnostic-data"></a>Transferir dados de diagnóstico
Para o SDK 2.5 e posteriormente, o pedido de transferência de dados de diagnóstico pode ocorrer através do ficheiro de configuração. Pode transferir dados de diagnóstico em intervalos programados, conforme especificado na configuração.

Para SDK 2.4 e anterior pode solicitar a transferência dos dados de diagnóstico através do ficheiro de configuração, bem como programáticamente. A abordagem programática também permite fazer transferências a pedido.

> [!IMPORTANT]
> Ao transferir dados de diagnóstico para uma conta de armazenamento Azure, incorre em custos para os recursos de armazenamento que os seus dados de diagnóstico utilizam.
> 
> 

## <a name="store-diagnostic-data"></a>Armazenar dados de diagnóstico
Os dados de registo são armazenados no armazenamento de Blob ou Table com os seguintes nomes:

**Tabelas**

* **WadLogsTable** - Registos escritos em código utilizando o ouvinte de traços.
* **WADDiagnosticInfrastructureLogsTable** - Alterações de monitor de diagnóstico e configuração.
* **WADDirectoriesTable – Diretórios** que o monitor de diagnóstico está a monitorizar.  Isto inclui registos IIS, registos de pedidos falhados do IIS e diretórios personalizados.  A localização do ficheiro de registo de bolhas é especificada no campo de Contentores e o nome da bolha está no campo RelativePath.  O campo AbsolutePath indica a localização e o nome do ficheiro tal como existia na máquina virtual Azure.
* **WADPerformanceCountersTable** – Contadores de desempenho.
* **WADWindowsEventLogsTable** – registos de eventos do Windows.

**Blobs**

* **wad-control-container** – (Apenas para SDK 2.4 e anterior) Contém os ficheiros de configuração XML que controlam os diagnósticos Azure .
* **wad-iis-failedreqlogfiles** – Contém informações de registos de pedidos falhados do IIS.
* **wad-iis-logfiles** – Contém informações sobre registos IIS.
* **"personalizado"** – Um recipiente personalizado baseado em diretórios configurados que são monitorizados pelo monitor de diagnóstico.  O nome deste recipiente blob será especificado no WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para visualizar dados de diagnóstico
Várias ferramentas estão disponíveis para visualizar os dados depois de serem transferidos para armazenamento. Por exemplo:

* Server Explorer in Visual Studio - Se instalou as Ferramentas Azure para o Microsoft Visual Studio, pode utilizar o nó de armazenamento Azure no Server Explorer para ver apenas os dados de bolhas e tabelas de leitura a partir das suas contas de armazenamento Azure. Pode apresentar dados a partir da sua conta de emulador de armazenamento local e também a partir de contas de armazenamento que criou para o Azure. Para obter mais informações, consulte [procurar e gerir recursos de armazenamento com o Explorador do Servidor.](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)
* [O Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento Azure no Windows, OSX e Linux.
* [O Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Azure Diagnostics Manager que lhe permite visualizar, transferir e gerir os dados de diagnóstico recolhidos pelas aplicações em execução no Azure.

## <a name="next-steps"></a>Passos Seguintes
[Trace o fluxo numa aplicação de Cloud Services com Diagnósticos Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


