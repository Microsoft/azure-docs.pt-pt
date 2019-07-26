---
title: Esquema de função dos serviços de nuvem do Azure | Microsoft Docs
ms.custom: ''
ms.date: 12/07/2016
services: cloud-services
ms.service: cloud-services
ms.topic: reference
caps.latest.revision: 12
author: georgewallace
ms.author: gwallace
ms.openlocfilehash: 481301333ada39297bf2813bbea5f096c2abd3ad
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360651"
---
# <a name="azure-cloud-services-config-role-schema"></a>Esquema da função de configuração dos serviços de nuvem do Azure

O `Role` elemento do arquivo de configuração especifica o número de instâncias de função a serem implantadas para cada função no serviço, os valores de quaisquer parâmetros de configuração e as impressões digitais para quaisquer certificados associados a uma função.

Para obter mais informações sobre o esquema de configuração do serviço do Azure, consulte [esquema de configuração do serviço de nuvem (clássico)](schema-cscfg-file.md). Para obter mais informações sobre o esquema de definição de serviço do Azure, consulte [esquema de definição do serviço de nuvem (clássico)](schema-csdef-file.md).

##  <a name="Role"></a>Elemento Role
O exemplo a seguir mostra `Role` o elemento e seus elementos filho.

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

A tabela a seguir descreve os atributos para `Role` o elemento.

| Atributo | Descrição |
| --------- | ----------- |
| name   | Necessário. Especifica o nome da função. O nome deve corresponder ao nome fornecido para a função no arquivo de definição de serviço.|
| vmName | Opcional. Especifica o nome DNS para uma máquina virtual. O nome deve ter 10 caracteres ou menos.|

A tabela a seguir descreve os elementos filho do `Role` elemento.

| Elemento | Descrição |
| ------- | ----------- |
| instâncias | Necessário. Especifica o número de instâncias a serem implantadas para a função. O número de instâncias é definido por um inteiro para o `count` atributo.|
| Definição   | Opcional. Especifica um nome de configuração e um valor em uma coleção de configurações para uma função. O nome da configuração é definido por uma cadeia de `name` caracteres para o atributo e o valor da configuração é definido por `value` uma cadeia de caracteres para o atributo.|
| Certificado | Opcional. Especifica o nome, a impressão digital e o algoritmo de um certificado de serviço que deve ser associado à função. O nome do certificado é definido por uma cadeia de `name` caracteres para o atributo. A impressão digital do certificado é definida por uma cadeia de caracteres de números hexadecimais que `thumbprint` não contém espaços para o atributo. Os números hexadecimais devem ser representados usando dígitos e caracteres alfabéticos maiúsculos. O algoritmo de certificado é definido por uma cadeia de `thumbprintAlgorithm` caracteres para o atributo.|

## <a name="see-also"></a>Consultar Também
[Esquema de configuração do serviço de nuvem (clássico)](schema-cscfg-file.md)