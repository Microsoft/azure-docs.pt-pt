---
title: Papel de Serviços azure Cloud Schema Microsoft Docs
description: O elemento Role de um ficheiro de configuração de serviço especifica quantas instâncias de função devem ser implementadas para cada função, valores de configuração e impressões de polegares de certificado.
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: tgore03
ms.author: tagore
ms.openlocfilehash: b64f9d27e382a39b132593502fed32c565af473a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79528426"
---
# <a name="azure-cloud-services-config-role-schema"></a>Serviços azure cloud Config Role Schema

O `Role` elemento do ficheiro de configuração especifica o número de instâncias de função a implementar para cada função no serviço, os valores de quaisquer configurações de configuração e as impressões digitais para quaisquer certificados associados a uma função.

Para mais informações sobre o Esquema de Configuração de Serviço Azure, consulte [o Cloud Service (clássico) Configuração Schema](schema-cscfg-file.md). Para mais informações sobre o Schema de Definição de Serviço Azure, consulte [o Cloud Service (clássico) Definition Schema](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a>Elemento de Papel
O exemplo que `Role` se segue mostra o elemento e os elementos infantis.

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

A tabela seguinte descreve `Role` os atributos para o elemento.

| Atributo | Descrição |
| --------- | ----------- |
| nome   | Necessário. Especifica o nome do papel. O nome deve coincidir com o nome previsto para o papel no ficheiro de definição de serviço.|
| vmName | Opcional. Especifica o nome DNS para uma Máquina Virtual. O nome deve ser de 10 caracteres ou menos.|

A tabela seguinte descreve os `Role` elementos infantis do elemento.

| Elemento | Descrição |
| ------- | ----------- |
| de Instâncias | Necessário. Especifica o número de instâncias a implementar para o papel. O número de instâncias é definido por `count` um inteiro para o atributo.|
| Definição   | Opcional. Especifica um nome e valor de definição numa coleção de configurações para uma função. O nome de definição é `name` definido por uma corda para o `value` atributo e o valor de definição é definido por uma corda para o atributo.|
| Certificado | Opcional. Especifica o nome, impressão digital e algoritmo de um certificado de serviço que deve ser associado ao papel. O nome do certificado é `name` definido por uma corda para o atributo. A impressão digital do certificado é definida por uma série de `thumbprint` números hexadecimais que não contêm espaços para o atributo. Os números hexadecimais devem ser representados utilizando dígitos e caracteres alfa maiúsculos. O algoritmo de certificado é `thumbprintAlgorithm` definido por uma corda para o atributo.|

## <a name="see-also"></a>Veja também
[Cloud Service (clássico) Configuração Schema](schema-cscfg-file.md)