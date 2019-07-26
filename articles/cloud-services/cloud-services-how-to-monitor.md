---
title: Monitorar um serviço de nuvem do Azure | Microsoft Docs
description: Descreve o que o monitoramento de um serviço de nuvem do Azure envolve e quais são algumas das suas opções.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: gwallace
ms.openlocfilehash: ac0ea7557774f0e59cb6a6eca1fc739592ab971d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359107"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introdução ao monitoramento do serviço de nuvem

Você pode monitorar as principais métricas de desempenho para qualquer serviço de nuvem. Cada função de serviço de nuvem coleta dados mínimos: Uso de CPU, uso de rede e utilização de disco. Se o serviço de nuvem tiver `Microsoft.Azure.Diagnostics` a extensão aplicada a uma função, essa função poderá coletar pontos de dados adicionais. Este artigo fornece uma introdução ao Diagnóstico do Azure para serviços de nuvem.

Com o monitoramento básico, os dados do contador de desempenho das instâncias de função são amostrados e coletados em intervalos de 3 minutos. Esses dados básicos de monitoramento não são armazenados em sua conta de armazenamento e não têm nenhum custo adicional associado a ele.

Com o monitoramento avançado, métricas adicionais são amostradas e coletadas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados em uma conta de armazenamento, em tabelas e são limpos após 10 dias. A conta de armazenamento usada é configurada por função; Você pode usar contas de armazenamento diferentes para funções diferentes. Isso é configurado com uma cadeia de conexão nos arquivos [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .


## <a name="basic-monitoring"></a>Monitorização básica

Conforme mencionado na introdução, um serviço de nuvem coleta automaticamente dados de monitoramento básicos da máquina virtual do host. Esses dados incluem percentual de CPU, entrada/saída de rede e leitura/gravação de disco. Os dados de monitoramento coletados são exibidos automaticamente nas páginas visão geral e métricas do serviço de nuvem, na portal do Azure. 

O monitoramento básico não requer uma conta de armazenamento. 

![blocos de monitoramento de serviço de nuvem básico](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitorização avançada

O monitoramento avançado envolve o uso da extensão **diagnóstico do Azure** (e, opcionalmente, o SDK do Application insights) na função que você deseja monitorar. A extensão de diagnóstico usa um arquivo de configuração (por função) chamado **Diagnostics. wadcfgx** para configurar as métricas de diagnóstico monitoradas. A extensão de diagnóstico do Azure coleta e armazena dados em uma conta de armazenamento do Azure. Essas configurações são definidas nos arquivos **. wadcfgx**, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)e [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) . Isso significa que há um custo extra associado ao monitoramento avançado.

À medida que cada função é criada, o Visual Studio adiciona a extensão de Diagnóstico do Azure a ela. Essa extensão de diagnóstico pode coletar os seguintes tipos de informações:

* Contadores de desempenho personalizados
* Registos de aplicações
* Registos de eventos do Windows
* Origem do evento .NET
* Registos do IIS
* ETW com base em manifesto
* Informações de falhas de sistema
* Logs de erros do cliente

> [!IMPORTANT]
> Embora todos esses dados sejam agregados na conta de armazenamento, o portal não **fornece uma** maneira nativa de gráfico dos dados. É altamente recomendável que você integre outro serviço, como Application Insights, em seu aplicativo.

## <a name="setup-diagnostics-extension"></a>Extensão de diagnóstico de instalação

Primeiro, se você não tiver uma conta de armazenamento **clássico** , [crie uma](../storage/common/storage-quickstart-create-account.md). Verifique se a conta de armazenamento foi criada com o **modelo de implantação clássico** especificado.

Em seguida, navegue até o recurso **conta de armazenamento (clássico)** . Selecione **configurações** > **chaves de acesso** e copie o valor da **cadeia de conexão primária** . Você precisa desse valor para o serviço de nuvem. 

Há dois arquivos de configuração que você deve alterar para que os diagnósticos avançados sejam habilitados, o **indefinition. csdef** e o inconfiguration **. cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

No arquivo de imdefinição **. csdef** , adicione uma nova configuração chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada função que usa diagnóstico avançado. O Visual Studio adiciona esse valor ao arquivo quando você cria um novo projeto. Caso ele esteja ausente, você pode adicioná-lo agora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Isso define uma nova configuração que deve ser adicionada a cada arquivo **. cscfg** do onconfiguration. 

É muito provável que você tenha dois arquivos **. cscfg** , um chamado de nome de arquivo **. Cloud. cscfg** para implantação no Azure, e um nome de Reconfiguration **. local. cscfg** que é usado para implantações locais no ambiente emulado. Abra e altere cada arquivo **. cscfg** . Adicione uma configuração chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Defina o valor para a **cadeia de conexão primária** da conta de armazenamento clássica. Se você quiser usar o armazenamento local em seu computador de desenvolvimento, use `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Usar Application Insights

Ao publicar o serviço de nuvem do Visual Studio, você terá a opção de enviar os dados de diagnóstico para Application Insights. Você pode criar o Application Insights recurso do Azure nesse momento ou enviar os dados para um recurso existente do Azure. Seu serviço de nuvem pode ser monitorado por Application Insights para disponibilidade, desempenho, falhas e uso. Gráficos personalizados podem ser adicionados a Application Insights para que você possa ver os dados mais importantes. Os dados da instância de função podem ser coletados usando o SDK do Application Insights em seu projeto de serviço de nuvem. Para obter mais informações sobre como integrar Application Insights, consulte [Application insights com serviços de nuvem](../azure-monitor/app/cloudservices.md).

Observe que, embora você possa usar Application Insights para exibir os contadores de desempenho (e as outras configurações) que você especificou por meio da extensão do Windows Diagnóstico do Azure, você obtém uma experiência mais rica integrando o SDK do Application Insights ao seu funções Web e de trabalho.


## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre Application Insights com os serviços de nuvem](../azure-monitor/app/cloudservices.md)
- [Configurar contadores de desempenho](diagnostics-performance-counters.md)

