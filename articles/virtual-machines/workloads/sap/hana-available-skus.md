---
title: SKUs para SAP HANA on Azure (Grandes Instâncias) [ Microsoft Docs
description: SKUs para SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617266"
---
# <a name="available-skus-for-hli"></a>SKUs Disponíveis para HLI

O serviço SAP HANA on Azure (Grandes Instâncias) com base em selos de Revisão 3 está disponível em várias configurações nas regiões de Azure de:

- E.U.A. Oeste
- E.U.A. Leste
- Leste da Austrália
- Austrália Sudeste
- Europa ocidental
- Europa do Norte
- Leste do Japão
- Oeste do Japão

O serviço SAP HANA on Azure (Grandes Instâncias) com base nos selos da Revisão 4 está disponível em várias configurações nas regiões de Azure de:

- E.U.A.Oeste 2
- E.U.A. Leste
- E.U.A. Centro-Sul
- Europa ocidental
- Europa do Norte



[SAP HANA certificada SKUs da lista de grandes instâncias HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) como:

| Solução SAP | CPU | Memória | Storage | Disponibilidade |
| --- | --- | --- | --- | --- |
| Otimizado para OLAP: SAP BW, BW/4HANA<br /> ou SAP HANA para carga de trabalho genérica OLAP | SAP HANA no Azure S72<br /> – 2 x Intel® Xeon® Processador E7-8890 v3<br /> 36 núcleos CPU e 72 fios CPU |  768 GB |  3 TB | Não oferecido mais |
| --- | SAP HANA em Azure S144<br /> – 4 x Intel® Xeon® Processador E7-8890 v3<br /> 72 núcleos cpu e 144 fios CPU |  1,5 TB |  6 TB | Não oferecido mais |
| --- | SAP HANA em Azure S192<br /> – 4 x Intel® Xeon® Processador E7-8890 v4<br /> 96 núcleos cpu e 192 fios CPU |  2.0 TB |  8 TB | Não oferecido mais |
| --- | SAP HANA no Azure S224<br /> – 4 x Intel® Xeon® processador Platinum 8276 (também conhecido como lago Cascade)<br /> 112 núcleos CPU e 224 fios CPU |  3.0 TB |  6.3 TB | Disponível nos selos Revision3 e Revision4  |
| --- | SAP HANA em Azure S384<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos cpu e 384 fios CPU |  4.0 TB |  16 TB | Disponível em selos Revision4 |
| Otimizado para OLTP: SAP Business Suite<br /> em SAP HANA ou S/4HANA (OLTP),<br /> oLTP genérico | SAP HANA em Azure S72m<br /> – 2 x Intel® Xeon® Processador E7-8890 v3<br /> 36 núcleos CPU e 72 fios CPU |  1,5 TB |  6 TB | Não oferecido mais |
|---| SAP HANA em Azure S144m<br /> – 4 x Intel® Xeon® Processador E7-8890 v3<br /> 72 núcleos cpu e 144 fios CPU |  3.0 TB |  12 TB | Não oferecido mais |
|---| SAP HANA em Azure S192m<br /> – 4 x Intel® Xeon® Processador E7-8890 v4<br /> 96 núcleos cpu e 192 fios CPU  |  4.0 TB |  16 TB | Não oferecido mais |
| --- | SAP HANA em Azure S224m<br /> – 4 x Intel® Xeon® processador Platinum 8276 (também conhecido como lago Cascade)<br /> 112 núcleos CPU e 224 fios CPU |  6.0 TB |  10,5 TB | Disponível nos selos Revision3 e Revision4  |
|---| SAP HANA em Azure S384m<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos cpu e 384 fios CPU |  6.0 TB |  18 TB | Disponível em selos Revision4|
|---| SAP HANA em Azure S384xm<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos cpu e 384 fios CPU |  8.0 TB |  22 TB |  Disponível em selos Revision4 |
|---| SAP HANA em Azure S576m<br /> – 12 x Intel® Xeon® Processador E7-8890 v4<br /> 288 núcleos CPU e 576 fios CPU |  12.0 TB |  28 TB | Disponível em selos Revision4|
|---| SAP HANA em Azure S768m<br /> – 16 x Intel® Xeon® Processador E7-8890 v4<br /> 384 núcleos CPU e 768 fios CPU |  16.0 TB |  36 TB | Disponível em selos Revision4|
|---| SAP HANA em Azure S960m<br /> – 20 x Intel® Xeon® Processador E7-8890 v4<br /> 480 núcleos cpu e 960 fios CPU |  20.0 TB |  46 TB | Disponível em selos Revision4|


No âmbito do SAP HANA TDIv5, o SAP permite o dimensionamento específico do cliente e projetos específicos para o cliente, o que pode levar a configurações de servidores, que não estão listadas como certificadas em:

- [Eletrodomésticos Certificados SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Plataformas IaaS certificadas SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

Em muitos casos, estas configurações específicas do servidor têm mais memória do que as unidades de servidor certificadas com o SAP. Ao trabalhar com o SAP, os clientes têm a possibilidade de obter suporte SAP e certificar para as configurações do servidor de tamanho cliente específico. No Azure, estão disponíveis os seguintes SKUs padrão HANA Large Instance e na lista de preços da Microsoft para tais projetos de tamanho específicos para o cliente TDIv5.

| SKU|CPU | Memória | Storage | Disponibilidade |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA em Azure S96<br /> – 2 x Intel® Xeon® Processador E7-8890 v4<br /> 48 núcleos CPU e 96 fios CPU |  768 GB |  3 TB | Disponível nos selos Revision3 e Revision4|


| SKU original que pode ser <br /> estendido na memória | CPU | Memória | Storage | Disponibilidade |
| --- | --- | --- | --- | --- |
| S192m pode ser estendido a | SAP HANA em Azure S192xm<br /> – 4 x Intel® Xeon® Processador E7-8890 v4<br /> 96 núcleos cpu e 192 fios CPU |  6.0 TB |  16 TB | Não oferecido mais |
| S384xm pode ser estendido a | SAP HANA em Azure S384xxm<br /> – 8 x Intel® Xeon® Processador E7-8890 v4<br /> 192 núcleos cpu e 384 fios CPU |  12.0 TB |  28 TB | Disponível em selos Revision4 |
| S576m pode ser estendido a | SAP HANA em Azure S576xm<br /> – 12 x Intel® Xeon® Processador E7-8890 v4<br /> 288 núcleos CPU e 576 fios CPU |  18.0 TB |  41 TB | Disponível em selos Revision4|
| S768m pode ser estendido a | SAP HANA em Azure S768xm<br /> – 16 x Intel® Xeon® Processador E7-8890 v4<br /> 384 núcleos CPU e 768 fios CPU |  24.0 TB |  56 TB | Disponível em selos Revision4 |

- Núcleos CPU = soma de núcleos CPU não-hiper-roscados da soma dos processadores da unidade de servidor.
- Fios CPU = soma de fios computacionais fornecidos por núcleos de CPU hiper-roscados da soma dos processadores da unidade de servidor. A maioria das unidades são configuradas por padrão para usar a tecnologia Hyper-Threading.
- Com base nas recomendações do fornecedor S768m, S768xm e S960m não estão configurados para utilizar hiper-threading para executar SAP HANA.


As configurações específicas escolhidas dependem da carga de trabalho, dos recursos do CPU e da memória desejada. É possível que a carga de trabalho da OLTP utilize as SKUs otimizadas para a carga de trabalho oLAP. 

A base de hardware para as ofertas, com exceção das unidades para projetos de dimensionamento específicos do cliente, é certificada por SAP HANA TDI. Duas classes diferentes de hardware dividem as SKUs em:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 e S224m que são referidos como a "classe Tipo I" das SKUs.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm e S960m, que são referidos como a "classe Tipo II" das SKUs.
- Se estiver interessado noutras ofertas S224 SKUs de 4.5TB a 9TB com a Optane, contacte a sua equipa de conta microsoft para obter mais informações. 


Um carimbo completo hana large instance não é atribuído exclusivamente para um único cliente&#39;uso. Este facto aplica-se aos racks de recursos de computação e armazenamento ligados através de um tecido de rede implantado também no Azure. A infraestrutura HANA Large Instance, &quot;como&quot; a Azure, implanta diferentes inquilinos de clientes isolados uns dos outros nos seguintes três níveis:

- **Rede**: Isolamento através de redes virtuais dentro do carimbo HANA Large Instance.
- **Armazenamento**: Isolamento através de máquinas virtuais de armazenamento que tenham volumes de armazenamento atribuídos e isoladovolumes de armazenamento entre inquilinos.
- **Compute**: Atribuição dedicada de unidades de servidor a um único inquilino. Sem divisórias duras ou suaves de unidades de servidor. Sem partilha de um único servidor ou unidade de acolhimento entre inquilinos. 

As implantações de unidades hana large instance entre diferentes inquilinos não são visíveis umas para as outras. As unidades hana Large Instance implantadas em diferentes inquilinos não podem comunicar diretamente uns com os outros no nível de selo hana large instância. Apenas unidades hana large instance dentro de um inquilino podem comunicar entre si no nível de carimbo de grande instância HANA.

Um inquilino destacado no carimbo de Grande Instância é atribuído a uma assinatura Azure para efeitos de faturação. Para uma rede, pode ser acedida a partir de redes virtuais de outras subscrições do Azure dentro da mesma matrícula do Azure. Se você implementar com outra subscrição Azure na mesma região de Azure, você também pode optar por pedir um inquilino hana large instância separado.

Existem diferenças significativas entre executar sAP HANA em HANA Large Instance e SAP HANA em execução em VMs implantados em Azure:

- Não existe uma camada de virtualização para o SAP HANA em Azure (Grandes Instâncias). Obtém-se o desempenho do hardware de metal nu subjacente.
- Ao contrário do Azure, o servidor SAP HANA on Azure (Grandes Instâncias) é dedicado a um cliente específico. Não existe a possibilidade de uma unidade de servidor ou hospedeiro ser dura ou suave mente dividida. Como resultado, uma unidade HANA Large Instance é usada como um todo para um inquilino e com isso para você. Um reboot ou desativação do servidor não conduz automaticamente ao sistema operativo e o SAP HANA está a ser implantado noutro servidor. (Para as SKUs da classe I, a única exceção é se um servidor encontrar problemas e reimplementação precisa ser realizado em outro servidor.)
- Ao contrário do Azure, onde os tipos de processadores de anfitriões são selecionados para a melhor relação preço/desempenho, os tipos de processadores escolhidos para SAP HANA no Azure (Grandes Instâncias) são os mais bem-sucedidos da linha de processadorIntel E7v3 e E7v4.

**Passos seguintes**
- Consulte [o tamanho hli](hana-sizing.md)
