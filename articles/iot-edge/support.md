---
title: Sistemas operativos suportados, motores de contentores - Azure IoT Edge
description: Saiba quais os sistemas operativos que podem executar o daemon E o tempo de funcionamento do Azure IoT Edge e os motores de contentores suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 063ef6b76e031277d93297512e16d30a86aae45c
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808526"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas suportados Azure IoT Edge

Este artigo fornece detalhes sobre quais os sistemas e componentes que são suportados pela IoT Edge, seja oficialmente ou em pré-visualização.

Se tiver problemas durante a utilização do serviço Azure IoT Edge, existem várias formas de procurar suporte. Experimente um dos seguintes canais de apoio:

**Reportar bugs** – A maior parte do desenvolvimento que vai para o produto Azure IoT Edge acontece no projeto de código aberto IoT Edge. Os bugs podem ser reportados na página de [assuntos](https://github.com/azure/iotedge/issues) do projeto. As correções fazem rapidamente o seu caminho desde o projeto até atualizações de produtos.

**Equipa de Apoio ao Cliente da Microsoft** – Os utilizadores que tenham um plano de [suporte](https://azure.microsoft.com/support/plans/) podem envolver a equipa de Suporte ao Cliente da Microsoft criando um bilhete de suporte diretamente do [portal Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Pedidos de funcionalidades** – As faixas de características do produto Azure IoT Edge apresentam pedidos através da página user [Voice](https://feedback.azure.com/forums/907045-azure-iot-edge)do produto .

## <a name="container-engines"></a>Motores de contentores

Os módulos Azure IoT Edge são implementados como recipientes, pelo que o IoT Edge precisa de um motor de contentor para os lançar. A Microsoft fornece um motor de contentores, motor moby, para cumprir este requisito. Este motor de contentor é baseado no projeto Moby open-source. Docker CE e Docker EE são outros motores de contentores populares. Também são baseados no projeto Moby open-source e são compatíveis com Azure IoT Edge. A Microsoft oferece o melhor suporte de esforço para sistemas que utilizam esses motores de contentores; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esta razão, a Microsoft recomenda o uso de motor moby em sistemas de produção.

<br>
<center>

![O motor Moby como tempo de funcionação do contentor](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operativos

O Azure IoT Edge funciona na maioria dos sistemas operativos que podem funcionar contentores; no entanto, nem todos estes sistemas são igualmente apoiados. Os sistemas operativos são agrupados em níveis que representam o nível de suporte que os utilizadores podem esperar.

* Os sistemas de nível 1 são suportados. Para sistemas de nível 1, Microsoft:
  * tem este sistema operativo em testes automatizados
  * fornece pacotes de instalação para eles
* Os sistemas de nível 2 são compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Para os sistemas de nível 2:
  * A Microsoft fez testes informais nas plataformas ou sabe de um parceiro que executa com sucesso o Azure IoT Edge na plataforma
  * Pacotes de instalação para outras plataformas podem funcionar nestas plataformas

A família do so hospedeiro deve sempre corresponder à família do SO convidado utilizado dentro do contentor de um módulo. Por outras palavras, só pode utilizar recipientes Linux em recipientes Linux e Windows no Windows. Ao utilizar o Windows, apenas os recipientes isolados são suportados, e não recipientes isolados Hyper-V.  

<br>
<center>

![Host OS corresponde ao convidado OS](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Nível 1

Os sistemas listados na tabela seguinte são suportados pela Microsoft, geralmente disponíveis ou em pré-visualização pública, e são testados com cada nova versão. 

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Estiramento de Framboesa Pi OS |  | ![Estiramento de Framboesa Pi OS + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Servidor Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública  |
| [Ubuntu Server 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Servidor Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública |
| [Windows 10 IoT Enterprise](/windows/iot-core/windows-iot-enterprise), construa 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IoT Core](/windows/iot-core/windows-iot-core), construa 17763 | ![Núcleo IoT do Windows + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](/windows-server/get-started-19/rel-notes-19), construa 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](/windows/iot-core/windows-server), construa 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |

Os sistemas operativos Windows listados acima são os requisitos para dispositivos que executam contentores Windows no Windows, que é a única configuração suportada para produção. Os pacotes de instalação Azure IoT Edge para Windows permitem a utilização de contentores Linux no Windows; no entanto, esta configuração é apenas para desenvolvimento e teste. 

### <a name="tier-2"></a>Camada 2

Os sistemas listados na tabela seguinte são considerados compatíveis com o Azure IoT Edge, mas não são testados ou mantidos ativamente pela Microsoft.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Incorporado Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Incorporado Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Incorporado Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Incorporado Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Incorporado Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Incorporado Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Incorporado Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16.04](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rio Wind 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Framboesa Pi OS Buster <sup>1</sup> |  | ![Framboesa Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Framboesa Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>2</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Os sistemas Debian 10, incluindo Raspberry Pi OS Buster, utilizam uma versão do OpenSSL que o IoT Edge não suporta. Utilize o seguinte comando para instalar uma versão anterior antes de instalar o IoT Edge:

```bash
sudo apt-get install libssl1.1
```

<sup>2</sup> Os pacotes Debian 9 do [Azure IoT Edge lança repo](https://github.com/Azure/azure-iotedge/releases) devem funcionar fora da caixa com Ubuntu 20.04.

## <a name="releases"></a>Versões

Os ativos de libertação do IoT Edge estão disponíveis na página [de lançamentos azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Esta secção reflete informações dessas notas de lançamento para ajudá-lo a visualizar mais facilmente os componentes de cada versão.

Os componentes IoT Edge podem ser instalados ou atualizados individualmente, e são retrocompeitos com componentes de versões mais antigas. A tabela a seguir lista os componentes incluídos em cada versão:

| Libertar | Daemon de segurança | Hub de borda<br>Agente de borda | Libiothsm | Moby |
|--|--|--|--|--|
| **1.0.10** | 1.0.10 | 1.0.10 | 1.0.10 |  |
| **1.0.9** | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 | 1.0.9.5<br>1.0.9.4<br>1.0.9.3<br>1.0.9.2<br>1.0.9.1<br>1.0.9 |  |
| **1.0.8** | 1.0.8 | 1.0.8.5<br>1.0.8.4<br>1.0.8.3<br>1.0.8.2<br>1.0.8.1<br>1.0.8 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 1.0.7.1<br>1.0.7 | 3.0.5<br>3.0.4 (ARMv7hl, CentOS) |
| **1.0.6** | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 | 1.0.6.1<br>1.0.6 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

O IoT Edge utiliza o Microsoft.Azure.Devices.Client SDK. Para obter mais informações, consulte o [Azure IoT C# SDK GitHub repo](https://github.com/Azure/azure-iot-sdk-csharp) ou o [Azure SDK para conteúdo de referência .NET](/dotnet/api/overview/azure/iot/client). A lista a seguir mostra a versão do cliente SDK que cada versão é testada contra:

* **IoT Edge 1.0.10**: Cliente SDK 1.28.0
* **IoT Edge 1.0.9**: Cliente SDK 1.21.1
* **IoT Edge 1.0.8**: Cliente SDK 1.20.3
* **IoT Edge 1.0.7**: Cliente SDK 1.20.1
* **IoT Edge 1.0.6**: Cliente SDK 1.17.1
* **IoT Edge 1.0.5**: Cliente SDK 1.17.1

## <a name="virtual-machines"></a>Máquinas Virtuais

Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como dispositivo IoT Edge é comum quando os clientes querem aumentar a infraestrutura existente com inteligência de borda. A família do hospedeiro VM OS deve corresponder à família do so convidado utilizado dentro do contentor de um módulo. Este requisito é o mesmo que quando o Azure IoT Edge é executado diretamente num dispositivo. Azure IoT Edge é agnóstico da tecnologia de virtualização subjacente e funciona em VMs alimentados por plataformas como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge em um VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema

O Azure IoT Edge funciona muito bem em dispositivos tão pequenos como um Raspberry Pi3 para o hardware de qualidade do servidor. Escolher o hardware certo para o seu cenário depende das cargas de trabalho que pretende executar. Tomar a decisão final do dispositivo pode ser complicado; no entanto, pode facilmente começar a protótipo de uma solução em computadores portáteis ou desktops tradicionais.

Experimente enquanto a prototipagem ajudará a orientar a sua seleção final do dispositivo. As questões que deve considerar incluem:

* Quantos módulos tem na sua carga de trabalho?
* Quantas camadas os contentores dos seus módulos partilham?
* Em que língua estão os seus módulos escritos?
* Quantos dados os seus módulos serão processados?
* Os seus módulos precisam de algum hardware especializado para acelerar as suas cargas de trabalho?
* Quais são as características de desempenho desejadas da sua solução?
* Qual é o seu orçamento de hardware?
