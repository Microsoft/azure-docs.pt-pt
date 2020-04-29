---
title: Monitor ize um Serviço de Nuvem Azure [ Microsoft Docs
description: Descreve o que a monitorização de um Serviço Azure Cloud envolve e quais são algumas das suas opções.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 01/29/2018
ms.author: tagore
ms.openlocfilehash: 61c794ba03934ae1828ba310f3f776bfb61b652b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79273101"
---
# <a name="introduction-to-cloud-service-monitoring"></a>Introdução à Monitorização do Serviço de Nuvem

Pode monitorizar as métricas de desempenho das teclas para qualquer serviço na nuvem. Cada função de serviço na nuvem recolhe dados mínimos: utilização de CPU, utilização da rede e utilização do disco. Se o serviço `Microsoft.Azure.Diagnostics` de nuvem tiver a extensão aplicada a uma função, essa função pode recolher pontos adicionais de dados. Este artigo fornece uma introdução ao Azure Diagnostics for Cloud Services.

Com monitorização básica, os dados do contador de desempenho das instâncias de funções são recolhidos e recolhidos em intervalos de 3 minutos. Estes dados básicos de monitorização não são armazenados na sua conta de armazenamento e não têm custos adicionais associados a ele.

Com monitorização avançada, métricas adicionais são amostradas e recolhidas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados numa conta de armazenamento, em tabelas, e são purgados após 10 dias. A conta de armazenamento utilizada é configurada por função; pode utilizar diferentes contas de armazenamento para diferentes funções. Isto é configurado com uma cadeia de ligação nos [ficheiros .csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)


## <a name="basic-monitoring"></a>Monitorização básica

Tal como indicado na introdução, um serviço na nuvem recolhe automaticamente dados básicos de monitorização da máquina virtual do hospedeiro. Estes dados incluem percentagem de CPU, rede dentro/fora e leitura/escrita em disco. Os dados de monitorização recolhidos são automaticamente apresentados na visão geral e nas páginas métricas do serviço de nuvem, no portal Azure. 

A monitorização básica não requer uma conta de armazenamento. 

![azulejos básicos de monitorização do serviço de nuvem](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitorização avançada

A monitorização avançada envolve a utilização da extensão de Diagnóstico Sinuoso **(e** opcionalmente o SDK de Insights de Aplicação) sobre a função que pretende monitorizar. A extensão de diagnóstico utiliza um ficheiro config (por função) chamado **diagnostics.wadcfgx** para configurar as métricas de diagnóstico monitorizadas. A extensão Azure Diagnostic recolhe e armazena dados numa conta de Armazenamento Azure. Estas definições estão configuradas nos ficheiros **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)e [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg) Isto significa que existe um custo extra associado à monitorização avançada.

À medida que cada função é criada, o Visual Studio adiciona-lhe a extensão de Diagnóstico Sacana. Esta extensão de diagnóstico pode recolher os seguintes tipos de informação:

* Contadores de desempenho personalizados
* Registos de aplicação
* Registos de eventos do Windows
* .NET fonte de evento
* Registos do IIS
* ETW com base em manifesto
* Informações de falha de sistema
* Registos de erros do cliente

> [!IMPORTANT]
> Embora todos estes dados sejam agregados na conta de armazenamento, o portal **não** fornece uma forma nativa de traçar os dados. É altamente recomendável que integre outro serviço, como o Application Insights, na sua aplicação.

## <a name="setup-diagnostics-extension"></a>Extensão de diagnóstico de configuração

Primeiro, se não tiver uma conta de armazenamento **clássica,** [crie uma](../storage/common/storage-account-create.md). Certifique-se de que a conta de armazenamento é criada com o modelo de **implementação Classic** especificado.

Em seguida, navegue para o recurso **da conta de Armazenamento (clássico).** Selecione **Definições** > **As teclas de acesso** e copie o valor de cadeia de **ligação primária.** Precisa deste valor para o serviço de nuvem. 

Existem dois ficheiros config que deve alterar para que os diagnósticos avançados sejam ativados, **ServiceDefinition.csdef** e **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

No ficheiro **ServiceDefinition.csdef,** adicione uma `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` nova definição nomeada para cada função que utilize diagnósticos avançados. O Visual Studio adiciona este valor ao ficheiro quando cria um novo projeto. Caso falte, pode adicioná-lo agora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Isto define uma nova definição que deve ser adicionada a todos os ficheiros **ServiceConfiguration.cscfg.** 

Provavelmente tem dois **ficheiros .cscfg,** um chamado **ServiceConfiguration.cloud.cscfg** para implantação para Azure, e um chamado **ServiceConfiguration.local.cscfg** que é usado para implementações locais no ambiente emulado. Abra e altere cada ficheiro **.cscfg.** Adicione uma `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`definição chamada . Delineie o valor para a cadeia de **ligação primária** da conta de armazenamento clássica. Se quiser utilizar o armazenamento local na `UseDevelopmentStorage=true`sua máquina de desenvolvimento, utilize .

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

## <a name="use-application-insights"></a>Utilizar insights de aplicação

Ao publicar o Serviço cloud do Estúdio Visual, é-lhe dada a opção de enviar os dados de diagnóstico para A Aplicação Insights. Pode criar o recurso Application Insights Azure nessa altura ou enviar os dados para um recurso Azure existente. O seu serviço na nuvem pode ser monitorizado por Informações de Aplicação para disponibilidade, desempenho, falhas e utilização. Os gráficos personalizados podem ser adicionados aos Insights de Aplicação para que possa ver os dados que mais importam. Os dados da instância de função podem ser recolhidos utilizando o SDK de Insights de Aplicação no seu projeto de serviço na nuvem. Para obter mais informações sobre como integrar os Insights de Aplicação, consulte [os Insights de Aplicação com serviços na nuvem.](../azure-monitor/app/cloudservices.md)

Note que, embora possa utilizar os Insights de Aplicação para exibir os contadores de desempenho (e as outras definições) especificado através da extensão de Diagnóstico do Windows Azure, só obtém uma experiência mais rica integrando o SDK de Insights de Aplicação nos seus funções de trabalhador e web.


## <a name="next-steps"></a>Passos seguintes

- [Saiba mais sobre insights de aplicação com serviços na nuvem](../azure-monitor/app/cloudservices.md)
- [Configurar contadores de desempenho](diagnostics-performance-counters.md)




