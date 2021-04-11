---
title: Restaurar a base de dados de amostras AdventureWorks em SQL Managed Instance
description: Restaurar a base de dados de amostras AdventureWorks em SQL Managed Instance
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641811"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>Restaurar a base de dados de amostras AdventureWorks em SQL Managed Instance - Azure Arc

[AdventureWorks](/sql/samples/adventureworks-install-configure) é uma base de dados de amostras que contém uma base de dados OLTP que é frequentemente usada em tutoriais e exemplos. É fornecido e mantido pela Microsoft como parte das amostras do [sql Server GitHub repositório](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

Este documento descreve um processo simples para obter a base de dados de amostras AdventureWorks restaurada na sua SQL Managed Instance - Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Descarregue o ficheiro de backup adventureWorks

Descarregue o ficheiro de backup AdventureWorks (.bak) no seu contentor SQL Managed Instance. Neste exemplo, utilize o `kubectl exec` comando para executar remotamente um comando no interior do contentor SQL Managed Instance para descarregar o ficheiro .bak para dentro do recipiente. Descarregue este ficheiro a partir de qualquer local acessível `wget` se tiver outros ficheiros de backup de bases de dados que pretende puxar para estar dentro do contentor SQL Managed Instance. Uma vez dentro do recipiente SQL Managed Instance é fácil restaurar usando `RESTORE DATABASE` o T-SQL padrão.

Executar um comando como este para descarregar o ficheiro .bak substituindo o valor do nome do pod e nome do espaço antes de executá-lo.
> [!NOTE]
>  O seu contentor terá de ter conectividade com a Internet acima do 443 para descarregar o ficheiro do GitHub

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Exemplo

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>Restaurar a base de dados AdventureWorks

Da mesma forma, pode executar um `kubectl` comando executivo para utilizar a ferramenta `sqlcmd` CLI que está incluída no recipiente SQL Managed Instance para executar o comando T-SQL para RESTAURAR BASE DE DADOS.

Executar um comando como este para restaurar a base de dados. Substitua o valor do nome do pod, a palavra-passe e o nome do espaço antes de o executar.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Exemplo

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
