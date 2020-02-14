---
title: Versão da atualização do IoT Edge em dispositivos - Azure IoT Edge | Documentos da Microsoft
description: Como atualizar os dispositivos de IoT Edge para executar as versões mais recentes do daemon de segurança e o runtime do IoT Edge
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/07/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4a7c27beeb7208efcf6687e49193c8d3b68f5300
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186517"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>O daemon de segurança de IoT Edge e o tempo de execução de atualização

À medida que o serviço IoT Edge lança novas versões, irá querer atualizar os seus dispositivos IoT Edge para as mais recentes funcionalidades e melhorias de segurança. Este artigo fornece informações sobre como atualizar os seus dispositivos IoT Edge quando uma nova versão estiver disponível.

Dois componentes de um dispositivo IoT Edge precisam ser atualizados, se pretender mover para uma versão mais recente. O primeiro é o daemon de segurança, que funciona no dispositivo e inicia os módulos de tempo de funcionação quando o dispositivo começa. Atualmente, o daemon de segurança só pode ser atualizado partir do próprio dispositivo. O segundo componente é o tempo de funcionamento, composto pelo hub IoT Edge e módulos de agente IoT Edge. Dependendo da forma como estrutura sua implementação, o tempo de execução pode ser atualizado do dispositivo ou remotamente.

Para encontrar a versão mais recente do Azure IoT Edge, consulte os [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

## <a name="update-the-security-daemon"></a>Atualizar o daemon de segurança

O daemon de segurança de IoT Edge é um componente nativo que tem de ser atualizado utilizando o Gestor de pacotes do dispositivo IoT Edge.

Verifique a versão do daemon de segurança em execução no seu dispositivo utilizando o comando `iotedge version`.

### <a name="linux-devices"></a>Dispositivos do Linux

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

Se pretender atualizar para uma versão específica do daemon de segurança, encontre a versão que pretende direcionar a partir dos lançamentos do [IoT Edge](https://github.com/Azure/azure-iotedge/releases). Nessa versão, descarregue o ficheiro **Microsoft-Azure-IoTEdge.cab.** Em seguida, utilize o parâmetro `-OfflineInstallationPath` para indicar a localização do ficheiro local. Por exemplo:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -ContainerOs <Windows or Linux> -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>O parâmetro `-OfflineInstallationPath` procura um ficheiro chamado **Microsoft-Azure-IoTEdge.cab** no diretório fornecido. Começando com a versão 1.0.9-rc4 do IoT Edge, existem dois ficheiros .cab disponíveis para utilização, um para dispositivos AMD64 e um para ARM32. Descarregue o ficheiro correto para o seu dispositivo e, em seguida, mude o nome do ficheiro para remover o sufixo de arquitetura.

Para mais informações sobre as opções de atualização, utilize o comando `Get-Help Update-IoTEdge -full` ou consulte [todos os parâmetros](how-to-install-iot-edge-windows.md#all-installation-parameters)de instalação .

## <a name="update-the-runtime-containers"></a>Atualizar os contentores de tempo de execução

A forma como atualiza o agente IoT Edge e os recipientes do hub IoT Edge depende se utiliza etiquetas rolantes (como 1.0) ou etiquetas específicas (como 1.0.7) na sua implantação.

Verifique a versão do agente IoT Edge e dos módulos de hub IoT Edge atualmente no seu dispositivo utilizando os comandos `iotedge logs edgeAgent` ou `iotedge logs edgeHub`.

  ![Encontrar a versão do contentor nos registos](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Compreenda as marcas de IoT Edge

O agente IoT Edge e as imagens do hub IoT Edge estão marcados com a versão IoT Edge com a que estão associados. Existem duas formas diferentes de utilizar etiquetas com as imagens de tempo de execução:

* **Rolling tags** - Utilize apenas os dois primeiros valores do número da versão para obter a imagem mais recente que corresponda a esses dígitos. Por exemplo, 1.0 é atualizada sempre que houver uma nova versão para apontar para a versão mais recente do 1.0.x. Se o tempo de execução do contentor no seu dispositivo IoT Edge extraí a imagem, os módulos de tempo de execução são atualizados para a versão mais recente. Essa abordagem é sugerida para fins de desenvolvimento. Implementações a partir da predefinição do portal do Azure para implementar as etiquetas.

* **Etiquetas específicas** - Utilize os três valores do número da versão para definir explicitamente a versão da imagem. Por exemplo, 1.0.7 não mudará após o seu lançamento inicial. É possível declarar um novo número de versão no manifesto de implantação, quando estiver pronto para atualizar. Essa abordagem é sugerida para fins de produção.

### <a name="update-a-rolling-tag-image"></a>Atualizar uma imagem de marca sem interrupção

Se utilizar etiquetas rolantes na sua implantação (por exemplo, mcr.microsoft.com/azureiotedge-hub:**1.0**), então terá de forçar o tempo de execução do recipiente no seu dispositivo para puxar a versão mais recente da imagem.

Elimine a versão local da imagem do seu dispositivo IoT Edge. Nas máquinas Windows, desinstalar o daemon de segurança também remove as imagens de tempo de execução, pelo que não precisa de dar este passo novamente.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

Pode ser necessário utilizar a força `-f` bandeira para remover as imagens.

O serviço de IoT Edge irá extrair as versões mais recentes das imagens de tempo de execução e automaticamente iniciá-los no seu dispositivo novamente.

### <a name="update-a-specific-tag-image"></a>Atualizar uma imagem da etiqueta específica

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

## <a name="update-to-a-release-candidate-version"></a>Atualizar para uma versão candidata de lançamento

O Azure IoT Edge lança regularmente novas versões do serviço IoT Edge. Antes de cada lançamento estável, há uma ou mais versões candidatas a lançamento (RC). As versões RC incluem todas as funcionalidades planeadas para o lançamento, mas ainda estão a ser testadas e validadas. Se quiser testar uma nova funcionalidade mais cedo, pode instalar uma versão RC e fornecer feedback através do GitHub.

As versões candidatas de lançamento seguem a mesma convenção de lançamentos de números, mas têm **-rc** mais um número incremental anexado ao fim. Pode ver os candidatos de lançamento na mesma lista de lançamentos do [Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases) que as versões estáveis. Por exemplo, encontrar **1.0.7-rc1** e **1.0.7-rc2**, os dois candidatos de lançamento que vieram antes de **1.0.7**. Também pode ver que as versões RC estão marcadas com etiquetas **de pré-lançamento.**

O agente IoT Edge e os módulos hub têm versões RC que são marcadas com a mesma convenção. Por exemplo, **mcr.microsoft.com/azureiotedge-hub:1.0.7-rc2.**

Como pré-visualizações, as versões candidatas de lançamento não estão incluídas como a versão mais recente que os instaladores regulares têm como alvo. Em vez disso, tem de direcionar manualmente os ativos para a versão RC que pretende testar. Na maior parte das vezes, instalar ou atualizar para uma versão RC é o mesmo que direcionar qualquer outra versão específica do IoT Edge, exceto uma diferença para dispositivos Windows. 

Num candidato de lançamento, o script PowerShell que lhe permite instalar e gerir o daemon de segurança IoT Edge num dispositivo Windows pode ter uma funcionalidade diferente da versão mais recente disponível. Além de descarregar o ficheiro IoT Edge .cab para o RC, também descarregue o script **IotEdgeSecurityDaemon.ps1.** Utilize o fornecimento de [pontos](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_scripts?view=powershell-7#script-scope-and-dot-sourcing) para executar o script descarregado na fonte atual. Por exemplo: 

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Utilize as secções deste artigo para aprender a atualizar um dispositivo IoT Edge para uma versão específica dos módulos de daemon de segurança ou de tempo de execução.

Se estiver a instalar o IoT Edge numa nova máquina, utilize os seguintes links para aprender a instalar uma versão específica dependendo do sistema operativo do dispositivo:

* [Linux](how-to-install-iot-edge-linux.md#install-a-specific-runtime-version)
* [Windows](how-to-install-iot-edge-windows.md#offline-or-specific-version-installation)

## <a name="next-steps"></a>Passos seguintes

Veja os mais recentes [lançamentos do Azure IoT Edge.](https://github.com/Azure/azure-iotedge/releases)

Mantenha-se atualizado com atualizações recentes e anúncio no [blog Internet of Things](https://azure.microsoft.com/blog/topics/internet-of-things/)
