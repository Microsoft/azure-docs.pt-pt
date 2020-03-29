---
title: Opções de transferência de dados azure para grandes conjuntos de dados com largura de banda baixa ou sem rede Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando se limitou a não ter largura de banda de rede no seu ambiente e planeia transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60730695"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Data transfer for large datasets with low or no network bandwidth (Transferência de dados para conjuntos de dados grandes com pouca ou nenhuma largura de banda de rede)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando se limita a nenhuma largura de banda da rede no seu ambiente e está a planear transferir grandes conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher uma solução de transferência de [dados Azure.](storage-choose-data-transfer-solution.md)

## <a name="offline-transfer-or-network-transfer"></a>Transferência offline ou transferência de rede

Grandes conjuntos de dados implicam que você tem poucos TBs a poucos PBs de dados. Limitou-se a não ter largura de banda de rede, a sua rede é lenta, ou não é fiável. Além disso:

- Está limitado pelos custos de transferência de rede dos seus Fornecedores de Serviços de Internet (ISPs).
- As políticas de segurança ou organizacionais não permitem ligações de saída quando se trata de dados sensíveis.

Em todos os casos acima referidos, utilize um dispositivo físico para fazer uma transferência única de dados em massa. Escolha entre Data Box Disk, Data Box, Data Box Dispositivos pesados fornecidos pela Microsoft, ou Import/Export usando os seus próprios discos.

Para confirmar se um dispositivo físico é a opção certa, utilize a tabela seguinte. Mostra o tempo previsto para a transferência de dados de rede, para várias larguras de banda disponíveis (assumindo uma utilização de 90%). Se a transferência de rede for projetada como demasiado lenta, deverá utilizar um dispositivo físico.  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Opções recomendadas

As opções disponíveis neste cenário são dispositivos para transferência offline da Caixa de Dados Azure ou Importação/Exportação Azure.

- **Família Azure Data Box para transferências offline** – Utilize dispositivos de dispositivos Data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando estiver limitado pelo tempo, disponibilidade da rede ou custos. Copie os dados no local utilizando ferramentas como a Robocopy. Dependendo do tamanho dos dados destinados à transferência, pode escolher entre Data Box Disk, Data Box ou Data Box Heavy.
- **Azure Import/Export** – Use o serviço de importação/exportação Azure enviando as suas próprias unidades de disco para importar com segurança grandes quantidades de dados para o armazenamento da Blob Azure e ficheiros Azure. Este serviço também pode ser usado para transferir dados do armazenamento azure Blob para unidades de disco e enviar para os seus locais no local.

## <a name="comparison-of-key-capabilities"></a>Comparação de capacidades-chave

A tabela que se segue resume as diferenças nas capacidades-chave.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Tamanho dos dados                        |    Até 35 TBs                 |    Até 80 TBs por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    Tipo de dados                        |    Blobs do Azure                  |    Blobs do Azure<br>Ficheiros do Azure                    |    Blobs do Azure<br>Ficheiros do Azure            |    Blobs do Azure<br>Ficheiros do Azure          |
|    Fator de forma                      |    5 SSDs por encomenda             |    1 X 50 libras. dispositivo do tamanho de um ambiente de trabalho por encomenda    |    1 X ~500 lbs. grande dispositivo por ordem    |    Até 10 HDDs/SSDs por encomenda        |
|    Tempo inicial de configuração               |    Baixa <br>(15 minutos)            |    Baixo a moderado <br> (<30 minutos)               |    Moderado<br>(1-2 horas)               |    Moderado a difícil<br>(variável) |
|    Enviar dados para O Azure               |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    Exportar dados do Azure           |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    Encriptação                       |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    Hardware                         |     Microsoft fornecido          |    Microsoft fornecido                            |    Microsoft fornecido                    |    Cliente fornecido                   |
|    Interface de rede                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integração de parceiros              |    Alguns                         |    [Alto](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Alto](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Alguns                                |
|    Shipping                         |    A Microsoft geriu            |    A Microsoft geriu                             |    A Microsoft geriu                     |    Cliente gerido                    |
| Utilizar quando os dados se movem         |Dentro de um limite de comércio|Dentro de um limite de comércio|Dentro de um limite de comércio|Além das fronteiras geográficas, por exemplo, EUA para a UE|
|    Preços                          |    [Preços](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Passos seguintes

- Entender como

    - [Transferir dados com disco](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal)de caixa de dados .
    - [Transferir dados com Caixa](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal)de Dados.
    - [Transferir dados com importação/exportação.](/azure/storage/common/storage-import-export-data-to-blobs)
