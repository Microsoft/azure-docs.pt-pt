---
title: Esquema de definição (ficheiro. cscfg) dos serviços Cloud do Azure | Documentos da Microsoft
ms.custom: ''
origin.date: 12/07/2016
ms.date: 11/06/2017
ms.prod: azure
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: 3ddc7fea-3339-4fc0-bdf9-853c32b25f69
caps.latest.revision: 35
author: thraka
ms.author: v-yiso
manager: timlt
ms.openlocfilehash: 424381e2c243420cc2a68dc776d249cb17574f98
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130318"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Azure Cloud Services Config Schema (.cscfg File)
O ficheiro de configuração do serviço Especifica o número de instâncias de função para implementar para cada função no serviço, os valores de quaisquer definições de configuração e os thumbprints de todos os certificados associados uma função. Se o serviço faz parte de uma rede Virtual, as informações de configuração para a rede devem ser fornecidas no ficheiro de configuração do serviço, bem como no arquivo de configuração de rede virtual. A extensão de padrão para o ficheiro de configuração do serviço é o. cscfg.

O modelo de serviço é descrito pela [esquema de definição de serviço em nuvem (clássico)](schema-csdef-file.md).

Por predefinição, o ficheiro de esquema de configuração de diagnósticos do Azure está instalado para o `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` diretório. Substitua `<version>` com a versão instalada do [SDK do Azure](https://azure.microsoft.com/downloads/).

Para obter mais informações sobre como configurar funções num serviço, consulte [qual é o modelo de serviço em nuvem](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Esquema de configuração de serviço básico
Segue-se o formato básico do ficheiro de configuração de serviço.

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
Os tópicos seguintes descrevem o esquema para o `ServiceConfiguration` elemento:

- [Esquema de função](schema-cscfg-role.md)
- [Esquema de NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Espaço de nomes de configuração de serviço
O espaço de nomes XML para o ficheiro de configuração do serviço é: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="ServiceConfiguration"></a> ServiceConfiguration elemento
O `ServiceConfiguration` elemento é o elemento de nível superior do ficheiro de configuração de serviço.

A tabela seguinte descreve os atributos do `ServiceConfiguration` elemento. Todos os valores de atributos são tipos de cadeia de caracteres.

| Atributo | Descrição |
| --------- | ----------- |
|serviceName|Necessário. O nome do serviço cloud. O nome fornecido aqui tem de corresponder ao nome especificado no ficheiro de definição do serviço.|
|osFamily|Opcional. Especifica o sistema operacional convidado que será executado em instâncias de função no serviço cloud. Para obter informações sobre as versões de SO convidado suportados, consulte [versões de SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Se não incluir um `osFamily` valor e não tiver definido a `osVersion` é utilizado o atributo para uma versão específica do SO convidado, um valor predefinido de 1.|
|osVersion|Opcional. Especifica a versão do SO convidado que será executado em instâncias de função no serviço cloud. Para obter mais informações sobre as versões de SO convidado, consulte [versões do SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Pode especificar que o SO convidado devem ser atualizado automaticamente para a versão mais recente. Para tal, defina o valor do `osVersion` atributo para `*`. Quando definido como `*`, as instâncias de função são implementadas com a versão mais recente do SO convidado para a família de SO especificada e serão automaticamente atualizadas quando são lançadas novas versões do SO convidado.<br /><br /> Para especificar manualmente uma versão específica, utilize o `Configuration String` da tabela no **futuro, atual e versões de SO convidado Transitional** seção [versões de SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md) .<br /><br /> O valor predefinido para o `osVersion` atributo é `*`.|
|schemaVersion|Opcional. Especifica a versão do esquema de configuração do serviço. A versão do esquema permite o Visual Studio selecionar as ferramentas SDK corretas para utilizar para a validação de esquema mais de uma versão do SDK é instalada lado a lado. Para obter mais informações sobre a compatibilidade de esquema e a versão, consulte [versões de SO convidado do Azure e matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md)|

O ficheiro de configuração de serviço tem de conter um `ServiceConfiguration` elemento. O `ServiceConfiguration` elemento pode incluir qualquer número de `Role` elementos e zero ou 1 `NetworkConfiguration` elementos.