---
title: Monitorizar um serviço Cloud do Azure | Documentos da Microsoft
description: Descreve o que a monitorização de um serviço Cloud do Azure envolve e que algumas opções são.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: jeconnoc
ms.openlocfilehash: 844fef9a87c1db06c6415c59d4be26caf928382b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61432916"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introdução à monitorização do serviço Cloud

Pode monitorizar métricas de chave de desempenho para qualquer serviço cloud. Cada função do serviço cloud recolhe mínima de dados: Utilização da CPU, a utilização de rede e a utilização do disco. Se o serviço de nuvem tem o `Microsoft.Azure.Diagnostics` extensão aplicada a uma função, essa função pode recolher pontos adicionais de dados. Este artigo fornece uma introdução ao diagnóstico do Azure para serviços em nuvem.

Com a monitorização básica, os dados de contador de desempenho das instâncias de função é amostragem e coletados em intervalos de 3 minutos. Estes dados de monitorização básicos não são armazenados na conta de armazenamento e tem sem custos adicionais associados com o mesmo.

Com a monitorização avançada, métricas adicionais são objeto de amostragem e coletadas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados numa conta de armazenamento, nas tabelas e são removidos depois de 10 dias. A conta de armazenamento utilizada é configurada por função; Pode utilizar contas de armazenamento diferentes para diferentes funções. Este é configurado com uma cadeia de ligação no [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ficheiros.


## <a name="basic-monitoring"></a>Monitorização básica

Conforme mencionado na introdução, um serviço em nuvem recolhe automaticamente dados de monitorização básicos a partir da máquina virtual do anfitrião. Estes dados incluem a percentagem de CPU, rede de entrada/saída e de leitura/escrita de disco. Os dados de monitorização recolhidos automaticamente são apresentados nas páginas de descrição geral e métricas de serviço em nuvem, no portal do Azure. 

Monitorização básica não requer uma conta de armazenamento. 

![mosaicos de monitorização do serviço de nuvem básico](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitorização avançada

Monitorização avançada envolve a utilização a **diagnóstico do Azure** extensão (e, opcionalmente, o SDK do Application Insights) na função de que pretende monitorizar. A extensão de diagnóstico utiliza um ficheiro de configuração (por função) com o nome **diagnostics.wadcfgx** para configurar as métricas de diagnóstico monitorizadas. A extensão de diagnóstico do Azure recolhe e armazena dados numa conta de armazenamento do Azure. Estas definições são configuradas a **.wadcfgx**, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef), e [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ficheiros. Isso significa que não há adicionais custo associado a monitorização avançada.

À medida que cada função é criada, o Visual Studio adiciona a extensão de diagnóstico do Azure ao mesmo. Esta extensão de diagnóstico pode recolher os seguintes tipos de informações:

* Contadores de desempenho personalizados
* Registos de aplicações
* Registos de eventos do Windows
* Origem de evento do .NET
* Registos do IIS
* ETW com base em manifesto
* Informações de falha de sistema
* Registos de erros do cliente

> [!IMPORTANT]
> Embora todos estes dados são agregados na conta de armazenamento, o portal faz **não** proporcionam uma forma de nativa para os dados do gráfico. É altamente recomendável que integrar o outro serviço, como o Application Insights, na sua aplicação.

## <a name="setup-diagnostics-extension"></a>Extensão de diagnóstico de configuração

Primeiro, se não tiver uma **clássica** conta de armazenamento [criar um](../storage/common/storage-quickstart-create-account.md). Certifique-se a conta de armazenamento é criada com o **modelo de implementação clássica** especificado.

Em seguida, navegue para o **conta de armazenamento (clássico)** recursos. Selecione **configurações** > **chaves de acesso** e copie o **cadeia de ligação primária** valor. Este valor é necessário para o serviço cloud. 

Existem dois ficheiros de configuração tem de alterar para diagnóstico avançado esteja ativada **servicedefinition. Csdef** e **serviceconfiguration. Cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Na **servicedefinition. Csdef** do ficheiro, adicione uma nova definição denominada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada função que utiliza o diagnóstico avançado. O Visual Studio adiciona este valor para o ficheiro ao criar um novo projeto. No caso de ele está em falta, pode adicioná-lo agora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Isso define uma nova definição que tem de ser adicionada a cada **serviceconfiguration. Cscfg** ficheiro. 

Provavelmente terá dois **. cscfg** ficheiros, um denominado **Serviceconfiguration** para a implementação para o Azure e um com o nome **ServiceConfiguration.local.cscfg** que é utilizado para implementações locais no ambiente de emulados. Abra e altere cada **. cscfg** ficheiro. Adicione uma definição denominada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Defina o valor para o **cadeia de ligação primária** da conta de armazenamento clássico. Se pretender utilizar o armazenamento local no computador de desenvolvimento, utilize `UseDevelopmentStorage=true`.

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

## <a name="use-application-insights"></a>Utilizar o Application Insights

Quando publica o serviço em nuvem do Visual Studio, é-lhe dada a opção para enviar os dados de diagnóstico para o Application Insights. Pode criar o recurso do Application Insights Azure nessa altura ou enviar os dados para um recurso do Azure existente. Seu serviço cloud pode ser monitorizado pelo Application Insights para disponibilidade, desempenho, falhas e utilização. Gráficos personalizados podem ser adicionados ao Application Insights para que pode ver os dados que é o mais importante. Podem ser recolhidos dados de instâncias de função com o SDK do Application Insights no seu projeto de serviço em nuvem. Para obter mais informações sobre como integrar o Application Insights, veja [Application Insights com os serviços Cloud](../azure-monitor/app/cloudservices.md).

Observe que embora possa usar o Application Insights para apresentar os contadores de desempenho (e as outras definições) que especificou através da extensão do Windows Azure Diagnostics, apenas obter uma experiência mais rica ao integrar o SDK do Application Insights no seu funções de trabalho e web.


## <a name="next-steps"></a>Passos Seguintes

- [Saiba mais sobre o Application Insights com os serviços Cloud](../azure-monitor/app/cloudservices.md)
- [Configurar contadores de desempenho](diagnostics-performance-counters.md)

