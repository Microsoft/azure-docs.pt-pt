---
title: Sistemas operativos suportados, motores de contentores - Borda Azure IoT
description: Saiba quais os sistemas operativos que podem executar o daemon Azure IoT Edge e o tempo de funcionamento, e motores de contentores suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/06/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 42c0a5d0c590f8c395c2afe366a00fcb9c83ce46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79536943"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas suportados Azure IoT Edge

Este artigo fornece detalhes sobre quais os sistemas e componentes suportados pelo IoT Edge, oficialmente ou em pré-visualização.

Se tiver problemas ao utilizar o serviço Azure IoT Edge, existem várias formas de procurar apoio. Experimente um dos seguintes canais de apoio:

**Reportar bugs** – A maioria do desenvolvimento que entra no produto Azure IoT Edge acontece no projeto ioT Edge open-source. Bugs podem ser relatados na página de [problemas](https://github.com/azure/iotedge/issues) do projeto. As correções rapidamente fazem o seu caminho desde o projeto até às atualizações do produto.

**Equipa de Suporte ao Cliente** da Microsoft – Os utilizadores que tenham um [plano](https://azure.microsoft.com/support/plans/) de suporte podem contratar a equipa de Suporte ao Cliente da Microsoft, criando um bilhete de suporte diretamente a partir do [portal Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

Pedidos de **funcionalidades** – O produto Azure IoT Edge rastreia pedidos através da [página user voice](https://feedback.azure.com/forums/907045-azure-iot-edge)do produto .

## <a name="container-engines"></a>Motores de contentores

Os módulos Azure IoT Edge são implementados como recipientes, pelo que o IoT Edge precisa de um motor de contentor para os lançar. A Microsoft fornece um motor de contentores, moby-engine, para cumprir este requisito. Este motor de contentor baseia-se no projeto moby de código aberto. Docker CE e Docker EE são outros motores de contentores populares. Eles também são baseados no projeto de código aberto Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor suporte de esforço para sistemas que utilizam estes motores de contentores; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esta razão, a Microsoft recomenda a utilização de moby-engine nos sistemas de produção.

<br>
<center>

![O motor Moby como tempo de execução do recipiente](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operativos

O Azure IoT Edge funciona na maioria dos sistemas operativos que podem executar contentores; no entanto, nem todos estes sistemas são igualmente apoiados. Os sistemas operativos estão agrupados em níveis que representam o nível de suporte que os utilizadores podem esperar.

* Os sistemas de nível 1 são suportados. Para sistemas de nível 1, Microsoft:
  * tem este sistema operativo em testes automatizados
  * fornece pacotes de instalação para eles
* Os sistemas de nível 2 são compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Para sistemas de nível 2:
  * A Microsoft realizou testes informais nas plataformas ou sabe de um parceiro que executa com sucesso o Azure IoT Edge na plataforma
  * Pacotes de instalação para outras plataformas podem funcionar nestas plataformas

A família do hospedeiro osso deve sempre corresponder à família do oss o de hóspedes utilizado dentro do recipiente de um módulo. Por outras palavras, só pode utilizar recipientes Linux em recipientes Linux e Windows no Windows. Ao utilizar o Windows, apenas são suportados recipientes isolados de processo, e não recipientes isolados Hyper-V.  

<br>
<center>

![Anfitrião OS combina com os convidados](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nível 1

Os sistemas listados na tabela seguinte são suportados pela Microsoft, geralmente disponíveis ou em pré-visualização pública, e são testados com cada nova versão. 

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública |
| [Windows 10 IoT Core,](https://docs.microsoft.com/windows/iot-core/windows-iot-core)construção 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Enterprise,](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise)construção 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), construção 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), construção 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Os sistemas operativos Windows acima indicados são os requisitos para dispositivos que executam contentores Windows no Windows, que é a única configuração suportada para a produção. Os pacotes de instalação Azure IoT Edge para Windows permitem a utilização de recipientes Linux no Windows; no entanto, esta configuração é apenas para desenvolvimento e teste. Para mais informações, consulte [Use IoT Edge no Windows para executar recipientes Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Camada 2

Os sistemas listados na tabela seguinte são considerados compatíveis com o Azure IoT Edge, mas não são testados ou mantidos ativamente pela Microsoft.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debiano 8](https://www.debian.org/releases/jessie/) | ![Debiano 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debiano 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debiano 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debiano 9](https://www.debian.org/releases/stretch/) | ![Debiano 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debiano 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debiano 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debiano 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debiano 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debiano 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debiano 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rio Wind 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Rastejante Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Os sistemas Debian 10, incluindo o Raspian Buster, utilizam uma versão do OpenSSL que o IoT Edge não suporta. Utilize o seguinte comando para instalar uma versão anterior antes de instalar o IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="releases"></a>Versões

Os ativos de lançamento do IoT Edge e as notas de lançamento estão disponíveis na página de [lançamentos azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Esta secção reflete informações dessas notas de lançamento para ajudá-lo a visualizar os componentes de cada versão mais facilmente.

Os componentes IoT Edge podem ser instalados ou atualizados individualmente, e são compatíveis com componentes de versões mais antigas. A tabela seguinte lista os componentes incluídos em cada versão:

| Libertar   | Daemon de segurança  | Hub de borda<br>Agente de borda | Libiotismo | Rio Moby  |
| --------- | ---------------- | ---------------------- | --------- | ----- |
| **1.0.9** | 1.0.9            | 1.0.9                  | 1.0.9     |       |
| **1.0.8** | 1.0.8            | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7       | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6       | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5            | 1.0.5                  | 1.0.5     | 3.0.2 |

O IoT Edge utiliza o Microsoft.Azure.Device.Client SDK. Para mais informações, consulte o [representante Azure IoT C# SDK GitHub](https://github.com/Azure/azure-iot-sdk-csharp) ou o [Azure SDK para obter conteúdo](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet)de referência .NET . A lista que se segue mostra a versão do SDK cliente que cada lançamento é testada contra:

* **IoT Edge 1.0.9:** Cliente SDK 1.21.1
* **IoT Edge 1.0.8:** Cliente SDK 1.20.3
* **IoT Edge 1.0.7:** Cliente SDK 1.20.1
* **IoT Edge 1.0.6**: Cliente SDK 1.17.1
* **IoT Edge 1.0.5:** Cliente SDK 1.17.1

## <a name="virtual-machines"></a>Virtual Machines

O Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como um dispositivo IoT Edge é comum quando os clientes querem aumentar a infraestrutura existente com inteligência de ponta. A família do hospedeiro VM OS deve corresponder à família do oss o si convidado usado dentro do recipiente de um módulo. Este requisito é o mesmo que quando o Azure IoT Edge é executado diretamente num dispositivo. O Azure IoT Edge é agnóstico da tecnologia de virtualização subjacente e trabalha em VMs alimentados por plataformas como Hyper-V e vSphere.

<br>
<center>

![Borda azure ioT em um VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema

O Azure IoT Edge funciona muito bem em dispositivos tão pequenos como um Raspberry Pi3 para hardware de qualidade de servidor. Escolher o hardware certo para o seu cenário depende das cargas de trabalho que pretende executar. Tomar a decisão final do dispositivo pode ser complicado; no entanto, pode facilmente começar a protótipode uma solução em computadores portáteis ou desktops tradicionais.

A experiência enquanto prototipagem ajudará a orientar a seleção final do dispositivo. As perguntas que deve considerar incluem:

* Quantos módulos estão na sua carga de trabalho?
* Quantas camadas os recipientes dos seus módulos partilham?
* Em que língua estão escritos os seus módulos?
* Quantos dados os seus módulos vão ser processados?
* Os seus módulos precisam de algum hardware especializado para acelerar as suas cargas de trabalho?
* Quais são as características de desempenho desejadas da sua solução?
* Qual é o seu orçamento de hardware?
