---
title: SKUs para o SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: SKUs para o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 043ecdcb83d2ee39e3373f029c00c6237f87601c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709710"
---
# <a name="available-skus-for-hli"></a>SKUs Disponíveis para HLI

SAP HANA no serviço do Azure (instâncias grandes) está disponível em várias configurações nas regiões do Azure dos EUA oeste e EUA leste, leste da Austrália, Sudeste da Austrália, Europa Ocidental, Europa do Norte, leste do Japão e oeste do Japão.

[SKUs de HANA nas instâncias grandes com certificação SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) lista, como:

| Solução SAP | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho OLAP genérica | SAP HANA no Azure S72<br /> – 2 x Intel® Xeon® processador E7 8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  768 GB |  3 TB | Não é fornecido já |
| --- | SAP HANA no Azure S144<br /> – 4 x Intel® Xeon® processador E7 8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  1,5 TB |  6 TB | Não é fornecido já |
| --- | SAP HANA em Azure S192<br /> – 4 x Intel® Xeon® processador E7 8890 v4<br /> 96 núcleos de CPU e de 192 threads de CPU |  2.0 TB DE |  8 TB | Disponível |
| --- | SAP HANA no Azure S384<br /> e 8 x processadores Intel® Xeon® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4.0 TB |  16 TB | Disponível |
| Otimizado para OLTP: SAP Business Suite<br /> no SAP HANA ou S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA no Azure S72m<br /> – 2 x Intel® Xeon® processador E7 8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  1,5 TB |  6 TB | Não é fornecido já |
|---| SAP HANA no Azure S144m<br /> – 4 x Intel® Xeon® processador E7 8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  3.0 TB |  12 TB | Não é fornecido já |
|---| SAP HANA em Azure S192m<br /> – 4 x Intel® Xeon® processador E7 8890 v4<br /> 96 núcleos de CPU e de 192 threads de CPU  |  4.0 TB |  16 TB | Disponível |
|---| SAP HANA no Azure S384m<br /> e 8 x processadores Intel® Xeon® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6.0 TB |  18 TB | Disponível |
|---| SAP HANA no Azure S384xm<br /> e 8 x processadores Intel® Xeon® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8.0 TB |  22 TB |  Disponível |
|---| SAP HANA no Azure S576m<br /> – 12 x processadores Intel® Xeon® E7 8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12.0 TB |  28 TB | Disponível |
|---| SAP HANA no Azure S768m<br /> – 16 x processadores Intel® Xeon® E7 8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16.0 TB |  36 TB | Disponível |
|---| SAP HANA no Azure S960m<br /> a 20 x processadores Intel® Xeon® E7 8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20.0 TB |  46 TB | Disponível |


Em TDIv5 do SAP HANA, SAP permite o dimensionamento de específicas do cliente e projetos de específicas do cliente que podem levar a configurações de servidor que não estão listadas como a certificação na:

- [Aplicações com certificação do SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Plataformas de IaaS com certificação SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Em muitos casos, estas configurações de servidor específicas do cliente realizar mais memória do que as unidades de servidor com certificação SAP. Ao trabalhar com o SAP, os clientes têm a possibilidade de obter suporte do SAP e certificar-se para as respetivas configurações de servidor específicas do cliente de tamanho. No Azure, os seguintes SKU standard de instância grande do HANA estão disponíveis e no Microsoft preço de lista para tais projetos de dimensionamento de específicas do cliente TDIv5.

| SKU|CPU | Memória | Armazenamento | Disponibilidade |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA no Azure S96<br /> – 2 x Intel® Xeon® processador E7 8890 v4<br /> 48 núcleos de CPU e 96 threads de CPU |  768 GB |  3 TB | Disponível |


| SKU do original que pode ser <br /> expandido na memória | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| S192m podem ser estendidos para | SAP HANA no Azure S192xm<br /> – 4 x Intel® Xeon® processador E7 8890 v4<br /> 96 núcleos de CPU e de 192 threads de CPU |  6.0 TB |  16 TB | Disponível |
| S384xm podem ser estendidos para | SAP HANA no Azure S384xxm<br /> e 8 x processadores Intel® Xeon® E7 8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  12.0 TB |  28 TB | Disponível |
| S576m podem ser estendidos para | SAP HANA no Azure S576xm<br /> – 12 x processadores Intel® Xeon® E7 8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  VERSÕES 18.0 TB |  41 TB | Disponível |
| S768m podem ser estendidos para | SAP HANA no Azure S768xm<br /> – 16 x processadores Intel® Xeon® E7 8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  24.0 TB |  56 TB | Disponível |

- Núcleos de CPU = sum não-hyper-thread de núcleos de CPU da soma de processadores da unidade do servidor.
- Threads de CPU = sum de threads de computação fornecidos pelo hyper-thread núcleos de CPU da soma de processadores da unidade do servidor. A maioria das unidades são configuradas por predefinição para utilizar a tecnologia Hyper-Threading.
- Com base nas recomendações do fornecedor S768m, S768xm e S960m não estão configuradas para utilizar o Hyper-Threading para execução do SAP HANA.


As configurações específicas escolhidas são dependentes de carga de trabalho, recursos da CPU e memória pretendida. É possível que a carga de trabalho OLTP utilizar os SKUs que estão otimizados para a carga de trabalho OLAP. 

O hardware base para as ofertas, exceto unidades para projetos de dimensionamento de específicas do cliente, são com certificação TDI do SAP HANA. Duas classes diferentes de hardware dividem os SKUs em:

- S72, S72m, S96, S144, S144m, S192, S192m e S192xm, que são referidas como "Tipo de classe," de SKUs.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm e S960m, que são conhecidos como o "Tipo de classe II" dos SKUs.

Um carimbo de data / instância grande do HANA completo não está alocada exclusivamente para um único cliente&#39;s de utilização. Esse fato aplica-se para os bastidores de recursos de computação e armazenamento ligados através de um recurso de infraestrutura de rede implementado no Azure, também. Infraestrutura de instância grande do HANA, como o Azure, implementa o outro cliente &quot;inquilinos&quot; que são isolados uns dos outros nos seguintes três níveis:

- **Rede**: Isolamento através de redes virtuais dentro do carimbo de data / instância grande do HANA.
- **Armazenamento**: Isolamento através de máquinas de virtuais de armazenamento que tenham volumes de armazenamento atribuído e isolar os volumes de armazenamento entre inquilinos.
- **Computação**: Dedicada atribuição das unidades de servidor para um único inquilino. Não muito ou por software de criação de partições de unidades de servidor. Sem partilha de uma única unidade de anfitrião ou servidor entre inquilinos. 

As implementações de unidades de instância grande do HANA entre diferentes inquilinos não são visíveis para si. Unidades de instância grande do HANA implementadas em diferentes inquilinos não podem comunicar diretamente entre si no nível de carimbo de data / instância grande do HANA. Apenas as unidades de instância grande do HANA dentro de um inquilino podem comunicar entre si no nível de carimbo de data / instância grande do HANA.

Um inquilino implementado no carimbo de data / instância grande é atribuído a uma subscrição do Azure para efeitos de faturação. Para uma rede, pode ser acedido a partir de redes virtuais de outras subscrições do Azure dentro da mesmo inscrição do Azure. Se implementar com outra subscrição do Azure na mesma região do Azure, também pode optar por solicitar um inquilino de instância grande do HANA separado.

Existem diferenças significativas entre executar o SAP HANA nas instâncias grandes do HANA e SAP HANA em execução em VMs implementadas no Azure:

- Não há nenhuma camada de virtualização para o SAP HANA no Azure (instâncias grandes). Obtém o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o SAP HANA no servidor do Azure (instâncias grandes) é dedicado a um cliente específico. Não existe nenhuma possibilidade que uma unidade de servidor ou o anfitrião está duro ou por software particionado. Como resultado, uma unidade de instância grande do HANA é utilizada como atribuído como um todo para um inquilino e com isso para. Um reinício ou encerramento do servidor não gerar automaticamente o sistema operativo e a ser implementado em outro servidor do SAP HANA. (Para o tipo de classe eu SKUs, a única exceção é se um servidor encontra problemas e a nova implementação tem de ser efetuada noutro servidor.)
- Ao contrário do Azure, onde os tipos de processador do anfitrião estão selecionados para a proporção de relação preço/desempenho melhor, os tipos de processador escolhidos para SAP HANA no Azure (instâncias grandes) são a realização de mais alto da linha de processador Intel E7v3 e E7v4.

**Passos seguintes?**
- Consulte [HLI dimensionamento](hana-sizing.md)
