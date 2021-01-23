---
title: Azure Cloud Services (clássico) Definição Schema (.cscfg File) | Microsoft Docs
description: Um ficheiro de configuração de serviço (.cscfg) especifica quantas instâncias de função devem ser implantadas para cada função, valores de configuração e impressões digitais de certificado para uma função.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba933d4981fb7ed209a7fb5d5c41113750f312de
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743479"
---
# <a name="azure-cloud-services-classic-config-schema-cscfg-file"></a>Azure Cloud Services (clássico) Config Schema (.cscfg File)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

O ficheiro de configuração de serviço especifica o número de instâncias de função a implementar para cada função no serviço, os valores de quaisquer definições de configuração e as impressões digitais para quaisquer certificados associados a uma função. Se o serviço fizer parte de uma Rede Virtual, as informações de configuração da rede devem ser fornecidas no ficheiro de configuração de serviço, bem como no ficheiro de configuração de rede virtual. A extensão padrão para o ficheiro de configuração de serviço é .cscfg.

O modelo de serviço é descrito pelo Esquema de [Definição do Serviço de Nuvem (clássico).](schema-csdef-file.md)

Por predefinição, o ficheiro de configuração Azure Diagnostics é instalado no `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` diretório. `<version>`Substitua-a pela versão instalada do [Azure SDK](https://azure.microsoft.com/downloads/).

Para obter mais informações sobre a configuração de funções num serviço, consulte [o modelo 'O que é o Serviço de Cloud'.](cloud-services-model-and-package.md)

## <a name="basic-service-configuration-schema"></a>Esquema de configuração de serviço básico
O formato básico do ficheiro de configuração de serviço é o seguinte.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Definições de esquema
Os seguintes tópicos descrevem o esquema para o `ServiceConfiguration` elemento:

- [Esquema de função](schema-cscfg-role.md)
- [Esquema de NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Espaço de nome de configuração de serviço
O espaço de nome XML para o ficheiro de configuração de serviço é: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration` .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> Elemento deConfiguação de Serviços
O `ServiceConfiguration` elemento é o elemento de nível superior do ficheiro de configuração de serviço.

A tabela seguinte descreve os atributos do `ServiceConfiguration` elemento. Todos os valores de atributos são tipos de cordas.

| Atributo | Descrição |
| --------- | ----------- |
|nome de serviço|Obrigatório. O nome do serviço na nuvem. O nome aqui indicado deve corresponder ao nome especificado no ficheiro de definição de serviço.|
|osfímil|Opcional. Especifica o SO convidado que irá executar em instâncias de função no serviço de nuvem. Para obter informações sobre lançamentos de Guest OS suportados, consulte [os lançamentos do Azure Guest OS e a Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Se não incluir um `osFamily` valor e não tiver definido o atributo para uma versão específica do Guest `osVersion` OS, é utilizado um valor predefinido de 1.|
|osVersion|Opcional. Especifica a versão do So convidado que será executada em instâncias de função no serviço de nuvem. Para obter mais informações sobre as versões do Guest OS, consulte [os lançamentos do Azure Guest OS e a Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Pode especificar que o Sistema de Visitas deve ser automaticamente atualizado para a versão mais recente. Para isso, desa um valor do `osVersion` atributo para `*` . Quando definidos para `*` , as instâncias de função são implementadas usando a versão mais recente do So Convidado para a família OS especificada e serão automaticamente atualizadas quando forem lançadas novas versões do So convidado.<br /><br /> Para especificar uma versão específica manualmente, utilize `Configuration String` a secção de **versões os do hóspede** do futuro, corrente e transitório dos lançamentos do [Azure Guest OS e da Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).<br /><br /> O valor predefinido para o `osVersion` atributo é `*` .|
|schemaVersão|Opcional. Especifica a versão do esquema de configuração de serviço. A versão de esquema permite ao Visual Studio selecionar as ferramentas SDK corretas para usar para validação de esquemas se mais de uma versão do SDK for instalada lado a lado. Para obter mais informações sobre esquema e compatibilidade de versão, consulte [os lançamentos do Azure Guest OS e a Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md)|

O ficheiro de configuração de serviço deve conter um `ServiceConfiguration` elemento. O `ServiceConfiguration` elemento pode incluir qualquer número de `Role` elementos e zero ou 1 `NetworkConfiguration` elementos.