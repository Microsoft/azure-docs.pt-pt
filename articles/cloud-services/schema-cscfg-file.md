---
title: Esquema de definição de serviços de nuvem do Azure (arquivo. cscfg) | Microsoft Docs
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 0009f843f8de31b92817dc86ccd718fa5eeeb1ba
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358920"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Esquema de configuração dos serviços de nuvem do Azure (arquivo. cscfg)
O arquivo de configuração de serviço especifica o número de instâncias de função a serem implantadas para cada função no serviço, os valores de quaisquer parâmetros de configuração e as impressões digitais para quaisquer certificados associados a uma função. Se o serviço fizer parte de uma rede virtual, as informações de configuração para a rede deverão ser fornecidas no arquivo de configuração de serviço, bem como no arquivo de configuração de rede virtual. A extensão padrão para o arquivo de configuração de serviço é. cscfg.

O modelo de serviço é descrito pelo [esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md).

Por padrão, o arquivo de esquema de configuração diagnóstico do Azure é instalado `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` no diretório. Substitua `<version>` pela versão instalada do SDK do [Azure](https://azure.microsoft.com/downloads/).

Para obter mais informações sobre como configurar funções em um serviço, consulte [o que é o modelo de serviço de nuvem](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Esquema de configuração de serviço básico
O formato básico do arquivo de configuração de serviço é o seguinte.

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
Os tópicos a seguir descrevem o esquema para `ServiceConfiguration` o elemento:

- [Esquema de função](schema-cscfg-role.md)
- [Esquema de NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Namespace de configuração de serviço
O namespace XML para o arquivo de configuração de serviço `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`é:.

##  <a name="ServiceConfiguration"></a>Elemento de configuração
O `ServiceConfiguration` elemento é o elemento de nível superior do arquivo de configuração de serviço.

A tabela a seguir descreve os atributos do `ServiceConfiguration` elemento. Todos os valores de atributos são tipos de cadeia de caracteres.

| Atributo | Descrição |
| --------- | ----------- |
|serviceName|Necessário. O nome do serviço de nuvem. O nome fornecido aqui deve corresponder ao nome especificado no arquivo de definição de serviço.|
|osFamily|Opcional. Especifica o sistema operacional convidado que será executado em instâncias de função no serviço de nuvem. Para obter informações sobre as versões de SO convidado com suporte, consulte [versões do SO convidado do Azure e matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Se você não incluir um `osFamily` valor e não tiver definido o `osVersion` atributo para uma versão específica do sistema operacional convidado, será usado um valor padrão de 1.|
|osVersion|Opcional. Especifica a versão do sistema operacional convidado que será executado em instâncias de função no serviço de nuvem. Para obter mais informações sobre versões do sistema operacional convidado, consulte [versões do SO convidado do Azure e matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Você pode especificar que o sistema operacional convidado deve ser atualizado automaticamente para a versão mais recente. Para fazer isso, defina o valor do `osVersion` atributo como. `*` Quando definido como `*`, as instâncias de função são implantadas usando a versão mais recente do sistema operacional convidado para a família de sistemas operacionais especificada e serão atualizadas automaticamente quando novas versões do sistema operacional convidado forem liberadas.<br /><br /> Para especificar uma versão específica manualmente, use a `Configuration String` da tabela na seção **versões futuras, atuais e transacionais do sistema operacional convidado** de versões do [SO convidado do Azure e matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md).<br /><br /> O valor padrão para o `osVersion` atributo é `*`.|
|schemaVersion|Opcional. Especifica a versão do esquema de configuração de serviço. A versão do esquema permite que o Visual Studio selecione as ferramentas do SDK corretas a serem usadas para validação de esquema se mais de uma versão do SDK estiver instalada lado a lado. Para obter mais informações sobre compatibilidade de esquema e versão, consulte [versões do SO convidado do Azure e matriz de compatibilidade do SDK](cloud-services-guestos-update-matrix.md)|

O arquivo de configuração de serviço deve `ServiceConfiguration` conter um elemento. O `ServiceConfiguration` elemento pode incluir qualquer número de `Role` elementos e zero ou 1 `NetworkConfiguration` elementos.