---
title: Como utilizar o armazenamento do Azure para cópia de segurança e restaurar | Documentos da Microsoft
description: Saiba como criar cópias de segurança do SQL Server para o armazenamento do Azure. Explica as vantagens de cópia de segurança de bases de dados SQL ao armazenamento do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 1b6660a1565b3c119cc1dec0823870c7dd5bd24f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477149"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilizar o armazenamento do Azure para o restauro e cópia de segurança do SQL Server
## <a name="overview"></a>Descrição geral
A partir do SQL Server 2012 SP1 CU2, agora pode escrever cópias de segurança do SQL Server diretamente para o serviço de armazenamento de Blobs do Azure. Pode utilizar esta funcionalidade para voltar ao e restaurar a partir do serviço de Blobs do Azure com uma base de dados do SQL Server no local ou uma base de dados do SQL Server numa máquina virtual do Azure. Cópia de segurança para a cloud oferece benefícios de disponibilidade, ilimitado armazenamento no local georreplicado e facilidade de migração de dados para e partir da cloud. Pode emitir declarações de cópia de segurança ou RESTAURO com o Transact-SQL ou o SMO.

SQL Server 2016 apresenta novas capacidades; Pode usar [cópia de segurança de instantâneos de ficheiros](https://msdn.microsoft.com/library/mt169363.aspx) para efetuar cópias de segurança instantâneas e restauros incrivelmente rápidos.

Este tópico explica por que pode optar por utilizar o armazenamento do Azure para cópias de segurança do SQL e, em seguida, descreve os componentes envolvidos. Pode utilizar os recursos fornecidos no final do artigo para aceder às orientações passo a passo e informações adicionais para começar a utilizar este serviço com as cópias de segurança do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Benefícios da utilização do serviço de Blobs do Azure para cópias de segurança do SQL Server
Existem vários desafios que enfrentam ao fazer backup do SQL Server. Estes desafios incluem gerenciamento de armazenamento, o risco de falha de armazenamento, acesso ao armazenamento no local e a configuração de hardware. Muitos desses desafios são resolvidos ao utilizar o serviço de armazenamento de Blobs do Azure para cópias de segurança do SQL Server. Considere as seguintes vantagens:

* **Facilidade de uso**: Armazenar as cópias de segurança em blobs do Azure pode ser um conveniente, flexível e fácil de aceder a opção fora das instalações. A criação de armazenamento no local para as cópias de segurança do SQL Server podem ser tão fácil como modificar os scripts/trabalhos existentes para utilizar o **cópia de segurança para URL** sintaxe. Armazenamento no local, normalmente, deve ser suficientemente a partir da localização de base de dados de produção para impedir um desastre único que pode afetar a ambas as localizações de base de dados fora do local e de produção. Ao optar por [georreplicação de blobs do Azure](../../../storage/common/storage-redundancy.md), tem uma camada extra de proteção em caso de desastre que pode afetar a região inteira.
* **Arquivo de cópia de segurança**: O serviço de armazenamento de Blobs do Azure oferece uma melhor alternativa à opção da banda, muitas vezes, utilizado para arquivar as cópias de segurança. Armazenamento em banda pode necessitar de transportes físico para uma instalação fora das instalações e medidas para proteger o suporte de dados. Armazenar as cópias de segurança no armazenamento de Blobs do Azure fornece um instante, elevada disponibilidade, e o arquivamento de um durable opção.
* **Gerido hardware**: Não haverá sobrecarga de gerenciamento do hardware com serviços do Azure. Serviços do Azure gere o hardware e fornecem a georreplicação para redundância e a proteção contra falhas de hardware.
* **Armazenamento ilimitado**: Ao ativar uma cópia de segurança direta para blobs do Azure, terá acesso ao armazenamento virtualmente ilimitados. Em alternativa, o backup em que um disco de máquina virtual do Azure tem limites com base no tamanho da máquina. Existe um limite para o número de discos que pode anexar a uma máquina virtual do Azure para cópias de segurança. Este limite é de 16 discos de uma instância extra grande e menos para as instâncias de menores.
* **Disponibilidade de cópia de segurança**: Cópias de segurança armazenadas em blobs do Azure estão disponíveis a partir de qualquer lugar e em qualquer altura e podem ser acessadas facilmente para restauros para um servidor de SQL no local ou outro SQL Server em execução numa máquina Virtual do Azure, sem a necessidade de anexar/desanexar base de dados ou baixar o e anexar o VHD.
* **Custo**: Pague apenas o serviço que é utilizado. Pode ser económico como uma opção de arquivo fora do local de cópia de segurança. Consulte a [Calculadora de preços do Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de preços")e o [artigo de preços do Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "preços artigo") para obter mais informações.
* **Instantâneos de armazenamento**: Quando os ficheiros de base de dados são armazenados num blob do Azure e estiver a utilizar o SQL Server 2016, pode utilizar [cópia de segurança de instantâneos de ficheiros](https://msdn.microsoft.com/library/mt169363.aspx) para efetuar cópias de segurança instantâneas e restauros incrivelmente rápidos.

Para obter mais detalhes, consulte [cópia de segurança do SQL Server e restauro com o serviço de armazenamento de Blobs do Azure](https://go.microsoft.com/fwlink/?LinkId=271617).

As duas secções seguintes apresentam o serviço de armazenamento de Blobs do Azure, incluindo os componentes necessários do SQL Server. É importante compreender os componentes e suas interações para utilizar a cópia de segurança e restaurar a partir do serviço de armazenamento de Blobs do Azure com êxito.

## <a name="azure-blob-storage-service-components"></a>Componentes do serviço de armazenamento de Blobs do Azure
Os seguintes componentes do Azure são utilizados quando a cópia de segurança para o serviço de armazenamento de Blobs do Azure.

| Componente | Descrição |
| --- | --- |
| **Storage Account** |A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para aceder a um serviço de armazenamento de Blobs do Azure, crie primeiro uma conta de armazenamento do Azure. Para obter mais informações sobre o serviço de armazenamento de Blobs do Azure, consulte [como utilizar o serviço de armazenamento de Blobs do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contentor** |Um contentor fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de Blobs. Para escrever um SQL Server cópia de segurança a um serviço de Blobs do Azure, tem de ter, pelo menos, o contentor de raiz que criou. |
| **Blob** |Um ficheiro de qualquer tipo e tamanho. Os BLOBs são endereçáveis através do formato de URL seguinte: **https://[storage account].blob.core.windows.net/[container]/[blob]** . Para obter mais informações sobre Blobs de páginas, consulte [compreensão blocos e Blobs de páginas](https://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do SQL Server
Os seguintes componentes do SQL Server são utilizados quando a cópia de segurança para o serviço de armazenamento de Blobs do Azure.

| Componente | Descrição |
| --- | --- |
| **URL** |Um URL especifica um identificador URI (Uniform Resource) para um ficheiro de cópia de segurança exclusivo. O URL é utilizado para fornecer a localização e o nome do ficheiro de cópia de segurança do SQL Server. O URL tem de apontar para um blob real, não apenas um contêiner. Se o blob não existir, será criada. Se um blob existente for especificado, cópia de segurança falha, a menos que o > a opção WITH FORMAT está especificada. Segue-se um exemplo do URL tem de especificar no comando de cópia de segurança: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]** . HTTPS é recomendável, mas não obrigatório. |
| **Credencial** |As informações necessárias para ligar e autenticar-se ao serviço de armazenamento de Blobs do Azure são armazenadas como uma credencial.  Por ordem para o SQL Server escrever cópias de segurança para um Blob do Azure ou o restauro a partir do mesmo, tem de ser criada uma credencial do SQL Server. Para obter mais informações, consulte [credenciais do SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [!NOTE]
> SQL Server 2016 foi atualizada para suportar blobs de blocos. Consulte [Tutorial: Utilizar o serviço de armazenamento de Blobs do Microsoft Azure com bases de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx) para obter mais detalhes.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
1. Se ainda não tiver uma, crie uma conta do Azure. Se estiver a avaliar o Azure, considere a [avaliação gratuita](https://azure.microsoft.com/free/).
2. Em seguida, vá através de um dos seguintes tutoriais que orientam-na criação de uma conta de armazenamento e executar uma restauração.
   
   * **SQL Server 2014**: [Tutorial: Serviço de armazenamento de Blob de cópia de segurança do SQL Server 2014 e o restauro para o Microsoft Azure](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
   * **SQL Server 2016**: [Tutorial: Utilizar o serviço de armazenamento de Blobs do Microsoft Azure com bases de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)
3. Reveja a documentação adicional a partir [cópia de segurança do SQL Server e o restauro com o serviço de armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Se tiver quaisquer problemas, consulte o tópico [cópia de segurança do SQL Server para o URL de melhores práticas e resolução de problemas](https://msdn.microsoft.com/library/jj919149.aspx).

Para outro SQL Server de cópia de segurança e opções de restauro, veja [cópia de segurança e restaurar para o SQL Server em máquinas de virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

