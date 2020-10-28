---
title: Opções de transferência de dados Azure para grandes conjuntos de dados com largura de banda baixa ou sem rede Microsoft Docs
description: Saiba como escolher uma solução Azure para transferência de dados quando se limitou a nenhuma largura de banda de rede no seu ambiente e está a planear transferir grandes conjuntos de dados.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8afedec224fed55f617340af512485726ff44326
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792944"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Data transfer for large datasets with low or no network bandwidth (Transferência de dados para conjuntos de dados grandes com pouca ou nenhuma largura de banda de rede)
 
Este artigo fornece uma visão geral das soluções de transferência de dados quando se limitou a nenhuma largura de banda de rede no seu ambiente e está a planear transferir grandes conjuntos de dados. O artigo também descreve as opções recomendadas de transferência de dados e a respetiva matriz de capacidade chave para este cenário.

Para compreender uma visão geral de todas as opções de transferência de dados disponíveis, vá escolher [uma solução de transferência de dados Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Transferência offline ou transferência de rede

Grandes conjuntos de dados implicam que você tem poucas TBs para poucos PBs de dados. Limitou-se a nenhuma largura de banda de rede, a sua rede é lenta, ou não é fiável. Além disso:

- Está limitado pelos custos de transferência de rede dos seus Fornecedores de Serviços de Internet (ISPs).
- As políticas de segurança ou organizacionais não permitem ligações de saída quando se trata de dados sensíveis.

Em todos os casos acima, utilize um dispositivo físico para fazer uma transferência de dados a granel uma única vez. Escolha entre o Disco caixa de dados, a caixa de dados, os dispositivos pesados da caixa de dados fornecidos pela Microsoft, ou importe/exporte utilizando os seus próprios discos.

Para confirmar se um dispositivo físico é a opção certa, utilize a tabela seguinte. Mostra o tempo previsto para a transferência de dados de rede, para várias larguras de banda disponíveis (assumindo uma utilização de 90%). Se se prevê que a transferência de rede seja demasiado lenta, deve utilizar um dispositivo físico.  

![Transferência de rede ou transferência offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Opções recomendadas

As opções disponíveis neste cenário são dispositivos para transferência offline Azure Data Box ou Azure Import/Export.

- **Família Azure Data Box para transferências offline** – Utilize dispositivos de dispositivos Data Box fornecidos pela Microsoft para mover grandes quantidades de dados para o Azure quando estiver limitado pelo tempo, disponibilidade da rede ou custos. Copie os dados no local utilizando ferramentas como robocopia. Dependendo do tamanho dos dados destinados à transferência, pode escolher entre o Disco caixa de dados, a caixa de dados ou a caixa de dados pesada.
- **Azure Import/Export** – Use o serviço Azure Import/Export enviando as suas próprias unidades de disco para importar de forma segura grandes quantidades de dados para o armazenamento e arquivos Azure Blob. Este serviço também pode ser usado para transferir dados do armazenamento Azure Blob para unidades de discos e enviar para os seus sites no local.

## <a name="comparison-of-key-capabilities"></a>Comparação das capacidades-chave

A tabela seguinte resume as diferenças nas capacidades-chave.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Importação/Exportação                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    **Tamanho dos dados**                    |    Até 35 TBs                 |    Até 80 TBs por dispositivo                       |    Até 800 TB por dispositivo               |    Variável                            |
|    **Tipo de dados**                    |    Blobs do Azure                  |    Blobs do Azure<br>Ficheiros do Azure                    |    Blobs do Azure<br>Ficheiros do Azure            |    Blobs do Azure<br>Ficheiros do Azure          |
|    **Fator de forma**                  |    5 SSDs por encomenda             |    1 X 50-lbs. dispositivo do tamanho de desktop por ordem    |    1 X ~500-lbs. grande dispositivo por ordem    |    Até 10 HDDs/SSDs por encomenda        |
|    **Tempo inicial de configuração**           |    Baixo <br>(15 minutos)            |    Baixo a moderado <br> (<30 minutos)               |    Moderado<br>(1-2 horas)               |    Moderado a difícil<br>(variável) |
|    **Enviar dados para a Azure**           |    Sim                          |    Sim                                           |    Sim                                   |    Sim                                 |
|    **Exportar dados do Azure**       |    Não                           |    Não                                            |    Não                                    |    Sim                                 |
|    **Encriptação**                   |    AES 128-bit                  |    AES 256-bit                                   |    AES 256-bit                           |    AES 128-bit                         |
|    **Hardware**                     |     Microsoft fornecida          |    Microsoft fornecida                            |    Microsoft fornecida                    |    Cliente fornecido                   |
|    **Interface de rede**            |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    **Integração de parceiros**          |    Algum                         |    [Alto](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Alto](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Algum                                |
|    **Envio**                     |    Microsoft gerido            |    Microsoft gerido                             |    Microsoft gerido                     |    Gerido pelo cliente                    |
| **Use quando os dados se movem**     |Dentro de um limite de comércio|Dentro de um limite de comércio|Dentro de um limite de comércio|Além das fronteiras geográficas, por exemplo, eua à UE|
|    **Preços**                      |    [Preços](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Preços](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Preços](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Preços](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Passos seguintes

- Compreender como

    - [Transferir dados com o disco da caixa de dados.](../../databox/data-box-disk-quickstart-portal.md)
    - [Transferir dados com a Caixa de Dados.](../../databox/data-box-quickstart-portal.md)
    - [Dados de transferência com Importação/Exportação.](./storage-import-export-data-to-blobs.md)