---
title: Arquitetura de armazenamento do SAP HANA no Azure (instâncias grandes) | Documentos da Microsoft
description: Arquitetura de armazenamento de como implementar o SAP HANA no Azure (instâncias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5c5f1d8d7a9c84d807db53933f0cbb176f9fb7f2
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551973"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Arquitetura de armazenamento do SAP HANA (instâncias grandes)

O esquema de armazenamento para o SAP HANA no Azure (instâncias grandes) é configurado pelo SAP HANA no modelo de implementação clássica por SAP diretrizes recomendada. As diretrizes estão documentadas no [requisitos de armazenamento do SAP HANA](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) white paper.

Instância grande do HANA, do tipo de classe que é fornecido com quatro vezes o volume de memória como volume de armazenamento. Para a classe de tipo II de unidades de instância grande do HANA, o armazenamento não mais de quatro vezes. As unidades são fornecidos com um volume que pretende armazenar os backups de log de transação do HANA. Para obter mais informações, consulte [instalar e configurar o SAP HANA (instâncias grandes) no Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte a tabela seguinte em termos de alocação de armazenamento. A tabela lista a capacidade estimada para os volumes diferentes fornecido com as diferentes unidades de instância grande do HANA.

| Instância grande do HANA SKU | dados do Hana | Hana/do registo | Hana/partilhado | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608 GB | 1024 GB | 1,536 GB | 1024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12 000 GB | 2,050 GB | 2,050 GB | 2040 GB |
| S384xm | GB DE 16.000 | 2,050 GB | 2,050 GB | 2040 GB |
| S384xxm |  20.000 GB | 3100 GB | 2,050 GB | 3100 GB |
| S576m | 20.000 GB | 3100 GB | 2,050 GB | 3100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2.048 GB | 4,096 GB |
| S768m | 28,000 GB | 3100 GB | 2,050 GB | 3100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


Volumes implementados reais podem variar com base na implementação e a ferramenta que é utilizada para mostrar os tamanhos de volume.

Se subdividir um SKU de instância grande do HANA, alguns exemplos das partes de divisão possível podem ter o seguinte aspeto:

| Partição de memória em GB | dados do Hana | Hana/do registo | Hana/partilhado | Hana/registo/cópia de segurança |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


Estes tamanhos são números de volume aproximado que podem variar ligeiramente com base na implementação e as ferramentas usadas para examinar os volumes. Também há outros tamanhos de partição, como 2,5 TB. Estes tamanhos de armazenamento são calculados com uma fórmula semelhante ao utilizado para as partições anteriores. O termo "partições" não significa que o sistema operativo, memória ou recursos da CPU são de qualquer forma particionados. Ele indica as partições de armazenamento para as diferentes instâncias HANA, que pode querer implementar numa única unidade de instância grande do HANA. 

Poderá precisar de mais armazenamento. Pode adicionar armazenamento ao comprar armazenamento adicional em unidades de 1 TB. Este armazenamento adicional pode ser adicionado como volume adicional. Ele também pode ser usado para estender um ou mais dos volumes existentes. Não é possível diminuir os tamanhos dos volumes como originalmente implementado e quase totalmente documentados por tabelas anteriores. Também não é possível alterar os nomes dos volumes ou nomes de montagem. Os volumes de armazenamento descritos anteriormente, são anexados às unidades instância grande do HANA como NFS4 volumes.

Pode utilizar instantâneos de armazenamento para fins de recuperação e cópia de segurança e restauro. Para obter mais informações, consulte [SAP HANA (instâncias grandes) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consultar [cenários suportados de HLI](hana-supported-scenario.md) para obter detalhes de esquema de armazenamento para o seu cenário.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Executar várias instâncias do SAP HANA numa unidade de instância grande do HANA

É possível hospedar mais de uma instância ativa do SAP HANA nas unidades de instância grande do HANA. Para fornecer os recursos de instantâneos de armazenamento e recuperação após desastre, esta configuração requer que um volume definido por instância. Atualmente, as unidades de instância grande do HANA puderem ser subdivididas da seguinte forma:

- **S72, S72m, S96, S144, S192**: Em incrementos de 256 GB, com 256 GB a unidade de partida mais pequeno. Incrementos diferentes, como 256 GB e 512 GB podem ser combinados para o máximo da memória da unidade.
- **S144m e S192m**: Em incrementos de 256 GB, com a menor unidade de 512 GB. Incrementos diferentes, como 512 GB e 768 GB podem ser combinados para o máximo da memória da unidade.
- **Tipo de classe II**: Em incrementos de 512 GB, com a unidade de partida mais pequeno de 2 TB. Incrementos diferentes como 512 GB, 1 TB e 1,5 TB podem ser combinados para o máximo da memória da unidade.

Alguns exemplos de várias instâncias do SAP HANA em execução podem ser semelhante ao seguinte:

| SKU | Tamanho da memória | Tamanho de armazenamento | Tamanhos com várias bases de dados |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | instância HANA 1 x 768 GB<br /> ou instância de 1 x 512 GB + 1 x 256 GB de instância<br /> ou instâncias de 3 x 256 GB | 
| S72m | 1,5 TB | 6 TB | 3x512GB HANA nas instâncias<br />ou a instância de 1 x 512 GB + a instância de 1 x 1 TB<br />ou instâncias de 6 x 256 GB<br />ou instância de 1x1.5 TB | 
| S192m | 4 TB | 16 TB | instâncias de 8 x 512 GB<br />ou instâncias de 4 x 1 TB<br />ou instâncias de 4 x 512 GB + instâncias de 2 x 1 TB<br />ou instâncias de 4 x 768 GB + 2 x 512 GB instâncias<br />ou instância de 1 x 4 TB |
| S384xm | 8 TB | 22 TB | instâncias de 4 x 2 TB<br />ou instâncias de 2 x 4 TB<br />ou instâncias de 2 x 3 TB + instâncias de 1 x 2 TB<br />ou instâncias de 2x2.5 TB + instâncias de 1 x 3 TB<br />ou instância de 1 x 8 TB |


Existem também outras variações. 

## <a name="encryption-of-data-at-rest"></a>Encriptação de dados inativos
O armazenamento utilizado para a instância grande do HANA permite a criptografia transparente de dados quando ele é armazenado nos discos. Quando uma unidade de instância grande do HANA é implementada, pode ativar este tipo de criptografia. Também pode alterar para volumes criptografados após a implantação se efetivar. A mudança de não encriptadas para volumes criptografados é transparente e não exige período de indisponibilidade. 

Com o tipo de a classe de SKUs, o volume de arranque LUN é armazenado, são encriptados. Para a classe de tipo II de SKUs de instância grande do HANA, tem de encriptar o arranque LUN com métodos de SO. Para obter mais informações, contacte a equipa de gestão de serviço da Microsoft.

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Definições necessárias para as instâncias grandes do HANA nas instâncias grandes do HANA
O armazenamento utilizado nas instâncias grandes do HANA tem uma limitação de tamanho de ficheiro. O [limitação de tamanho é 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por ficheiro. Ao contrário das limitações de tamanho de ficheiro em sistemas de ficheiros EXT3, HANA není implicitamente atento a limitação de armazenamento imposta pelo armazenamento de instâncias grandes do HANA. como resultado HANA não criará automaticamente um novo ficheiro de dados quando é atingido o limite de tamanho de ficheiro de 16TB. Como HANA tenta aumentar o arquivo para além dos 16 TB, o HANA relatório de erros e o servidor de índices falhará no final.

> [!IMPORTANT]
> Para impedir que o HANA tentando aumentar os arquivos de dados além do limite de tamanho de ficheiro de 16 TB de armazenamento de instâncias grandes do HANA, tem de definir os seguintes parâmetros no ficheiro de configuração global.ini do HANA
> 
- datavolume_striping=true
- datavolume_striping_size_gb = 15000
- Consulte também a SAP note [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
- Tenha em atenção a nota SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Passos seguintes?**
- Consulte [cenários suportados para instâncias grandes do HANA](hana-supported-scenario.md)