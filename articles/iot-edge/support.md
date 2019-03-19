---
title: Sistemas operativos, mecanismos de contentor - Azure IoT Edge suportados | Documentos da Microsoft
description: Saiba quais sistemas operacionais podem executar o daemon de Azure IoT Edge e tempo de execução e mecanismos de contentor suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5c215c125237d72875155f15002fca6c4bee53b1
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58077739"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas de suporte do Azure IoT Edge

Existem várias formas para buscar suporte para o produto do Azure IoT Edge.

**Relatório de bugs** – a maior parte do desenvolvimento que vai para o produto do Azure IoT Edge acontece no projeto de código-fonte aberto do IoT Edge. Bugs podem ser comunicados sobre o [página problemas](https://github.com/azure/iotedge/issues) do projeto. Correções rapidamente se tornam do projeto para atualizações de produtos.

**Equipa de suporte ao cliente da Microsoft** – os utilizadores que têm um [plano de suporte](https://azure.microsoft.com/support/plans/) pode envolver a equipe de suporte ao cliente da Microsoft através da criação de um pedido de suporte diretamente a partir do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Pedidos de funcionalidades** – produto o Azure IoT Edge controla as solicitações de recursos através do produto [página de voz do utilizador](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contentor
O Azure IoT Edge tem um mecanismo de contentor para iniciar módulos, uma vez que eles são implementados como contentores. A Microsoft fornece um mecanismo de contentor, o mecanismo moby, para cumprir este requisito. Baseia-se no projeto de código-fonte aberto Moby. Docker CE e o Docker EE são outros mecanismos de contentores populares. Eles também são baseados no projeto de código-fonte aberto Moby e são compatíveis com o Azure IoT Edge. A Microsoft fornece o melhor de esforço de suporte para sistemas usando esses mecanismos de contentor; No entanto, a Microsoft não tem a capacidade de enviarem correções para problemas nas mesmas. Por esse motivo, a Microsoft recomenda a utilização de mecanismo de moby em sistemas de produção.

<br>
<center>

![Moby como tempo de execução do contentor](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operativos
O Azure IoT Edge é executado na maioria dos sistemas operacionais que possam executar contentores; No entanto, todos esses não são igualmente suportados. Sistemas operativos são agrupados em camadas que representam o nível de suporte, os usuários podem esperar.
* Sistemas de escalão 1 podem ser considerados como oficialmente suportada. Isso significa que Microsoft:
    * tem este sistema operativo em testes automatizados
    * Fornece os pacotes de instalação das mesmas
* Os sistemas de camada 2 podem ser considerados como compatível com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Isso significa que:
    * A Microsoft fez teste ad hoc nas plataformas ou sabe-se de um parceiro com êxito com o Azure IoT Edge na plataforma
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família de SO anfitrião tem de corresponder sempre a família de utilizado no interior do contentor de um módulo de SO convidado. Em outras palavras, só pode utilizar contentores do Linux no Linux e contentores do Windows no Windows. Ao utilizar o Windows, apenas os contentores de processo isolado são suportados, não hyper-v contentores isolados.  

<br>
<center>

![SO anfitrião corresponda ao SO convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Escalão 1
Disponível em geral

| Sistema Operativo | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian stretch | Não | Sim|
| Ubuntu Server 16.04 | Sim | Não |
| Ubuntu Server 18.04 | Sim | Não |

Pré-visualização pública

| Sistema Operativo | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Compilação do Windows 10 IoT Core 17763 | Sim | Não |
| Windows 10 IoT Enterprise compilação 17763 | Sim | Não |
| Windows Server 2019 | Sim | Não |

Os sos de Windows acima são os requisitos para dispositivos que executam contentores do Windows no Windows. Esta é a única configuração suportada para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem a utilização de contentores do Linux no Windows; No entanto, isso é para desenvolvimento e teste apenas. Utilização de contentores do Linux no Windows não é uma configuração suportada para produção. Qualquer versão de compilação 14393 ou mais recente do Windows 10 e Windows Server 2016 ou mais recente pode ser utilizada para este cenário de desenvolvimento.

### <a name="tier-2"></a>Escalão 2

| Sistema Operativo | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Sim | Sim |
| Debian 8 | Sim | Sim |
| Debian 9 | Sim | Sim |
| RHEL 7.5 | Sim | Sim |
| Ubuntu 18.04 | Sim | Sim |
| Ubuntu 16.04 | Sim | Sim |
| O rio de vento 8 | Sim | Não |
| Yocto | Sim | Não |


## <a name="virtual-machines"></a>Virtual Machines
O Azure IoT Edge pode ser executado em máquinas virtuais. Isso é comum quando os clientes que pretendem aumentar a infraestrutura existente com informações de periferia. A família de SO da VM anfitrião tem de corresponder a família de utilizado no interior do contentor de um módulo de SO convidado. Este é o requisito mesmo quando o Azure IoT Edge é executado diretamente num dispositivo. O Azure IoT Edge é agnóstico da tecnologia de Virtualização subjacente e funciona em VMs com tecnologia de plataformas como o Hyper-V e do vSphere.

<br>
<center>

![O Azure IoT Edge numa VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
O Azure IoT Edge é executado excelente nos dispositivos tão pequenos como um Raspberry Pi3 ao hardware de nível de servidor. Escolher o hardware certo para o seu cenário é bastante dependente de cargas de trabalho que pretende executar. Tomar a decisão final do dispositivo pode ser complicado. No entanto, pode começar facilmente uma solução de criação de protótipos em laptops tradicionais ou ambientes de trabalho.

Experiência durante a criação de protótipos irá ajudá-lo a seleção de final do dispositivo. Perguntas que deve considerar incluem: quantos módulos compõe a sua carga de trabalho, o número de camadas de fazer, compartilhamento de contentores dos seus módulos, em que idioma são os módulos de escrita, a quantidade de dados serão seus módulos estar a processar, fazer sua necessidade de módulos qualquer especializadas hardware para acelerar as cargas de trabalho, quais são as características de desempenho pretendido da sua solução, o que é o seu orçamento de hardware?
