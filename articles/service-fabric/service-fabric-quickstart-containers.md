---
title: Criar uma aplicação de contentor do Windows no Service Fabric no Azure | Microsoft Docs
description: Neste guia de introdução, crie a sua primeira aplicação de contentor do Windows no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: jpconnock
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 096a398b8fc4f7f42dcc42feb7fe00b182d7649b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68599368"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Início rápido: Implantar contêineres do Windows para Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos par implementar e gerir microsserviços e contentores dimensionáveis e fiáveis.

Para executar uma aplicação existente num contentor do Windows num cluster do Service Fabric, não precisa de fazer quaisquer alterações à sua aplicação. Este guia de início rápido mostra como implementar uma imagem de contentor do Docker pré-concebida numa aplicação do Service Fabric. Quando tiver terminado, você terá um servidor do Windows Server Core 2016 em execução e o contêiner do IIS. Este guia de início rápido descreve a implantação de um contêiner do Windows. Leia [este guia de início rápido](service-fabric-quickstart-containers-linux.md) para implantar um contêiner do Linux.

![Página Web do IIS predefinida][iis-default]

Neste início rápido, vai aprender a:

* Empacotar um contentor de imagens do Docker
* Configurar a comunicação
* Criar e empacotar a aplicação do Service Fabric
* Implementar a aplicação de contentor no Azure


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure (pode criar uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Um computador de programação com:
  * Visual Studio 2019 ou Windows 2019.
  * [SDK e ferramentas do Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Empacotar um contentor de imagens do Docker com o Visual Studio

O SDK e as ferramentas do Service Fabric fornecem um modelo de serviço para o ajudar a implementar um contentor num cluster do Service Fabric.

Inicie o Visual Studio como “Administrador”.  Selecione **Ficheiro** > **Novo** > **Projeto**.

Selecione **Service Fabric aplicativo**, nomeie-o como "MyFirstContainer" e clique em **criar**.

Selecione **Contentor** nos modelos de **Contentores e Aplicações Alojados**.

Em **nome da imagem**, insira "MCR.Microsoft.com/Windows/servercore/IIS:windowsservercore-ltsc2016", o [servidor do Windows Server Core e a imagem base do IIS](https://hub.docker.com/_/microsoft-windows-servercore-iis).

Configure o mapeamento de portas, da porta para o anfitrião do contentor, de modo que os pedidos recebidos para o serviço na porta 80 sejam mapeados para a porta 80 no contentor.  Defina a **Porta de Contentor** para "80" e defina a **Porta do Anfitrião** para "80".  

Dê ao serviço o nome "MyContainerService" e clique em **OK**.

![Caixa de diálogo do novo serviço][new-service]

## <a name="specify-the-os-build-for-your-container-image"></a>Especificar a compilação do SO para a imagem do contentor

Os contentores criados com uma versão específica do Windows Server poderão não funcionar num anfitrião com uma versão diferente do Windows Server. Por exemplo, os contêineres criados usando o Windows Server versão 1709 não são executados em hosts que executam o Windows Server 2016. Para obter mais informações, veja [Compatibilidade do sistema operativo do contentor do Windows Server e do sistema operativo do sistema anfitrião ](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Com a versão 6.1 do runtime do Service Fabric e com versões mais recentes, pode especificar várias imagens de sistema operativo por contentor e etiquetar cada uma com a versão do sistema operativo para a qual deve ser implementada. Este procedimento ajuda-o a verificar se a aplicação funcionará em sistemas anfitriões com versões diferentes do sistema operativo Windows. Para saber mais, veja [Indicar imagens de contentor específicas da compilação de SO](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

A Microsoft publica imagens diferentes para as versões do IIS criadas em diferentes versões do Windows Server. Para verificar se o Service Fabric implementa um contentor compatível com a versão do Windows Server que em execução nos nós do cluster onde implementa a aplicação, adicione as seguintes linhas ao ficheiro *ApplicationManifest.xml*. A versão de compilação do Windows Server 2016 é 14393 e a da versão 1709 do Windows Server é 16299.

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1803" /> 
          <Image Name= "mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016" Os="14393" /> 
          <Image Name="mcr.microsoft.com/windows/servercore/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

O manifesto de serviço continua a especificar apenas uma imagem para o Nano Server, `mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2016`.

Também no arquivo *ApplicationManifest. xml* , altere **PasswordEncrypted** para **false**. A conta e a senha estão em branco para a imagem de contêiner pública que está no Hub do Docker, portanto, desativamos a criptografia porque a criptografia de uma senha em branco gerará um erro de compilação.

```xml
<RepositoryCredentials AccountName="" Password="" PasswordEncrypted="false" />
```

## <a name="create-a-cluster"></a>Criar um cluster

O script de exemplo a seguir cria um cluster de cinco nós Service Fabric protegido com um certificado X. 509. O comando cria um certificado autoassinado e carrega-o para um novo cofre de chaves. O certificado é também copiado para um diretório local. Você pode saber mais sobre como criar um cluster usando esse script em [criar um Service Fabric cluster](scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Se necessário, instale o Azure PowerShell usando as instruções encontradas no [Guia de Azure PowerShell](/powershell/azure/overview).

Antes de executar o script a seguir, no PowerShell `Connect-AzAccount` , execute para criar uma conexão com o Azure.

Copie o script a seguir na área de transferência e abra **ISE do Windows PowerShell**.  Cole o conteúdo na janela Untitled1. ps1 vazia. Em seguida, forneça valores para as variáveis no script `subscriptionId`: `certpwd` `certfolder`, `adminuser` `adminpwd`,,, e assim por diante.  O diretório especificado para `certfolder` deve existir antes de você executar o script.

[!code-powershell[main](../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

Depois de fornecer seus valores para as variáveis, pressione **F5** para executar o script.

Depois que o script for executado e o cluster for criado, `ClusterEndpoint` localize o na saída. Por exemplo:

```powershell
...
ClusterEndpoint : https://southcentralus.servicefabric.azure.com/runtime/clusters/b76e757d-0b97-4037-a184-9046a7c818c0
```

### <a name="install-the-certificate-for-the-cluster"></a>Instalar o certificado para o cluster

Agora, instalaremos o PFX no repositório de certificados do *CurrentUser\My* . O arquivo PFX estará no diretório especificado usando a `certfolder` variável de ambiente no script do PowerShell acima.

Altere para esse diretório e, em seguida, execute o seguinte comando do PowerShell, substituindo o nome do arquivo PFX que `certfolder` está em seu diretório e a senha que você especificou `certpwd` na variável. Neste exemplo, o diretório atual é definido como o diretório especificado pela `certfolder` variável no script do PowerShell. A partir daí `Import-PfxCertificate` , o comando é executado:

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\mysfclustergroup20190130193456.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString Password#1234 -AsPlainText -Force)
```

O comando retorna a impressão digital:

```powershell
  ...
  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0AC30A2FA770BEF566226CFCF75A6515D73FC686  CN=mysfcluster.SouthCentralUS.cloudapp.azure.com
```

Lembre-se do valor da impressão digital para a etapa a seguir.

## <a name="deploy-the-application-to-azure-using-visual-studio"></a>Implementar a aplicação no Azure com o Visual Studio

Agora que a aplicação está pronta, pode implementá-la num cluster diretamente a partir do Visual Studio.

Clique com o botão direito do rato em **MyFirstContainer**, no Explorador de Soluções, e escolha **Publicar**. É apresentada a caixa de diálogo Publicar.

Copie o conteúdo após **CN =** na janela do PowerShell quando você executou `Import-PfxCertificate` o comando acima e adicione a `19000` porta a ele. Por exemplo, `mysfcluster.SouthCentralUS.cloudapp.azure.com:19000`. Copie-o para o campo **ponto de extremidade de conexão** . Lembre-se desse valor porque você precisará dele em uma etapa futura.

Clique em **Parâmetros de Ligação Avançada** e verifique as informações dos parâmetros da ligação.  Os valores FindValue e *ServerCertThumbprint* devem corresponder à impressão digital do certificado instalado quando você `Import-PfxCertificate` executou na etapa anterior.

![Caixa de diálogo Publicar](./media/service-fabric-quickstart-containers/publish-app.png)

Clique em **Publicar**.

Cada aplicação no cluster tem de ter um nome exclusivo. Se houver um conflito de nome, renomeie o projeto do Visual Studio e implante novamente.

Abra um navegador e navegue até o endereço que você colocou no campo **ponto de extremidade de conexão** na etapa anterior. Opcionalmente, pode preceder o identificador do esquema, `http://`, e acrescentar a porta, `:80`, ao URL. Por exemplo, http:\//mysfcluster.SouthCentralUS.cloudapp.Azure.com:80.

 Você deverá ver a página da Web padrão do IIS: ![Página da Web padrão do IIS][iis-default]

## <a name="clean-up"></a>Limpeza

Você continuará a incorrer em encargos enquanto o cluster estiver em execução. Considere [excluir o cluster](service-fabric-cluster-delete.md).

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, aprendeu a:

* Empacotar um contentor de imagens do Docker
* Configurar a comunicação
* Criar e empacotar a aplicação do Service Fabric
* Implementar a aplicação de contentor no Azure

Para saber mais sobre como trabalhar com contentores do Windows no Service Fabric, avance para o tutorial para aplicações de contentor do Windows.

> [!div class="nextstepaction"]
> [Criar uma aplicação contentora do Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
