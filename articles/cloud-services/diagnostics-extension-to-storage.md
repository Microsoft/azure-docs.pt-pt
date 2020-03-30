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
ms.openlocfilehash: 17430330d068fb55b45f073afecb8ba348286cb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472677"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e ver dados de diagnósticos no Armazenamento do Azure
Os dados de diagnóstico não são armazenados permanentemente a menos que os transfira para o emulador de armazenamento do Microsoft Azure ou para o armazenamento Do Azure. Uma vez armazenado, pode ser visto com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especificar uma conta de armazenamento
Especifica a conta de armazenamento que pretende utilizar no ficheiro ServiceConfiguration.cscfg. A informação da conta é definida como uma cadeia de ligação numa configuração. O exemplo que se segue mostra a cadeia de ligação padrão criada para um novo projeto de Cloud Service no Estúdio Visual:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Pode alterar esta cadeia de ligação para fornecer informações sobre a conta para uma conta de armazenamento Azure.

Dependendo do tipo de dados de diagnóstico que está a ser recolhido, o Azure Diagnostics utiliza o serviço Blob ou o serviço Mesa. O quadro seguinte mostra as fontes de dados que são persistidas e o seu formato.

| Origem de dados | Formato de armazenamento |
| --- | --- |
| Troncos azure |Tabela |
| IIS 7.0 troncos |Blobs |
| Registos da infraestrutura do Diagnóstico do Azure |Tabela |
| Registos de rastreio supérão de pedido falhado |Blobs |
| Registos do Evento Windows |Tabela |
| Contadores de desempenho |Tabela |
| Informações de falha de sistema |Blobs |
| Registos de erros personalizados |Blobs |

## <a name="transfer-diagnostic-data"></a>Transferir dados de diagnóstico
Para SDK 2.5 e posteriormente, o pedido de transferência de dados de diagnóstico pode ocorrer através do ficheiro de configuração. Pode transferir dados de diagnóstico em intervalos programados, conforme especificado na configuração.

Para SDK 2.4 e anterior, pode solicitar a transferência dos dados de diagnóstico através do ficheiro de configuração, bem como programática. A abordagem programática também permite fazer transferências a pedido.

> [!IMPORTANT]
> Ao transferir dados de diagnóstico para uma conta de armazenamento Azure, incorre em custos para os recursos de armazenamento que os seus dados de diagnóstico utilizam.
> 
> 

## <a name="store-diagnostic-data"></a>Armazenar dados de diagnóstico
Os dados de registo são armazenados no armazenamento de Blob ou Mesa com os seguintes nomes:

**Tabelas**

* **WadLogsTable** - Registos escritos em código utilizando o ouvinte de rastreio.
* **WADDiagnosticInfrastructureLogsTable** - Alterações do monitor de diagnóstico e configuração.
* **WADDirectoriesTable** – Diretórios que o monitor de diagnóstico está a monitorizar.  Isto inclui registos IIS, registos de pedidos falhados do IIS e diretórios personalizados.  A localização do ficheiro de registo blob é especificada no campo do contentor e o nome da bolha está no campo RelativePath.  O campo AbsolutePath indica a localização e o nome do ficheiro tal como existia na máquina virtual Azure.
* **WADPerformanceCountersTable** – Contadores de desempenho.
* **WADWindowsEventLogsTable** – Registos do Evento Windows.

**Blobs**

* **Wad-control-container** – (Apenas para SDK 2.4 e anterior) contém os ficheiros de configuração XML que controlam os diagnósticos Do Azure .
* **ficheiros wad-iis-failedreqlogfiles** – Contém informações de registos de Pedidos Falhados iIS.
* **ficheiros de registo sinuoso** - Contém informações sobre registos IIS.
* **"personalizado"** – Um recipiente personalizado baseado na configuração de diretórios que são monitorizados pelo monitor de diagnóstico.  O nome deste recipiente blob será especificado no WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para visualizar dados de diagnóstico
Várias ferramentas estão disponíveis para visualizar os dados depois de transferidos para o armazenamento. Por exemplo:

* Server Explorer em Estúdio Visual - Se tiver instalado as Ferramentas Azure para o Microsoft Visual Studio, pode utilizar o nó de Armazenamento Azure no Server Explorer para visualizar os dados de blob e tabela apenas de leitura das suas contas de armazenamento Azure. Pode apresentar dados da sua conta de emulador de armazenamento local e também das contas de armazenamento que criou para o Azure. Para mais informações, consulte [Navegação e Gestão de Recursos](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)de Armazenamento com o Server Explorer .
* O [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados do Armazenamento Azure no Windows, OSX e Linux.
* O [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o Azure Diagnostics Manager que lhe permite visualizar, descarregar e gerir os dados de diagnóstico recolhidos pelas aplicações em execução no Azure.

## <a name="next-steps"></a>Passos Seguintes
[Trace o fluxo em uma aplicação de Serviços cloud com Diagnósticos Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


