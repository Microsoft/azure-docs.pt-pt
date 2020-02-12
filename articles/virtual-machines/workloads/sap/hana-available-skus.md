---
title: SKUs para SAP HANA no Azure (instâncias grandes) | Microsoft Docs
description: SKUs para SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d5ed903eefea3a8c13bb9e43f5ef71063b453d93
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806648"
---
# <a name="available-skus-for-hli"></a>SKUs Disponíveis para HLI

SAP HANA no serviço do Azure (instâncias grandes) com base em carimbos de revisão 3 está disponível em várias configurações nas regiões do Azure de:

- E.U.A. Oeste
- E.U.A. Leste
- Leste da Austrália
- Austrália Sudeste
- Europa Ocidental
- Europa do Norte
- Leste do Japão
- Oeste do Japão

SAP HANA no serviço do Azure (instâncias grandes) com base na revisão 4 carimbos está disponível em várias configurações nas regiões do Azure de:

- E.U.A. Oeste 2
- E.U.A. Leste
- E.U.A. Centro-Sul
- Europa Ocidental
- Europa do Norte



[SAP Hana SKUs certificados da lista de instâncias grandes do Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como:

| Solução SAP | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho OLAP genérica | SAP HANA no Azure s72<br /> – 2 x Intel® Xeon® Processador E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  768 GB |  3 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S144<br /> – 4 x Intel® Xeon® Processador E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  1.5 TB |  6 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S192<br /> – 4 x Intel® Xeon® Processador E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  2,0 TB |  8 TB | Não é mais oferecido |
| --- | SAP HANA no Azure S224<br /> – 4 x Intel® Xeon® Processador Platinum 8276 (também conhecido como Cascade Lake)<br /> 112 núcleos de CPU e 224 threads de CPU |  3,0 TB |  6,3 TB | Disponível nos carimbos Revision3 e Revision4  |
| --- | SAP HANA no Azure S384<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  4,0 TB |  16 TB | Disponível em carimbos de Revision4 |
| Otimizado para OLTP: SAP Business Suite<br /> em SAP HANA ou S/4HANA (OLTP),<br /> OLTP genérico | SAP HANA no Azure S72m<br /> – 2 x Intel® Xeon® Processador E7-8890 v3<br /> 36 núcleos de CPU e 72 threads de CPU |  1.5 TB |  6 TB | Não é mais oferecido |
|---| SAP HANA no Azure S144m<br /> – 4 x Intel® Xeon® Processador E7-8890 v3<br /> 72 núcleos de CPU e 144 threads de CPU |  3,0 TB |  12 TB | Não é mais oferecido |
|---| SAP HANA no Azure S192m<br /> – 4 x Intel® Xeon® Processador E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU  |  4,0 TB |  16 TB | Não é mais oferecido |
|---| SAP HANA no Azure S384m<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  6,0 TB |  18 TB | Disponível em carimbos de Revision4|
|---| SAP HANA no Azure S384xm<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  8,0 TB |  22 TB |  Disponível em carimbos de Revision4 |
|---| SAP HANA no Azure S576m<br /> – 12 x Intel® Xeon® Processador E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  12,0 TB |  28 TB | Disponível em carimbos de Revision4|
|---| SAP HANA no Azure S768m<br /> – 16 x Intel® Xeon® Processador E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  16,0 TB |  36 TB | Disponível em carimbos de Revision4|
|---| SAP HANA no Azure S960m<br /> – 20 x Intel® Xeon® Processador E7-8890 v4<br /> 480 núcleos de CPU e 960 threads de CPU |  20,0 TB |  46 TB | Disponível em carimbos de Revision4|


Em SAP HANA TDIv5, o SAP permite o dimensionamento específico do cliente e projetos específicos do cliente, o que pode levar a configurações do servidor, que não estão listadas como certificadas no:

- [SAP HANA dispositivos certificados](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA plataformas IaaS certificadas](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Em muitos casos, essas configurações de servidor específicas do cliente carregam mais memória do que as unidades de servidor certificadas com o SAP. Ao trabalhar com o SAP, os clientes têm a possibilidade de obter suporte ao SAP e certificar para suas configurações de servidor dimensionadas específicas do cliente. No Azure, os SKUs padrão do SAP HANA em instâncias grandes estão disponíveis e na lista de preços da Microsoft para esses projetos de dimensionamento específicos do cliente TDIv5.

| SKU|CPU | Memória | Armazenamento | Disponibilidade |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA no Azure S96<br /> – 2 x Intel® Xeon® Processador E7-8890 v4<br /> 48 núcleos de CPU e 96 threads de CPU |  768 GB |  3 TB | Disponível nos carimbos Revision3 e Revision4|


| SKU original que pode ser <br /> memória estendida | CPU | Memória | Armazenamento | Disponibilidade |
| --- | --- | --- | --- | --- |
| S192m pode ser estendido para | SAP HANA no Azure S192xm<br /> – 4 x Intel® Xeon® Processador E7-8890 v4<br /> 96 núcleos de CPU e 192 threads de CPU |  6,0 TB |  16 TB | Não é mais oferecido |
| S384xm pode ser estendido para | SAP HANA no Azure S384xxm<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos de CPU e 384 threads de CPU |  12,0 TB |  28 TB | Disponível em carimbos de Revision4 |
| S576m pode ser estendido para | SAP HANA no Azure S576xm<br /> – 12 x Intel® Xeon® Processador E7-8890 v4<br /> 288 núcleos de CPU e 576 threads de CPU |  18,0 TB |  41 TB | Disponível em carimbos de Revision4|
| S768m pode ser estendido para | SAP HANA no Azure S768xm<br /> – 16 x Intel® Xeon® Processador E7-8890 v4<br /> 384 núcleos de CPU e 768 threads de CPU |  24,0 TB |  56 TB | Disponível em carimbos de Revision4 |

- Núcleos de CPU = soma de núcleos de CPU não hyper-threaded da soma dos processadores da unidade do servidor.
- Threads de CPU = soma dos threads de computação fornecidos por núcleos de CPU Hyper-threaded da soma dos processadores da unidade do servidor. A maioria das unidades é configurada por padrão para usar a tecnologia Hyper-Threading.
- Com base nas recomendações de fornecedor, o S768m, o S768xm e o S960m não estão configurados para usar o hyperthreading para execução de SAP HANA.


As configurações específicas escolhidas dependem da carga de trabalho, dos recursos da CPU e da memória desejada. É possível que a carga de trabalho OLTP use as SKUs otimizadas para a carga de trabalho OLAP. 

A base de hardware para as ofertas, exceto unidades para projetos de dimensionamento específicos do cliente, é SAP HANA certificados por TDI. Duas classes diferentes de hardware dividem as SKUs em:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm e S224, que são chamados de "classe Type I" de SKUs.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm e S960m, que são chamados de "classe do tipo II" de SKUs.
- Se você estiver interessado em outras ofertas de S224 SKUs de 4,5 TB a 9TB com Optane, entre em contato com sua equipe de conta Microsoft para obter mais informações. 


Um carimbo de instância grande do HANA completo não é alocado exclusivamente para&#39;uso de um único cliente. Esse fato se aplica aos racks de recursos de computação e armazenamento conectados por meio de uma malha de rede implantada no Azure também. A infraestrutura do HANA em instâncias grandes, como o Azure, implanta diferentes locatários de &quot;de clientes&quot; isolados uns dos outros nos três níveis a seguir:

- **Rede**: isolamento por meio de redes virtuais dentro do carimbo de instância grande do Hana.
- **Armazenamento**: isolamento por meio de máquinas virtuais de armazenamento que têm volumes de armazenamento atribuídos e isolam volumes de armazenamento entre locatários.
- **Computação**: atribuição dedicada de unidades de servidor a um único locatário. Nenhum particionamento de unidades de servidor ou hardware rígido. Nenhum compartilhamento de uma única unidade de host ou servidor entre locatários. 

As implantações de unidades de instância grande do HANA entre locatários diferentes não são visíveis entre si. As unidades do HANA em instâncias grandes implantadas em locatários diferentes não podem se comunicar diretamente entre si no nível de carimbo de instância grande do HANA. Somente as unidades de instância grande do HANA dentro de um locatário podem se comunicar entre si no nível de carimbo de instância grande do HANA.

Um locatário implantado no carimbo de instância grande é atribuído a uma assinatura do Azure para fins de cobrança. Para uma rede, ela pode ser acessada de redes virtuais de outras assinaturas do Azure dentro do mesmo registro do Azure. Se você implantar com outra assinatura do Azure na mesma região do Azure, também poderá optar por solicitar um locatário de instância grande do HANA separado.

Há diferenças significativas entre a execução de SAP HANA na instância grande do HANA e SAP HANA em execução em VMs implantadas no Azure:

- Não há nenhuma camada de virtualização para SAP HANA no Azure (instâncias grandes). Você Obtém o desempenho do hardware bare-metal subjacente.
- Ao contrário do Azure, o servidor de SAP HANA no Azure (instâncias grandes) é dedicado a um cliente específico. Não há nenhuma possibilidade de que uma unidade de servidor ou host seja particionado de maneira rígida ou flexível. Como resultado, uma unidade de instância grande do HANA é usada como sendo atribuída como um todo para um locatário e com isso para você. Uma reinicialização ou desligamento do servidor não leva automaticamente ao sistema operacional e SAP HANA implantado em outro servidor. (Para SKUs de classe do tipo I, a única exceção é se um servidor encontra problemas e a reimplantação precisa ser executada em outro servidor.)
- Ao contrário do Azure, em que os tipos de processador de host são selecionados para a melhor taxa de preço/desempenho, os tipos de processador escolhidos para SAP HANA no Azure (instâncias grandes) são o mais alto desempenho da linha de processador Intel E7v3 e E7v4.

**Passos seguintes?**
- Fazer referência ao [dimensionamento de HLI](hana-sizing.md)
