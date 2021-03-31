---
title: Pacote autónomo de tecido de serviço Azure para servidor windows
description: Descrição e conteúdo do pacote autónomo de tecido de serviço Azure para o Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: fb9a52510788a275bdeea67cd5c1fdd2e894738d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86261027"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Conteúdo do pacote autónomo de tecido de serviço para windows server
No pacote De Serviço Is Autónomo de Serviço [descarregado,](https://go.microsoft.com/fwlink/?LinkId=730690) encontrará os seguintes ficheiros:

| **Nome de ficheiro** | **Breve descrição** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Um script PowerShell que cria o cluster utilizando as definições ClusterConfig.jsligado. |
| RemoveServiceFabricCluster.ps1 |Um script PowerShell que remove um cluster utilizando as definições ClusterConfig.jsligado. |
| AddNode.ps1 |Um script PowerShell para adicionar um nó a um cluster implantado existente na máquina atual. |
| RemoveNode.ps1 |Um script PowerShell para remover um nó de um cluster implantado existente da máquina atual. |
| CleanFabric.ps1 |Um script PowerShell para a limpeza de uma instalação autónoma do tecido de serviço da máquina atual. As instalações MSI anteriores devem ser removidas utilizando os seus próprios desinstaladores associados. |
| TestConfiguration.ps1 |Um script PowerShell para analisar a infraestrutura conforme especificado no Cluster.js. |
| DownloadServiceFabricRuntimePackage.ps1 |Um script PowerShell usado para descarregar o mais recente pacote de tempo de execução fora da banda, para cenários onde a máquina de implantação não está ligada à internet. |
| DeploymentComponentsAutoextractor.exe |Arquivo de auto-extracção contendo Componentes de Implantação utilizados pelos scripts do pacote Autónomo. |
| EULA_ENU.txt |Os termos de licença para a utilização do pacote autónomo do Microsoft Azure Service Fabric Windows Server. Já pode [descarregar uma cópia do EULA.](https://go.microsoft.com/fwlink/?LinkID=733084) |
| Readme.txt |Uma ligação com as notas de desbloqueio e instruções básicas de instalação. É um subconjunto das instruções deste documento. |
| TerceiroPartyNotice.rtf |Aviso de software de terceiros que está no pacote. |
| Tools\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe que é executado a pedido para recolher e carregar registos de vestígios para a Microsoft para fins de suporte. |
| Tools\ServiceFabricUpdateService.zip |Uma ferramenta usada para permitir o upgrade de código automático para clusters que não têm acesso à Internet. Estão disponíveis mais detalhes [aqui](service-fabric-cluster-upgrade-windows-server.md)|

**Modelos** 

| **Nome de ficheiro** | **Breve descrição** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.jsem |Um ficheiro de amostra de configuração de cluster que contém as definições para um cluster de desenvolvimento de tipo não seguro, de três nós, de uma máquina única (ou de uma máquina virtual), incluindo as informações para cada nó no cluster. |
| ClusterConfig.Unsecure.MultiMachine.jsem |Um ficheiro de amostra de configuração de cluster que contém as definições de um cluster não seguro, multi-máquina (ou máquina virtual), incluindo as informações para cada máquina no cluster. |
| ClusterConfig.Windows.DevCluster.jsem |Um ficheiro de amostra de configuração de cluster que contém todas as definições para um cluster de desenvolvimento seguro, de três nós, de uma máquina única (ou de uma máquina virtual), incluindo as informações para cada nó que está no cluster. O cluster é protegido utilizando [identidades do Windows.](/previous-versions/msp-n-p/ff649396(v=pandp.10)) |
| ClusterConfig.Windows.MultiMachine.jsem |Um ficheiro de amostra de configuração de cluster que contém todas as definições para um cluster seguro, multi-máquina (ou máquina virtual) utilizando a segurança do Windows, incluindo as informações para cada máquina que está no cluster seguro. O cluster é protegido utilizando [identidades do Windows.](/previous-versions/msp-n-p/ff649396(v=pandp.10)) |
| ClusterConfig.x509.DevCluster.jsem |Um ficheiro de amostra de configuração de cluster que contém todas as definições para um cluster de desenvolvimento seguro, de três nós, de uma máquina única (ou de uma máquina virtual), incluindo as informações para cada nó no cluster. O cluster é seguro usando certificados x509. |
| ClusterConfig.x509.MultiMachine.jsem |Um ficheiro de amostra de configuração de cluster que contém todas as definições para o cluster seguro, multi-máquina (ou máquina virtual), incluindo as informações para cada nó no cluster seguro. O cluster é seguro usando certificados x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.jsem |Um ficheiro de amostra de configuração de cluster que contém todas as definições para o cluster seguro, multi-máquina (ou máquina virtual), incluindo as informações para cada nó no cluster seguro. O cluster é protegido através de [contas de serviço geridas pelo grupo.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11)) |

## <a name="cluster-configuration-samples"></a>Amostras de configuração do cluster
As versões mais recentes dos modelos de configuração do cluster podem ser encontradas na página GitHub: [Amostras de configuração de cluster autónoma](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

## <a name="independent-runtime-package"></a>Pacote de tempo de execução independente
O mais recente pacote de tempo de execução é descarregado automaticamente durante a implementação do cluster a partir do [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relacionado
* [Criar um cluster de tecido de serviço Azure autónomo](service-fabric-cluster-creation-for-windows-server.md)
* [Cenários de segurança do cluster de tecido de serviço](service-fabric-windows-cluster-windows-security.md)
