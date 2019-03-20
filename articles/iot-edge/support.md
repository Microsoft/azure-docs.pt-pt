---
title: Sistemas operativos, mecanismos de contentor - Azure IoT Edge suportados | Documentos da Microsoft
description: Saiba quais sistemas operacionais podem executar o daemon de Azure IoT Edge e tempo de execução e mecanismos de contentor suportados para os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5bc133e81f9917aafb406a6bfb27922cdba48ef5
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58190010"
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
O Azure IoT Edge é executado na maioria dos sistemas operacionais que possam executar contentores; No entanto, todos esses sistemas não são igualmente suportados. Sistemas operativos são agrupados em camadas que representam o nível de suporte, os usuários podem esperar.
* Sistemas de escalão 1 podem ser considerados como oficialmente suportada. Para sistemas de camada 1, Microsoft:
    * tem este sistema operativo em testes automatizados
    * Fornece os pacotes de instalação das mesmas
* Os sistemas de camada 2 podem ser considerados como compatível com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Para sistemas de camada 2:
    * A Microsoft fez teste ad hoc nas plataformas ou sabe-se de um parceiro com êxito com o Azure IoT Edge na plataforma
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família de SO anfitrião tem de corresponder sempre a família de utilizado no interior do contentor de um módulo de SO convidado. Em outras palavras, só pode utilizar contentores do Linux no Linux e contentores do Windows no Windows. Ao utilizar o Windows, o processo só são suportados contentores isolados, o Hyper-V não isolado contentores.  

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
| No Windows 10 IoT Core, compilação 17763 | Sim | Não |
| Windows 10 IoT Enterprise, build 17763 | Sim | Não |
| Windows Server 2019 | Sim | Não |

Os sistemas de operativos Windows indicados acima são os requisitos para dispositivos que executam os contentores do Windows no Windows. Esta configuração é a única configuração suportada para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem a utilização de contentores do Linux no Windows; No entanto, esta configuração é para desenvolvimento e teste apenas. Utilização de contentores do Linux no Windows não é uma configuração suportada para produção. Qualquer versão de compilação 14393 ou mais recente do Windows 10 e Windows Server 2016 ou mais recente pode ser utilizada para este cenário de desenvolvimento.

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
O Azure IoT Edge pode ser executado em máquinas virtuais. Dispositivo utilizando uma máquina virtual como do IoT Edge é comum quando os clientes que pretendem aumentar a infraestrutura existente com informações de periferia. A família de SO da VM anfitrião tem de corresponder a família de utilizado no interior do contentor de um módulo de SO convidado. Este requisito é igual ao Azure IoT Edge é executado diretamente num dispositivo. O Azure IoT Edge é agnóstico da tecnologia de Virtualização subjacente e funciona em VMs com tecnologia de plataformas como o Hyper-V e do vSphere.

<br>
<center>

![O Azure IoT Edge numa VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
O Azure IoT Edge é executado excelente nos dispositivos tão pequenos como um Raspberry Pi3 ao hardware de nível de servidor. Escolher o hardware certo para o seu cenário depende as cargas de trabalho que pretende executar. Tomar a decisão final do dispositivo pode ser complicado. No entanto, pode começar facilmente uma solução de criação de protótipos em laptops tradicionais ou ambientes de trabalho.

Experiência durante a criação de protótipos irá ajudá-lo a seleção de final do dispositivo. Perguntas que deve considerar incluem: 

* Quantos módulos são na carga de trabalho?
* Como muitas camadas deve partilhar contentores dos seus módulos
* Em que idioma são seus módulos escritos? 
* A quantidade de dados serão seus módulos estar a processar?
* Os módulos é necessário qualquer hardware especializado para acelerar as cargas de trabalho?
* Quais são as características de desempenho pretendido da sua solução?
* O que é o seu orçamento de hardware?
