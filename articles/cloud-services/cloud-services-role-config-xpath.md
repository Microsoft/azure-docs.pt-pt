---
title: Cloud Services Role config XPath cheat sheet [ Microsoft Docs
description: As várias definições xPath que pode utilizar na função de serviço na nuvem config para expor as definições como uma variável ambiental.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 380b0be4e4e4b19d16cb611b0b472294339f2199
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75386090"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Expor as definições de configuração de funções como uma variável ambiental com XPath
No ficheiro de definição de serviço de serviço na nuvem ou na definição de serviço web, pode expor os valores de configuração do tempo de execução como variáveis ambientais. São suportados os seguintes valores XPath (que correspondem aos valores da API).

Estes valores XPath também estão disponíveis através da biblioteca [Microsoft.WindowsAzure.ServiceRuntime.](/previous-versions/azure/reference/ee773173(v=azure.100)) 

## <a name="app-running-in-emulator"></a>App em execução em emulador
Indica que a aplicação está a funcionar no emulador.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated |
| Código |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>ID de implantação
Recupera o ID de implantação, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id |
| Código |var implementaçãoId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>Id de papel
Recupera o id de função atual, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id |
| Código |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Atualizar domínio
Recupera o domínio de atualização da instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain |
| Código |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domínio de avaria
Recupera o domínio de avaria da instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain |
| Código |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nome da função
Recupera o nome do papel dos casos.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName |
| Código |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Definição de config
Recupera o valor da definição de configuração especificada.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Configurações/ConfiguraçãoDefinição='Definição1']@name/@value" |
| Código |configuração var = RoleEnvironment.GetConfigurationSettingValue("Definição1"); |

## <a name="local-storage-path"></a>Caminho de armazenamento local
Recupera o caminho de armazenamento local, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| Código |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1"). Caminho-de-raiz; |

## <a name="local-storage-size"></a>Tamanho do armazenamento local
Recupera o tamanho do armazenamento local, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| Código |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo endpoint
Recupera o protocolo de ponto final, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| Código |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocolo; |

## <a name="endpoint-ip"></a>Ponto final IP
Obtém o endereço IP do ponto final especificado.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| Código |endereço var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Address |

## <a name="endpoint-port"></a>Porta endpoint
Recupera a porta do ponto final, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| Código |var porta = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Exemplo
Aqui está um exemplo de um papel de trabalhador `TestIsEmulated` que cria uma tarefa de arranque com uma variável ambiental denominada definida para o [ @emulated valor xpath](#app-running-in-emulator). 

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

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o ficheiro [ServiceConfiguration.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)

Crie um pacote [ServicePackage.cspkg.](cloud-services-model-and-package.md#servicepackagecspkg)

Ativar o [ambiente de trabalho remoto](cloud-services-role-enable-remote-desktop-new-portal.md) para uma função.




