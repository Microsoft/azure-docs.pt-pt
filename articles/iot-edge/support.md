---
title: Sistemas operativos suportados, motores de contentores - Azure IoT Edge
description: Saiba quais os sistemas operativos que podem executar o daemon E o tempo de funcionamento do Azure IoT Edge e os motores de contentores suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 0bd6a8af4850f3a0519bac7644100c2dcf883635
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031177"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas suportados Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Este artigo fornece detalhes sobre quais os sistemas e componentes que são suportados pela IoT Edge, seja oficialmente ou em pré-visualização.

## <a name="get-support"></a>Obter suporte

Se tiver problemas durante a utilização do serviço Azure IoT Edge, existem várias formas de procurar suporte. Experimente um dos seguintes canais de apoio:

**Reportar bugs** – A maior parte do desenvolvimento que vai para o produto Azure IoT Edge acontece no projeto de código aberto IoT Edge. Os bugs podem ser reportados na página de [assuntos](https://github.com/azure/iotedge/issues) do projeto. Os bugs relacionados com o Azure IoT Edge para Linux no Windows podem ser reportados na [página de problemas iotedge-eflow](https://github.com/azure/iotedge-eflow/issues). As correções fazem rapidamente o seu caminho desde os projetos até às atualizações de produtos.

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

A família do so hospedeiro deve sempre corresponder à família do SO convidado utilizado dentro do contentor de um módulo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Por outras palavras, só pode utilizar recipientes Linux em recipientes Linux e Windows no Windows. Quando utilizar recipientes Windows, apenas os recipientes isolados são suportados e não recipientes isolados Hyper-V.  

IoT Edge para Linux no Windows utiliza IoT Edge numa máquina virtual Linux que funciona num anfitrião do Windows. Desta forma, pode executar módulos Linux num dispositivo Windows.
:::moniker-end
<!-- end 1.1 -->

### <a name="tier-1"></a>Nível 1

Os sistemas listados nas seguintes tabelas são suportados pela Microsoft, geralmente disponíveis ou em pré-visualização pública, e são testados com cada nova versão.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
O Azure IoT Edge suporta módulos construídos como recipientes Linux ou Windows. Os contentores Linux podem ser implantados em dispositivos Linux ou implantados em dispositivos Windows utilizando ioT Edge para Linux no Windows. Os recipientes windows só podem ser implantados em dispositivos Windows.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
A versão 1.2 do Azure IoT Edge suporta apenas módulos construídos como recipientes Linux.

Atualmente, não existe uma forma suportada de executar a versão 1.2 do IoT Edge em dispositivos Windows. [IoT Edge para Linux no Windows](iot-edge-for-linux-on-windows.md) é a forma recomendada de executar IoT Edge em dispositivos Windows, mas atualmente apenas executa IoT Edge 1.1. Para mais informações, consulte a versão [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) deste artigo.

:::moniker-end
<!-- end 1.2 -->

#### <a name="linux-containers"></a>Contentores do Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Os módulos construídos como recipientes Linux podem ser implantados em dispositivos Linux ou Windows. Para os dispositivos Linux, o tempo de funcionaamento do IoT Edge é instalado diretamente no dispositivo anfitrião. Para dispositivos Windows, uma máquina virtual Linux pré-construída com o tempo de execução IoT Edge funciona no dispositivo anfitrião.

[O IoT Edge para o Linux no Windows](iot-edge-for-linux-on-windows.md) encontra-se atualmente em pré-visualização pública, mas é a forma recomendada de executar ioT Edge em dispositivos Windows.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Estiramento de Framboesa Pi OS |  | ![Estiramento de Framboesa Pi OS + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Servidor Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública |
| Windows 10 Pro | Pré-visualização pública |  |  |
| Windows 10 Enterprise | Pré-visualização pública |  |  |
| Windows 10 IoT Enterprise | Pré-visualização pública |  |  |
| Windows Server 2019 | Pré-visualização pública |  |  |

Todos os sistemas operativos Windows devem ser da versão 1809 (construção 17763) ou posterior.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Estiramento de Framboesa Pi OS |  | ![Estiramento de Framboesa Pi OS + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 18.04 | ![Servidor Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública |

:::moniker-end
<!-- end 1.2 -->

>[!NOTE]
>O suporte ubuntu Server 16.04 terminou com o lançamento da versão 1.1 do IoT Edge.

#### <a name="windows-containers"></a>Contentores do Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
>[!IMPORTANT]
>IoT Edge 1.1 LTS é o último canal de lançamento que irá suportar recipientes Windows. A partir da versão 1.2, os recipientes windows não serão suportados. Considere usar ou mover-se para [IoT Edge para o Linux no Windows](iot-edge-for-linux-on-windows.md) para executar IoT Edge em dispositivos Windows.

Os módulos construídos como recipientes Windows só podem ser implantados em dispositivos Windows.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Windows 10 IoT Enterprise | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019  | ![check1](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019 | ![check1](./media/tutorial-c-module/green-check.png) |  |  |

Todos os sistemas operativos Windows devem ser a versão 1809 (construção 17763). A construção específica do Windows é necessária para o IoT Edge no Windows, uma vez que a versão dos recipientes windows deve corresponder exatamente à versão do dispositivo Windows anfitrião. Atualmente, os recipientes do Windows utilizam apenas a construção 17763.

>[!NOTE]
>O suporte do Windows 10 IoT Core terminou com o lançamento da versão 1.1 do IoT Edge.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
IoT Edge 1.1 LTS é o último canal de lançamento que suporta recipientes Windows. A partir da versão 1.2, os recipientes windows não são suportados.

Para obter informações sobre sistemas operativos suportados para contentores Windows, consulte a versão [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) deste artigo.

:::moniker-end
<!-- end 1.2 -->

### <a name="tier-2"></a>Camada 2

Os sistemas listados na tabela seguinte são considerados compatíveis com o Azure IoT Edge, mas não são testados ou mantidos ativamente pela Microsoft.

| Sistema Operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| [CentOS 7.5](https://wiki.centos.org/Manuals/ReleaseNotes/CentOS7.1804) | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 20.04 <sup>1</sup>](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes) | ![Ubuntu 20.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 20.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 9](https://www.debian.org/releases/stretch/) | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Debian 10](https://www.debian.org/releases/buster/) | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Incorporado Linux Flex OS](https://www.mentor.com/embedded-software/linux/mel-flex-os/) | ![Mentor Incorporado Linux Flex OS + AMD64](./media/tutorial-c-module/green-check.png) | ![Mentor Incorporado Linux Flex OS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Mentor Incorporado Linux Flex OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [Mentor Incorporado Linux Omni OS](https://www.mentor.com/embedded-software/linux/mel-omni-os/) | ![Mentor Incorporado Linux Omni OS + AMD64](./media/tutorial-c-module/green-check.png) |  | ![Mentor Incorporado Linux Omni OS + ARM64](./media/tutorial-c-module/green-check.png) |
| [RHEL 7.5](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/7.5_release_notes/index) | ![RHEL 7.5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7.5 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Ubuntu 18.04](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes) | ![Ubuntu 18.04 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18.04 + ARM64](./media/tutorial-c-module/green-check.png) |
| [Wind River 8](https://docs.windriver.com/category/os-wind_river_linux) | ![Rio Wind 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| [Yocto](https://www.yoctoproject.org/) | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Framboesa Pi OS Buster |  | ![Framboesa Pi OS Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Framboesa Pi OS Buster + ARM64](./media/tutorial-c-module/green-check.png) |

<sup>1</sup> Os passos de instalação do Ubuntu Server 18.04 no [Install or desinstalar Azure IoT Edge para Linux](how-to-install-iot-edge.md) devem funcionar sem alterações no Ubuntu 20.04.

## <a name="releases"></a>Versões

Os ativos de libertação do IoT Edge estão disponíveis na página [de lançamentos azure-iotedge.](https://github.com/Azure/azure-iotedge/releases) Esta secção reflete informações dessas notas de lançamento para ajudá-lo a visualizar mais facilmente os componentes de cada versão.

Os componentes IoT Edge podem ser instalados ou atualizados individualmente, e são retrocompeitos com componentes de versões mais antigas. A tabela a seguir lista os componentes incluídos em cada versão:

| Libertar | Daemon de segurança | Hub de borda<br>Agente de borda | Libiothsm | Moby |
|--|--|--|--|--|
| **1.1 LTS**<sup>1</sup> | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 | 1.1.0<br>1.1.1 |   |
| **1.0.10** | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br>1.0.10.3<br>1.0.10.4 | 1.0.10<br>1.0.10.1<br>1.0.10.2<br><br>1.0.10.4 |  |
| **1.0.9** | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 | 1.0.9<br>1.0.9.1<br>1.0.9.2<br>1.0.9.3<br>1.0.9.4<br>1.0.9.5 |  |
| **1.0.8** | 1.0.8 | 1.0.8<br>1.0.8.1<br>1.0.8.2<br>1.0.8.3<br>1.0.8.4<br>1.0.8.5 | 1.0.8 | 3.0.6 |
| **1.0.7** | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 1.0.7<br>1.0.7.1 | 3.0.4 (ARMv7hl, CentOS)<br>3.0.5 |
| **1.0.6** | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 | 1.0.6<br>1.0.6.1 |  |
| **1.0.5** | 1.0.5 | 1.0.5 | 1.0.5 | 3.0.2 |

<sup>1</sup> IoT Edge 1.1 é o primeiro canal de lançamento de suporte a longo prazo (LTS). Esta versão não introduziu novas funcionalidades, mas irá receber atualizações de segurança e correções para regressões. IoT Edge 1.1 LTS utiliza .NET Core 3.1, e será suportado até 3 de dezembro de 2022 para corresponder ao [ciclo de vida de lançamento .NET Core e .NET 5](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

>[!IMPORTANT]
>Com o lançamento de um canal de suporte de longo prazo, recomendamos que todos os clientes atuais que executem 1.0.x atual atualizem os seus dispositivos para 1.1.x para receber suporte contínuo.

O IoT Edge utiliza o Microsoft.Azure.Devices.Client SDK. Para obter mais informações, consulte o [Azure IoT C# SDK GitHub repo](https://github.com/Azure/azure-iot-sdk-csharp) ou o [Azure SDK para conteúdo de referência .NET](/dotnet/api/overview/azure/iot/client). A lista a seguir mostra a versão do cliente SDK que cada versão é testada contra:

| Versão do IoT Edge | Microsoft.Azure.Devices.Client SDK versão |
|------------------|--------------------------------------------|
| 1.1 (LTS)      | 1.28.0                                     |
| 1.0.10           | 1.28.0                                     |
| 1.0.9            | 1.21.1                                     |
| 1.0.8            | 1.20.3                                     |
| 1.0.7            | 1.20.1                                     |
| 1.0.6            | 1.17.1                                     |
| 1.0.5            | 1.17.1                                     |

## <a name="virtual-machines"></a>Máquinas Virtuais

Azure IoT Edge pode ser executado em máquinas virtuais. Usar uma máquina virtual como dispositivo IoT Edge é comum quando os clientes querem aumentar a infraestrutura existente com inteligência de borda. A família do hospedeiro VM OS deve corresponder à família do so convidado utilizado dentro do contentor de um módulo. Este requisito é o mesmo que quando o Azure IoT Edge é executado diretamente num dispositivo. Azure IoT Edge é agnóstico da tecnologia de virtualização subjacente e funciona em VMs alimentados por plataformas como Hyper-V e vSphere.

<br>

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

<center>

![Azure IoT Edge em um VM](./media/support/edge-on-vm-with-windows.png)

</center>

::: moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

<center>

![Azure IoT Edge em um VM](./media/support/edge-on-vm.png)

</center>

:::moniker-end

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
