---
title: Armazenar e Ver Dados de Diagnósticos no Armazenamento do Azure
description: Saiba como coletar dados de diagnóstico do Azure em uma conta de armazenamento do Azure para que você possa exibi-los com uma das várias ferramentas disponíveis.
services: azure-monitor
author: jpconnock
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: jeconnoc
ms.subservice: diagnostic-extension
ms.openlocfilehash: 35e852a36ebc52edff338ed640419afe32297b81
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304966"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Armazenar e ver dados de diagnósticos no Armazenamento do Azure
Os dados de diagnóstico não são armazenados permanentemente, a menos que você o transfira para o emulador de armazenamento Microsoft Azure ou para o armazenamento do Azure. Uma vez no armazenamento, ele pode ser exibido com uma das várias ferramentas disponíveis.

## <a name="specify-a-storage-account"></a>Especificar uma conta de armazenamento
Especifique a conta de armazenamento que você deseja usar no arquivo. cscfg de configuração. As informações da conta são definidas como uma cadeia de conexão em uma definição de configuração. O exemplo a seguir mostra a cadeia de conexão padrão criada para um novo projeto de serviço de nuvem no Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Você pode alterar essa cadeia de conexão para fornecer informações de conta para uma conta de armazenamento do Azure.

Dependendo do tipo de dados de diagnóstico que está sendo coletado, Diagnóstico do Azure usa o serviço BLOB ou o serviço tabela. A tabela a seguir mostra as fontes de dados que são persistidas e seu formato.

| Origem de dados | Formato de armazenamento |
| --- | --- |
| Logs do Azure |Tabela |
| Logs do IIS 7,0 |Blobs |
| Registos da infraestrutura do Diagnóstico do Azure |Tabela |
| Logs de rastreamento de solicitação com falha |Blobs |
| Registos de Eventos do Windows |Tabela |
| Contadores de desempenho |Tabela |
| Informações de falha de sistema |Blobs |
| Registos de erros personalizados |Blobs |

## <a name="transfer-diagnostic-data"></a>Transferir dados de diagnóstico
Para o SDK 2,5 e posterior, a solicitação para transferir dados de diagnóstico pode ocorrer por meio do arquivo de configuração. Você pode transferir dados de diagnóstico em intervalos agendados, conforme especificado na configuração.

Para o SDK 2,4 e anterior, você pode solicitar para transferir os dados de diagnóstico por meio do arquivo de configuração, bem como programaticamente. A abordagem programática também permite que você faça transferências sob demanda.

> [!IMPORTANT]
> Ao transferir dados de diagnóstico para uma conta de armazenamento do Azure, você incorre em custos para os recursos de armazenamento que seus dados de diagnóstico usam.
> 
> 

## <a name="store-diagnostic-data"></a>Armazenar dados de diagnóstico
Os dados de log são armazenados em um armazenamento de BLOB ou de tabela com os seguintes nomes:

**Tabelas**

* **WadLogsTable** -logs gravados no código usando o ouvinte de rastreamento.
* **WADDiagnosticInfrastructureLogsTable** -monitor de diagnóstico e alterações de configuração.
* **WADDirectoriesTable** – diretórios que o monitor de diagnóstico está monitorando.  Isso inclui logs do IIS, logs de solicitação com falha do IIS e diretórios personalizados.  O local do arquivo de log de blob é especificado no campo contêiner e o nome do blob está no campo RelativePath.  O campo AbsolutePath indica o local e o nome do arquivo como ele existia na máquina virtual do Azure.
* **WADPerformanceCountersTable** – contadores de desempenho.
* **WADWindowsEventLogsTable** – logs de eventos do Windows.

**Blobs**

* **wad-Control-container** – (somente para o SDK 2,4 e anterior) contém os arquivos de configuração XML que controlam o diagnóstico do Azure.
* **wad-iis-failedreqlogfiles** – contém informações de logs de solicitação com falha do IIS.
* **wad-IIS-LogFiles** – contém informações sobre logs do IIS.
* **"personalizado"** – um contêiner personalizado com base na configuração de diretórios que são monitorados pelo monitor de diagnóstico.  O nome desse contêiner de blob será especificado em WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Ferramentas para exibir dados de diagnóstico
Várias ferramentas estão disponíveis para exibir os dados depois que eles são transferidos para o armazenamento. Por exemplo:

* Gerenciador de Servidores no Visual Studio-se você tiver instalado as ferramentas do Azure para Microsoft Visual Studio, poderá usar o nó de armazenamento do Azure no Gerenciador de Servidores para exibir dados de tabela e de blob somente leitura de suas contas de armazenamento do Azure. Você pode exibir dados de sua conta do emulador de armazenamento local e também de contas de armazenamento que você criou para o Azure. Para obter mais informações, consulte [navegando e Gerenciando recursos de armazenamento com Gerenciador de servidores](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Gerenciador de armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md) é um aplicativo autônomo que permite que você trabalhe facilmente com os dados do armazenamento do Azure no Windows, no OSX e no Linux.
* O [Management Studio do Azure](https://www.cerebrata.com/products/azure-management-studio/introduction) inclui o diagnóstico do Azure Manager, que permite exibir, baixar e gerenciar os dados de diagnóstico coletados pelos aplicativos em execução no Azure.

## <a name="next-steps"></a>Passos Seguintes
[Rastrear o fluxo em um aplicativo de serviços de nuvem com Diagnóstico do Azure](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


