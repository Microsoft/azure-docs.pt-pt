---
title: Folha de consulta XPath de configuração de função de serviços de nuvem | Microsoft Docs
description: As várias configurações XPath que podem ser usadas na configuração da função de serviço de nuvem para expor as configurações como uma variável de ambiente.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: cd2bdc4fc4b2a135907851ca4d3034430618e0cd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359015"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Expor definições de configuração de função como uma variável de ambiente com XPath
No trabalho do serviço de nuvem ou no arquivo de definição de serviço de função Web, você pode expor valores de configuração de tempo de execução como variáveis de ambiente. Há suporte para os seguintes valores XPath (que correspondem aos valores de API).

Esses valores XPath também estão disponíveis por meio da biblioteca [Microsoft. WindowsAzure. inruntime](/previous-versions/azure/reference/ee773173(v=azure.100)) . 

## <a name="app-running-in-emulator"></a>Aplicativo em execução no emulador
Indica que o aplicativo está em execução no emulador.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| Código |var x = RoleEnvironment. isemulated; |

## <a name="deployment-id"></a>ID de Implementação
Recupera a ID de implantação da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| Código |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID de Função
Recupera a ID da função atual para a instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| Código |var ID = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Atualizar domínio
Recupera o domínio de atualização da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| Código |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domínio de falhas
Recupera o domínio de falha da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| Código |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nome da função
Recupera o nome da função das instâncias.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| Código |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Configuração
Recupera o valor da definição de configuração especificada.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| Código |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Caminho de armazenamento local
Recupera o caminho de armazenamento local da instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>Tamanho do armazenamento local
Recupera o tamanho do armazenamento local para a instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo de ponto de extremidade
Recupera o protocolo de ponto de extremidade para a instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>IP do ponto de extremidade
Obtém o endereço IP do ponto de extremidade especificado.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta do ponto de extremidade
Recupera a porta do ponto de extremidade para a instância.

| Type | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código |var Port = RoleEnvironment. CurrentRoleInstance. InstanceEndpoints ["Endpoint1"]. IPEndpoint. Port; |

## <a name="example"></a>Exemplo
Aqui está um exemplo de uma função de trabalho que cria uma tarefa de inicialização com uma variável `TestIsEmulated` de ambiente nomeada definida como o [ @emulated valor XPath](#app-running-in-emulator). 

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
Saiba mais sobre o arquivo [onconfiguration. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) .

Criar um pacote do pacote [. cspkg](cloud-services-model-and-package.md#servicepackagecspkg) .

Habilite a [área de trabalho remota](cloud-services-role-enable-remote-desktop-new-portal.md) para uma função.

