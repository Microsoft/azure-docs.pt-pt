---
title: As variáveis de ambiente do Azure Service Fabric | Documentos da Microsoft
description: Documentação de referência para as variáveis de ambiente do Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946727"
---
# <a name="service-fabric-environment-variables"></a>Variáveis de ambiente do Service Fabric

Service Fabric tem variáveis de ambiente incorporada definido para cada instância de serviço. A lista completa de variáveis de ambiente está ilustrado abaixo:

| Variável de ambiente                         | Descrição                                                            | Exemplo                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | O nome de uri de recursos de infraestrutura da aplicação                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | O nome do pacote de código ao qual pertence o processo              | Código                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | O endereço ip ou FQDN do ponto final                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Número de porta para o ponto final                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Pasta de registo                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Pasta temporária                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Pasta de trabalho                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Na pasta raiz de aplicativos                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Um bool especificar se o processo é um contentor                   | false                                                                |
| Fabric_NodeId                                | A ID do nó de execução do processo                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | O IP ou FQDN do nó, conforme especificado no cluster de arquivo de manifesto. | localhost ou 10.0.0.1                                                |
| Fabric_NodeName                              | O nome do nó do nó de execução do processo                          | _Node_0                                                              |
| Fabric_ServiceName                           | O nome de uri de recursos de infraestrutura do serviço, se o serviço estiver alojado no modo de ExclusiveProcess. Este valor da variável só está disponível se criar o serviço com ServicePackageActivationMode ExclusiveProcess.  | Fabric: / MyApplication/Meuserviço                                               |
| Fabric_ServicePackageActivationId            | The ServicePackageActivationId                                         | UM GUID                                                               |
| Fabric_ServicePackageName                    | Nome do pacote de serviço o processo faz parte do                     | Web1Pkg                                                              |

Variáveis de ambiente interno utilizadas pelo tempo de execução do Service Fabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
