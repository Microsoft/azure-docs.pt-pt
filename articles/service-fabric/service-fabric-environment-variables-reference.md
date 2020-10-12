---
title: Variáveis ambientais do tecido de serviço Azure
description: Saiba mais sobre variáveis ambientais no Azure Service Fabric. Contém uma referência de uma lista completa de variáveis e seus usos.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645689"
---
# <a name="service-fabric-environment-variables"></a>Variáveis ambientais do tecido de serviço

O Tecido de Serviço tem variáveis ambientais incorporadas definidas para cada instância de serviço. A lista completa de variáveis ambientais é a seguinte:

| Variável ambiente                         | Descrição                                                            | Exemplo                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | O nome uri de tecido da aplicação                                 | tecido:/MyApplication                                                |
| Fabric_CodePackageName                       | O nome do pacote de código a que pertence o processo              | Código                                                                 |
| Fabric_Endpoint \_ IPOrFQDN \_ *ServiceEndpointName*     | O endereço IP ou FQDN do ponto final                                 | 10.0.0.1                                                     |
| Fabric \_ Endpoint \_ *ServiceEndpointName*              | Número de porta para o ponto final                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Pasta de registo                                                             | C: \\ \\ Registo de MyApplicationType_App12 de \\ \\ _App de \\ \\ \\ \\ \\ \\ dados _Node_0      |
| Fabric_Folder_App_Temp                       | Pasta Temp                                                            | C: \\ \\ _App de \\ \\ \\ \\ dados _Node_0 \\ \\ MyApplicationType_App12 \\ \\ temperatura     |
| Fabric_Folder_App_Work                       | Pasta de trabalho                                                            | C: \\ \\ Trabalho de MyApplicationType_App12 de \\ \\ _App de \\ \\ \\ \\ \\ \\ dados _Node_0     |
| Fabric_Folder_Application                    | A pasta home de aplicações                                           | C: \\ \\ Data \\ \\ _App \\ \\ _Node_0 \\ \\ MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Um bool especificando se o processo é um recipiente                   | false                                                                |
| Fabric_NodeId                                | O nó ID do nó que executa o processo                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | O IP ou FQDN do nó, conforme especificado no ficheiro manifesto de cluster. | local ou 10.0.0.1                                                |
| Fabric_NodeName                              | O nome do nó do nó que executa o processo                          | _Node_0                                                              |
| Fabric_ServiceName                           | O nome uri de tecido do serviço, se o serviço estiver hospedado no modo ExclusiveProcess. Este valor variável só está disponível se criar o serviço com ServicePackageActivationMode ExclusiveProcess.  | tecido:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | O ServicePackageActivationId                                         | UM GUIA                                                               |
| Fabric_ServicePackageName                    | Nome do pacote de serviço o processo faz parte                     | Web1Pkg                                                              |

Variáveis de ambiente interno utilizadas pelo tempo de execução do tecido de serviço:

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
- Nome hospedado do Serviço
