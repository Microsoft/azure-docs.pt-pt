---
title: Azure Cloud Services Role Schema Microsoft Docs
description: O elemento Role de um ficheiro de configuração de serviço especifica quantas instâncias de função devem ser implantadas para cada função, valores de configuração e impressões digitais de certificado.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79528426"
---
# <a name="azure-cloud-services-config-role-schema"></a>Azure Cloud Services Config Role Schema

O `Role` elemento do ficheiro de configuração especifica o número de instâncias a implementar para cada função no serviço, os valores de quaisquer definições de configuração e as impressões digitais para quaisquer certificados associados a uma função.

Para obter mais informações sobre o Esquema de Configuração do Serviço Azure, consulte o Esquema de [Configuração do Serviço de Nuvem (clássico).](schema-cscfg-file.md) Para obter mais informações sobre o Esquema de Definição de Serviço Azure, consulte [o Esquema de Definição do Serviço de Nuvem (clássico).](schema-csdef-file.md)

##  <a name="role-element"></a><a name="Role"></a>Elemento de papel
O exemplo a seguir mostra o `Role` elemento e os elementos da criança.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

A tabela seguinte descreve os atributos para o `Role` elemento.

| Atributo | Descrição |
| --------- | ----------- |
| name   | Necessário. Especifica o nome do papel. O nome deve corresponder ao nome previsto para a função no ficheiro de definição de serviço.|
| vmName | Opcional. Especifica o nome DNS para uma Máquina Virtual. O nome deve ter 10 caracteres ou menos.|

A tabela seguinte descreve os elementos da criança do `Role` elemento.

| Elemento | Description |
| ------- | ----------- |
| de Instâncias | Necessário. Especifica o número de casos a implementar para o papel. O número de ocorrências é definido por um inteiro para o `count` atributo.|
| Definição   | Opcional. Especifica um nome e valor de definição numa coleção de configurações para uma função. O nome de definição é definido por uma cadeia para o `name` atributo e o valor de definição é definido por uma cadeia para o `value` atributo.|
| Certificado | Opcional. Especifica o nome, impressão digital e algoritmo de um certificado de serviço que deve ser associado com o papel. O nome do certificado é definido por uma cadeia para o `name` atributo. A impressão digital do certificado é definida por uma série de números hexadémicos que não contêm espaços para o `thumbprint` atributo. Os números hexadecimais devem ser representados com os dígitos e caracteres alfa maiúsculas. O algoritmo de certificado é definido por uma cadeia para o `thumbprintAlgorithm` atributo.|

## <a name="see-also"></a>Consulte também
[Esquema de configuração do Serviço de Nuvem (clássico)](schema-cscfg-file.md)