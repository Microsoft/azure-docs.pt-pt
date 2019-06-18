---
title: Esquema de função dos serviços Cloud do Azure | Documentos da Microsoft
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.reviewer: ''
ms.service: cloud-services
ms.suite: ''
ms.tgt_pltfrm: ''
ms.topic: reference
ms.assetid: e4fbffc1-98eb-449c-971c-de415e45ab34
caps.latest.revision: 12
author: jpconnock
ms.author: jeconnoc
manager: timlt
ms.openlocfilehash: aa6f8a821edea6261d64bb411154e82fdf212a8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62130250"
---
# <a name="azure-cloud-services-config-role-schema"></a>Esquema de configuração de função dos serviços Cloud do Azure

O `Role` elemento do ficheiro de configuração especifica o número de instâncias de função para implementar para cada função no serviço, os valores de quaisquer definições de configuração, e os thumbprints de todos os certificados a uma função.

Para obter mais informações sobre o esquema de configuração de serviço do Azure, consulte [esquema de configuração do serviço em nuvem (clássico)](schema-cscfg-file.md). Para obter mais informações sobre o esquema de definição de serviço do Azure, consulte [esquema de definição de serviço em nuvem (clássico)](schema-csdef-file.md).

##  <a name="Role"></a> Elemento de função
A exemplo a seguir mostra o `Role` elemento e os respetivos elementos subordinados.

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
| name   | Necessário. Especifica o nome da função. O nome tem de corresponder ao nome fornecido para a função no arquivo de definição do serviço.|
| vmName | Opcional. Especifica o nome DNS para uma Máquina Virtual. O nome deve ter 10 carateres ou menos.|

A tabela seguinte descreve os elementos filho do `Role` elemento.

| Elemento | Descrição |
| ------- | ----------- |
| instâncias | Necessário. Especifica o número de instâncias a implementar para a função. O número de instâncias é definido por um número inteiro para o `count` atributo.|
| Definição   | Opcional. Especifica um nome de definição e valor numa coleção de definições para uma função. O nome da definição é definido por uma cadeia de caracteres para o `name` atributo e o valor da definição é definido por uma cadeia de caracteres para o `value` atributo.|
| Certificado | Opcional. Especifica o nome, o thumbprint e o algoritmo de um certificado de serviço que está a ser associado com a função. O nome do certificado é definido por uma cadeia de caracteres para o `name` atributo. O thumbprint do certificado é definido por uma cadeia de números hexadecimais contendo sem espaços para o `thumbprint` atributo. Os números hexadecimais devem ser representados com dígitos e carateres alfabéticos em maiúsculas. O algoritmo de certificado é definido por uma cadeia de caracteres para o `thumbprintAlgorithm` atributo.|

## <a name="see-also"></a>Consultar Também
[Esquema de configuração do cloud Service (clássico)](schema-cscfg-file.md)