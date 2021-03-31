---
title: Scripts para Azure IoT Edge com recipientes Windows | Microsoft Docs
description: Informações de referência para scripts IoT Edge PowerShell para instalar, desinstalar ou atualizar em dispositivos Windows
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/06/2020
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a919238e4a62ae8954e101cb21a2fd4943191f6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103489529"
---
# <a name="powershell-scripts-for-iot-edge-with-windows-containers"></a>Scripts PowerShell para IoT Edge com recipientes Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Compreenda os scripts PowerShell que instalam, atualizam ou desinstalam o IoT Edge nos dispositivos Windows.

Os comandos descritos neste artigo são do `IoTEdgeSecurityDaemon.ps1` ficheiro que é lançado com cada versão [IoT Edge](https://github.com/Azure/azure-iotedge/releases). A versão mais recente do script está sempre disponível em aka.ms/iotedge-win.

Pode executar qualquer um dos comandos utilizando o `Invoke-WebRequest` cmdlet para aceder à versão mais recente do script. Por exemplo:

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge
```

Também pode descarregar este script, ou uma versão do script a partir de uma versão específica, para executar os comandos. Por exemplo:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Deploy-IoTEdge
```

O guião fornecido é assinado para aumentar a segurança. Pode verificar a assinatura descarregando o script para o seu dispositivo e executando o seguinte comando PowerShell:

```powershell
Get-AuthenticodeSignature "C:\<path>\IotEdgeSecurityDaemon.ps1"
```

O estado de saída é **válido** se a assinatura for verificada.

## <a name="deploy-iotedge"></a>Deploy-IoTEdge

O comando Deploy-IoTEdge descarrega e implementa o IoT Edge Security Daemon e as suas dependências. O comando de implantação aceita estes parâmetros comuns, entre outros. Para a lista completa, utilize o comando `Get-Help Deploy-IoTEdge -full` .  

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado nenhum sistema operativo de contentores, o Windows é o valor predefinido.<br><br>Para recipientes Windows, o IoT Edge utiliza o motor de contentores Moby incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentores antes de iniciar a instalação. |
| **Proxy** | URL de procuração | Inclua este parâmetro se o seu dispositivo precisar de passar por um servidor proxy para chegar à internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro for incluído, o instalador verificará o diretório listado para os ficheiros MSI de cabaça IoT Edge e VC runtime necessários para a instalação. Todos os ficheiros não encontrados no diretório são descarregados. Se ambos os ficheiros estiverem no diretório, pode instalar o IoT Edge sem uma ligação à Internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **Invocar WebRequestParameters** | Haxixe de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **RestartIfNeed** | nenhum | Esta bandeira permite que o script de implantação reinicie a máquina sem aviso, se necessário. |

## <a name="initialize-iotedge"></a>Initialize-IoTEdge

O comando Initialize-IoTEdge configura o IoT Edge com a cadeia de ligação do dispositivo e detalhes operacionais. Grande parte da informação gerada por este comando é então armazenada no ficheiro iotedge\config.yaml. O comando de inicialização aceita estes parâmetros comuns, entre outros. Para a lista completa, utilize o comando `Get-Help Initialize-IoTEdge -full` .

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **ManualConnectionString** | Nenhum | **Parâmetro de comutação**. **Valor predefinido**. Se não for especificado nenhum tipo de provisionamento, o provisionamento manual com uma cadeia de ligação é o valor predefinido.<br><br>Declara que irá fornecer uma cadeia de ligação do dispositivo para fornecer o dispositivo manualmente. |
| **ManualX509** | Nenhum | **Parâmetro de comutação**. Se não for especificado nenhum tipo de provisionamento, o provisionamento manual com uma cadeia de ligação é o valor predefinido.<br><br>Declara que irá fornecer um certificado de identidade e chave privada para fornecer o dispositivo manualmente.
| **DpsTpm** | Nenhum | **Parâmetro de comutação**. Se não for especificado nenhum tipo de provisionamento, o provisionamento manual com uma cadeia de ligação é o valor predefinido.<br><br>Declara que irá fornecer um ID de âmbito do Serviço de Provisionamento de Dispositivos (DPS) e o ID de Registo do seu dispositivo através de DPS.  |
| **DpsSymmetricKey** | Nenhum | **Parâmetro de comutação**. Se não for especificado nenhum tipo de provisionamento, o provisionamento manual com uma cadeia de ligação é o valor predefinido.<br><br>Declara que irá fornecer um ID de âmbito do Serviço de Provisionamento de Dispositivos (DPS) e o ID de Registo do seu dispositivo através de DPS, juntamente com uma chave simétrica para atestado. |
| **DpsX509** | Nenhum | **Parâmetro de comutação**. Se não for especificado nenhum tipo de provisionamento, o provisionamento manual com uma cadeia de ligação é o valor predefinido.<br><br>Declara que irá fornecer um ID de âmbito do Serviço de Provisionamento de Dispositivos (DPS) e o ID de Registo do seu dispositivo através de DPS, juntamente com um certificado de identidade X.509 e chave privada para atestado.  |
| **DeviceConnectionString** | Uma cadeia de ligação de um dispositivo IoT Edge registado num Hub IoT, em cotações únicas | **Necessário** para o provisionamento manual com uma cadeia de ligação. Se não fornecer uma cadeia de ligação nos parâmetros do script, será solicitado um. |
| **Nome IotHubHost** | O nome de anfitrião do hub IoT a que um dispositivo se conecta. | **Obrigatório** para o provisionamento manual com certificados X.509. Toma o formato *{nome hub}.azure-devices.net*. |
| **DeviceId** | O ID do dispositivo a partir de uma identidade de dispositivo registado no IoT Hub. | **Obrigatório** para o provisionamento manual com certificados X.509. |
| **ScopeId** | Uma identificação de âmbito a partir de uma instância do Serviço de Provisionamento de Dispositivos associado ao seu Hub IoT. | **Requerido** para o provisionamento de DPS. Se não fornecer uma identificação de âmbito nos parâmetros do script, será solicitado para um. |
| **RegistroD** | Um ID de registo gerado pelo seu dispositivo | **Requerido** provisões de DPS se utilizar atestado de teclas TPM ou simétricas. **Opcional** se utilizar o certificado X.509. |
| **X509IdentityCertificate** | O caminho URI para o certificado de identidade do dispositivo X.509 no dispositivo. | **Necessário** para o provisionamento manual ou DPS se utilizar o atestado de certificado X.509. |
| **X509IdentityPrivateKey** | O caminho URI para a chave de certificado de identidade do dispositivo X.509 no dispositivo. | **Necessário** para o provisionamento manual ou DPS se utilizar o atestado de certificado X.509. |
| **Chave Simétrica** | A chave simétrica utilizada para a disponibilização da identidade do dispositivo IoT Edge ao utilizar dPS | **Requerido** provisão de DPS se utilizar o atestado de chave simétrica. |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado nenhum sistema operativo de contentores, o Windows é o valor predefinido.<br><br>Para recipientes Windows, o IoT Edge utiliza o motor de contentores Moby incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentores antes de iniciar a instalação. |
| **Certificado de DispositivoSCA** | O caminho URI para o certificado CA do dispositivo X.509 no dispositivo. | Também pode ser configurado no `C:\ProgramData\iotedge\config.yaml` ficheiro. Para obter mais informações, consulte [Gerir os certificados num dispositivo IoT Edge](how-to-manage-device-certificates.md). |
| **DeviceCAPrivateKey** | O caminho URI para a chave privada ca dispositivo X.509 no dispositivo. | Também pode ser configurado no `C:\ProgramData\iotedge\config.yaml` ficheiro. Para obter mais informações, consulte [Gerir os certificados num dispositivo IoT Edge](how-to-manage-device-certificates.md). |
| **Invocar WebRequestParameters** | Haxixe de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Imagem de Agente** | IoT Edge imagem agente URI | Por predefinição, uma nova instalação IoT Edge utiliza a mais recente etiqueta rolante para a imagem do agente IoT Edge. Utilize este parâmetro para definir uma etiqueta específica para a versão de imagem, ou para fornecer a sua própria imagem de agente. Para obter mais informações, consulte [as etiquetas IoT Edge](how-to-update-iot-edge.md#understand-iot-edge-tags). |
| **Nome de Utilizador** | Nome de utilizador do registo de contentores | Utilize este parâmetro apenas se definir o parâmetro -AgentImage num contentor num registo privado. Fornecer um nome de utilizador com acesso ao registo. |
| **Palavra-passe** | Cadeia de senha segura | Utilize este parâmetro apenas se definir o parâmetro -AgentImage num contentor num registo privado. Forneça a senha para aceder ao registo. |

## <a name="update-iotedge"></a>Update-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Contentores** | **Janelas** ou **Linux** | Se não for especificado nenhum sistema operativo de contentores, o Windows é o valor predefinido. Para os recipientes windows, um motor de contentores será incluído na instalação. Para os recipientes Linux, é necessário instalar um motor de contentores antes de iniciar a instalação. |
| **Proxy** | URL de procuração | Inclua este parâmetro se o seu dispositivo precisar de passar por um servidor proxy para chegar à internet. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **Invocar WebRequestParameters** | Haxixe de parâmetros e valores | Durante a instalação, são feitos vários pedidos web. Utilize este campo para definir parâmetros para esses pedidos web. Este parâmetro é útil para configurar credenciais para servidores proxy. Para obter mais informações, consulte [configurar um dispositivo IoT Edge para comunicar através de um servidor proxy](how-to-configure-proxy-support.md). |
| **OfflineInstallationPath** | Caminho do diretório | Se este parâmetro for incluído, o instalador verificará o diretório listado para os ficheiros MSI de cabaça IoT Edge e VC runtime necessários para a instalação. Todos os ficheiros não encontrados no diretório são descarregados. Se ambos os ficheiros estiverem no diretório, pode instalar o IoT Edge sem uma ligação à Internet. Também pode utilizar este parâmetro para utilizar uma versão específica. |
| **RestartIfNeed** | nenhum | Esta bandeira permite que o script de implantação reinicie a máquina sem aviso, se necessário. |

## <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| Parâmetro | Valores aceites | Comentários |
| --------- | --------------- | -------- |
| **Force** | nenhum | Esta bandeira força a desinstalação no caso de a tentativa anterior de desinstalar não ter sido bem sucedida.
| **RestartIfNeed** | nenhum | Esta bandeira permite que o script de desinstalar reinicie a máquina sem aviso, se necessário. |

## <a name="next-steps"></a>Passos seguintes

Saiba como utilizar estes comandos no seguinte artigo:

* [Instale ou desinstale a borda IoT do Azure para windows](how-to-install-iot-edge-windows-on-windows.md)
