---
title: Cloud Services função config notas de XPath | Documentos da Microsoft
description: As várias configurações de XPath pode utilizar a configuração de função de serviço em nuvem para expor as definições como uma variável de ambiente.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c51e4493-0643-4d05-bc44-06c76bcbf7d1
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: 2db63be6c6997840f7409a3ca79f1845f30e4ceb
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008064"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Exponha as definições de configuração de função como uma variável de ambiente com XPath
No ficheiro de definição de serviço de função web ou função de trabalho de serviço de nuvem, pode expor os valores de configuração de tempo de execução como variáveis de ambiente. Os seguintes valores de XPath são suportados (que correspondem aos valores de API).

Estes valores de XPath também estão disponíveis através da [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) biblioteca. 

## <a name="app-running-in-emulator"></a>Aplicação em execução no emulador
Indica que a aplicação está em execução no emulador.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/Deployment/@emulated" |
| Código |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID de Implementação
Obtém o ID de implementação para a instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/Deployment/@id" |
| Código |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID de Função
Obtém o ID de função atual para a instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@id" |
| Código |id de var = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Atualizar domínio
Obtém o domínio de atualização da instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domínio de falhas
Obtém o domínio de falhas da instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nome da função
Obtém o nome de função das instâncias.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/RoleEnvironment/CurrentInstance/@roleName" |
| Código |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Definição de configuração
Obtém o valor da definição de configuração especificado.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/ RoleEnvironment/a CurrentInstance/ConfigurationSettings/ConfigurationSetting [@name= 'Setting1']/@value" |
| Código |definição de var = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Caminho de armazenamento local
Obtém o caminho de armazenamento local para a instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/ RoleEnvironment/a CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@path" |
| Código |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Tamanho de armazenamento local
Obtém o tamanho do armazenamento local para a instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/ RoleEnvironment/a CurrentInstance/LocalResources/LocalResource [@name= 'LocalStore1']/@sizeInMB" |
| Código |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo de ponto final
Obtém o protocolo de ponto final para a instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/ RoleEnvironment/a CurrentInstance/pontos finais/ponto de extremidade [@name= 'Endpoint1']/@protocol" |
| Código |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocolo; |

## <a name="endpoint-ip"></a>Ponto final de IP
Obtém o endereço IP do ponto de extremidade especificado.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/ RoleEnvironment/a CurrentInstance/pontos finais/ponto de extremidade [@name= 'Endpoint1']/@address" |
| Código |var endereço = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta de ponto final
Obtém a porta de ponto final para a instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |XPath = "/ RoleEnvironment/a CurrentInstance/pontos finais/ponto de extremidade [@name= 'Endpoint1']/@port" |
| Código |var porta = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Exemplo
Eis um exemplo de uma função de trabalho que cria uma tarefa de arranque com uma variável de ambiente com o nome `TestIsEmulated` definido como o [ @emulated xpath valor](#app-running-in-emulator). 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre o [serviceconfiguration. Cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ficheiro.

Criar uma [ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) pacote.

Ativar [ambiente de trabalho remoto](cloud-services-role-enable-remote-desktop-new-portal.md) para uma função.

