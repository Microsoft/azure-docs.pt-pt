---
title: Variáveis ambientais de tecido de serviço Azure
description: Conheça as variáveis ambientais no Tecido de Serviço Azure. Contém uma referência de uma lista completa de variáveis e seus usos.
author: mikkelhegn
ms.topic: reference
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: b13522b1d9f2acd2aa3f7923c1b623fab696056d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645689"
---
# <a name="service-fabric-environment-variables"></a>Variáveis ambientais de tecido de serviço

O Service Fabric tem variáveis ambientais incorporadas definidas para cada instância de serviço. A lista completa de variáveis ambientais é a seguir:

| Variável ambiental                         | Descrição                                                            | Exemplo                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | O nome uri tecido da aplicação                                 | tecido:/MyApplication                                                |
| Fabric_CodePackageName                       | O nome do pacote de código a que o processo pertence              | Código                                                                 |
| Fabric_Endpoint\_nome\_*de ponto final* do serviço IPOrFQDN     | O endereço ip ou FQDN do ponto final                                 | 10.0.0.1                                                     |
| Nome\_de\_*endpoint de endpoint* de endpoint de acabamento de tecido              | Número de porta para o ponto final                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Pasta de log                                                             | C:\\\\\\\\Registo\\\\\\\\de\\MyApplicationType_App12\\de _Node_0 _App de dados      |
| Fabric_Folder_App_Temp                       | Pasta Temp                                                            | C:\\\\\\\\_App\\\\\\\\de\\\\dados _Node_0 MyApplicationType_App12 temperatura     |
| Fabric_Folder_App_Work                       | Pasta de trabalho                                                            | C:\\\\\\\\_App\\\\\\\\de\\\\dados _Node_0 MyApplicationType_App12 trabalho     |
| Fabric_Folder_Application                    | A pasta casa aplicações                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Um bool especificando se o processo é um recipiente                   | false                                                                |
| Fabric_NodeId                                | A identificação do nó do nó que executa o processo                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | O IP ou FQDN do nó, conforme especificado no ficheiro manifesto do cluster. | local de acolhimento ou 10.0.0.1                                                |
| Fabric_NodeName                              | O nome do nó do nó que executa o processo                          | _Node_0                                                              |
| Fabric_ServiceName                           | O nome uri de tecido do serviço, se o serviço estiver hospedado no modo ExclusiveProcess. Este valor variável só está disponível se criar o serviço com o ServicePackageActivationMode ExclusiveProcess.  | tecido:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | O ServicePackageActivationId                                         | Um GUID                                                               |
| Fabric_ServicePackageName                    | Nome do pacote de serviço o processo faz parte de                     | Web1Pkg                                                              |

Variáveis ambientais internas utilizadas pelo tempo de funcionar do tecido de serviço:

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
- Nome de ficheiro de arquivo de tecido
- Nome de serviço hospedado
