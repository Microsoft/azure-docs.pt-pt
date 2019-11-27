---
title: Sistemas operativos, mecanismos de contentor - Azure IoT Edge suportados | Documentos da Microsoft
description: Saiba quais sistemas operacionais podem executar o daemon de Azure IoT Edge e tempo de execução e mecanismos de contentor suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0fe4a13a33b6d93266d68e632864e0b61a7eaf29
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452519"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas de suporte do Azure IoT Edge

Este artigo fornece detalhes sobre quais sistemas e componentes têm suporte pelo IoT Edge, seja oficialmente ou em versão prévia. 

Se você tiver problemas ao usar o serviço de Azure IoT Edge, há várias maneiras de buscar o suporte. Experimente um dos seguintes canais para obter suporte:

**Relatando bugs** – a maior parte do desenvolvimento que entra no Azure IOT Edge produto ocorre no IOT Edge projeto de software livre. Os bugs podem ser relatados na [página problemas](https://github.com/azure/iotedge/issues) do projeto. Correções rapidamente se tornam do projeto para atualizações de produtos.

**Equipe de suporte ao cliente da Microsoft** – os usuários que têm um [plano de suporte](https://azure.microsoft.com/support/plans/) podem envolver a equipe de suporte ao cliente da Microsoft criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – o produto Azure IOT Edge rastreia solicitações de recursos por meio da [página de voz do usuário](https://feedback.azure.com/forums/907045-azure-iot-edge)do produto.

## <a name="container-engines"></a>Mecanismos de contentor

Azure IoT Edge módulos são implementados como contêineres, portanto IoT Edge precisa de um mecanismo de contêiner para iniciá-los. A Microsoft fornece um mecanismo de contentor, o mecanismo moby, para cumprir este requisito. Esse mecanismo de contêiner é baseado no projeto de código-fonte aberto do Moby. Docker CE e o Docker EE são outros mecanismos de contentores populares. Eles também são baseados no projeto de código-fonte aberto do Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor esforço para sistemas que usam esses mecanismos de contêiner; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esse motivo, a Microsoft recomenda a utilização de mecanismo de moby em sistemas de produção.

<br>
<center>

](./media/support/only-moby-for-production.png)
de ![Moby como tempo de execução do contêiner </center>

## <a name="operating-systems"></a>Sistemas operativos
Azure IoT Edge é executado na maioria dos sistemas operacionais que podem executar contêineres; no entanto, não há suporte igualmente para todos esses sistemas. Sistemas operativos são agrupados em camadas que representam o nível de suporte, os usuários podem esperar.
* Há suporte para sistemas da camada 1. Para sistemas da camada 1, a Microsoft:
    * tem este sistema operacional em testes automatizados
    * Fornece os pacotes de instalação das mesmas
* Os sistemas de camada 2 são compatíveis com Azure IoT Edge e podem ser usados relativamente facilmente. Para sistemas de camada 2:
    * A Microsoft fez testes ad hoc nas plataformas ou sabe que um parceiro executou Azure IoT Edge com êxito na plataforma
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família do sistema operacional do host deve sempre corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Em outras palavras, você só pode usar contêineres do Linux em contêineres do Linux e do Windows no Windows. Ao usar o Windows, somente os contêineres isolados de processo têm suporte, não contêineres isolados do Hyper-V.  

<br>
<center>

![sistema operacional host corresponde ao sistema operacional convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Escalão 1

Os sistemas listados na tabela a seguir têm suporte da Microsoft, seja geralmente disponível ou em visualização pública, e são testados com cada nova versão. 

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian Stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| [Ubuntu Server 16, 4](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu Server 16, 4 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública  |
| [Ubuntu Server 18, 4](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu Server 18, 4 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública |
| [Windows 10 IOT Core](https://docs.microsoft.com/windows/iot-core/windows-iot-core), Build 17763 | ![Windows IoT Core + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows 10 IOT Enterprise](https://docs.microsoft.com/windows/iot-core/windows-iot-enterprise), Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server 2019](https://docs.microsoft.com/windows-server/get-started-19/rel-notes-19), Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Windows Server IoT 2019](https://docs.microsoft.com/windows/iot-core/windows-server), Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |


Os sistemas operacionais Windows listados acima são os requisitos para dispositivos que executam contêineres do Windows no Windows, que é a única configuração com suporte para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem o uso de contêineres do Linux no Windows; no entanto, essa configuração é apenas para desenvolvimento e teste. Para obter mais informações, consulte [usar IOT Edge no Windows para executar contêineres do Linux](how-to-install-iot-edge-windows-with-linux.md).

### <a name="tier-2"></a>Escalão 2

Os sistemas listados na tabela a seguir são considerados compatíveis com Azure IoT Edge, mas não são ativamente testados ou mantidos pela Microsoft.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7,5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 8](https://www.debian.org/releases/jessie/) | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) <sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Sistema operacional flexível Linux com mentor incorporado](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Embedded Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Embedded Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [SO Omni OS do mentor Embedded Linux](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Embedded Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Embedded Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7,5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 16, 4](https://wiki.ubuntu.com/XenialXerus/ReleaseNotes) | ![Ubuntu 16, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18, 4](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Vento Rio 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Vento Rio 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster <sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Debian 10 sistemas, incluindo Raspian Buster, usam uma versão do OpenSSL que o IOT Edge não dá suporte. Use o seguinte comando para instalar uma versão anterior antes de instalar o IoT Edge: 

```bash
sudo apt-get install libssl1.0.2
```

## <a name="virtual-machines"></a>Virtual Machines
Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como um dispositivo IoT Edge é comum quando os clientes desejam aumentar a infraestrutura existente com o Edge Intelligence. A família do sistema operacional da VM host deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Esse requisito é o mesmo que quando Azure IoT Edge é executado diretamente em um dispositivo. Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs com plataforma, como Hyper-V e vSphere.

<br>
<center>

![Azure IoT Edge em uma VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
Azure IoT Edge funciona muito bem em dispositivos tão pequenos quanto um Raspberry Pi3 para o hardware de nível de servidor. Escolher o hardware certo para seu cenário depende das cargas de trabalho que você deseja executar. Tomar a decisão final do dispositivo pode ser complicado; no entanto, você pode facilmente iniciar o protótipo de uma solução em laptops ou desktops tradicionais.

A experiência durante o protótipo ajudará a orientar sua seleção final de dispositivo. As perguntas que você deve considerar incluem: 

* Quantos módulos estão em sua carga de trabalho?
* Quantas camadas os contêineres dos seus módulos compartilham?
* Em qual idioma os módulos são gravados? 
* Qual a quantidade de dados que seus módulos serão processando?
* Seus módulos precisam de qualquer hardware especializado para acelerar suas cargas de trabalho?
* Quais são as características de desempenho desejadas da sua solução?
* Qual é o seu orçamento de hardware?
