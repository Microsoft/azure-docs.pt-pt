---
title: Decidindo quando usar os Blobs do Azure, ficheiros do Azure ou discos do Azure
description: Saiba mais sobre as diferentes formas de armazenar e aceder a dados no Azure para o ajudar a que decidir a tecnologia desejada.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 869d2105ccf635a46a21e9b7f382ddbef713d68b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483422"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidindo quando usar os Blobs do Azure, ficheiros do Azure ou discos do Azure
Microsoft Azure fornece vários recursos no armazenamento do Azure para armazenar e aceder aos seus dados na cloud. Este artigo aborda os ficheiros do Azure, Blobs e discos e foi concebido para ajudar a escolher entre esses recursos.

## <a name="scenarios"></a>Cenários
A tabela seguinte compara os ficheiros, Blobs e discos e mostra os cenários de exemplo apropriado para cada um.

| Funcionalidade | Descrição | Quando utilizar |
|--------------|-------------|-------------|
| **Ficheiros do Azure** | Fornece uma interface SMB, bibliotecas de cliente e um [REST interface](/rest/api/storageservices/file-service-rest-api) que permita o acesso em qualquer lugar para ficheiros armazenados. | Pretende "lift- and -shift" uma aplicação para a nuvem que já utiliza as APIs do sistema de arquivo nativo para partilhar dados entre os mesmos e outras aplicações em execução no Azure.<br/><br/>Pretende armazenar o desenvolvimento e ferramentas que precisam de ser acedidos a partir de várias máquinas virtuais de depuração. |
| **Blobs do Azure** | Dispõe de bibliotecas de cliente e um [REST interface](/rest/api/storageservices/blob-service-rest-api) que permite que os dados não estruturados ser armazenados e acedidos em grande escala nos blobs de blocos.<br/><br/>Também suporta [geração 2 de armazenamento do Azure Data Lake](../blobs/data-lake-storage-introduction.md) para soluções de análise de grandes volumes de dados empresariais. | Pretende que a aplicação para suportar a transmissão em fluxo e os cenários de acesso aleatório.<br/><br/>Quer ser capaz de acessar dados da aplicação de qualquer lugar.<br/><br/>Pretende compilar um lake de dados empresariais no Azure e realizar análises de macrodados. |
| **Discos do Azure** | Dispõe de bibliotecas de cliente e um [REST interface](/rest/api/compute/manageddisks/disks/disks-rest-api) que permite que os dados de forma permanente ser armazenados e acedidos a partir de um disco rígido virtual anexado. | Pretende lift- and -shift de aplicações que utilizam APIs de sistema de ficheiros de nativo para ler e gravar dados em discos persistentes.<br/><br/>Deseja armazenar dados que não é necessários seja acessado de fora da máquina virtual à qual o disco está ligado. |

## <a name="comparison-files-and-blobs"></a>Comparação: Ficheiros e Blobs
A tabela seguinte compara os ficheiros do Azure com Blobs do Azure.  
  
||||  
|-|-|-|  
|**Atributo**|**Blobs do Azure**|**Ficheiros do Azure**|  
|Opções de durabilidade|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Acessibilidade|APIs REST|APIs REST<br /><br /> SMB 2.1 e o SMB 3.0 (sistema de ficheiros padrão APIs)|  
|Conectividade|APIs REST, como, em todo o mundo|APIs REST - em todo o mundo<br /><br /> O SMB 2.1 – numa região<br /><br /> O SMB 3.0 – em todo o mundo|  
|Pontos Finais|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Diretórios|Espaço de nomes simples|Objetos de diretório verdadeiro|  
|Maiúsculas e minúsculas do nomes|Sensível a maiúsculas e minúsculas|Maiúsculas e minúsculas, mas a preservação de caso|  
|Capacidade|Até 2 de conta do PiB de limite |5 TiB as partilhas de ficheiros|  
|Débito|Até 60 MiB/s por blob de blocos|Até 60 MiB/s por partilha|  
|Tamanho do objeto|Até cerca de 4.75 TiB por blob de blocos|Até 1 TiB por ficheiro|  
|Capacidade de faturação|Com base nos bytes escritos|Com base no tamanho de ficheiro|  
|Bibliotecas de cliente|Vários idiomas|Vários idiomas|  
  
## <a name="comparison-files-and-disks"></a>Comparação: Ficheiros e discos
Os ficheiros do Azure complementam os discos do Azure. Um disco só pode ser anexado a uma Máquina Virtual do Azure ao mesmo tempo. Os discos são armazenadas como blobs de páginas no armazenamento do Azure de VHDs de formato fixo e são utilizados pela máquina virtual para armazenar dados durável. Partilhas de ficheiros nos ficheiros do Azure podem ser acedidas da mesma forma como o disco local é acessado (ao utilizar o sistema de arquivos nativos APIs) e podem ser partilhadas entre várias máquinas virtuais.  
 
A tabela seguinte compara os ficheiros do Azure com discos do Azure.  
 
||||  
|-|-|-|  
|**Atributo**|**Discos do Azure**|**Ficheiros do Azure**|  
|Âmbito|Exclusivo para uma única máquina virtual|Acesso partilhado em várias máquinas virtuais|  
|Os instantâneos e copiar|Sim|Sim|  
|Configuração|Conectada durante a inicialização da máquina virtual|Ligado depois da máquina virtual foi iniciada|  
|Autenticação|Incorporado|Configurar com utilização net|  
|Acesso com REST|Não não possível aceder a ficheiros dentro do VHD|Armazenados numa partilha de ficheiros podem ser acedidos|  
|Tamanho Máx|4 de TiB de disco|Partilha de ficheiros do 5 TiB e 1 TiB ficheiro na partilha|  
|máx IOps|500 IOps|1000 IOps|  
|Débito|Até 60 MiB/s por disco|Destino é 60 MiB/s por partilha de ficheiros (pode obter superior para tamanhos de e/s superior)|  

## <a name="next-steps"></a>Passos Seguintes
Quando tomar decisões sobre a forma como os seus dados são armazenados e acedidos, também deve considerar os custos envolvidos. Para obter mais informações, consulte [preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Algumas funcionalidades do SMB não são aplicáveis para a cloud. Para obter mais informações, consulte [funcionalidades não suportadas pelo serviço de ficheiros do Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Para obter mais informações acerca dos discos, consulte nosso [introdução aos discos geridos](../../virtual-machines/windows/managed-disks-overview.md) e [como anexar um disco de dados a uma máquina de Virtual do Windows](../../virtual-machines/windows/attach-managed-disk-portal.md).
