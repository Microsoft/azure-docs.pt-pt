---
title: Definição de Serviços azure Cloud Schema (.cscfg File) [ Arquivo Azure Cloud Services) [ Microsoft Docs
description: Um ficheiro de configuração de serviço (.cscfg) especifica quantas instâncias de função a implementar para cada função, valores de configuração e impressões digitais de certificado para uma função.
services: cloud-services
ms.custom: ''
ms.date: 12/07/2016
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 35
author: tgore03
ms.author: tagore
ms.openlocfilehash: cb77181e00c97b7f426429793f17af3cb5e84ebe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534750"
---
# <a name="azure-cloud-services-config-schema-cscfg-file"></a>Serviços Azure Cloud Config Schema (.cscfg File)
O ficheiro de configuração do serviço especifica o número de instâncias de função a implementar para cada função no serviço, os valores de quaisquer configurações de configuração e as impressões digitais para quaisquer certificados associados a uma função. Se o serviço fizer parte de uma Rede Virtual, as informações de configuração da rede devem ser fornecidas no ficheiro de configuração do serviço, bem como no ficheiro de configuração de rede virtual. A extensão predefinida para o ficheiro de configuração do serviço é .cscfg.

O modelo de serviço é descrito pelo [Cloud Service (clássico) Definition Schema](schema-csdef-file.md).

Por predefinição, o ficheiro esquema de configuração `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas` do Azure Diagnostics está instalado no diretório. Substitua-a `<version>` pela versão instalada do [Azure SDK](https://azure.microsoft.com/downloads/).

Para obter mais informações sobre a configuração de funções num serviço, consulte [o modelo Cloud Service](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Esquema de Configuração de Serviço Básico
O formato básico do ficheiro de configuração do serviço é o seguinte.

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
Os seguintes tópicos descrevem o `ServiceConfiguration` esquema para o elemento:

- [Esquema de função](schema-cscfg-role.md)
- [Esquema de NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Espaço de nome de configuração de serviço
O espaço de nome XML para `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`o ficheiro de configuração do serviço é: .

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a>Elemento de configuração de serviço
O `ServiceConfiguration` elemento é o elemento de nível superior do ficheiro de configuração do serviço.

A tabela seguinte descreve `ServiceConfiguration` os atributos do elemento. Todos os valores dos atributos são tipos de cordas.

| Atributo | Descrição |
| --------- | ----------- |
|serviceName|Necessário. O nome do serviço de nuvem. O nome aqui dado deve coincidir com o nome especificado no ficheiro de definição de serviço.|
|osFamília|Opcional. Especifica o Os convidado que irá funcionar em instâncias de funções no serviço de nuvem. Para obter informações sobre lançamentos de Os convidados suportados, consulte [lançamentos do Os o so do Azure Guest e matriz de compatibilidade SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Se não incluir `osFamily` um valor e não `osVersion` tiver definido o atributo para uma versão específica do Os do Hóspede, é utilizado um valor predefinido de 1.|
|osVersion|Opcional. Especifica a versão do Os convidado que irá funcionar em instâncias de funções no serviço de nuvem. Para obter mais informações sobre as versões do Os convidado, consulte [o Azure Guest OS Releases e a SDK Compatibilidade Matrix](cloud-services-guestos-update-matrix.md).<br /><br /> Pode especificar que o Os do Hóspede deve ser automaticamente atualizado para a versão mais recente. Para tal, detete `osVersion` o valor `*`do atributo para . Quando definidos para `*`, as instâncias de função são implementadas usando a versão mais recente do Os convidado para a família OS especificada e serão automaticamente atualizadas quando novas versões do Os convidado forem lançadas.<br /><br /> Para especificar manualmente uma versão `Configuration String` específica, utilize a tabela na secção de **versões Os versões de Os do Futuro, Corrente e Transitória** dos [Lançamentos do Os do Hóspede Azure e da Matriz de Compatibilidade SDK](cloud-services-guestos-update-matrix.md).<br /><br /> O valor padrão `osVersion` para `*`o atributo é .|
|schemaVersion|Opcional. Especifica a versão do esquema de Configuração de Serviço. A versão schema permite ao Visual Studio selecionar as ferramentas SDK corretas para a validação de esquemas se mais de uma versão do SDK for instalada lado a lado. Para obter mais informações sobre a compatibilidade de schema e versão, consulte [O SO Releases do Os e](cloud-services-guestos-update-matrix.md) a Matriz de Compatibilidade SDK|

O ficheiro de configuração do serviço deve conter um `ServiceConfiguration` elemento. O `ServiceConfiguration` elemento pode incluir `Role` qualquer número de `NetworkConfiguration` elementos e zero ou 1 elementos.