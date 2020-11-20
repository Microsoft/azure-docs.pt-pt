---
title: SKUs para SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: SKUs para SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKUs, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2732f9fc4b1b9251391180874a055e8ffd8d9e7a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94985081"
---
# <a name="available-skus-for-hana-large-instances"></a>SKUs disponíveis para grandes instâncias HANA

O serviço SAP HANA on Azure (Grandes Instâncias) baseado apenas na Revisão 3 selos, está disponível em várias configurações nas regiões Azure de:

- Leste da Austrália
- Austrália Sudeste
- Leste do Japão
- Oeste do Japão

O serviço SAP HANA on Azure (Grandes Instâncias) com base na Revisão 4 selos está disponível em várias configurações nas regiões Azure de:

- E.U.A. Oeste 2
- E.U.A. Leste

Serviço de Infraestrutura BareMetal (certificado para cargas de trabalho SAP HANA) com base em selos da Revisão 4.2. Está disponível em várias configurações nas regiões Azure de:
- Europa Ocidental
- Europa do Norte
- E.U.A. Leste 2
- E.U.A. Centro-Sul




A lista de instâncias disponíveis do Azure Large que são oferecidas listas como as seguintes.

> [!IMPORTANT]
> Esteja ciente da primeira coluna que representa o estatuto da certificação HANA para cada um dos tipos de Grande Instância da lista. A coluna deve correlacionar-se com o [diretório de hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) para os SKUs Azure que começam com a letra **S**



| SAP HANA certificado | Modelação | Memória Total | DRAM de memória | Optane memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- | --- | --- |
| SIM <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA em Azure S96<br /> – 2 x Intel® Xeon® Processador E7-8890 v4 <br /> 48 núcleos de CPU e 96 fios de CPU |  768 GB | 768 GB | --- | 3.0 TB | Disponível |
| SIM <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA em Azure S224<br /> – 4 x Processador Intel® Xeon® Platinum 8276 <br /> 112 núcleos de CPU e 224 fios de CPU |  3.0 TB | 3.0 TB | --- | 6.3 TB | Disponível |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA em Azure S224m<br /> – 4 x Processador Intel® Xeon® Platinum 8276 <br /> 112 núcleos de CPU e 224 fios de CPU |  6.0 TB | 6.0 TB | --- | 10.5 TB | Disponível |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA em Azure S224om<br /> – 4 x Processador Intel® Xeon® Platinum 8276 <br /> 112 núcleos de CPU e 224 fios de CPU | 6.0 TB |  3.0 TB |  3.0 TB | 10.5 TB | Disponível |
| NO | SAP HANA em Azure S224oo<br /> – 4 x Processador Intel® Xeon® Platinum 8276 <br /> 112 núcleos de CPU e 224 fios de CPU | 4.5 TB |  1,5 TB |  3.0 TB | 8.4 TB | Disponível |
| NO | SAP HANA em Azure S224ooo<br /> – 4 x Processador Intel® Xeon® Platinum 8276 <br /> 112 núcleos de CPU e 224 fios de CPU | 7.5 TB |  1,5 TB |  6.0 TB | 12.7 TB | Disponível |
| NO | SAP HANA em Azure S224oom<br /> – 4 x Processador Intel® Xeon® Platinum 8276 <br /> 112 núcleos de CPU e 224 fios de CPU | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB | Disponível |
| SIM <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA em Azure S384<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 fios de CPU |  4.0 TB | 4.0 TB | --- | 16 TB | Disponível |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA em Azure S384m<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 fios de CPU |  6.0 TB | 6.0 TB | --- | 18 TB |  Disponível  |
| SIM <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA em Azure S384xm<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 fios de CPU |  8.0 TB | 8.0 TB | --- | 22 TB | Disponível |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA em Azure S448<br /> – 8 x Intel® Xeon® processador Platinum 8276 <br /> 224 núcleos de CPU e 448 fios de CPU | 6.0 TB |  6.0 TB |  --- | 10.5 TB | Disponível (apenas rev 4) |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA em Azure S448m<br /> – 8 x Intel® Xeon® processador Platinum 8276 <br /> 224 núcleos de CPU e 448 fios de CPU | 12.0 TB |  12.0 TB |  --- | 18.9 TB | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S448oo<br /> – 8 x Intel® Xeon® processador Platinum 8276 <br /> 224 núcleos de CPU e 448 fios de CPU | 9.0 TB |  3.0 TB |  6.0 TB | 14.8 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S448om<br /> – 8 x Intel® Xeon® processador Platinum 8276 <br /> 224 núcleos de CPU e 448 fios de CPU | 12.0 TB |  6.0 TB |  6.0 TB | 18.9 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S448ooo<br /> – 8 x Intel® Xeon® processador Platinum 8276 <br /> 224 núcleos de CPU e 448 fios de CPU | 15.0 TB |  3.0 TB |  12.0 TB | 23.2 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S448oom<br /> – 8 x Intel® Xeon® processador Platinum 8276 <br /> 224 núcleos de CPU e 448 fios de CPU | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | Disponível (apenas rev 4) |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA em Azure S576m<br /> – 12 x Intel® Xeon® Processador E7-8890 v4<br /> 288 núcleos de CPU e 576 fios de CPU |  12.0 TB | 12.0 TB | --- | 28 TB | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S576xm<br /> – 12 x Intel® Xeon® Processador E7-8890 v4<br /> 288 núcleos de CPU e 576 fios de CPU |  18.0 TB | 18.0 | --- |  41 TB | Disponível |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA em Azure S672<br /> – 12 x Processador Intel® Xeon® Platinum 8276 <br /> 336 núcleos de CPU e 672 fios de CPU | 9.0 TB |  9.0 TB |  --- | 14.7 TB | Disponível (apenas rev 4) |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA em Azure S672m<br /> – 12 x Processador Intel® Xeon® Platinum 8276 <br /> 336 núcleos de CPU e 672 fios de CPU | 18.0 TB |  18.0 TB |  --- | 27.4 TB | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S672oo<br /> – 12 x Processador Intel® Xeon® Platinum 8276 <br /> 336 núcleos de CPU e 672 fios de CPU | 13.5 TB |  4.5 TB |  9.0 TB | 21.1 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S672om<br /> – 12 x Processador Intel® Xeon® Platinum 8276 <br /> 336 núcleos de CPU e 672 fios de CPU | 18.0 TB |  9.0 TB |  9.0 TB | 27.4 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S672ooo<br /> – 12 x Processador Intel® Xeon® Platinum 8276 <br /> 336 núcleos de CPU e 672 fios de CPU | 22.5 TB |  4.5 TB |  18.0 TB | 33.7 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S672oom<br /> – 12 x Processador Intel® Xeon® Platinum 8276 <br /> 336 núcleos de CPU e 672 fios de CPU | 27.0 TB |  9.0 TB |  18.0 TB | 40.0 TB  | Disponível (apenas rev 4) |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA em Azure S768m<br /> – 16 x Intel® Xeon® Processador E7-8890 v4<br /> 384 núcleos de CPU e 768 fios de CPU |  16.0 TB | 16.0 TB | -- | 36 TB | Disponível |
| NO | SAP HANA em Azure S768xm<br /> – 16 x Intel® Xeon® Processador E7-8890 v4<br /> 384 núcleos de CPU e 768 fios de CPU |  24.0 TB | 24.0 TB | --- | 56 TB | Disponível |
|  SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA em Azure S896<br /> – 16 x Processador Intel® Xeon® Platinum 8276 <br /> 448 núcleos de CPU e 896 fios de CPU | 12.0 TB |  12.0 TB |  --- | 18.9 TB | Disponível (apenas rev 4) |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA em Azure S896m<br /> – 16 x Processador Intel® Xeon® Platinum 8276 <br /> 448 núcleos de CPU e 896 fios de CPU | 24.0 TB | 24.0 TB | -- | 35.8 TB | Disponível |
| NO | SAP HANA em Azure S896oo<br /> – 16 x Processador Intel® Xeon® Platinum 8276 <br /> 448 núcleos de CPU e 896 fios de CPU | 18.0 TB |  6.0 TB |  12.0 TB | 27.4 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S896om<br /> – 16 x Processador Intel® Xeon® Platinum 8276 <br /> 448 núcleos de CPU e 896 fios de CPU | 24.0 TB |  12.0 TB |  12.0 TB | 35.8 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S896ooo<br /> – 16 x Processador Intel® Xeon® Platinum 8276 <br /> 448 núcleos de CPU e 896 fios de CPU | 30.0 TB |  6.0 TB |  24.0 TB | 44.3 TB  | Disponível (apenas rev 4) |
| NO | SAP HANA em Azure S896oom<br /> – 16 x Processador Intel® Xeon® Platinum 8276 <br /> 448 núcleos de CPU e 896 fios de CPU | 36.0 TB |  12.0 TB |  24.0 TB | 52.7 TB  | Disponível (apenas rev 4) |
| SIM <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA em Azure S960m<br /> – 20 x Intel® Xeon® Processador E7-8890 v4<br /> 480 núcleos de CPU e 960 fios de CPU |  20.0 TB | 20.0 TB | -- | 46 TB | Disponível (apenas rev 4) |


- Núcleos cpu = soma de núcleos de CPU não hiper-roscados da soma dos processadores da unidade do servidor.
- Fios CPU = soma de fios de cálculo fornecidos por núcleos de CPU hiper roscados da soma dos processadores da unidade do servidor. A maioria das unidades são configuradas por padrão para usar Hyper-Threading Tecnologia.
- Com base nas recomendações do fornecedor S768m, S768xm e S960m não estão configurados para utilizar Hyper-Threading para executar o SAP HANA.


> [!IMPORTANT]
> Os seguintes SKUs, embora ainda apoiados, já não podem ser comprados: S72, S72m, S144, S144m, S192 e S192m 

As configurações específicas escolhidas dependem da carga de trabalho, dos recursos da CPU e da memória desejada. É possível que a carga de trabalho da OLTP utilize os SKUs otimizados para a carga de trabalho do OLAP. 

Duas classes diferentes de hardware dividem os SKUs em:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, e S224m, S224oo, S224om, S224ooo, S224oom são referidos como a "classe Tipo I" dos SKUs.
- Todos os outros SKUs são referidos como a "classe tipo II" das SKUs.
- Se estiver interessado em SKUs que ainda não estejam listados no diretório de hardware SAP, contacte a sua equipa de conta microsoft para obter mais informações. 


Um carimbo completo de HANA Large Instance não é atribuído exclusivamente para um único cliente&#39;uso. Este facto aplica-se aos racks de recursos de computação e armazenamento ligados através de um tecido de rede implantado também no Azure. A infraestrutura HANA Large Instance, tal como a Azure, implanta diferentes &quot; inquilinos de clientes &quot; que estão isolados uns dos outros nos seguintes três níveis:

- **Rede**: Isolamento através de redes virtuais dentro do carimbo HANA Large Instance.
- **Armazenamento**: Isolamento através de máquinas virtuais de armazenamento que tenham volumes de armazenamento atribuídos e isole volumes de armazenamento entre inquilinos.
- **Cálculo**: Atribuição dedicada de unidades de servidor a um único inquilino. Nenhuma divisão dura ou suave de unidades de servidor. Nenhuma partilha de um único servidor ou unidade de anfitrião entre inquilinos. 

As implantações de unidades de grande instância HANA entre diferentes inquilinos não são visíveis entre si. As unidades de grande instância HANA implantadas em diferentes inquilinos não podem comunicar diretamente uns com os outros no nível de selo HANA Large Instance. Apenas unidades de HANA Large Instance dentro de um inquilino podem comunicar entre si no nível de selo HANA Large Instance.

Um inquilino implantado no carimbo de Grande Instância é atribuído a uma subscrição da Azure para efeitos de faturação. Para uma rede, pode ser acedido a partir de redes virtuais de outras subscrições do Azure dentro da mesma inscrição do Azure. Se você implementar com outra subscrição Azure na mesma região de Azure, você também pode optar por pedir um inquilino separado HANA Large Instance.

Existem diferenças significativas entre a execução do SAP HANA na HANA Large Instance e a SAP HANA em VMs implantadas em Azure:

- Não existe uma camada de virtualização para SAP HANA em Azure (Grandes Instâncias). Obtém-se o desempenho do hardware de metal nu.
- Ao contrário do Azure, o servidor SAP HANA on Azure (Large Instances) é dedicado a um cliente específico. Não existe a possibilidade de uma unidade de servidor ou hospedeiro ser dura ou macia. Como resultado, uma unidade HANA Large Instance é usada como um todo atribuído a um inquilino e com isso para você. Um reboot ou encerramento do servidor não leva automaticamente ao sistema operativo e o SAP HANA está a ser implantado noutro servidor. (Para SKUs da classe Tipo I, a única exceção é se um servidor encontrar problemas e a reafectação precisar de ser realizada noutro servidor.)
- Ao contrário do Azure, onde os tipos de processadores hospedeiros são selecionados para a melhor relação preço/desempenho, os tipos de processador escolhidos para SAP HANA em Azure (Grandes Instâncias) são os mais bem-executadas da linha de processador Intel E7v3 e E7v4.

**Próximos passos**
- Consulte [o tamanho do HLI](hana-sizing.md)
