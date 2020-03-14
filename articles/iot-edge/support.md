---
title: Sistemas operativos suportados, motores de contentores - Borda Azure IoT
description: Saiba quais sistemas operacionais podem executar o daemon de Azure IoT Edge e tempo de execução e mecanismos de contentor suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3f1f4efc13e2d11e7111264564a227a484d611d5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284814"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas de suporte do Azure IoT Edge

Este artigo fornece detalhes sobre quais os sistemas e componentes suportados pelo IoT Edge, oficialmente ou em pré-visualização.

Se tiver problemas ao utilizar o serviço Azure IoT Edge, existem várias formas de procurar apoio. Experimente um dos seguintes canais de apoio:

**Reportar bugs** – A maioria do desenvolvimento que entra no produto Azure IoT Edge acontece no projeto ioT Edge open-source. Bugs podem ser relatados na página de [problemas](https://github.com/azure/iotedge/issues) do projeto. Correções rapidamente se tornam do projeto para atualizações de produtos.

**Equipa de Suporte ao Cliente** da Microsoft – Os utilizadores que tenham um [plano](https://azure.microsoft.com/support/plans/) de suporte podem contratar a equipa de Suporte ao Cliente da Microsoft, criando um bilhete de suporte diretamente a partir do [portal Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

Pedidos de **funcionalidades** – O produto Azure IoT Edge rastreia pedidos através da [página user voice](https://feedback.azure.com/forums/907045-azure-iot-edge)do produto .

## <a name="container-engines"></a>Mecanismos de contentor

Os módulos Azure IoT Edge são implementados como recipientes, pelo que o IoT Edge precisa de um motor de contentor para os lançar. A Microsoft fornece um mecanismo de contentor, o mecanismo moby, para cumprir este requisito. Este motor de contentor baseia-se no projeto moby de código aberto. Docker CE e o Docker EE são outros mecanismos de contentores populares. Eles também são baseados no projeto de código aberto Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor suporte de esforço para sistemas que utilizam estes motores de contentores; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esse motivo, a Microsoft recomenda a utilização de mecanismo de moby em sistemas de produção.

<br>
<center>

![o motor Moby como](./media/support/only-moby-for-production.png)
de corrida de contentores </center>

## <a name="operating-systems"></a>Sistemas operativos

O Azure IoT Edge funciona na maioria dos sistemas operativos que podem executar contentores; no entanto, nem todos estes sistemas são igualmente apoiados. Sistemas operativos são agrupados em camadas que representam o nível de suporte, os usuários podem esperar.

* Os sistemas de nível 1 são suportados. Para sistemas de nível 1, Microsoft:
  * tem este sistema operativo em testes automatizados
  * Fornece os pacotes de instalação das mesmas
* Os sistemas de nível 2 são compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Para sistemas de nível 2:
  * A Microsoft realizou testes informais nas plataformas ou sabe de um parceiro que executa com sucesso o Azure IoT Edge na plataforma
  * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas

A família do hospedeiro osso deve sempre corresponder à família do oss o de hóspedes utilizado dentro do recipiente de um módulo. Por outras palavras, só pode utilizar recipientes Linux em recipientes Linux e Windows no Windows. Ao utilizar o Windows, apenas são suportados recipientes isolados de processo, e não recipientes isolados Hyper-V.  

<br>
<center>

![anfitrião do OS combina com](./media/support/edge-on-device.png)
</center> de os sois

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

### <a name="tier-2"></a>Escalão 2

Os sistemas listados na tabela seguinte são considerados compatíveis com o Azure IoT Edge, mas não são testados ou mantidos ativamente pela Microsoft.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [Centos 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debiano 8](https://www.debian.org/releases/jessie/) | ![Debiano 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debiano 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debiano 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debiano 9](https://www.debian.org/releases/stretch/) | ![Debiano 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debiano 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debiano 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debiano 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debiano 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debiano 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debiano 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Embedded Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Rio Wind 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rio Wind 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Rastejante Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Os sistemas Debian 10, incluindo o Raspian Buster, utilizam uma versão do OpenSSL que o IoT Edge não suporta. Utilize o seguinte comando para instalar uma versão anterior antes de instalar o IoT Edge:

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtual Machines

O Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como um dispositivo IoT Edge é comum quando os clientes querem aumentar a infraestrutura existente com inteligência de ponta. A família do hospedeiro VM OS deve corresponder à família do oss o si convidado usado dentro do recipiente de um módulo. Este requisito é o mesmo que quando o Azure IoT Edge é executado diretamente num dispositivo. O Azure IoT Edge é agnóstico da tecnologia de virtualização subjacente e trabalha em VMs alimentados por plataformas como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge em um </center> de](./media/support/edge-on-vm.png)
VM

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
