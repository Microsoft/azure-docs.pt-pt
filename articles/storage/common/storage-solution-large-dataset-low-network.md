---
title: Opções para grandes conjuntos de dados com baixa ou largura de banda de transferência de dados do Azure | Documentos da Microsoft
description: Saiba como escolher uma solução do Azure para a transferência de dados quando de limitaram a largura de banda no seu ambiente e estiver a planear transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/10/2018
ms.author: alkohli
ms.openlocfilehash: a6505232066d7a2e63c3e9508cb16cb9a31f8e35
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776670"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Transferência de dados para grandes conjuntos de dados com baixa ou não largura de banda de rede
 
Este artigo fornece uma descrição geral da transferência de dados soluções quando de limitaram a largura de banda no seu ambiente e estiver a planear transferir grandes conjuntos de dados. O artigo também descreve as opções de transferência de dados recomendada e a matriz de respetivas capacidades-chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, aceda a [escolher uma solução de transferência de dados do Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Transferência de rede ou de transferência offline

Grandes conjuntos de dados implicam que tenha alguns TB para PBs alguns dos dados. Limitado a nenhum largura de banda de rede, sua rede é lenta ou é pouco fiável. Além disso:

- Estão limitadas por custos de transferência de rede a partir de seus fornecedores de serviços de Internet (ISPs).
- Segurança ou políticas organizacionais não permitem ligações de saída ao lidar com dados confidenciais.

Todas as instâncias acima, utilize um dispositivo físico para fazer uma transferência de dados em massa única. Escolha de disco do Data Box, Data Box, em dispositivos pesadas de caixa de dados que são fornecidos pela Microsoft ou com seus próprios discos de importação/exportação.

Para confirmar se um dispositivo físico é a opção correta, utilize a tabela seguinte. Mostra a hora prevista para transferência de dados de rede, para várias larguras de banda disponíveis (partindo do princípio de 90% da utilização). Se a transferência de rede está projetada para ser muito lento, deve usar um dispositivo físico.  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Opções recomendadas

As opções disponíveis neste cenário são dispositivos para transferência offline do Azure Data Box ou importar/exportar do Azure.

- **O Azure Data Box família para transferências offline** – utilizar dispositivos a partir de dispositivos de fornecido pelo Microsoft Data Box para mover grandes quantidades de dados para o Azure quando está limitado pelo tempo, disponibilidade de rede ou custos. Copie dados no local através de ferramentas, tal como Robocopy. Dependendo do tamanho de dados que se destina a transferência, pode escolher entre o disco Data Box, caixa de dados ou dados caixa pesada.
- **Importar/exportar do Azure** – serviço de importação/exportação de utilização do Azure envie as suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o armazenamento de Blobs do Azure e ficheiros do Azure. Este serviço também pode ser utilizado para transferir dados do armazenamento de Blobs do Azure para unidades de disco e disponibilize aos seus sites no local.

## <a name="comparison-of-key-capabilities"></a>Comparação dos principais recursos

A tabela seguinte resume as diferenças nas capacidades principais.

|                                     |    Disco do Data Box (pré-visualização)    |    Data Box                                      |    Dados caixa pesadas (pré-visualização)              |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Tamanho dos dados                        |    Até 35 TB                 |    Até 80 TB por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    Tipo de dados                        |    Blobs do Azure                  |    Blobs do Azure<br>Ficheiros do Azure                    |    Blobs do Azure<br>Ficheiros do Azure            |    Blobs do Azure<br>Ficheiros do Azure          |
|    Fator de formulário                      |    5 SSDs por ordem             |    1 x 50-lbs. dispositivo de porte no ambiente de trabalho de mensagens em fila por ordem    |    1 X ~ 500-lbs. dispositivo grandes por ordem    |    Até 10 HDDs/SSDs por ordem        |
|    Tempo de configuração inicial               |    Baixo <br>(15 min)            |    Baixo a moderado <br> (< 30 minutos)               |    Moderado<br>(1 a 2 horas)               |    Moderar a difícil<br>(variável) |
|    Enviar dados para o Azure               |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    Exportar dados do Azure           |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    Encriptação                       |    AES 128 bits                  |    AES de 256 bits                                   |    AES de 256 bits                           |    AES 128 bits                         |
|    Hardware                         |     Microsoft fornecido          |    Microsoft fornecido                            |    Microsoft fornecido                    |    Cliente fornecido                   |
|    Interface de rede                |    3.1/SATA DE USB                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integração de parceiros              |    Alguns                         |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alta](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alguns                                |
|    Envio                         |    Gerida pela Microsoft            |    Gerida pela Microsoft                             |    Gerida pela Microsoft                     |    Gerida pelo cliente                    |
| Quando move os dados de utilização         |Num limite de comércio|Num limite de comércio|Num limite de comércio|Através das fronteiras geográficas, por exemplo, da UE|
|    Preços                          |    [Preços](https://azure.microsoft.com/pricing/details/storage/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Passos Seguintes

- Compreender como

    - [Transferir dados com dados de caixa de disco](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferir dados com o Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Transferir dados com a importação/exportação](/azure/storage/common/storage-import-export-data-to-blobs).