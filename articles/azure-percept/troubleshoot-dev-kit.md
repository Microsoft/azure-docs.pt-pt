---
title: Problemas gerais de resolução de problemas com Azure Percept DK e IoT Edge
description: Obtenha dicas de resolução de problemas para algumas das questões mais comuns com Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: c9c62ec07873272b956877ec51d8765ae0bbd100
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605642"
---
# <a name="azure-percept-dk-troubleshooting"></a>Azure Percept DK resolução de problemas

Consulte as orientações abaixo para obter dicas gerais de resolução de problemas para o Azure Percept DK.

## <a name="general-troubleshooting-commands"></a>Comandos gerais de resolução de problemas

Para executar estes comandos, [sSH no kit dev](./how-to-ssh-into-percept-dk.md) e insira os comandos no pedido do cliente SSH.

Para redirecionar qualquer saída para um ficheiro .txt para análise mais aprofundada, utilize a seguinte sintaxe:

```console
sudo [command] > [file name].txt
```

Altere as permissões do ficheiro .txt para que possa ser copiado:

```console
sudo chmod 666 [file name].txt
```

Depois de redirecionar a saída para um ficheiro .txt, copie o ficheiro para o seu PC anfitrião via SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` refere-se à localização no seu PC anfitrião a que gostaria de copiar o ficheiro .txt. ```[remote username]``` é o nome de utilizador SSH escolhido durante a [experiência de configuração](./quickstart-percept-dk-set-up.md).

Para obter informações adicionais sobre os comandos Azure IoT Edge, consulte a documentação de [resolução de problemas do dispositivo Azure IoT Edge](../iot-edge/troubleshoot.md).

|Categoria:         |Comando:                    |Função:                  |
|------------------|----------------------------|---------------------------|
|SO                |```cat /etc/os-release```         |verificar versão de imagem Mariner |
|SO                |```cat /etc/os-subrelease```      |verificar versão de imagem derivada |
|SO                |```cat /etc/adu-version```        |verificar versão ADU |
|Temperatura       |```cat /sys/class/thermal/thermal_zone0/temp``` |verificar a temperatura do devkit |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |verifique os registos SoftAP|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |verificar registos de serviços Wi-Fi |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |verificar Wi-Fi registos de Serviço de Provisionamento de Toque Zero |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |verificar registos de pilhas de rede mariner |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |verificar detalhes de configuração de ponto de acesso wi-fi |
|OOBE              |```sudo journalctl -u oobe -b```       |verifique os registos da OOBE |
|Telemetria         |```sudo azure-device-health-id```      |encontrar telemetria única HW_ID |
|Azure IoT Edge          |```sudo iotedge check```          |executar verificações de configuração e conectividade para questões comuns |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |verificar registos de contentores, tais como módulos de fala e visão |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |recolher registos de módulos, registos de gestores de segurança Azure IoT Edge, troncos de motor de contentores, ```iotedge check``` saída JSON e outras informações úteis de depuração da última hora |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |ver os registos do gestor de segurança Azure IoT Edge |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |reiniciar o daemon de segurança Azure IoT Edge |
|Azure IoT Edge          |```sudo iotedge list```           |listar os módulos Azure IoT Edge implantados |
|Outro             |```df [option] [file]```          |exibir informações sobre o espaço disponível/total em sistemas de ficheiros especificados |
|Outro             |`ip route get 1.1.1.1`        |informação do dispositivo de exibição IP e interface |
|Outro             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |apenas endereço IP do dispositivo de exibição |


Os ```journalctl``` comandos Wi-Fi podem ser combinados no seguinte comando único:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Comandos de resolução de problemas de Docker

|Comando:                        |Função:                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[mostra quais os contentores que estão a correr](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[mostra quais as imagens que estão no dispositivo](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[elimina uma imagem do dispositivo](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[toma registos de contentores de módulo especificado](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[remove todas as imagens penduradas](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |verificar estado de descarregamento de contentor de estivador |

## <a name="usb-updates"></a>Atualizações USB

|Erro:                                    |Solução:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX durante flash USB via UUU |Este erro é o resultado de uma falha de ligação USB durante a atualização da UUU. Se o cabo USB não estiver corretamente ligado às portas USB do PC ou da placa de porta-aviões Percept DK, ocorrerá um erro deste formulário. Tente desligar e voltar a ligar ambas as extremidades do cabo USB e agitar o cabo para garantir uma ligação segura. Isto quase sempre resolve o problema. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Azure Percept DK porta-aviões LED afirma

Há três pequenos LEDs em cima da caixa do quadro de transporte. Um ícone de nuvem é impresso ao lado do LED 1, um ícone de Wi-Fi é impresso ao lado do LED 2, e um ícone de marca de exclamação é impresso ao lado do LED 3. Consulte a tabela abaixo para obter informações sobre cada estado LED.

|LED             |Estado      |Descrição                      |
|----------------|-----------|---------------------------------|
|LED 1 (Hub IoT) |Em (sólido) |O dispositivo está ligado a um hub IoT. |
|LED 2 (Wi-Fi)   |Piscar lentamente |O dispositivo está pronto para ser configurado pelo Wi-Fi Easy Connect e está a anunciar a sua presença num configurador. |
|LED 2 (Wi-Fi)   |Piscar rápido |A autenticação foi bem sucedida, a associação de dispositivos em curso. |
|LED 2 (Wi-Fi)   |Em (sólido) |A autenticação e a associação foram bem sucedidas; o dispositivo está ligado a uma rede Wi-Fi. |
|LED 3           |ND         |LED não está a ser utilizado. |