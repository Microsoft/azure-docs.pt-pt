---
title: Sistemas operativos, mecanismos de contentor - Azure IoT Edge suportados | Documentos da Microsoft
description: Saiba quais sistemas operacionais podem executar o daemon de Azure IoT Edge e tempo de execução e mecanismos de contentor suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9b9acac829fd128a66e3ceea603bb804adf2a88b
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598598"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas de suporte do Azure IoT Edge

Este artigo fornece detalhes sobre quais sistemas e componentes têm suporte pelo IoT Edge, seja oficialmente ou em versão prévia. 

Se você tiver problemas ao usar o serviço de Azure IoT Edge, há várias maneiras de buscar o suporte. Experimente um dos seguintes canais para obter suporte:

**Relatório de bugs** – a maior parte do desenvolvimento que vai para o produto do Azure IoT Edge acontece no projeto de código-fonte aberto do IoT Edge. Bugs podem ser comunicados sobre o [página problemas](https://github.com/azure/iotedge/issues) do projeto. Correções rapidamente se tornam do projeto para atualizações de produtos.

**Equipa de suporte ao cliente da Microsoft** – os utilizadores que têm um [plano de suporte](https://azure.microsoft.com/support/plans/) pode envolver a equipe de suporte ao cliente da Microsoft através da criação de um pedido de suporte diretamente a partir do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Pedidos de funcionalidades** – produto o Azure IoT Edge controla as solicitações de recursos através do produto [página de voz do utilizador](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contentor

Azure IoT Edge módulos são implementados como contêineres, portanto IoT Edge precisa de um mecanismo de contêiner para iniciá-los. A Microsoft fornece um mecanismo de contentor, o mecanismo moby, para cumprir este requisito. Esse mecanismo de contêiner é baseado no projeto de código-fonte aberto do Moby. Docker CE e o Docker EE são outros mecanismos de contentores populares. Eles também são baseados no projeto de código-fonte aberto do Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor esforço para sistemas que usam esses mecanismos de contêiner; no entanto, a Microsoft não pode enviar correções para problemas neles. Por esse motivo, a Microsoft recomenda a utilização de mecanismo de moby em sistemas de produção.

<br>
<center>

![Moby como tempo de execução do contêiner](./media/support/only-moby-for-production.png)
</center>

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

![O sistema operacional host corresponde ao SO convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Escalão 1

Os sistemas listados na tabela a seguir têm suporte da Microsoft, seja geralmente disponível ou em visualização pública, e são testados com cada nova versão. 

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| Raspbian stretch |  | ![Raspbian Stretch + ARM32v7](./media/tutorial-c-module/green-check.png) |  |
| Ubuntu Server 16.04 | ![Ubuntu Server 16, 4 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública  |
| Ubuntu Server 18.04 | ![Ubuntu Server 18, 4 + AMD64](./media/tutorial-c-module/green-check.png) |  | Pré-visualização pública |
| Windows 10 IoT Enterprise, Build 17763 | ![Windows 10 IoT Enterprise + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server 2019, Build 17763 | ![Windows Server 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows Server IoT 2019, Build 17763 | ![Windows Server IoT 2019 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Windows 10 IoT Core, Build 17763 | Pré-visualização pública |  |  |


Os sistemas operacionais Windows listados acima são os requisitos para dispositivos que executam contêineres do Windows no Windows. Essa configuração é a única configuração com suporte para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem o uso de contêineres do Linux no Windows; no entanto, essa configuração é apenas para desenvolvimento e teste. O uso de contêineres do Linux no Windows não é uma configuração com suporte para produção. Qualquer versão do Windows 10 Build 14393 ou mais recente e Windows Server 2016 ou mais recente pode ser usada para este cenário de desenvolvimento.

### <a name="tier-2"></a>Escalão 2

Os sistemas listados na tabela a seguir são considerados compatíveis com Azure IoT Edge, mas não são ativamente testados ou mantidos. 

| Sistema operativo | AMD64 | ARM32v7 | ARM64 |
| ---------------- | ----- | ------- | ----- |
| CentOS 7.5 | ![CentOS + AMD64](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM32v7](./media/tutorial-c-module/green-check.png) | ![CentOS + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 8 | ![Debian 8 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 8 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 9 | ![Debian 9 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 9 + ARM64](./media/tutorial-c-module/green-check.png) |
| Debian 10<sup>1</sup> | ![Debian 10 + AMD64](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Debian 10 + ARM64](./media/tutorial-c-module/green-check.png) |
| RHEL 7.5 | ![RHEL 7,5 + AMD64](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![RHEL 7,5 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 16.04 | ![Ubuntu 16, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 16, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| Ubuntu 18.04 | ![Ubuntu 18, 4 + AMD64](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18, 4 + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Ubuntu 18, 4 + ARM64](./media/tutorial-c-module/green-check.png) |
| O rio de vento 8 | ![Vento Rio 8 + AMD64](./media/tutorial-c-module/green-check.png) |  |  |
| Yocto | ![Yocto + AMD64](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Yocto + ARM64](./media/tutorial-c-module/green-check.png) |
| Raspbian Buster<sup>1</sup> |  | ![Raspbian Buster + ARM32v7](./media/tutorial-c-module/green-check.png) | ![Raspbian Buster + ARM64](./media/tutorial-c-module/green-check.png) |

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
