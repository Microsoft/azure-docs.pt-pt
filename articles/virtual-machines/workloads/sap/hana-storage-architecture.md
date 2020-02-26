---
title: Arquitetura de armazenamento da SAP HANA em Azure (Grandes Instâncias)  Microsoft Docs
description: Arquitetura de armazenamento de como implantar SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1d25201c8195fa6c4c36e159904b5b71a20a45ea
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598498"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Arquitetura de armazenamento SAP HANA (Grandes Instâncias)

O layout de armazenamento para SAP HANA on Azure (Grandes Instâncias) é configurado pela SAP HANA no modelo de implementação clássico de acordo com as diretrizes recomendadas pelo SAP. As diretrizes estão documentadas no livro branco de [armazenamento SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

A grande instância HANA da classe Tipo I vem com quatro vezes o volume de memória como volume de armazenamento. Para a classe Tipo II das unidades HANA Large Instance, o armazenamento não é quatro vezes mais. As unidades vêm com um volume destinado a armazenar cópias de segurança de registo de transações HANA. Para mais informações, consulte [Instalar e configurar o SAP HANA (Grandes Instâncias) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte a tabela seguinte em termos de atribuição de armazenamento. A tabela enumera a capacidade áspera para os diferentes volumes fornecidos com as diferentes unidades HANA Large Instance.

| HANA Grande Instância SKU | hana/dados | hana/log | hana/compartilhado | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3\.328 GB | 768 GB |1\.280 GB | 768 GB |
| S96 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4\.608 GB | 1\.024 GB | 1\.536 GB | 1\.024 GB |
| S192m | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S192xm |  11.520 GB |  1\.536 GB |  1\.792 GB |  1\.536 GB |
| S224 |  4\.224 GB |  512 GB |  1\.024 GB |  512 GB |
| S224m |  8\.448 GB |  512 GB |  1\.024 GB |  512 GB |
| S384 | 11.520 GB | 1\.536 GB | 1\.792 GB | 1\.536 GB |
| S384m | 12.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xm | 16.000 GB | 2\.050 GB | 2\.050 GB | 2\.040 GB |
| S384xxm |  20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576m | 20.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S576xm | 31.744 GB | 4\.096 GB | 2\.048 GB | 4\.096 GB |
| S768m | 28.000 GB | 3\.100 GB | 2\.050 GB | 3\.100 GB |
| S768xm | 40.960 GB | 6\.144 GB | 4\.096 GB | 6\.144 GB |
| S960m | 36.000 GB | 4\.100 GB | 2\.050 GB | 4\.100 GB |


Os volumes implantados reais podem variar em função da implementação e da ferramenta que é usada para mostrar os tamanhos de volume.

Se subdividir um HANA Large Instance SKU, alguns exemplos de possíveis peças de divisão podem parecer:

| Partição de memória em GB | hana/dados | hana/log | hana/compartilhado | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1\.280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1\.792 GB | 640 GB | 1\.024 GB | 640 GB |
| 1,536 | 3\.328 GB | 768 GB | 1\.280 GB | 768 GB |


Estes tamanhos são números de volume brutoque podem variar ligeiramente com base na implementação e nas ferramentas usadas para olhar para os volumes. Há também outros tamanhos de divisórias, como 2,5 TB. Estes tamanhos de armazenamento são calculados com uma fórmula semelhante à utilizada para as divisórias anteriores. O termo "divisórias" não significa que o sistema operativo, a memória ou os recursos da CPU estejam de alguma forma divididos. Indica divisórias de armazenamento para as diferentes instâncias HANA que você pode querer implementar numa única unidade HANA Large Instance. 

Pode precisar de mais armazenamento. Pode adicionar armazenamento comprando armazenamento adicional em unidades de 1-TB. Este armazenamento adicional pode ser adicionado como volume adicional. Também pode ser utilizado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes como originalmente implantados e principalmente documentados pelas tabelas anteriores. Também não é possível alterar os nomes dos volumes ou nomes de montagem. Os volumes de armazenamento anteriormente descritos são anexados às unidades HANA Large Instance como volumes NFS4.

Você pode usar instantâneos de armazenamento para fins de backup e restauro e recuperação de desastres. Para mais informações, consulte [AF HANA (Grandes Instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte [cenários apoiados pelo HLI](hana-supported-scenario.md) para obter detalhes de layout de armazenamento para o seu cenário.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executar várias instâncias SAP HANA em uma unidade HANA Large Instance

É possível alojar mais do que uma instância ativa da SAP HANA nas unidades hana large instance. Para fornecer as capacidades de instantâneos de armazenamento e recuperação de desastres, tal configuração requer um conjunto de volume por exemplo. Atualmente, as unidades HANA Large Instance podem ser subdivididas da seguinte forma:

- **S72, S72m, S96, S144, S192**: Em incrementos de 256 GB, com 256 GB a unidade de partida mais pequena. Diferentes incrementos como 256 GB e 512 GB podem ser combinados até ao máximo da memória da unidade.
- **S144m e S192m**: Em incrementos de 256 GB, com 512 GB a unidade mais pequena. Diferentes incrementos como 512 GB e 768 GB podem ser combinados até ao máximo da memória da unidade.
- **Classe tipo II**: Em incrementos de 512 GB, com a menor unidade de arranque de 2 TB. Diferentes incrementos como 512 GB, 1 TB e 1,5 TB podem ser combinados até ao máximo da memória da unidade.

Poucos exemplos de execução de vários casos sap HANA podem parecer os seguintes.

| SKU | Tamanho da memória | Tamanho do armazenamento | Tamanhos com várias bases de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768-GB HANA<br /> ou 1x512-GB exemplo + 1x256-GB<br /> ou 3x256-GB | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA casos<br />ou 1x512-GB exemplo + 1x1-TB<br />ou 6x256-GB<br />ou 1x1.5-TB | 
| S192m | 4 TB | 16 TB | 8x512-GB<br />ou 4x1-TB casos<br />ou 4x512-GB casos + 2x1-TB<br />ou 4x768-GB casos + 2x512-GB<br />ou 1x4-TB |
| S384xm | 8 TB | 22 TB | 4x2-TB casos<br />ou 2x4-TB casos<br />ou 2x3-TB casos + 1x2-TB<br />ou 2x2,5-TB casos + 1x3-TB<br />ou 1x8-TB |


Há outras variações também. 

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso
O armazenamento utilizado para hana large instance usa encriptação transparente para os dados como está armazenado nos discos desde o final do ano de 2018. Em implementações anteriores, pode optar por encriptar os volumes. Se decidiu contra essa opção, pode solicitar que os volumes fiquem encriptados online. A mudança de volumes não encriptados para volumes encriptados é transparente e não requer tempo de inatividade. 

Com a classe Tipo I de SKUs, o volume em que a lUN de arranque é armazenada, é encriptado. Na Revisão 3 HANA Grandes Instâncias, utilizando a classe Tipo II de SKUs de HANA Large Instance, você precisa encriptar o lUN de arranque com métodos de S. Na Revisão 4 HANA Grandes Instâncias, utilizando unidades do tipo II o volume da bota LUN é armazenado e é encriptado em repouso por padrão também. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Definições necessárias para instâncias HANA maiores em grandes instâncias HANA
O armazenamento utilizado em HANA Grandes Instâncias tem uma limitação de tamanho de ficheiro. A limitação do [tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por ficheiro. Ao contrário das limitações de tamanho de ficheiro nos sistemas de ficheiros EXT3, a HANA não está ciente implicitamente da limitação de armazenamento imposta pelo armazenamento HANA Large Instances. Como resultado, a HANA não criará automaticamente um novo ficheiro de dados quando o limite de tamanho de ficheiro de 16TB for atingido. À medida que a HANA tenta aumentar o ficheiro para além de 16 TB, a HANA reportará erros e o servidor de índice sairá no final.

> [!IMPORTANT]
> Para evitar que a HANA tente cultivar ficheiros de dados para além do limite de tamanho de ficheiro de 16 TB do armazenamento HANA Large Instance, precisa de definir os seguintes parâmetros no ficheiro de configuração global.ini da HANA
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Consulte também a nota SAP [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Esteja atento à nota da SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Passos seguintes?**
- Consulte [cenários apoiados para grandes instâncias HANA](hana-supported-scenario.md)