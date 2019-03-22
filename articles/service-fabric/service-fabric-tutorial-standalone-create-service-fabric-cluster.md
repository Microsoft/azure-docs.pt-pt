---
title: Tutorial de Instalação de Cliente autónomo do Service Fabric - Azure Service Fabric | Microsoft Docs
description: Neste tutorial irá aprender a instalar o cliente autónomo do Service Fabric no cluster que criou no artigo do tutorial anterior.
services: service-fabric
documentationcenter: .net
author: david-stanford
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dastanfo
ms.custom: mvc
ms.openlocfilehash: e4995890a1f2ceec8ddf5d4dafafdd13382864db
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311463"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Tutorial: Instalar e criar o cluster do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série de tutoriais, vai criar um cluster autónomo alojado no AWS e instale uma aplicação no mesmo.

Este tutorial é a segunda parte de uma série. Este tutorial explica os passos para criar um cluster autónomo do Service Fabric.

Na segunda parte da série, saiba como:

> [!div class="checklist"]
> * Transfira e instale o pacote autónomo do Service Fabric
> * Criar o cluster do Service Fabric
> * Ligar ao cluster do Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Transfira o pacote do Service Fabric para o Windows Server

O Service Fabric fornece um pacote de configuração para criar clusters autónomos do Service Fabric.  [Transfira o pacote de configuração](https://go.microsoft.com/fwlink/?LinkId=730690) para o seu computador local.  Depois de transferir com êxito, faça uma cópia sobre a ligação de RDP para a instância de EC2 e cole-a no Ambiente de trabalho.

Selecione o ficheiro zip, abra o menu de contexto e selecione **Extrair Todos** > **Extrair**.  À medida que extrai os ficheiros, irá gerar uma pasta no ambiente de trabalho com o mesmo nome de ficheiro do zip.

Se pretender obter mais detalhes sobre os [conteúdos do pacote de configuração](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Configurar o ficheiro de configuração

Está a criar um cluster do Windows de três nós, por isso irá precisar de modificar o ficheiro `ClusterConfig.Unsecure.MultiMachine.json`.

Em seguida, atualize as três linhas do ipAddress que ocorrem no ficheiro nas linhas 8, 15 e 22 para os Endereços IP para cada uma das instâncias.

Depois de atualizar os nós, estes aparecem da seguinte forma:

```json
        {
            "nodeName": "vm0",
            "ipAddress": "172.31.27.1",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD0"
        }
```

Em seguida, precisa de atualizar algumas das propriedades.  Na linha 34, tem de modificar a cadeia de ligação para o arquivo de diagnóstico que deve ser semelhante a `"connectionstring": "C:\\ProgramData\\SF\\DiagnosticsStore"`

Por fim, na secção `nodeTypes` da configuração, adicione uma nova secção para mapear as portas efémeras que o Windows utilizará.  O ficheiro de configuração deve ser semelhante ao seguinte:

```json
"applicationPorts": {
    "startPort": "20001",
    "endPort": "20031"
},
"ephemeralPorts": {
    "startPort": "20606",
    "endPort": "20861"
},
"isPrimary": true
```

## <a name="validate-the-environment"></a>Validar o ambiente

O script *TestConfiguration.ps1* no pacote autónomo é utilizado como um analisador de melhores práticas para validar se um cluster pode ser implementado num determinado ambiente. A [preparação da implementação](service-fabric-cluster-standalone-deployment-preparation.md) lista os pré-requisitos e os requisitos do ambiente. Execute o script para verificar se pode criar o cluster de desenvolvimento:

```powershell
cd .\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\
.\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json
```

Deverá ver um resultado como o seguinte. Se o campo inferior "Passou" for devolvido como `True`, as verificações de sanidade passaram e o cluster parece estar implementável com base na configuração de entrada.

```powershell
Trace folder already exists. Traces will be written to existing trace folder: C:\Users\Administrator\Desktop\Microsoft.Azure.ServiceFabric.WindowsServer.6.2.274.9494\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 :
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
DataDrivesAvailable        : True
NoDomainController         : True
Passed                     : True
```

## <a name="create-the-cluster"></a>Criar o cluster

Depois de validar com êxito a configuração do cluster, execute o script *CreateServiceFabricCluster.ps1* para implementar o cluster do Service Fabric para as máquinas virtuais no ficheiro de configuração.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.MultiMachine.json -AcceptEULA
```

Se tudo funcionar, irá obter um resultado semelhante ao seguinte:

```powershell
Your cluster is successfully created! You can connect and manage your cluster using Microsoft Azure Service Fabric Explorer or PowerShell. To connect through PowerShell, run 'Connect-ServiceFabricCluster [ClusterConnectionEndpoint]'.
```

> [!NOTE]
> Os rastreios de implementação são escritos na VM/máquina em que executou o script do PowerShell CreateServiceFabricCluster.ps1. Pode encontrá-los na subpasta DeploymentTraces, com base no diretório do qual o script foi executado. Para ver se o Service Fabric foi corretamente implementado numa máquina, encontre os ficheiros instalados no diretório FabricDataRoot, conforme detalhado na secção FabricSettings do ficheiro de configuração do cluster (por predefinição C:\ProgramData\SF). Desta forma, os processos FabricHost.exe e Fabric.exe podem ser vistos em execução no Gestor de Tarefas.
>
>

### <a name="bring-up-service-fabric-explorer"></a>Apresentação do Service Fabric Explorer

Agora pode ligar ao cluster com o Service Fabric Explorer diretamente a partir de uma das máquinas com http:\//localhost:19080/Explorer/index.html ou remotamente com o http:\/ /<  *IPAddressofaMachine*>: 19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós

Pode adicionar ou remover nós para o cluster do Service Fabric autónomo, conforme as necessidades da empresa são alteradas. Veja [Adicionar ou Remover nós para um cluster autónomo do Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md) para obter passos detalhados.

## <a name="next-steps"></a>Passos seguintes

Na segunda parte da série, aprendeu a carregar grandes quantidades de dados aleatórios para uma conta de armazenamento em paralelo, tais como:

> [!div class="checklist"]
> * Configurar a cadeia de ligação
> * Criar a aplicação
> * Executar a aplicação
> * Validar o número de ligações

Avance para a parte três da série para instalar uma aplicação no cluster que criou.

> [!div class="nextstepaction"]
> [Instalar a aplicação no cluster do service fabric](service-fabric-tutorial-standalone-install-an-application.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
