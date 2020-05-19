---
title: Utilizando pacotes DAC de Base de Dados SQL - Borda SQL Azure (pré-visualização)
description: Saiba mais sobre o uso de dacpacs em Azure SQL Edge (Pré-visualização)
keywords: SQL Edge, sqlpackage
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 17d0ba4d7298e60255477c4801e0bcb9b9a4ecac
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598270"
---
# <a name="sql-database-dac-packages-in-sql-edge"></a>Pacotes DAC da Base de Dados SQL em SQL Edge

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado, orientado para implantações ioT e borda. É construído com base nas versões mais recentes do Microsoft SQL Server Database Engine, que fornece capacidades de desempenho, segurança e processamento de consultas líderes na indústria. Juntamente com as principais capacidades de gestão de bases de dados relacionais líderes na indústria do SQL Server, o Azure SQL Edge fornece capacidade de streaming incorporada para análise em tempo real e processamento complexo de eventos.

O Azure SQL Edge também fornece uma implementação nativa de SqlPackage.exe que lhe permite implementar um pacote DAC de Base de [Dados SQL](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implementação do SQL Edge. Os dacpacs da Base de Dados SQL podem ser implantados para SQL Edge utilizando o parâmetro SqlPackage exposto através `module twin's desired properties` da opção do módulo SQL Edge:

```json
{
    "properties.desired":
    {
        "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
        "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
    }
}
```

|Campo | Descrição |
|------|-------------|
| SqlPackage | Armazenamento Azure Blob URI para o ficheiro *.zip que contém o pacote DAC da Base de Dados De Base de Dados SQL.
| ASAJobInfo | Armazenamento Azure Blob URI para o trabalho asa edge.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Utilize um pacote DAC de Base de Dados SQL com Borda SQL

Para utilizar um pacote DAC de Base de Dados SQL (*.dacpac) com SQL Edge, siga estes passos:

1. Crie ou extraia um pacote DAC de Base de Dados SQL. Consulte [a extração de um DAC a partir de uma base](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) de dados para obter informações sobre como gerar um pacote DAC para uma base de dados existente do Servidor SQL.

2. Feche o *.dacpac e carregue-o para uma conta de armazenamento Azure Blob. Para obter mais informações sobre o upload de ficheiros para o armazenamento do Blob Azure, consulte upload, download e lista de [blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso partilhado para o ficheiro zip utilizando o portal Azure. Para mais informações, consulte o [acesso do Delegado com assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo SQL Edge para incluir o URI de acesso partilhado para o pacote DAC. Para atualizar o módulo SQL Edge, tome estas medidas:

    1. No portal Azure, vá ao seu ioT Hub implantação.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página **IoT Edge,** encontre e selecione o IoT Edge onde o módulo SQL Edge está implantado.

    4. Na página do **dispositivo IoT Edge Device,** selecione **Módulo de Conjunto**.

    5. Na página **de módulos set,** selecione **Configure** contra o módulo SQL Edge.

    6. No painel **ioT Edge Custom Modules,** selecione **set module twin propriedades desejadas**. Atualize as propriedades desejadas para incluir o URI para a `SQLPackage` opção, como mostra o seguinte exemplo.

        > [!NOTE]
        > O SAS URI no JSON seguinte é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac",
                }
            }
        ```

    7. Selecione **Guardar**.

    8. Na página **de módulos set,** selecione **Next**.

    9. Na página **de módulos set,** selecione **Seguinte** e, em seguida, **Submeta**.

5. Após a atualização do módulo, o ficheiro de pacote SC é descarregado, desapertado e implantado contra a instância SQL Edge.

Em cada reinício do recipiente Azure SQL Edge, o pacote de ficheiros *.dacpac é descarregado e avaliado para alterações. Se for encontrada uma nova versão do ficheiro dacpac, as alterações são implementadas na base de dados do SQL Edge.

## <a name="next-steps"></a>Passos seguintes

- [Implementar o SQL Edge através](deploy-portal.md)do portal Azure .
- [Dados de fluxo](stream-data.md)
- [Machine learning e IA com ONNX em SQL Edge (Pré-visualização)](onnx-overview.md)
