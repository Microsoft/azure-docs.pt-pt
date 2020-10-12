---
title: Arquitetura de armazenamento da SAP HANA em Azure (Grandes Instâncias) Microsoft Docs
description: Arquitetura de armazenamento de como implantar SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aff1c8f68e3950b49a0a1bd8e99020b77e0f2019
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84677309"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Sap HANA (Grandes Instâncias) arquitetura de armazenamento

O layout de armazenamento para SAP HANA em Azure (Grandes Instâncias) é configurado pela SAP HANA no modelo clássico de implementação de acordo com as diretrizes recomendadas pela SAP. As diretrizes estão documentadas nos [requisitos de armazenamento SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

A HANA Large Instance da classe Tipo I vem com quatro vezes o volume de memória como volume de armazenamento. Para a classe tipo II das unidades HANA Large Instance, o armazenamento não é quatro vezes mais. As unidades vêm com um volume destinado a armazenar cópias de segurança de registo de transações HANA. Para obter mais informações, consulte [instalar e configurar o SAP HANA (Grandes Instâncias) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte a tabela seguinte em termos de atribuição de armazenamento. A tabela lista a capacidade bruta para os diferentes volumes fornecidos com as diferentes unidades HANA Large Instance.

| HANA Grande Instância SKU | hana/dados | hana/log | hana/compartilhado | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3.328 GB | 768 GB |1.280 GB | 768 GB |
| S96 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4.608 GB | 1.024 GB | 1,536 GB | 1.024 GB |
| S192m | 11.520 GB | 1,536 GB | 1.792 GB | 1,536 GB |
| S192xm |  11.520 GB |  1,536 GB |  1.792 GB |  1,536 GB |
| S384 | 11.520 GB | 1,536 GB | 1.792 GB | 1,536 GB |
| S384m | 12.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xm | 16.000 GB | 2.050 GB | 2.050 GB | 2.040 GB |
| S384xxm |  20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576m | 20.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S576xm | 31.744 GB | 4.096 GB | 2.048 GB | 4.096 GB |
| S768m | 28.000 GB | 3.100 GB | 2.050 GB | 3.100 GB |
| S768xm | 40.960 GB | 6,144 GB | 4.096 GB | 6,144 GB |
| S960m | 36.000 GB | 4.100 GB | 2.050 GB | 4.100 GB |
| S896m | 33.792 GB | 512 GB | 1.024 GB | 512 GB |

SkUs mais recentes de HANA Large Instances são entregues com configurações de armazenamento como:

| HANA Grande Instância SKU | hana/dados | hana/log | hana/compartilhado | hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4.224 GB | 512 GB | 1.024 GB | 512 GB |
| S224oo | 6,336 GB | 512 GB | 1.024 GB | 512 GB |
| S224m | 8.448 GB | 512 GB | 1.024 GB | 512 GB |
| S224om | 8.448 GB | 512 GB | 1.024 GB | 512 GB |
| S224ooo | 10.560 GB | 512 GB | 1.024 GB | 512 GB |
| S224oom | 12,672 GB | 512 GB | 1.024 GB | 512 GB |
| S448 | 8.448 GB | 512 GB | 1.024 GB | 512 GB |
| S448oo | 12,672 GB | 512 GB | 1.024 GB | 512 GB |
| S448m | 16.896 GB | 512 GB | 1.024 GB | 512 GB |
| S448om | 16.896 GB | 512 GB | 1.024 GB | 512 GB |
| S448ooo | 21,120 GB | 512 GB | 1.024 GB | 512 GB |
| S448oom | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S672 | 12,672 GB | 512 GB | 1.024 GB | 512 GB |
| S672oo | 19.008 GB | 512 GB | 1.024 GB | 512 GB |
| S672m | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S672om | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S672ooo | 31.680 GB | 512 GB | 1.024 GB | 512 GB |
| S672oom | 38.016 GB | 512 GB | 1.024 GB | 512 GB |
| S896 | 16.896 GB | 512 GB | 1.024 GB | 512 GB |
| S896oo | 25.344 GB | 512 GB | 1.024 GB | 512 GB |
| S896om | 33.792 GB | 512 GB | 1.024 GB | 512 GB |
| S896ooo | 42,240 GB | 512 GB | 1.024 GB | 512 GB |
| S896oom | 50.688 GB | 512 GB | 1.024 GB | 512 GB |


Os volumes implantados reais podem variar em função da implantação e da ferramenta que é usada para mostrar os tamanhos de volume.

Se subdividir um SKU de Grande Instância HANA, alguns exemplos de possíveis peças de divisão podem parecer:

| Partição de memória em GB | hana/dados | hana/log | hana/compartilhado | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1.280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1.792 GB | 640 GB | 1.024 GB | 640 GB |
| 1,536 | 3.328 GB | 768 GB | 1.280 GB | 768 GB |


Estes tamanhos são números de volume bruto que podem variar ligeiramente com base na implementação e nas ferramentas usadas para olhar para os volumes. Há também outros tamanhos de partição, tais como 2,5 TB. Estes tamanhos de armazenamento são calculados com uma fórmula semelhante à utilizada para as divisórias anteriores. O termo "partições" não significa que o sistema operativo, a memória ou os recursos da CPU sejam de alguma forma divididos. Indica divisórias de armazenamento para as diferentes instâncias HANA que você pode querer implementar em uma única unidade HANA Large Instance. 

Pode precisar de mais armazenamento. Pode adicionar armazenamento comprando armazenamento adicional em unidades de 1-TB. Este armazenamento adicional pode ser adicionado como volume adicional. Também pode ser utilizado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes tal como originalmente implantados e maioritariamente documentados pelas tabelas anteriores. Também não é possível alterar os nomes dos volumes ou dos nomes de montagem. Os volumes de armazenamento previamente descritos são anexados às unidades HANA Large Instance como volumes NFS4.

Pode utilizar instantâneos de armazenamento para fins de backup e restauro e recuperação de desastres. Para obter mais informações, consulte [SAP HANA (Grandes Instâncias) alta disponibilidade e recuperação de desastres em Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte [cenários apoiados pelo HLI](hana-supported-scenario.md) para obter detalhes do layout de armazenamento para o seu cenário.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executar vários casos SAP HANA em uma unidade HANA Large Instance

É possível hospedar mais de uma instância sap hana ativa em unidades de HANA Large Instance. Para fornecer as capacidades de armazenamento de instantâneos e recuperação de desastres, tal configuração requer um volume definido por instância. Atualmente, as unidades HANA Large Instance podem ser subdivididas da seguinte forma:

- **S72, S72m, S96, S144, S192**: Em incrementos de 256 GB, com 256 GB a unidade inicial mais pequena. Diferentes incrementos tais como 256 GB e 512 GB podem ser combinados ao máximo da memória da unidade.
- **S144m e S192m**: Em incrementos de 256 GB, com 512 GB a unidade mais pequena. Diferentes incrementos tais como 512 GB e 768 GB podem ser combinados ao máximo da memória da unidade.
- **Classe Tipo II**: Em incrementos de 512 GB, com a unidade inicial mais pequena de 2 TB. Diferentes incrementos tais como 512 GB, 1 TB e 1,5 TB podem ser combinados ao máximo da memória da unidade.

Alguns exemplos de execução de várias instâncias SAP HANA podem parecer os seguintes.

| SKU | Tamanho da memória | Tamanho do armazenamento | Tamanhos com várias bases de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1x768-GB HANA instância<br /> ou instância de 1x512-GB + instância de 1x256-GB<br /> ou 3x256-GB instâncias | 
| S72m | 1,5 TB | 6 TB | 3x512GB de HANA instâncias<br />ou instância 1x512-GB + instância 1x1-TB<br />ou 6x256-GB instâncias<br />ou 1x1.5-TB instância | 
| S192m | 4 TB | 16 TB | 8x512-GB instâncias<br />ou 4x1-TB instâncias<br />ou instâncias 4x512-GB + instâncias 2x1-TB<br />ou instâncias 4x768-GB + instâncias de 2x512-GB<br />ou 1x4-TB instância |
| S384xm | 8 TB | 22 TB | 4x2-TB instâncias<br />ou 2x4-TB instâncias<br />ou 2x3-TB instâncias + 1x2-TB instâncias<br />ou 2x2.5-TB instâncias + 1x3-TB instâncias<br />ou 1x8-TB instância |


Há outras variações também. 

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados em repouso
O armazenamento utilizado para a HANA Large Instance utiliza encriptação transparente para os dados, uma vez que está armazenado nos discos desde finais do ano de 2018. Em implementações anteriores, pode optar por encriptar os volumes. Se decidir contra essa opção, pode solicitar a encriptação dos volumes. A mudança de volumes não encriptados para volumes encriptados é transparente e não requer tempo de inatividade. 

Com a classe tipo I de SKUs, o volume em que a bota LUN está armazenada é encriptado. Na Revisão 3 selos HANA Large Instance, utilizando a classe tipo II de SKUs de HANA Large Instance, é necessário encriptar o botão LUN com métodos DE. Na Revisão 4 selos HANA Large Instance, utilizando unidades do Tipo II o volume da bota LUN é armazenado e é encriptado em repouso por padrão também. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Definições necessárias para casos de HANA maiores em HANA Grandes Instâncias
O armazenamento utilizado em HANA Large Instances tem uma limitação do tamanho do ficheiro. A [limitação do tamanho é de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por ficheiro. Ao contrário das limitações de tamanho de ficheiro nos sistemas de ficheiros EXT3, a HANA não está consciente implicitamente da limitação de armazenamento imposta pelo armazenamento HANA Large Instances. Como resultado, a HANA não criará automaticamente um novo ficheiro de dados quando o limite de tamanho do ficheiro de 16 TB for atingido. À medida que a HANA tenta aumentar o ficheiro para além de 16 TB, a HANA reportará erros e o servidor de índice falhará no final.

> [!IMPORTANT]
> Para evitar que a HANA tente cultivar ficheiros de dados para além do limite de tamanho de ficheiro de 16 TB do armazenamento HANA Large Instance, é necessário definir os seguintes parâmetros no ficheiro de configuração global.ini de HANA
> 
> - datavolume_striping=verdade
> - datavolume_striping_size_gb = 15000
> - Consulte também a nota SAP [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Esteja atento à nota SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Próximos passos**
- Consulte [cenários apoiados para HANA Grandes Instâncias](hana-supported-scenario.md)