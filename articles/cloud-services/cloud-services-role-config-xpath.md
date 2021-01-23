---
title: Cloud Services (clássico) Role config XPath cheat sheet | Microsoft Docs
description: As várias definições de XPath que pode utilizar na função de serviço de nuvem config para expor as definições como uma variável ambiental.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 397bc6845dc8d2d8bc44c00c27f6c12037651337
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741388"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>Expor as definições de configuração de função como uma variável de ambiente com XPath

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

No ficheiro de definição de serviço de serviço de nuvem ou web role, pode expor os valores de configuração do tempo de execução como variáveis ambientais. Os seguintes valores XPath são suportados (que correspondem aos valores API).

Estes valores XPath também estão disponíveis através da biblioteca [Microsoft.WindowsAzure.ServiceRuntime.](/previous-versions/azure/reference/ee773173(v=azure.100)) 

## <a name="app-running-in-emulator"></a>App em execução no emulador
Indica que a aplicação está a funcionar no emulador.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath=" /RoleEnvironment/Deployment/@emulated " |
| Código |var x = RoleEnvironment.IsEmulado; |

## <a name="deployment-id"></a>ID de implantação
Recupera o ID de implantação, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath=" /RoleEnvironment/Deployment/@id " |
| Código |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ID de papel
Recupera o iD de função atual, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath=" /RoleEnvironment/CurrentInstance/@id " |
| Código |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>Atualizar domínio
Recupera o domínio de atualização da instância.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath=" /RoleEnvironment/CurrentInstance/@updateDomain " |
| Código |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>Domínio de avarias
Recupera o domínio da falha da ocorrência.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath=" /RoleEnvironment/CurrentInstance/@faultDomain " |
| Código |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>Nome da função
Recupera o nome de papel dos casos.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath=" /RoleEnvironment/CurrentInstance/@roleName " |
| Código |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>Configuração config
Recupera o valor da definição de configuração especificada.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[ @name ='Setting1'] /@value " |
| Código |var definição = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>Caminho de armazenamento local
Recupera o caminho de armazenamento local, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[ @name ='LocalStore1'] /@path |
| Código |var localResourcePath = RoleEnvironment.GetLocalResource ("LocalStore1"). RootPath; |

## <a name="local-storage-size"></a>Tamanho de armazenamento local
Recupera o tamanho do armazenamento local, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[ @name ='LocalStore1'] /@sizeInMB |
| Código |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1"). MáximaSizeInMegabytes; |

## <a name="endpoint-protocol"></a>Protocolo de ponto final
Recupera o protocolo de ponto final, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoints/Endpoints[ @name ='Endpoint1'] /@protocol " |
| Código |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. Protocolo; |

## <a name="endpoint-ip"></a>IP de ponto final
Obtém o endereço IP especificado do ponto final.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoints/Endpoints[ @name ='Endpoint1'] /@address " |
| Código |endereço var = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Endereço |

## <a name="endpoint-port"></a>Porta endpoint
Recupera a porta de ponta, por exemplo.

| Tipo | Exemplo |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoints/Endpoints[ @name ='Endpoint1'] /@port " |
| Código |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"]. IPEndpoint.Port; |

## <a name="example"></a>Exemplo
Aqui está um exemplo de um papel de trabalhador que cria uma tarefa de startup com uma variável ambiental chamada `TestIsEmulated` definida para o valor [ @emulated xpath](#app-running-in-emulator). 

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

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre o ficheiro [ServiceConfiguration.cscfg.](cloud-services-model-and-package.md#serviceconfigurationcscfg)

Crie um pacote [ServicePackage.cspkg.](cloud-services-model-and-package.md#servicepackagecspkg)

Ativar [o ambiente de trabalho remoto](cloud-services-role-enable-remote-desktop-new-portal.md) para uma função.




