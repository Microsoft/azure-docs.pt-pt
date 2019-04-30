---
title: Pacote autónomo de recursos de infraestrutura de serviço do Azure para o Windows Server | Documentos da Microsoft
description: Descrição e o conteúdo do pacote autónomo do Azure Service Fabric para Windows Server.
services: service-fabric
documentationcenter: .net
author: maburlik
manager: chackdan
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: facdcd162826e6f77ace098391459cba00061c4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711089"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Conteúdo do pacote autónomo do Service Fabric para o Windows Server
Na [transferido](https://go.microsoft.com/fwlink/?LinkId=730690) pacote autónomo do Service Fabric, encontrará os seguintes ficheiros:

| **Nome de ficheiro** | **Breve descrição** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Um script do PowerShell que cria o cluster utilizando as definições nas ClusterConfig.json. |
| RemoveServiceFabricCluster.ps1 |Um script do PowerShell que remove um cluster com as definições no ClusterConfig.json. |
| AddNode.ps1 |Um script do PowerShell para adicionar um nó de um existente já implementou o cluster no computador atual. |
| RemoveNode.ps1 |Um script do PowerShell para remover um nó de um existente já implementou o cluster da máquina atual. |
| CleanFabric.ps1 |Um script do PowerShell para a limpeza autónoma de instalação do Service Fabric fora da máquina atual. Instalações anteriores do MSI devem ser removidas com seus próprios desinstaladores associados. |
| TestConfiguration.ps1 |Um script do PowerShell para analisar a infraestrutura conforme especificado no JSON. |
| DownloadServiceFabricRuntimePackage.ps1 |Um script do PowerShell utilizado para transferir o pacote de tempo de execução mais recente fora de banda, para cenários em que a máquina de implementação não está ligada à internet. |
| DeploymentComponentsAutoextractor.exe |Arquivo auto-extraível que contém os componentes de implantação usado pelos scripts de pacote autónomo. |
| EULA_ENU.txt |Os termos de licenciamento para a utilização do pacote do Microsoft Azure Service Fabric autônomo do Windows Server. Pode [transferir uma cópia do EULA](https://go.microsoft.com/fwlink/?LinkID=733084) agora. |
| Readme. txt |Uma ligação para as notas de versão e instruções de instalação básica. É um subconjunto das instruções neste documento. |
| ThirdPartyNotice.rtf |Aviso de software de terceiros que está no pacote. |
| Tools\Microsoft.Azure.ServiceFabric.Windowsserver.SupportPackage.zip |StandaloneLogCollector.exe qual é executado sob demanda para recolher e carregar registos de rastreio para a Microsoft para fins de suporte. |
| Tools\ServiceFabricUpdateService.zip |Uma ferramenta usada para ativar a atualização do código de automática para clusters que não têm acesso à internet. Podem encontrar mais detalhes [aqui](service-fabric-cluster-upgrade-windows-server.md)|

**Modelos** 

| **Nome de ficheiro** | **Breve descrição** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Um ficheiro de exemplo de configuração do cluster contém as definições para um desprotegido, três nós, única máquina (ou máquina virtual) cluster de desenvolvimento, incluindo as informações para cada nó no cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém as definições para um cluster não seguro, máquina multi (ou máquina virtual), incluindo as informações para cada máquina no cluster. |
| ClusterConfig.Windows.DevCluster.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições de um segura e três nós, única máquina (ou máquina virtual) cluster de desenvolvimento, incluindo as informações para cada nó que está no cluster. O cluster estiver protegido com o [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para um cluster de máquina multi (ou máquina virtual) segura, através da segurança do Windows, incluindo as informações para cada máquina que está em cluster seguro. O cluster estiver protegido com o [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições de um segura e três nós, única máquina (ou máquina virtual) cluster de desenvolvimento, incluindo as informações para cada nó no cluster. O cluster estiver protegido com x509 certificados. |
| ClusterConfig.x509.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para o cluster seguro, máquina multi (ou máquina virtual), incluindo as informações para cada nó no cluster seguro. O cluster estiver protegido com x509 certificados. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Um ficheiro de exemplo de configuração do cluster contém todas as definições para o cluster seguro, máquina multi (ou máquina virtual), incluindo as informações para cada nó no cluster seguro. O cluster estiver protegido usando [contas de serviço geridas de grupo](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx). |

## <a name="cluster-configuration-samples"></a>Exemplos de configuração de cluster
Versões mais recentes dos modelos de configuração do cluster podem ser encontradas na página do GitHub: [Exemplos de configuração de Cluster autónomo](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Independente de tempo de execução de pacote
O pacote de tempo de execução mais recente é transferido automaticamente durante a implementação de cluster a partir [Link transferir - tempo de execução do Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relacionado
* [Criar um cluster autónomo de Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md)
* [Cenários de segurança de cluster do Service Fabric](service-fabric-windows-cluster-windows-security.md)
