---
title: Armazenar e Ver Dados de Diagnósticos no Armazenamento do Azure
description: Obter dados de diagnóstico do Azure no armazenamento do Azure e veja-os
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.subservice: diagnostic-extension
ms.openlocfilehash: 23379e9d9bb29efb7fb026260e8245e8eb8a2d71
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60395072"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e ver dados de diagnósticos no Armazenamento do Azure
Dados de diagnóstico não são armazenados permanentemente, a menos que transferi-la no emulador de armazenamento do Microsoft Azure ou para o armazenamento do Azure. Uma vez no armazenamento, podem ser visualizado com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especifique uma conta de armazenamento
Especifique a conta de armazenamento que pretende utilizar no arquivo serviceconfiguration. Cscfg. As informações da conta são definidas como uma cadeia de ligação numa definição de configuração. O exemplo seguinte mostra a cadeia de ligação predefinida criada para um novo projeto de serviço em nuvem no Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Pode alterar esta cadeia de ligação para fornecer informações de conta para uma conta de armazenamento do Azure.

Dependendo do tipo de dados de diagnóstico que estão a ser recolhidos, o diagnóstico do Azure utiliza o serviço de BLOBs ou o serviço de tabela. A tabela seguinte mostra as origens de dados que são mantidas e seu formato.

| Origem de dados | Formato de armazenamento |
| --- | --- |
| Registos do Azure |Tabela |
| Logs do IIS 7.0 |Blobs |
| Registos da infraestrutura do Diagnóstico do Azure |Tabela |
| Falha de registos de rastreio de pedido |Blobs |
| Registos de Eventos do Windows |Tabela |
| Contadores de desempenho |Tabela |
| Informações de falha de sistema |Blobs |
| Registos de erros personalizados |Blobs |

## <a name="transfer-diagnostic-data"></a>Transferência de dados de diagnóstico
Para o SDK 2.5 e posterior, o pedido de transferência de dados de diagnóstico pode ocorrer por meio do arquivo de configuração. Pode transferir dados de diagnóstico em intervalos agendados, conforme especificado na configuração.

Para o SDK 2.4 e anterior pode pedir para transferir os dados de diagnóstico através do arquivo de configuração, bem como por meio de programação. A abordagem programática também permite-lhe fazer transferências sob demanda.

> [!IMPORTANT]
> Quando transferir dados de diagnóstico para uma conta de armazenamento do Azure, pode implicar custos para os recursos de armazenamento que utiliza os dados de diagnóstico.
> 
> 

## <a name="store-diagnostic-data"></a>Dados de diagnóstico de Store
Dados de registo são armazenados no armazenamento de BLOBs ou tabelas com os seguintes nomes:

**Tabelas**

* **WadLogsTable** - registos escritos em código usando o serviço de escuta de rastreio.
* **WADDiagnosticInfrastructureLogsTable** -alterações de configuração e o monitor de diagnóstico.
* **WADDirectoriesTable** – diretórios que o monitor de diagnóstico está a monitorizar.  Isto inclui registos do IIS, o IIS falhou registos de pedidos e diretórios personalizados.  A localização do blob do ficheiro de registo é especificada no campo de contentor e o nome do blob está no campo RelativePath.  O campo de AbsolutePath indica a localização e o nome do ficheiro, como ele existia na máquina virtual do Azure.
* **WADPerformanceCountersTable** – contadores de desempenho.
* **WADWindowsEventLogsTable** – os logs de eventos do Windows.

**Blobs**

* **contêiner de controle de WAD** – (apenas SDK 2.4 e anterior) contém os ficheiros de configuração XML que controla o diagnóstico do Azure.
* **WAD-iis-failedreqlogfiles** – contém informações dos registos de falha de pedidos do IIS.
* **WAD-iis-logfiles** – contém informações sobre os registos de IIS.
* **"personalizado"** – um contentor personalizado com base na configuração de diretórios que são monitorizados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado no WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para ver dados de diagnóstico
Várias ferramentas estão disponíveis para ver os dados depois de serem transferido para o armazenamento. Por exemplo:

* Explorador de servidores no Visual Studio – se tiver instalado as ferramentas do Azure para Microsoft Visual Studio, pode utilizar o nó de armazenamento do Azure no Explorador de servidores para ver dados de tabela de blob de só de leitura e das suas contas de armazenamento do Azure. Pode exibir os dados da sua conta do emulador de armazenamento local e também das contas de armazenamento que criou para o Azure. Para obter mais informações, consulte [navegação e gerenciamento de recursos de armazenamento com o Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Explorador de armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é uma aplicação autónoma que lhe permite trabalhar facilmente com dados de armazenamento do Azure no Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui Gestor de diagnóstico do Azure, que permite-lhe ver, transferir e gerir os dados de diagnóstico recolhidos pelas aplicações em execução no Azure.

## <a name="next-steps"></a>Próximos Passos
[Rastrear o fluxo num aplicativo de serviços em nuvem com o diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


