---
title: Utilização de pacotes SQL Database DACPAC e BACPAC - Azure SQL Edge
description: Saiba mais sobre o uso de dacpacs e bacpacs em Azure SQL Edge
keywords: SQL Edge, sqlpackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392253"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>Pacotes SQL Database DACPAC e BACPAC em SQL Edge

O SQL do Azure no Edge é um motor de base de dados relacional otimizado para implementações de IoT e no edge. É construído nas versões mais recentes do Microsoft SQL Database Engine, que fornece capacidades de processamento de desempenho, segurança e consulta líderes do setor. Juntamente com as capacidades de gestão de bases de dados relacionais líderes no setor do SQL Server, o Azure SQL Edge fornece capacidade de streaming incorporada para análise em tempo real e processamento complexo de eventos.

O Azure SQL Edge fornece mecanismos nativos que lhe permitem implantar um pacote [DE BASE DE DADOS SQL DACPAC e BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications) durante, ou após a implantação do SQL Edge.

Os pacotes daCpac e bacpac da SQL Database podem ser implantados na SQL Edge utilizando a `MSSQL_PACKAGE` variável ambiental. A variável ambiente pode ser configurada com qualquer uma das seguintes.  
- Uma localização de pasta local dentro do recipiente SQL contendo os ficheiros dacpac e bacpac. Esta pasta pode ser mapeada para um volume de hospedeiro utilizando pontos de montagem ou recipientes de volume de dados. 
- Um caminho de arquivo local dentro do mapeamento do recipiente SQL para o dacpac ou o ficheiro bacpac. Esta trajetória de ficheiro pode ser mapeada para um volume de hospedeiro utilizando pontos de montagem ou recipientes de volume de dados. 
- Um caminho de arquivo local dentro do mapeamento do recipiente SQL para um ficheiro zip contendo os ficheiros dacpac ou bacpac. Esta trajetória de ficheiro pode ser mapeada para um volume de hospedeiro utilizando pontos de montagem ou recipientes de volume de dados. 
- Um URL Azure Blob SAS para um ficheiro zip contendo os ficheiros dacpac e bacpac.
- Um URL Azure Blob SAS para um dacpac ou um ficheiro bacpac. 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Utilize um pacote DAC de base de dados SQL com sql edge

Para implantar (ou importar) um pacote DAC de base de dados SQL `(*.dacpac)` ou um ficheiro BACPAC utilizando o armazenamento `(*.bacpac)` Azure Blob e um ficheiro zip, siga os passos abaixo. 

1. Criar/Extrair um pacote DAC ou exportar um ficheiro Bacpac utilizando o mecanismo abaixo mencionado. 
    - Criar ou extrair um pacote DAC de base de dados SQL. Consulte [a extração de um DAC de uma base de dados](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/) para obter informações sobre como gerar um pacote DAC para uma base de dados do SQL Server existente.
    - Exportação de um pacote DAC implantado ou uma base de dados. Consulte [exportar uma aplicação de nível de dados](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/) para obter informações sobre como gerar um ficheiro bacpac para uma base de dados do SQL Server existente.

2. Feche `*.dacpac` o ficheiro ou o `*.bacpac` ficheiro e faça o upload para uma conta de armazenamento Azure Blob. Para obter mais informações sobre o upload de ficheiros para o armazenamento do Azure Blob, consulte [upload, download e liste blobs com o portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Gere uma assinatura de acesso partilhado para o ficheiro zip utilizando o portal Azure. Para obter mais informações, consulte [o acesso do Delegado com assinaturas de acesso partilhado (SAS)](../storage/common/storage-sas-overview.md).

4. Atualize a configuração do módulo SQL Edge para incluir o URI de acesso partilhado para o pacote DAC. Para atualizar o módulo SQL Edge, tome estes passos:

    1. No portal Azure, vá para a sua implantação IoT Hub.

    2. No painel esquerdo, selecione **IoT Edge**.

    3. Na página **IoT Edge,** encontre e selecione o IoT Edge onde o módulo SQL Edge está implantado.

    4. Na página do **dispositivo IoT Edge Device,** selecione **O Módulo de Conjunto**.

    5. Na página **de módulos set** e clique no módulo Azure SQL Edge.

    6. No painel do **módulo IoT Edge da atualização,** selecione **Variáveis ambientais**. Adicione a `MSSQL_PACKAGE` variável ambiental e especifique o URL SAS gerado no passo 3 acima como o valor para a variável ambiental. 

    7. Selecione **Atualizar**.

    8. Na página **de módulos set,** selecione **'Rever + criar.**

    9. Na página **de módulos set,** selecione **Criar**.

5. Após a atualização do módulo, os ficheiros do pacote são descarregados, desapertados e implantados contra a instância SQL Edge.

Em cada reinicio do recipiente Azure SQL Edge, o SQL Edge tenta descarregar o pacote de ficheiros com fecho e avaliar as alterações. Se for encontrada uma nova versão do ficheiro dacpac, as alterações são implementadas na base de dados em SQL Edge.

## <a name="known-issue"></a>Questão conhecida

Durante algumas implementações DACPAC ou BACPAC, os utilizadores podem encontrar um tempo limite de comando, resultando na falha da operação de implantação da dacpac. Se encontrar este problema, utilize a SQLPackage.exe (ou SQL Client Tools) para aplicar o DACPAC ou BACPAC. 

## <a name="next-steps"></a>Passos seguintes

- [Implementar sql edge através do portal Azure](deploy-portal.md).
- [Dados de Fluxo](stream-data.md)
- [Machine learning e IA com ONNX em SQL Edge](onnx-overview.md)