---
title: Pacote autónomo de tecido de serviço Azure para servidor windows
description: Descrição e conteúdo do pacote autónomo de tecido de serviço Azure para o Windows Server.
author: maburlik
ms.topic: conceptual
ms.date: 8/10/2017
ms.author: maburlik
ms.openlocfilehash: 1bb73fa69717f067139067f127a0d50af4878d6c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75451850"
---
# <a name="contents-of-service-fabric-standalone-package-for-windows-server"></a>Conteúdo do pacote autónomo de tecido de serviço para servidor windows
No pacote autónomo de tecido de serviço [descarregado,](https://go.microsoft.com/fwlink/?LinkId=730690) encontrará os seguintes ficheiros:

| **Nome de ficheiro** | **Descrição curta** |
| --- | --- |
| CreateServiceFabricCluster.ps1 |Um script PowerShell que cria o cluster utilizando as definições em ClusterConfig.json. |
| RemoverServiceFabricCluster.ps1 |Um script PowerShell que remove um cluster utilizando as definições em ClusterConfig.json. |
| AddNode.ps1 |Um script PowerShell para adicionar um nó a um cluster implantado existente na máquina atual. |
| Remover Node.ps1 |Um script PowerShell para remover um nó de um cluster implantado existente da máquina atual. |
| CleanFabric.ps1 |Um script PowerShell para limpar uma instalação autónoma de tecido de serviço fora da máquina atual. As instalações anteriores da MSI devem ser removidas utilizando os seus próprios desinstaladores associados. |
| TestConfiguration.ps1 |Um script PowerShell para analisar a infraestrutura conforme especificado no Cluster.json. |
| DownloadServiceFabricRuntimePackage.ps1 |Um script PowerShell usado para descarregar o mais recente pacote de tempo de execução fora da banda, para cenários em que a máquina de implantação não está ligada à internet. |
| Componentes auto-extractor.exe |Arquivo de auto-extracção contendo componentes de implantação utilizados pelos scripts de pacote autónomo. |
| EULA_ENU.txt |Os termos de licença para a utilização do pacote De Servidor Windows Server autónomo do Microsoft Azure Service Fabric. Já pode [descarregar uma cópia da EULA.](https://go.microsoft.com/fwlink/?LinkID=733084) |
| Readme.txt |Um link para as notas de lançamento e instruções básicas de instalação. É um subconjunto das instruções deste documento. |
| ThirdPartyNotice.rtf |Aviso de software de terceiros que está no pacote. |
| Ferramentas\Microsoft.Azure.ServiceFabric.WindowsServer.SupportPackage.zip |StandaloneLogCollector.exe que é executado a pedido para recolher e carregar registos de rastreio para a Microsoft para fins de suporte. |
| Ferramentas\ServiceFabricUpdateService.zip |Uma ferramenta usada para permitir a atualização do código automático para clusters que não têm acesso à Internet. Mais detalhes podem ser encontrados [aqui](service-fabric-cluster-upgrade-windows-server.md)|

**Modelos** 

| **Nome de ficheiro** | **Descrição curta** |
| --- | --- |
| ClusterConfig.Unsecure.DevCluster.json |Um ficheiro de amostra de configuração de cluster que contém as definições para um cluster de desenvolvimento não seguro, de três nós, de uma única máquina (ou máquina virtual), incluindo a informação de cada nó no cluster. |
| ClusterConfig.Unsecure.MultiMachine.json |Um ficheiro de amostra de configuração de cluster que contém as definições para um cluster não seguro, multi-máquina (ou máquina virtual), incluindo a informação de cada máquina no cluster. |
| ClusterConfig.Windows.DevCluster.json |Um ficheiro de amostra de configuração de cluster que contém todas as definições para um cluster de desenvolvimento seguro, de três nós, de uma única máquina (ou máquina virtual), incluindo a informação para cada nó que está no cluster. O cluster é protegido utilizando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.Windows.MultiMachine.json |Um ficheiro de amostra de configuração de cluster que contém todas as definições para um cluster seguro, multi-máquina (ou máquina virtual) utilizando a segurança do Windows, incluindo as informações para cada máquina que se encontra no cluster seguro. O cluster é protegido utilizando [identidades do Windows](https://msdn.microsoft.com/library/ff649396.aspx). |
| ClusterConfig.x509.DevCluster.json |Um ficheiro de amostra de configuração de cluster que contém todas as definições para um cluster de desenvolvimento seguro, de três nós, de uma única máquina (ou máquina virtual), incluindo a informação de cada nó no cluster. O cluster é protegido com certificados x509. |
| ClusterConfig.x509.MultiMachine.json |Um ficheiro de amostra de configuração de cluster que contém todas as definições para o cluster seguro, multi-máquina (ou máquina virtual), incluindo a informação de cada nó no cluster seguro. O cluster é protegido com certificados x509. |
| ClusterConfig.gMSA.Windows.MultiMachine.json |Um ficheiro de amostra de configuração de cluster que contém todas as definições para o cluster seguro, multi-máquina (ou máquina virtual), incluindo a informação de cada nó no cluster seguro. O cluster é protegido utilizando contas de [serviço geridas](https://technet.microsoft.com/library/jj128431(v=ws.11).aspx)pelo grupo . |

## <a name="cluster-configuration-samples"></a>Amostras de configuração de cluster
As versões mais recentes dos modelos de configuração do cluster podem ser encontradas na página GitHub: [Amostras de configuração](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)autónoma do cluster .

## <a name="independent-runtime-package"></a>Pacote independente de tempo de execução
O pacote de tempo de execução mais recente é descarregado automaticamente durante a implementação do cluster a partir do [Download Link - Service Fabric Runtime - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354).

## <a name="related"></a>Relacionado
* [Criar um cluster de tecido de serviço Azure autónomo](service-fabric-cluster-creation-for-windows-server.md)
* [Cenários de segurança do cluster de tecido de serviço](service-fabric-windows-cluster-windows-security.md)
