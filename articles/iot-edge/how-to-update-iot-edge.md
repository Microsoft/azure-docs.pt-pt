---
title: Atualizar versão IoT Edge em dispositivos - Azure IoT Edge / Microsoft Docs
description: Como atualizar os dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do tempo de execução IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/22/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7cde23372f6a3af0320e2d48c78a0d7fe69a2600
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045708"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualizar o daemon de segurança e o tempo de execução do IoT Edge

À medida que o serviço IoT Edge lança novas versões, irá querer atualizar os seus dispositivos IoT Edge para as mais recentes funcionalidades e melhorias de segurança. Este artigo fornece informações sobre como atualizar os seus dispositivos IoT Edge quando uma nova versão está disponível.

Dois componentes de um dispositivo IoT Edge precisam de ser atualizados se quiser passar para uma versão mais recente. O primeiro é o daemon de segurança, que funciona no dispositivo e inicia os módulos de tempo de funcionação quando o dispositivo começa. Atualmente, o daemon de segurança só pode ser atualizado a partir do próprio dispositivo. O segundo componente é o tempo de execução, composto pelos módulos de agente IoT Edge e IoT Edge. Dependendo da forma como estrutura a sua implementação, o tempo de funcionamento pode ser atualizado a partir do dispositivo ou remotamente.

Para encontrar a versão mais recente do Azure IoT Edge, consulte as [versões do Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

## <a name="update-the-security-daemon"></a>Atualize o daemon de segurança

O daemon de segurança IoT Edge é um componente nativo que precisa de ser atualizado usando o gestor de pacotes no dispositivo IoT Edge.

Verifique a versão do daemon de segurança em execução no seu dispositivo utilizando o comando `iotedge version` .

### <a name="linux-devices"></a>Dispositivos Linux

Nos dispositivos Linux x64, utilize o apt-get ou o seu gestor de pacotes apropriado para atualizar o daemon de segurança para a versão mais recente.

Obtenha a mais recente configuração de repositório da Microsoft:

* **Servidor Ubuntu 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Servidor Ubuntu 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Estirpe raspbiana:**

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Copie a lista gerada.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Atualizar apt.

   ```bash
   sudo apt-get update
   ```

Verifique quais as versões do IoT Edge disponíveis.

   ```bash
   apt list -a iotedge
   ```

Se pretender atualizar para a versão mais recente do daemon de segurança, utilize o seguinte comando que também atualiza **a secção libiothsm para** a versão mais recente:

   ```bash
   sudo apt-get install iotedge
   ```

Se pretender atualizar para uma versão específica do daemon de segurança, especifique a versão a partir da saída da lista apta. Sempre que **o iotedge** é atualizado, tenta automaticamente atualizar o pacote **libiothsm-std** para a sua versão mais recente, o que pode causar um conflito de dependência. Se não for à versão mais recente, não se esqueça de direcionar ambos os pacotes para a mesma versão. Por exemplo, o seguinte comando instala uma versão específica da versão 1.0.9:

   ```bash
   sudo apt-get install iotedge=1.0.9-1 libiothsm-std=1.0.9-1
   ```

Se a versão que pretende instalar não estiver disponível através do apt-get, pode utilizar o curl para direcionar qualquer versão do [repositório IoT Edge.](https://github.com/Azure/azure-iotedge/releases) Para qualquer versão que pretenda instalar, localize os **ficheiros libiothsm-std apropriados** e **iotedge** para o seu dispositivo. Para cada ficheiro, clique com o botão direito no link do ficheiro e copie o endereço de link. Utilize o endereço de ligação para instalar as versões específicas desses componentes:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Dispositivos Windows

Nos dispositivos Windows, utilize o script PowerShell para atualizar o daemon de segurança. O script puxa automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

Executar o comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens do recipiente de tempo de execução. O ficheiro config.yaml é mantido no dispositivo, bem como dados do motor de contentores Moby (se estiver a utilizar recipientes Windows). Manter as informações de configuração significa que não tem de fornecer novamente informações sobre a cadeia de ligação ou serviço de fornecimento de dispositivos para o seu dispositivo durante o processo de atualização.

Se pretender atualizar para uma versão específica do daemon de segurança, encontre a versão que pretende direcionar a partir das [versões IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue o ficheiro **Microsoft-Azure-IoTEdge.cab.** Em seguida, use o `-OfflineInstallationPath` parâmetro para apontar para a localização do ficheiro local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um ficheiro chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. A partir da versão IoT Edge 1.0.9-rc4, existem dois ficheiros .cab disponíveis para usar, um para dispositivos AMD64 e outro para ARM32. Faça o download do ficheiro correto para o seu dispositivo e, em seguida, mude o nome do ficheiro para remover o sufixo de arquitetura.

Para obter mais informações sobre opções de atualização, utilize o comando ou consulte o `Get-Help Update-IoTEdge -full` [script PowerShell para IoT Edge no Windows](reference-windows-scripts.md).

## <a name="update-the-runtime-containers"></a>Atualizar os recipientes de tempo de execução

A forma como atualiza os recipientes do ioT Edge e do hub IoT Edge depende se utiliza etiquetas rolantes (como 1.0) ou tags específicas (como 1.0.7) na sua implantação.

Verifique a versão dos módulos IoT Edge e hub IoT Edge atualmente no seu dispositivo utilizando os comandos `iotedge logs edgeAgent` ou `iotedge logs edgeHub` .

  ![Encontre a versão do recipiente em troncos](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Compreender tags IoT Edge

As imagens IoT Edge e IoT Edge estão marcadas com a versão IoT Edge a que estão associadas. Existem duas maneiras diferentes de usar tags com as imagens de tempo de execução:

* **Etiquetas rolantes** - Utilize apenas os dois primeiros valores do número da versão para obter a imagem mais recente que corresponda a esses dígitos. Por exemplo, o 1.0 é atualizado sempre que há um novo lançamento para apontar para a versão mais recente de 1.0.x. Se o tempo de funcionamento do contentor no seu dispositivo IoT Edge voltar a puxar a imagem, os módulos de tempo de execução são atualizados para a versão mais recente. Esta abordagem é sugerida para fins de desenvolvimento. Implementações do portal Azure por defeito para tags rolantes.

* **Etiquetas específicas** - Utilize os três valores do número da versão para definir explicitamente a versão de imagem. Por exemplo, 1.0.7 não mudará após o lançamento inicial. Pode declarar um novo número de versão no manifesto de implementação quando estiver pronto para atualizar. Esta abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de marca rolante

Se utilizar etiquetas rolantes na sua colocação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), então tem de forçar o tempo de funcionamento do contentor no seu dispositivo para retirar a versão mais recente da imagem.

Elimine a versão local da imagem do seu dispositivo IoT Edge. Nas máquinas do Windows, desinstalar o daemon de segurança também remove as imagens de tempo de execução, pelo que não precisa de dar este passo novamente.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Pode ser necessário usar a bandeira de força `-f` para remover as imagens.

O serviço IoT Edge irá retirar as versões mais recentes das imagens de tempo de execução e reiniciá-las automaticamente no seu dispositivo.

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem de marcação específica

Se utilizar etiquetas específicas na sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.8),** então tudo o que precisa de fazer é atualizar a etiqueta no seu manifesto de implantação e aplicar as alterações no seu dispositivo.

1. No Hub IoT no portal Azure, selecione o seu dispositivo IoT Edge e selecione **set Modules**.

1. Na secção **módulos IoT Edge Modules,** selecione **Definições de tempo de execução**.

   ![Configurar configurações de tempo de execução](./media/how-to-update-iot-edge/configure-runtime.png)

1. Em **Definições de tempo de**execução, atualize o valor de **imagem** para **Edge Hub** com a versão desejada. Não selecione **Save** ainda.

   ![Atualizar versão Edge Hub Image](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Desabar as definições do **Edge Hub,** ou desloque-se para baixo e atualize o valor **de imagem** para o **Edge Agent** com a mesma versão desejada.

   ![Atualizar versão do Agente Edge Hub](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selecione **Guardar**.

1. Selecione **Rever + criar,** rever a implementação e selecione **Criar**.

## <a name="update-offline-or-to-a-specific-version"></a>Atualizar offline ou para uma versão específica

Se pretender atualizar um dispositivo offline ou atualizar para uma versão específica do IoT Edge em vez da versão mais recente, pode fazê-lo com o `-OfflineInstallationPath` parâmetro.

Dois componentes são utilizados para atualizar um dispositivo IoT Edge:

* Um script PowerShell, que contém as instruções de instalação
* Cabine Microsoft Azure IoT Edge, que contém o daemon de segurança IoT Edge (iotedged), motor de contentores Moby, e Moby CLI

1. Para obter os mais recentes ficheiros de instalação IoT Edge juntamente com versões anteriores, consulte as [versões Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

2. Encontre a versão que pretende instalar e descarregue os seguintes ficheiros da secção **Ativos** das notas de lançamento para o seu dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab dos lançamentos 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab dos lançamentos 1.0.8 ou mais antigos.

   Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir de 1.0.9 apenas para fins de teste. O IoT Edge não é suportado atualmente em dispositivos Windows ARM32.

   É importante utilizar o script PowerShell a partir da mesma versão que o ficheiro .cabin que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

3. Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Para atualizar com componentes offline, [ponto fonte](/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) da cópia local do script PowerShell. Em seguida, use o `-OfflineInstallationPath` parâmetro como parte do comando e forneça o caminho absoluto para o `Update-IoTEdge` diretório de ficheiros. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Atualização para uma versão de candidato de lançamento

O Azure IoT Edge lança regularmente novas versões do serviço IoT Edge. Antes de cada lançamento estável, há uma ou mais versões de candidatos de lançamento (RC). As versões RC incluem todas as funcionalidades planeadas para o lançamento, mas ainda estão a passar por testes e validação. Se quiser testar uma nova funcionalidade mais cedo, pode instalar uma versão RC e fornecer feedback através do GitHub.

As versões de candidatos de lançamento seguem a mesma convenção de numeração de lançamentos, mas têm **-rc** mais um número incremental anexado ao final. Pode ver os candidatos de lançamento na mesma lista de lançamentos do [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) que as versões estáveis. Por exemplo, encontre **1.0.9-rc5** e **1.0.9-rc6**, dois dos candidatos de libertação que vieram antes de **1.0.9**. Também pode ver que as versões RC estão marcadas com etiquetas **pré-lançamento.**

O agente IoT Edge e os módulos hub têm versões RC que estão marcadas com a mesma convenção. Por exemplo, **mcr.microsoft.com/azureiotedge-hub:1.0.9-rc6.**

Como pré-visualizações, as versões de lançamento dos candidatos não estão incluídas como a versão mais recente que os instaladores regulares visam. Em vez disso, tem de direcionar manualmente os ativos para a versão RC que pretende testar. Na maior parte das vezes, instalar ou atualizar para uma versão RC é o mesmo que visar qualquer outra versão específica do IoT Edge.

Utilize as secções deste artigo para aprender a atualizar um dispositivo IoT Edge para uma versão específica dos módulos de segurança ou de tempo de execução.

Se estiver a instalar o IoT Edge, em vez de atualizar uma instalação existente, utilize os passos na [instalação de versão offline ou específica](how-to-install-iot-edge.md#offline-or-specific-version-installation).

## <a name="next-steps"></a>Passos seguintes

Veja as versões mais recentes do [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases).

Mantenha-se atualizado com as recentes atualizações e anúncios no [blog da Internet das Coisas](https://azure.microsoft.com/blog/topics/internet-of-things/)