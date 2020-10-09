---
title: Instalar cliente autónomo do Tecido de Serviço
description: Neste tutorial, aprenda a instalar o cliente autónomo do Service Fabric no cluster.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae0b343be986f4d8d5176c1f39eef6b23ca81278
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91840647"
---
# <a name="tutorial-install-and-create-service-fabric-cluster"></a>Tutorial: Instalar e criar cluster do Service Fabric

Os clusters autónomos do Service Fabric oferecem a opção de escolher o seu ambiente e criar um cluster como parte da abordagem "qualquer SO, qualquer cloud" que o Service Fabric está a realizar. Nesta série tutorial, você cria um cluster autónomo hospedado em AWS ou Azure e instala uma aplicação nele.

Este tutorial é a segunda parte de uma série. Este tutorial explica os passos para criar um cluster autónomo do Service Fabric.

Neste artigo, aprenderá a:

> [!div class="checklist"]
> * Transfira e instale o pacote autónomo do Service Fabric
> * Criar o cluster do Service Fabric
> * Ligar ao cluster do Service Fabric

## <a name="download-the-service-fabric-for-windows-server-package"></a>Transfira o pacote do Service Fabric para o Windows Server

O Service Fabric fornece um pacote de configuração para criar clusters autónomos do Service Fabric.  [Transfira o pacote de configuração](https://go.microsoft.com/fwlink/?LinkId=730690) para o seu computador local.  Uma vez descarregado com sucesso, copie-o através da ligação RDP ao seu VM e cole-o no Ambiente de Trabalho.

Selecione o ficheiro zip e abra o menu de contexto e selecione **Extract All**  >  **Extract**.  À medida que extrai os ficheiros, irá gerar uma pasta no ambiente de trabalho com o mesmo nome de ficheiro do zip.

Se pretender obter mais detalhes sobre os [conteúdos do pacote de configuração](service-fabric-cluster-standalone-package-contents.md).

## <a name="set-up-your-configuration-file"></a>Configurar o ficheiro de configuração

Está a criar um cluster do Windows de três nós, por isso irá precisar de modificar o ficheiro `ClusterConfig.Unsecure.MultiMachine.json`.

Em seguida, atualize as três linhas ipAddress que ocorrem no ficheiro nas linhas 8, 15 e 22 para os endereços IP para cada uma das instâncias.

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

Deve ver a saída como o exemplo abaixo. Se o campo inferior "Passou" for devolvido como `True`, as verificações de sanidade passaram e o cluster parece estar implementável com base na configuração de entrada.

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

Assim que tiver validado com sucesso o seu cluster config, execute * o *CreateServiceFabricCluster.ps1script para implantar o cluster de Tecido de Serviço para as máquinas virtuais no ficheiro de configuração.

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

### <a name="open-service-fabric-explorer"></a>Explorador de tecido de serviço aberto

Agora pode ligar-se ao cluster com o Service Fabric Explorer diretamente de uma das máquinas com http: \/ /localhost:19080/Explorer/index.html ou remotamente com http: \/ /< *IPAddressofaMachine*>:19080/Explorer/index.html.

## <a name="add-and-remove-nodes"></a>Adicionar e remover nós

Pode adicionar ou remover nós para o cluster do Service Fabric autónomo, conforme as necessidades da empresa são alteradas. Veja [Adicionar ou Remover nós para um cluster autónomo do Service Fabric](service-fabric-cluster-windows-server-add-remove-nodes.md) para obter passos detalhados.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu sobre o upload de grandes quantidades de dados aleatórios para uma conta de armazenamento em paralelo, como como:

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
