---
title: Monitorize um serviço de nuvem Azure (clássico) | Microsoft Docs
description: Descreve o que envolve a monitorização de um Serviço de Nuvem Azure e quais são algumas das suas opções.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743377"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Introdução ao Serviço de Nuvem (clássico) Monitoring

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Pode monitorizar as principais métricas de desempenho para qualquer serviço na nuvem. Cada função de serviço na nuvem recolhe dados mínimos: utilização do CPU, utilização da rede e utilização do disco. Se o serviço de nuvem tiver a `Microsoft.Azure.Diagnostics` extensão aplicada a uma função, essa função pode recolher pontos adicionais de dados. Este artigo fornece uma introdução ao Azure Diagnostics for Cloud Services.

Com a monitorização básica, os dados de contador de desempenho de instâncias de função são amostrados e recolhidos em intervalos de 3 minutos. Estes dados básicos de monitorização não estão armazenados na sua conta de armazenamento e não têm qualquer custo adicional associado aos mesmos.

Com uma monitorização avançada, as métricas adicionais são amostradas e recolhidas em intervalos de 5 minutos, 1 hora e 12 horas. Os dados agregados são armazenados numa conta de armazenamento, em tabelas, e são purgados após 10 dias. A conta de armazenamento utilizada é configurada por função; pode utilizar diferentes contas de armazenamento para diferentes funções. Isto é configurado com uma cadeia de ligação nos [ficheiros .csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)


## <a name="basic-monitoring"></a>Monitorização básica

Conforme indicado na introdução, um serviço de nuvem recolhe automaticamente dados básicos de monitorização da máquina virtual hospedeira. Estes dados incluem percentagem de CPU, rede dentro/fora e leitura/escrita de disco. Os dados de monitorização recolhidos são automaticamente apresentados nas páginas de visão geral e métricas do serviço em nuvem, no portal Azure. 

A monitorização básica não requer uma conta de armazenamento. 

![azulejos básicos de monitorização do serviço de nuvem](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitorização avançada

A monitorização avançada envolve a utilização da extensão **Azure Diagnostics** (e opcionalmente a Aplicação Insights SDK) sobre a função que pretende monitorizar. A extensão de diagnóstico utiliza um ficheiro config (por função) chamado **diagnóstico.wadcfgx** para configurar as métricas de diagnóstico monitorizadas. A extensão Azure Diagnostic recolhe e armazena dados numa conta de Armazenamento Azure. Estas definições estão configuradas nos ficheiros **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)e [.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg) Isto significa que há um custo extra associado à monitorização avançada.

À medida que cada papel é criado, o Visual Studio adiciona-lhe a extensão Azure Diagnostics. Esta extensão de diagnóstico pode recolher os seguintes tipos de informação:

* Contadores de desempenho personalizados
* Registos de aplicações
* Registos de eventos do Windows
* .NET fonte de evento
* Registos do IIS
* ETW com base em manifesto
* Informações de falha de sistema
* Registos de erro do cliente

> [!IMPORTANT]
> Embora todos estes dados são agregados na conta de armazenamento, o portal **não** fornece uma forma nativa de traçar os dados. É altamente recomendável que integre outro serviço, como o Application Insights, na sua aplicação.

## <a name="setup-diagnostics-extension"></a>Extensão de diagnóstico de configuração

Primeiro, se não tiver uma conta de armazenamento **clássica,** [crie uma.](../storage/common/storage-account-create.md) Certifique-se de que a conta de armazenamento é criada com o **modelo de implementação Clássico** especificado.

Em seguida, navegue para o recurso **de conta de Armazenamento (clássico).** Selecione **Definições**  >  **As teclas de acesso** e copie o valor da cadeia de **ligação primária.** Precisa deste valor para o serviço na nuvem. 

Existem dois ficheiros config que tem de alterar para que os diagnósticos avançados sejam ativados, **ServiceDefinition.csdef** e **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiçoDefinition.csdef

No ficheiro **ServiceDefinition.csdef,** adicione uma nova definição nomeada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` para cada função que utilize diagnósticos avançados. O Visual Studio adiciona este valor ao ficheiro quando cria um novo projeto. No caso de faltar, pode adicioná-lo agora. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Isto define uma nova definição que deve ser adicionada a todos os **ficheiros ServiceConfiguration.cscfg.** 

O mais provável é que tenha dois **ficheiros .cscfg,** um chamado **ServiceConfiguration.cloud.cscfg** para implantação em Azure, e um chamado **ServiceConfiguration.local.cscfg** que é usado para implementações locais no ambiente emulado. Abra e altere cada ficheiro **.cscfg.** Adicione uma definição chamada `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Desa cosma o valor da **cadeia de ligação primária** da conta de armazenamento clássica. Se pretender utilizar o armazenamento local na sua máquina de desenvolvimento, `UseDevelopmentStorage=true` utilize.

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

## <a name="use-application-insights"></a>Utilizar insights de aplicações

Ao publicar o Serviço cloud do Visual Studio, é-lhe dada a opção de enviar os dados de diagnóstico para o Application Insights. Pode criar o recurso Application Insights Azure nessa altura ou enviar os dados para um recurso Azure existente. O seu serviço na nuvem pode ser monitorizado pela Application Insights para disponibilidade, desempenho, falhas e utilização. Os gráficos personalizados podem ser adicionados ao Application Insights para que possa ver os dados que mais importam. Os dados da instância de função podem ser recolhidos utilizando o SDK Application Insights no seu projeto de serviço na nuvem. Para obter mais informações sobre como integrar insights de aplicações, consulte [Insights de Aplicação com Serviços cloud](../azure-monitor/app/cloudservices.md).

Note que, embora possa utilizar o Application Insights para exibir os contadores de desempenho (e as outras definições) que especificou através da extensão de Diagnóstico do Windows Azure, só obtém uma experiência mais rica integrando o SDK de Insights de Aplicação nas suas funções de trabalhador e web.


## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre insights de aplicações com serviços cloud](../azure-monitor/app/cloudservices.md)
- [Configurar contadores de desempenho](diagnostics-performance-counters.md)




