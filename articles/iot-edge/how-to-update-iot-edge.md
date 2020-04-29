---
title: Versão IoT Edge atualizada nos dispositivos - Azure IoT Edge / Microsoft Docs
description: Como atualizar os dispositivos IoT Edge para executar as versões mais recentes do daemon de segurança e do tempo de execução ioT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ce69593c1df0039d64f89e79124af1150409eff7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113308"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>Atualizar o daemon de segurança e o tempo de execução do IoT Edge

À medida que o serviço IoT Edge lança novas versões, irá querer atualizar os seus dispositivos IoT Edge para as mais recentes funcionalidades e melhorias de segurança. Este artigo fornece informações sobre como atualizar os seus dispositivos IoT Edge quando uma nova versão está disponível.

Dois componentes de um dispositivo IoT Edge precisam de ser atualizados se quiser passar para uma versão mais recente. O primeiro é o daemon de segurança, que funciona no dispositivo e inicia os módulos de tempo de funcionação quando o dispositivo começa. Atualmente, o daemon de segurança só pode ser atualizado a partir do próprio dispositivo. O segundo componente é o tempo de funcionamento, composto pelo hub IoT Edge e módulos de agente IoT Edge. Dependendo da forma como estrutura a sua implementação, o tempo de funcionamento pode ser atualizado a partir do dispositivo ou remotamente.

Para encontrar a versão mais recente do Azure IoT Edge, consulte os [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança IoT Edge é um componente nativo que precisa de ser atualizado usando o gestor de pacotes no dispositivo IoT Edge.

Verifique a versão do daemon de segurança em `iotedge version`execução no seu dispositivo utilizando o comando .

### <a name="linux-devices"></a>Dispositivos Linux

Nos dispositivos Linux x64, utilize o apt-get ou o seu gestor de pacotes apropriado para atualizar o daemon de segurança para a versão mais recente.

```bash
apt-get update
apt-get install libiothsm iotedge
```

Se pretender atualizar para uma versão específica do daemon de segurança, encontre a versão que pretende direcionar a partir dos lançamentos do [IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, localize os **ficheiros adequados de libiothsm-std** e **iotedge** para o seu dispositivo. Para cada ficheiro, clique no link de ficheiro e copie o endereço de link. Utilize o endereço de ligação para instalar as versões específicas desses componentes:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
```

### <a name="windows-devices"></a>Dispositivos Windows

Nos dispositivos Windows, utilize o script PowerShell para atualizar o daemon de segurança. O script puxa automaticamente a versão mais recente do daemon de segurança.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux>
```

A execução do comando Update-IoTEdge remove e atualiza o daemon de segurança do seu dispositivo, juntamente com as duas imagens do recipiente de tempo de funcionamento. O ficheiro config.yaml é mantido no dispositivo, bem como dados do motor de contentormo Moby (se estiver a utilizar recipientes Windows). Manter as informações de configuração significa que não tem de fornecer novamente as informações do Serviço de Fornecimento de Fios de Ligação ou de Dispositivos para o seu dispositivo durante o processo de atualização.

Se pretender atualizar para uma versão específica do daemon de segurança, encontre a versão que pretende direcionar a partir dos lançamentos do [IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue o ficheiro **Microsoft-Azure-IoTEdge.cab.** Em seguida, `-OfflineInstallationPath` utilize o parâmetro para indicar a localização do ficheiro local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O `-OfflineInstallationPath` parâmetro procura um ficheiro chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Começando com a versão 1.0.9-rc4 do IoT Edge, existem dois ficheiros .cab disponíveis para utilização, um para dispositivos AMD64 e um para ARM32. Descarregue o ficheiro correto para o seu dispositivo e, em seguida, mude o nome do ficheiro para remover o sufixo de arquitetura.

Para mais informações sobre as `Get-Help Update-IoTEdge -full` opções de atualização, utilize o comando ou consulte [todos os parâmetros](how-to-install-iot-edge-windows.md#all-installation-parameters)de instalação .

## <a name="update-the-runtime-containers"></a>Atualizar os recipientes de tempo de execução

A forma como atualiza o agente IoT Edge e os recipientes do hub IoT Edge depende se utiliza etiquetas rolantes (como 1.0) ou etiquetas específicas (como 1.0.7) na sua implantação.

Verifique a versão do agente IoT Edge e dos módulos de hub `iotedge logs edgeAgent` IoT Edge atualmente no seu dispositivo utilizando os comandos ou `iotedge logs edgeHub`.

  ![Encontre a versão do recipiente em diários de bordo](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Compreenda as tags IoT Edge

O agente IoT Edge e as imagens do hub IoT Edge estão marcados com a versão IoT Edge com a que estão associados. Existem duas maneiras diferentes de usar etiquetas com as imagens de tempo de execução:

* **Rolling tags** - Utilize apenas os dois primeiros valores do número da versão para obter a imagem mais recente que corresponda a esses dígitos. Por exemplo, o 1.0 é atualizado sempre que há um novo lançamento para apontar para a versão mais recente de 1.0.x. Se o tempo de funcionamento do recipiente no seu dispositivo IoT Edge puxar novamente a imagem, os módulos de tempo de funcionamento são atualizados para a versão mais recente. Esta abordagem é sugerida para fins de desenvolvimento. As implantações do portal Azure padrão para as etiquetas de rolamento.

* **Etiquetas específicas** - Utilize os três valores do número da versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.7 não mudará após o seu lançamento inicial. Pode declarar um novo número de versão no manifesto de implementação quando estiver pronto para atualizar. Esta abordagem é sugerida para fins produtivos.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de tag rolante

Se utilizar etiquetas rolantes na sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), então terá de forçar o tempo de execução do recipiente no seu dispositivo para puxar a versão mais recente da imagem.

Elimine a versão local da imagem do seu dispositivo IoT Edge. Nas máquinas Windows, desinstalar o daemon de segurança também remove as imagens de tempo de execução, pelo que não precisa de dar este passo novamente.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Pode ser necessário utilizar `-f` a bandeira de força para remover as imagens.

O serviço IoT Edge irá puxar as versões mais recentes das imagens de tempo de execução e ligá-las automaticamente no seu dispositivo novamente.

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem de etiqueta específica

Se utilizar etiquetas específicas na sua implementação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0.8**), tudo o que precisa de fazer é atualizar a etiqueta no seu manifesto de implementação e aplicar as alterações no seu dispositivo.

1. No IoT Hub no portal Azure, selecione o seu dispositivo IoT Edge e selecione **Módulos set**.

1. Na secção **Módulos IoT Edge,** selecione **Definições de tempo**de execução .

   ![Configurar as definições de tempo de execução](./media/how-to-update-iot-edge/configure-runtime.png)

1. Em Definições de Tempo de **Execução,** atualize o valor **de imagem** para **edge hub** com a versão desejada. Não selecione **Save** ainda.

   ![Versão update Edge Hub Image](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Destrua as definições **do Edge Hub,** ou desloque-se para baixo e atualize o valor **de imagem** para o Agente **Edge** com a mesma versão desejada.

   ![Versão update Edge Hub Agent](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Selecione **Guardar**.

1. Selecione **Rever + criar,** rever a implementação e selecionar **Criar**.

## <a name="update-offline-or-to-a-specific-version"></a>Atualizar offline ou para uma versão específica

Se pretender atualizar um dispositivo offline, ou atualizar para uma versão específica do IoT Edge em `-OfflineInstallationPath` vez da versão mais recente, pode fazê-lo com o parâmetro.

Dois componentes são usados para atualizar um dispositivo IoT Edge:

* Um script PowerShell, que contém as instruções de instalação
* Cabine Microsoft Azure IoT Edge, que contém o daemon de segurança IoT Edge (iotedged), motor de contentorMo by e Moby CLI

1. Para obter os mais recentes ficheiros de instalação IoT Edge juntamente com versões anteriores, consulte [lançamentos Do Edge Azure IoT.](https://github.com/Azure/azure-iotedge/releases)

2. Encontre a versão que pretende instalar e descarregue os seguintes ficheiros da secção **Assets** das notas de lançamento no seu dispositivo IoT:

   * IoTEdgeSecurityDaemon.ps1
   * Microsoft-Azure-IoTEdge-amd64.cab a partir de lançamentos 1.0.9 ou mais recentes, ou Microsoft-Azure-IoTEdge.cab a partir dos lançamentos 1.0.8 ou mais velhos.

   O Microsoft-Azure-IotEdge-arm32.cab também está disponível a partir de 1.0.9 apenas para efeitos de teste. O IoT Edge não é atualmente suportado em dispositivos ARM32 do Windows.

   É importante utilizar o script PowerShell a partir do mesmo lançamento que o ficheiro .cab que utiliza porque a funcionalidade muda para suportar as funcionalidades em cada versão.

3. Se o ficheiro .cab que descarregou tiver um sufixo de arquitetura, mude o nome do ficheiro para apenas **Microsoft-Azure-IoTEdge.cab**.

4. Para atualizar com componentes offline, [faça o ponto de origem](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) da cópia local do script PowerShell. Em seguida, `-OfflineInstallationPath` use o parâmetro `Update-IoTEdge` como parte do comando e forneça o caminho absoluto para o diretório de ficheiros. Por exemplo,

   ```powershell
   . <path>\IoTEdgeSecurityDaemon.ps1
   Update-IoTEdge -OfflineInstallationPath <path>
   ```

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão candidata de lançamento

O Azure IoT Edge lança regularmente novas versões do serviço IoT Edge. Antes de cada lançamento estável, há uma ou mais versões candidatas a lançamento (RC). As versões RC incluem todas as funcionalidades planeadas para o lançamento, mas ainda estão a ser testadas e validadas. Se quiser testar uma nova funcionalidade mais cedo, pode instalar uma versão RC e fornecer feedback através do GitHub.

As versões candidatas de lançamento seguem a mesma convenção de lançamentos de números, mas têm **-rc** mais um número incremental anexado ao fim. Pode ver os candidatos de lançamento na mesma lista de lançamentos do [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) que as versões estáveis. Por exemplo, encontrar **1.0.7-rc1** e **1.0.7-rc2**, os dois candidatos de lançamento que vieram antes de **1.0.7**. Também pode ver que as versões RC estão marcadas com etiquetas **de pré-lançamento.**

O agente IoT Edge e os módulos hub têm versões RC que são marcadas com a mesma convenção. Por exemplo, **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2.**

Como pré-visualizações, as versões candidatas de lançamento não estão incluídas como a versão mais recente que os instaladores regulares têm como alvo. Em vez disso, tem de direcionar manualmente os ativos para a versão RC que pretende testar. Na maior parte das vezes, instalar ou atualizar para uma versão RC é o mesmo que visar qualquer outra versão específica do IoT Edge.

Utilize as secções deste artigo para aprender a atualizar um dispositivo IoT Edge para uma versão específica dos módulos de daemon de segurança ou de tempo de execução.

Se estiver a instalar o IoT Edge numa nova máquina, utilize os seguintes links para aprender a instalar uma versão específica dependendo do sistema operativo do dispositivo:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Passos seguintes

Veja os mais recentes [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

Mantenha-se atualizado com atualizações recentes e anúncio no [blog Internet of Things](https://azure.microsoft.com/blog/topics/internet-of-things/)
