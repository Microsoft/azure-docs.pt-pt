---
title: Utilização de pacotes SQL Database DACPAC e BACPAC - Azure SQL Edge (Pré-visualização)
description: Saiba mais sobre a utilização de dacpacs e bacpacs em Azure SQL Edge (Preview)
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 52c8e9586d8ee53cdaac28cb1c48d2927d82c2ed
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89462764"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Pacotes SQL Database DACPAC e BACPAC em SQL Edge

Azure SQL Edge (Preview) é um motor de base de dados relacional otimizado, orientado para implementações de IoT e bordas. É construído nas versões mais recentes do Microsoft SQL Server Database Engine, que fornece capacidades de processamento de desempenho, segurança e consulta líderes do setor. Juntamente com as capacidades de gestão de bases de dados relacionais líderes no setor do SQL Server, o Azure SQL Edge fornece capacidade de streaming incorporada para análise em tempo real e processamento complexo de eventos.

O Azure SQL Edge também fornece uma implementação nativa de SqlPackage.exe que permite implementar um pacote [DE BASE DE DADOS SQL DACPAC e BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications) durante a implementação do SQL Edge. Os dacpacs sql Database podem ser implantados na SQL Edge utilizando o parâmetro SqlPackage exposto através `module twin's desired properties` da opção do módulo SQL Edge:

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
| SqlPackage | URI de armazenamento Azure Blob para o ficheiro *.zip* que contém o pacote SQL Database DAC ou BACPAC. O ficheiro zip pode conter tanto vários pacotes dac como ficheiros bacpac.
| ASAJobInfo | Azure Blob armazenamento URI para o trabalho ASA Edge.

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Utilize um pacote DAC de base de dados SQL com sql edge

Para utilizar um pacote DAC de base de dados SQL `(*.dacpac)` ou um ficheiro BACPAC com `(*.bacpac)` SqL Edge, siga estes passos:

1. Criar/Extrair um pacote DAC ou exportar um ficheiro Bacpac utilizando o mecanismo abaixo mencionado. 
    - Criar ou extrair um pacote DAC de base de dados SQL. Consulte [a extração de um DAC de uma base de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote DAC para uma base de dados do SQL Server existente.
    - Exportação de um pacote DAC implantado ou uma base de dados. Consulte [exportar uma aplicação de nível de dados](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) para obter informações sobre como gerar um ficheiro bacpac para uma base de dados do SQL Server existente.

2. Feche `*.dacpac` o ficheiro ou o `*.bacpac` ficheiro e faça o upload para uma conta de armazenamento Azure Blob. Para obter mais informações sobre o upload de ficheiros para o armazenamento do Azure Blob, consulte [upload, download e liste blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso partilhado para o ficheiro zip utilizando o portal Azure. Para obter mais informações, consulte [o acesso do Delegado com assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo SQL Edge para incluir o URI de acesso partilhado para o pacote DAC. Para atualizar o módulo SQL Edge, tome estes passos:

    1. No portal Azure, vá para a sua implantação IoT Hub.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página **IoT Edge,** encontre e selecione o IoT Edge onde o módulo SQL Edge está implantado.

    4. Na página do **dispositivo IoT Edge Device,** selecione **O Módulo de Conjunto**.

    5. Na página **de módulos set,** selecione **Configurar** contra o módulo SQL Edge.

    6. No painel **IoT Edge Custom Modules,** selecione **Definir as propriedades desejadas do módulo Twin**. Atualize as propriedades desejadas para incluir o URI para a `SQLPackage` opção, como mostra o exemplo seguinte.

        > [!NOTE]
        > O SAS URI no seguinte JSON é apenas um exemplo. Substitua o URI pelo URI real da sua implantação.

        ```json
            {
                "properties.desired":
                {
                    "SqlPackage": "<<<SAS URL for the *.zip file containing the dacpac and/or the bacpac files",
                }
            }
        ```

    7. Selecione **Guardar**.

    8. Na página **de módulos set,** selecione **Seguinte**.

    9. Na página **de módulos set,** selecione **Seguinte** e, em seguida, **Submeta.**

5. Após a atualização do módulo, o ficheiro do pacote é descarregado, desapertado e implantado contra a instância SQL Edge.

Em cada reinicio do recipiente Azure SQL Edge, o `*.dacpac` pacote de ficheiros é descarregado e avaliado para alterações. Se for encontrada uma nova versão do ficheiro dacpac, as alterações são implementadas na base de dados em SQL Edge. Ficheiros Bacpac 

## <a name="next-steps"></a>Próximos passos

- [Implementar sql edge através do portal Azure](deploy-portal.md).
- [Dados de Fluxo](stream-data.md)
- [Machine learning e IA com ONNX em SQL Edge (Pré-visualização)](onnx-overview.md)
