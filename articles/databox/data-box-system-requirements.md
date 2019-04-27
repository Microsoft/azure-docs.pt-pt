---
title: Requisitos de sistema do Microsoft Azure Data Box | Documentos da Microsoft
description: Saiba mais sobre o software e os requisitos de rede para o Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/23/2019
ms.author: alkohli
ms.openlocfilehash: 7d52af9e3948f40936795efab5b6671c3f71007a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60746935"
---
# <a name="azure-data-box-system-requirements"></a>Requisitos de sistema do Azure Data Box

Este artigo descreve os requisitos de sistema importante para o Microsoft Azure Data Box e para os clientes ligar para o Data Box. Recomendamos que reveja as informações cuidadosamente antes de implementar o Data Box e, em seguida, regressar mais tarde a ela conforme necessário durante a implantação e operação subseqüente.

Os requisitos de sistema incluem:

* **Requisitos de software para os anfitriões a ligar ao Data Box** -descreve as plataformas suportadas, navegadores da web local da interface do Usuário, os clientes do SMB e quaisquer requisitos adicionais para anfitriões que podem ligar-se para o Data Box.
* **Requisitos de rede para o Data Box** -fornece informações sobre os requisitos de rede para a operação ideal da caixa de dados.


## <a name="software-requirements"></a>Requisitos de software

Os requisitos de software incluem as informações sobre os clientes SMB, sistemas operativos suportados e browsers suportados para a web local da interface do Usuário.

### <a name="supported-operating-systems-for-clients"></a>Sistemas operativos suportados por clientes

Aqui está uma lista de sistemas operativos suportados para a operação de cópia de dados por meio dos clientes ligados ao dispositivo do Data Box.

| **Sistema operativo** | **Versões** | 
| --- | --- | 
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 | 
| Windows |7, 8, 10 | 
|Linux    |         |

### <a name="supported-file-systems-for-linux-clients"></a>Sistemas de ficheiros suportados para clientes do Linux

| **Protocolos** | **Versões** | 
| --- | --- | 
| SMB |2.X e posterior |
| NFS | Todas as versões até e incluindo 4.1|

### <a name="supported-storage-accounts"></a>Contas de armazenamento suportadas

Aqui está uma lista de contas de armazenamento suportadas e os tipos de armazenamento para o dispositivo do Data Box. Para obter uma lista completa de todos os diferentes tipos de contas de armazenamento e as respetivas funcionalidades completas, consulte [tipos de contas de armazenamento](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Conta de armazenamento / suportado tipos de armazenamento** | **Blob de blocos** |**BLOBs de páginas*** |**Ficheiros do Azure** |**Notas**|
| --- | --- | -- | -- | -- |
| Standard clássico | S | S | S |
| Standard para fins gerais v1  | S | S | S | São suportadas frequente e esporádico.|
| Premium para fins gerais v1  |  | S| | |
| Para fins gerais v2 Standard  | S | S | S | São suportadas frequente e esporádico.|
| Premium para fins gerais v2  |  |S | | |
| Armazenamento de BLOBs Standard |S | | |São suportadas frequente e esporádico. |

\* *-Dados carregados para blobs de páginas tem de ser alinhados, como vhds de 512 bytes.*

>[!NOTE]
> Contas de armazenamento Gen 2 do Azure Data Lake não são suportadas.


### <a name="supported-storage-types"></a>Tipos de armazenamento suportadas

Aqui está uma lista dos tipos de armazenamento suportadas para o dispositivo do Data Box.

| **Formato de ficheiro** | **Notas** |
| --- | --- |
| Blob de blocos do Azure | |
| BLOBs de páginas do Azure  | Os dados devem ser alinhadas de 512 bytes.|
| Ficheiros do Azure | |


### <a name="supported-web-browsers"></a>Browsers suportados

Aqui está uma lista dos browsers suportados para a IU da web local.

| **Browser** | **Versões** | **Notas/requisitos adicionais** |
| --- | --- | --- |
| Google Chrome |Versão mais recente |Testado com o Chrome|
| Microsoft Edge |Versão mais recente | |
| FireFox | Versão mais recente | Testado com o FireFox|
| Internet Explorer |Versão mais recente |Se não pode iniciar sessão, verifique se o Javascript e cookies estão ativados. Para ativar o acesso de interface do Usuário, adicione o IP do dispositivo para **ações de privacidade** para que o dispositivo pode aceder a cookies. |


## <a name="networking-requirements"></a>Requisitos de rede

O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, uma ligação de dados de 1 GbE pode ser utilizada para copiar dados, mas a cópia velocidades são afetadas.

## <a name="next-step"></a>Passo seguinte

* [Implementar o Azure Data Box](data-box-deploy-ordered.md)

